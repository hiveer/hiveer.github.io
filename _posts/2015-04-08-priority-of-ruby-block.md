---
layout: post
---

#### Two format for block

##### bracket syntax

    method {}

##### do .. end syntax

    method do
      ...
    end

#### The difference

    1.upto 3 do |x|
      puts x
    end

    1.upto 3 { |x| puts x }
    # SyntaxError: compile error

Cause `{}` has a higher priority, the second example will be a case like this

    method x y

`I think it's easy for you to look out the problem here, Ruby have no this param syntax for method call`

~~~ ruby
def puts
  puts 'yes'
end
~~~

```ruby
def new_method
end
```

#### Another example

    method1 method2 do
      puts "hi"
    end

    method1 method2 {
      puts "hi"
    }

The first case, `method1` will be called with the `do...end` block and with method2 as the argument, which is equivalent to
`method1(method2) { puts 'hi' }`

The second case, `method2` will be called with the `{}` block and the excuted result will be the argument of `method1`

```javascript
/* Some pointless Javascript */
var rawr = ["r", "a", "w", "r"];
```
