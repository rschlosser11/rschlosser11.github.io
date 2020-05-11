---
layout: post
title:      "Using #send"
date:       2020-05-11 02:52:53 +0000
permalink:  using_send
---


While working through the Ruby lessons, I learned about object oriented programming with the use of classes and methods. During this process, I wondered how to create multiple instances of different classes without manually writing each method. Several lessons later that I learned about mass assignment.

The #instance method sorted through a hash and used #send method. 

```
def initialize(attributes)
    attributes.each {|key, value| self.send(("#{key}="), value)}
  end
```


At first, I didn’t really understand how #send worked. Just by looking at it I could see that it was a method being called upon ‘self’, which, in this case, was an instance of the class, but I didn’t know why the key was being forced into a string. I did a little research.

The #send method is an instance method of the Objects class in Ruby and, since everything in Ruby is an object, it can be called on everything. The first argument passed to #send is the name of the method that you want to ‘send’ to the object and can be expressed as a string or a symbol. Any subsequent arguments are the arguments that you are sending to the method you put as the first argument. 

I broke it down with a simple example and thought through it some more.

```
class Person
  attr_accessor :name, :birthday, :hair_color, :eye_color, :height

  def initialize(attributes)
    attributes.each {|key, value| self.send(("#{key}="), value)}
  end
end
```

I know that attr_accessor is used to write both the reader and writer methods. 

```
def name=(name) # Writer Method 'name='
    @name = name 
  end

  def name # Reader Method 'name'
    @name 
  end
```

Next, I wanted to create a new instance of a person.

```
student1 = Person.new({name: 'John', hair_color: 'brown', eye_color: 'green'})
```

For each key/value pair the writer method would be called using the name provided by the key, that had become a string using string interpolation, and passed the value as the argument.

```
student1 = Person.new({name: 'John', hair_color: 'brown', eye_color: 'green'})

student1.name=('John') # => 'John'
student1.hair_color=('brown') # => 'brown'
student1.eye_color=('green') # => 'green'
```

I noticed that this looked a lot like what we learned early on in object oriented ruby and it seemed like #initialize could be written as follows:

```
def initialize(name, hair_color, eye_color)
    @name = name
    @hair_color = hair_color 
    @eye_color = eye_color
  end
```

Why couldn't we just use this? But then I realized that this means that every new Person instance would HAVE to be initialized with a name, hair_color, and eye_color, but what if that information wasn't included for a Person, but one of the other attributes, like height, was? 

Then I thought, why can't I just add '=nil' to each argument in initialize and set the default value to 'nil'. I realized that this would work if I had just a couple of possible attributes, but what if I had more? I know I would be a bit irritated by having to add '=nil' to several, but what if there were 20 or 30 possibilities? It would quickly become an inefficient use of my time and energy.

That's when the true power of #send dawned on me. If I was collecting information about a Person, like email, age, gender, etc. for a marketing company and wanted to keep all of that information within a single instance of a Person. I'd likely need to import hundreds and thousands and I wouldn't know what information I had or had not been able to capture for each individual. Using #send would enable me to create all of these instances of a Person, without worrying about what information I did or did not have.  
