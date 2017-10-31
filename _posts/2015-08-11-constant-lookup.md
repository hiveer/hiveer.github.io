---
layout: post
---
#### Foreword
  这是一篇转载，[原文](https://cirw.in/blog/constant-lookup) 是我读过的，关于Ruby 常量寻找的最好的文章！
  Ruby is flexible enough for you to express yourself. But sometimes, you'll be confused by this.
  When we access a constant like `FooClass`, Ruby will go to `Module.nesting`, `Module.nesting.first.ancestors`
and `Object.ancestors` if `Module.nesting.first` is a nil or a module.

#### Module.nesting

```ruby
module A
  module B; end
    module C
      module D
        B == A::B
      end
    end
  end
end
```
It first looks for A::C::D::B (doesn't exist), then A::C::B (still doesn't exist), and then finally A::B (which does).

**notice:**
If you've ever tried to take a short-cut when re-opening a module, you may have noticed that constants from skipped
namespaces aren't available. This is because the outer namespaces are not added to Module.nesting.

    module A
      module B; end
    end

    module A::C
      B
    end
    # NameError: uninitialized constant A::C::B
#### Ancestors
**_If the constant cannot be found by looking at any of the modules in Module.nesting
Ruby takes the currently open module or class, and looks at its ancestors._**

    class A
      module B; end
    end

    class C < A
      B == A::B
    end
    # => true

The currently open class or module is the innermost class or module statement in the code.
A common misconception is that constant lookup uses self.class, which is not true.

    class A
      def get_c; C; end
    end

    class B < A
      module C; end
    end

    B.new.get_c
    # NameError: uninitialized constant A::C

#### Object
when `Module.nesting == []` that means you are at the top level, and so constant lookup starts at the currently open
class and its ancestors. While there's no class or module statement that you can see, it is
taken for granted that at the top level of a ruby file the currently open class is Object:

    class Object
      module C; end
    end
    C == Object::C
    # => true

Almost all classes in Ruby inherit from Object, so Object is almost always included in
the list of ancestors of the currently open class, and thus its constants are almost always available.

That said, if you've ever used a BasicObject, and noticed that top-level constants are missing, you now know why.
Because BasicObject does not subclass Object, all of the constants are not in the lookup chain:

    class Foo < BasicObject
    Kernel
    end
    # NameError: uninitialized constant Foo::Kernel

For cases like this, and anywhere else you want to be explicit, Ruby allows you to use ::Kernel to access Object::Kernel.

**_Ruby assumes that you will mix modules into something that inherits from Object. So if the currently open module is a module
it will also add Object.ancestors to the lookup chain so that top-level constants work as expected:_**

    module A; end
    module B;
      A == Object::A
    end

#### class_eval
As mentioned above, constant lookup uses the currently open class, as determined by class and module statements.
Importantly, if you pass a block into class_eval or module_eval (or instance_eval or define_method), this won't change constant lookup.
It continues to use the constant lookup at the point the block was defined:

    class A
      module B; end
    end

    class C
      module B; end
      A.class_eval{ B } == C::B
    end

**_Confusingly however, if you pass a String to these methods, then the String is evaluated with Module.nesting
containing just the class itself (for class_eval) or just the singleton class of the object (for instance_eval)._**

    class A
      module B; end
    end

    class C
      module B; end
      A.class_eval("B") == A::B
    end

#### Other gotchas
Finally I want to point out that if you're in a singleton class of a class, you don't get access to constants defined in the class itself:

    class A
      module B; end
    end

    class << A
      B
    end
    # NameError: uninitialized constant Class::B

This is because the ancestors of the singleton class of a class do not include the class itself, they start at the Class class.

    class A
      module B; end
    end

    class << A; ancestors; end
    [Class, Module, Object, Kernel, BasicObject]

In a similar vein, it may also worth noting that superclasses of things in Module.nesting are ignored. For example:

    class A
      module B; end
    end
    class C < A
      class D
        B
      end
    end
    # NameError: uninitialized constant C::D::B




