---
layout: post
title:      "has_secure_password 'B'Crypted"
date:       2020-06-08 16:53:13 +0000
permalink:  has_secure_password_bcrypted
---


When I first started learning more of the behind-the-scenes of how the internet and secure sites worked, I was fascinated about how developers secured sensitive information. The gem that was introduced to me through the Sinatra section was bcrypt.

At first I was a little bit confused about how it worked — about what it was doing behind the scenes. First we went over how it was implemented.

Make sure the gem is in the gemfile

```
gem 'bcrypt'

```

Next, your user table must have a column called 'password_digest'. So your create_table migration could look something like this: 

```
create_table :users do |t|
      t.string :username
      t.string :password_digest
end

```

Then in your user model to add the phrase 'has_secure_password', so your model looked like this: 

```ruby
class User < ActiveRecord::Base
  has_secure_password
end

```

Then somehow it magically protected the user's password. But how? What exactly was it doing for me?

From the lessons, I learned that I would set up my signup form to be something like this:

```html
<h3>Sign Up</h3>
<form action="/signup" method="POST">
  <input type="text" name="username" placeholder="Username:">
  <input type="password" name="password" placeholder="Password:">
  <input type="submit" value="Sign Up">
</form>

```

I knew from previous lessons that the text following name= was the text that refered to the name of the column in the users table. Right away, I noticed that I was using "password" to refer the the password_digest column instead of "password_digest," which is different than how I'd referenced table columns in forms before and how I was referencing the 'username' column in the form above.

As I thought about it more, I realized that we would never want to store a users password as plaintext in the table, so I came to the conclusion that bcrypt must be running something that caught the password on the way to the table and secured it before it was saved.

That meant that bcrypt's 'has_secure_password' must be a macro, a code that wrote more code, just like attr_accessor wrote both the reader and writer methods for classes. I also knew that the code must also be intercepting the create method that comes from inheriting from ActiveRecord::Base, since it was securing the password before it was saved to the table.

As the lesson continued, I learned about another method that 'has_secure_password' must be writing, #authenticate. This method is used to make sure that the user's password was correct when they were logging in. So the controller path would look something like the following: 

```ruby
post "/login" do
    user = User.find_by(username: params[:username])

    if user && user.authenticate(params[:password])
      session[:user_id] = user.id
      redirect "/account"
    else
      redirect "/failure"
    end
  end
```

I tested this in my terminal using rake console and discovered that authenticate returns the user if the password is correct and false if it is not.

```ruby
user = User.new(name: 'Sue', password: 'tooeasy')
user.save
user.authenticate('notright')      # => false
user.authenticate('tooeasy') # => user
```

This ensures that the user will only be logged in if they exist and their password is correct or else they will be directed to the failure path.

I wanted to know a little bit more about what exactly BCrypt was doing, so I looked at the docs and played around in my terminal using my rake console and I discovered that I could call #password_digest directly. 

```console
[13] pry(main)> user = User.new
=> #<User:0x00007f9cc071e558 id: nil, username: nil, password_digest: nil>
[14] pry(main)> user.password = nil
=> nil
[15] pry(main)> user.password_digest # => nil
=> nil
[16] pry(main)> user.password = 'mUc3m00RsqyRe'
=> "mUc3m00RsqyRe"
[17] pry(main)> user.password_digest # => "$2a$10$4LEA7r4YmNHtvlAvHhsYAeZmk/xeUVtMTYqwIvYY76EW5GUqDiP4."
```

But what was that giant string of numbers, letters, and symbols? After doing a bit more research, I discovered a great [article on Auth0](https://auth0.com/blog/hashing-in-action-understanding-bcrypt/) that despite at times being a bit over my head, as a newbie to securing passwords, was able to shed a little light on how the password is encrypted. BCrypt used both salts and hashs when encrypting the password. A salt is some random data that is used in addition to the hash data to make a password even more secure and a hash uses a mathematical algorithm to map data to fixed-size bit string.

At this point, I realized that there is so much more to learn about how to secure passwords, but at least I had a better understanding of BCrypt's #has_secure_password.

