---
layout: post
title:      "Using Active Storage in Rails"
date:       2020-07-11 14:55:38 +0000
permalink:  using_active_storage_in_rails
---

When I started my rails project I knew that I wanted to have some images, after all a picture is worth a thousand words, right? I knew how to use the images folder to hold images that I was going to use for my site, ie) logo, hero images, etc. But I knew that I also wanted users to be able to add images themselves. I did a little research and found that Rails came with a solution called ActiveStorage.

To get started I had to install it by running ```rails active_storage:install``` in the command line. After it is installed, I saw that a new migration appeared that created two new tables, active_storage_blobs and active_storage_attachments. According to the Active Storage [README](https://github.com/rails/rails/blob/d3893ec38ec61282c2598b01a298124356d6b35a/activestorage/README.md) on github, "Active Storage uses polymorphic associations via the Attachment join model, which then connects to the actual Blob" and the "Blob models store attachment metadata (filename, content-type, etc.), and their identifier key in the storage service". 

I wasn't sure what a polymorphic relationship was, so I looked it up and discovered that a model can be associated with more than one other model. For example, if you have three tables reviews, events, and restaurants and you want the reviews to belong to an event or a restaurant. In order to do reviews would need a polymorphic column. There are several things that are required to use a polymorphic association, but I don't want to go into that in this article. If you're want to know more, feel free to check out the [rails guides](https://guides.rubyonrails.org/association_basics.html#polymorphic-associations).

Active Storage can be used to store files in various cloud services like, Amazon S3, Google Cloud, and Microsoft Azure, but I decided to start off by just storing images locally. To do this make sure that I had to make sure that in my config/environments/development.rb the active storage service was set to local.

```

config.active_storage.service = :local

```

Now how do we use it? After running `rails db:migrate`, only a few changes are needed to use Active Storage. (Note that you do not have to have columns in your tables for the images, so no changes needed there).

First, add the association to your model:

```

class Book < ApplicationRecord
  has_one_attached :cover
	
end

```

This associates the attachment and blob with a book instance. There is also a has_many_attached option that you can use if you have a model that can have more than one image.

Since I wanted users to be able to add upload a cover image to a book when they create one, I also needed to update the strong params in my Book Controller, so cover would be accepted.

```

def book_params
    params.require(:book).permit(:title, :summary, :cover)
  end
	
```

Great! Now I had by backend all set up, but I still needed my users to be able to add a image file. After doing a little research about what Form Helpers would help me do this, I discovered the file_field helper. So in my book's new view, I added

```

<%= form_for @book do |f| %>
  <%= f.label :title %>
  <%= f.text_field :title %><br>
  <%= f.label :summary %><br>
  <%= f.text_area :summary %><br>
	<%= f.label :cover %>
  <%= f.file_field :cover %><br>
  <%= f.submit %>
<% end %>

```

But did it work? To see what was going on when the form was submitted I took a look at my terminal that was running my rails server. My params included a key of cover, so far so good! I noticed that in addition to creating the book instance, it also created an ActiveStorage::Blob instance and associated that blob with the book by creating an ActiveStorage::Attachment instance. Everything seemed to be working fine.

Next I needed to display the image. After reading a little more, I found that displaying the image was pretty simple. Just use the image_tag! In my book show view I added

```

<%= image_tag(@book.cover, alt: "book cover") %>
<h1><%= @book.title %></h1>
<p><%= @book.summary %></p>

```

Now I was curious, what was happening behind the scenes when I went to the show page and viewed the image. In my terminal I saw that what was usually a single SELECT request to the table became three separate requests, one to the books table, one to the attachments table, and one to the blobs table.

For whatever reason when I first decided I wanted to use images in my project, I thought it was going to be really difficult. However, after learning a bit about Active Storage, it was pretty straight forward to use.
