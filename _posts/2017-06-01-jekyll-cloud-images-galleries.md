---
layout: post
published: true
title: "Jekyll with Cloud Images and Galleries"
author: Varun
image: /img/gallery-Thales-flickr.jpg
credit: Thales @ flickr
categories: Technology
galleries:
  example:
    pictures:
      - url: "cloudinary-localhost.png"
        caption: "This is what happens when using the Cloudinary plugin while drafting in your local environment."
---

_As far as blogging platforms go, Jekyll is both a blessing and a curse. It has taken time to turn it into something I like; let me show you what I've done._

I had previouly created my blog with WordPress and moved to Drupal. I don't need dynamic online content creation, and I was tired of getting notices from Dreamhost that my site was compromised and I needed to clean it up. Jekyll is static, manageable with git, uses ruby, and easy for me to configure to publish with a simple `git push live`.

{% include image.md image_url="/img/2017/06/cloudinary-logo.png" %}

These days with smartwatches, 5K desktops, and everything in between, it's important to serve up appropriately-sized images for every site visitor. You don't want pixelated images, you don't want people to use up their data plan, and you don't want them to wait forever for pixels they're never going to see. [Cloudinary](http://cloudinary.com) is great for this service, as they handle resizing and CDN needs with aplomb.

#### Problems

**Cloud, only sometimes**: [nhoizey](https://github.com/nhoizey) wrote a [Jekyll plugin](https://nhoizey.github.io/jekyll-cloudinary/) which adds a `{% raw %}{% cloudinary image_path %}{%endraw%}` liquid tag. It is highly customizable, but the most important features for me are pass-through upload and defining imagesets. I don't have to worry about uploading images to them myself, and Cloudinary serves up resized versions appropriate for different devices via their CDN. However, **the plugin doesn't work for images when they're hosted on localhost, only when your posts are in a production (internet-accessible) environment!**

{% include gallery.html gallery_id="example" %}

**Galleries, but easy**: I want to be able to easily put small galleries of images together (like above) with captions. **[Lightbox2](http://lokeshdhakar.com/projects/lightbox2/)** serves the stylesheets and javascript, and it does so marvelously. *However*, creating galleries out of liquid tags for each image is too cumbersome. **I'm too lazy for that.**

#### Solution

**Jekyll is highly customizable.** The front matter section of each post is YAML which you can use for any purpose. Therefore I used it to define as many galleries for the post as I'd like.

Advantages:

* Galleries for a post are kept in the same file. No bouncing back and forth between different documents.
* Posts read cleanly when editing. A huge amount of image, gallery, and caption markdown is abstracted away.
* Images use Cloudinary when the blog is pushed live, but just read from jekyll server when in `developer_mode`.
* Images are expected to exist in a folder according to the post's year and month. This allows a reasonable level of image file organization, without going too crazy.

Disadvantages:

* Cloudinary transformations and resizes are not performed when drafting your posts locally.
* If captions are key to your post's narrative, it can be annoying to scroll to the top of the .md to edit the front-matter and go back down to your content markdown.
* I sometimes forget the format of the `include` statement.

#### Code

**Site config**

{% highlight yaml %}
# _config_dev.yml
developer_mode: true
{% endhighlight %}

Then start your local server using `jekyll serve --config _config_dev.yml,_config.yml`.

**Post front matter**

{% highlight yaml %}
# my-blog-post.md
----
title: "Jekyll with Cloud Images and Galleries"
galleries:
  gallery1:
    pictures:
      - url: "picture1.jpg"
        caption: "Picture 1 has a nice caption."
      - url: "picture2.jpg"
        caption: "Picture 2 has a caption"
      - url: "picture3.jpg"
  gallery2:
    pictures:
      - url: "you-get-it.png"
        caption: "I think you see how this works now."
----
{% endhighlight %}

**Gallery include**

{% highlight liquid linenos %}
{%raw%}
<!-- /_includes/gallery.html -->
{% capture year %}{{ page.date | date: "%Y" }}{% endcapture %}
{% capture month %}{{ page.date | date: "%m" }}{% endcapture %}
<div class="mdl-grid">
{% for picture in page.galleries[include.gallery_id].pictures %}
  {% if page.galleries[include.gallery_id].pictures.size > 1 %}
  <div class="mdl-cell mdl-cell--4-col">
  {% else %}
  <div class="mdl-cell mdl-cell--12-col">
  {% endif %}
    {% capture picture_url %}/img/{{year}}/{{month}}/{{picture.url}}{% endcapture %}
    <a href="{{picture_url}}" data-lightbox="{{include.gallery_id}}" title="{{picture.caption}}">
      {% if site.developer_mode %}
        <img src="{{picture_url}}"/>
      {% else %}
        {% cloudinary {{picture_url}} %}
      {% endif %}
    </a>
  </div>
{% endfor %}
</div>
{%endraw%}
{% endhighlight %}

Now inserting a gallery in a post is as simple as including
{% raw %} `{% include gallery.html gallery_id="gallery1" %}`{% endraw %}.

**Image include**

For inline images, without galleries and captions, I defined `/_includes/image.md` which works similarly.

{% highlight liquid linenos%}
{%raw%}
{% if site.developer_mode %}
  ![]({{include.image_url}})
{% else %}
  {% cloudinary {{include.image_url}} %}
{% endif %}
{%endraw%}
{% endhighlight %}

#### A little more

The [`jekyll-cloudinary`](https://nhoizey.github.io/jekyll-cloudinary/) plugin already makes extensive use of _config.yml variables. It may make sense to add a 'passthrough' option, similar to my `developer_mode` _config variable. Unfortunately, since no resizes or other transforms would be performed on the images, it may lead to confusion for rookie bloggers. It has been **[discussed by nhoizey](https://github.com/nhoizey/jekyll-cloudinary/issues/20)** and hasn't happened yet, so I'm not too keen on submitting a pull request. Maybe I'll make my own branch, which you can pull if you'd like.

I also don't handle image titles and alt tags yet, so accessibility suffers right now. Some small modification to the front matter schema and image markdown may suffice.

#### Acknowledgements

Christian Specht had a great [blog post](https://christianspecht.de/2014/03/08/generating-an-image-gallery-with-jekyll-and-lightbox2/) which inspired me to use YAML to define the gallery details. I took a different approach, though: one better suited to my blogging needs. The [`jekyll-cloudinary`](https://nhoizey.github.io/jekyll-cloudinary/) plugin is essential, of course. [`jekyll-mdl`](https://github.com/gdg-managua/jekyll-mdl) was a starting point for my site's theme, but I found it needed some modifications to be usable.

*-vkm*

##### Who's Varun?

_I most recently was the founder of an HR tech startup, [Disqovery](http://disqovery.com). I have worn many hats, and I like making things. I also like talking business. You can reach me at [me@varunmehta.com](mailto:me@varunmehta.com), [Mastodon](https://fosstodon.org/@smartperson), [Github](https://github.com/smartperson), and [LinkedIn](https://linkedin.com/in/varunkmehta)._