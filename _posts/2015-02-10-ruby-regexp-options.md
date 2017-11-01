---
layout: post
---

#### Used with regular expression literal
`i` means case insensitive  `/Abc/i`
`o` means single interpolation

~~~ruby
# if /#{str} world!/ if going to be excuted many times in a block
# we can use option 'o' to store its value and then we just need
# to do it once, like
3.times do
    puts string.match(/#{str} world!/o)
end
~~~

`m` means multi-line

~~~ruby
"abc \ndef".match(/abc.+def/m)
 => #<MatchData "abc \ndef">
~~~

`x` means expanded mode

>  The expanded mode option causes unescaped whitespace in the regular expression itself to be ignored, as well as comments inside the regex.

~~~ruby
puts %r{
  a+ # Some a characters
  [0-9]+ # Some numbers
  :[0-9] # A colon then a number
}x =~ "aaa23:7"
~~~

#### Used with Regexp#new

~~~ruby
Regexp.new('dog', Regexp::EXTENDED)   #=> /dog/x

#other constants(didn't find the constant for options 'o')

Regexp::IGNORECASE
Regexp::MULTILINE
~~~
