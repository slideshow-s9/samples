title: merb - The Pocket Rocket Framework
gradient: top-bottom black maroon

# merb - The Pocket Rocket Framework

Rowan Hick @ Toronto Rails Night, June 2008

(Adapted S9 Version from Original PDF Slide Deck)

# Discussion tonight

* Intended for people who don't know what Merb is
* Good grasp of Rails concepts
* Not patient enough to google/irc/blog search etc to get started on Merb
* Touch on a lot of features
* All links/references up on [work.rowanhick.com](http://work.rowanhick.com) tomorrow


# About Rowan Hick

* Product Development Mgr for a startup in Toronto
* Full time with Rails for 2.5 years
* Previously PHP/MySQL for 4 years
* 6 years Prior QA/BA/PM for Enterprise CAD/CAM software dev company

# Objects of Desire

Super Bike   

Sport Cars

* What are these doing in a presentation about Ruby web frameworks?

# Bikes vs Cars

The Porsche, you just open the door, turn the key, and drive. It has a/c, a stereo, plush upholstery, everything. 
Accidents can and do happen but few and far between, but pretty user friendly.

The sports bike, it's raw, unadulterated. You can't hop on it wrapped in cotton wool, way easier to get burned. 
However it's faster, easier to replace, modify and tune things at a lower cost.

# Parallels to the world of MVC?

Rails is...

* Very user friendly & well documented
* suitable for 80% most web projects
* a turn-key framework.

# ...and

Merb is...

* aiming for that other 20%
* fast as all hell
* going to require a little more skill to get up and running with
* "the framework for hackers"

# Same same but different

* It's actually quite similar! (workflow, layout)
* However ... don't start emailing merb groups asking for feature XYZ to be implemented "like it is in Rails"
* Don't expect your app to port without code changes to Merb (although you'd be suprised how little needs to change)

# Who Built it

* Ezra Zygmuntowicz
* Yehuda Katz
* + many others
* You could get some glory too...
* [gweezlebur.com/2008/2/1/so-you-want-to-contribute-to-merb-core-part-1](http://gweezlebur.com/2008/2/1/so-you-want-to-contribute-to-merb-core-part-1)

# Key Philosophy

* All you need and none you don't
* No code is faster than no code

<!-- -->

* Anything that slows Merb down is a bug (my personal favourite)

# Merb-core -more gems

* What the? Isn't it just Merb?
* `merb-core` is the basic slimmed down bare minimum code
* `merb-more` contains niceties to help you along your way (action-args, assets, builder, cache, freezer, gen, haml, jquery, mailer, parts, slices)

# Switzerland

* Merb is the Switzerland of Ruby frameworks
* Doesn't care that you might favour Datamapper over ActiveRecord
* Lets you choose ORM, Test Framework, JS Framework, Rack Adaptor

# Getting started

To start off with get both core and more

```
gem install merb-core merb-more
```

Choose your ORM

1. `gem install merb-activerecord`
2. `gem install sequel`
3. `gem sources -a http://gems.datamapper.org && sudo gem install addressable english rspec data_objects do_mysql do_postgres do_sqlite3 dm-core dm-more`



# `merb-gen`

* Your new friend
* generates code/files for your app

```
rowan$ merb-gen app speeddemon
 RubiGen::Scripts::Generate
   create log
   create app
     ...etc
```

* `merb-gen app yourapp --flat` (or) `--very-flat` (puts your application on a serious diet)

# Differences

| . | Rails | Merb |
|---|-------|------|
| **Routes** | `config/routes.rb` | `config/router.rb` |
| **Initialization** | `config/environment.rb` | `config/init.rb` |
| Environment specific | `config/environments/*` | `config/environments/*` |
| **Plugins** | `vendor/plugins` | `gems` |
| **Migrations** | `db/migrate` | `schema/migrations` |
| **Testing** | `test` | `test` (or) `spec` |
| Public | `public`  | `public` |
| Models | `app/models` | `app/models` |
| Controllers | `app/controllers` | `app/controllers` |
| Helpers | `app/helpers` | `app/helpers` |
| **Lib** | `lib/` | (`config` in `init.rb`) |

# ~~vendor/plugins~~ gems

* Everything is a gem
* Freeze your gems with your app
* **gem install -i somegem ~/yourapp/gems/ --no-ri --no-rdoc**
* convert rails plugins, start with **merb-gen plugin plugin_name**
* Lots underway [github.com/search?q=merb](http://github.com/search?q=merb)

# edit `config/init.rb`

Choose your ORM

```
use_orm: activerecord
use_test: rspec
```

Include dependencies (eg mailer)

```
dependencies "merb_helpers", "merb-parts"
Merb::BootLoader.after_app_loads do
  # Add dependencies here that must load after the
application loads:
  dependencies "merb-mailer"
  # dependency "magic_admin" # this gem uses the
app's model classes
end
```


# I want my `database.yml`

Patience. Because you've told merb you want active record, just fire merb for the first time:

```
rowan-laptop:speeddemon rowan$ merb
 ~ Loaded DEVELOPMENT Environment...
 ~ loading gem 'merb_activerecord' ...
 ~ loading gem 'activerecord' ...
 ~ No database.yml file found in /Users/rowan/testing/speeddemon/config.
 ~ A sample file was created called database.sample.yml for you to copy and edit.
```

Copy, edit and save.

# `merb-gen` generators

Let's you know what you can create automagically:

```
rowan-laptop:b2b_merb rowan$ merb-gen generators

.....

Installed Generators

  Rubygems: authenticated, controller, database_sessions_migration,
migration, model, part_controller, resource, resource_controller
```

see more [rubigen.rubyforge.org](http://rubigen.rubyforge.org)

# `merb-gen model mymodel`

Synonymous with `script/generate mymodel`

Extra points:

* dependent on ORM - creates migration or not
* dependent on test framework - creates spec or unit test


# Setup your first REST resource

* `merb-gen` again

```
$ merb-gen resource Product title:string
```

* Sets up controller, views, model, test code
* Get coding!

# Controller code

Looks reasonably similar:

```
class Products < Application
  before :check_access_level
  provides :xml

  def index
    @products = Product.find(:all, :order => :sort_order )
    display @products
  end

  def show
    @product = Product.find_by_product_id(params[:id])
    raise NotFound unless @product
    display @product
  end
  ...
```


# Key Differences

| Rails | Merb |
|-------|------|
| `products_controller` | `products` |
| `redirect_to( hash )` | `redirect( string )` |
| `respond_to` | `provides` / `only_provides` / `display` |
| `before`/`after_filter` | `before`/`after` |
| `except`  | `exclude`  |
| `render(hash)` *optional  | `render` or `render('hello world')` *mandatory to get a view|
| `logger` | `merb.logger` |
| `url_for` | `url`  |



# `display`

* Display handles mime type response

```
provides :xml
def index
  @product = Product.find(params[:id])
  display @product
end
```

* Goto `http://127.0.0.1:4000/product/2.xml`
* Display then looks for `index.xml.erb`, if not found then calls `@product.to_xml`


# Syntactic sugar

* Making controller methods look more like methods

```
def my_action(id)
  product = Product.find(id)
end
```

* Automagically assigns `id` from `params[:id]`
* In `merb-more` (`merb-action-args`)

# Part Controllers

* Reusable 'components' for use in your views
* Not accessible from outside world, but can see params hash
* Their own generator
* Again in `merb-more` (`merb-parts`)

# Implementing Parts

In `config/init.rb`

```
dependency 'merb-parts'
```

Use `merb-gen` to set it up for you

```
$ merb-gen part_controller widget
...
create app/helpers/widget_part_helper.rb
create app/parts/widget_part.rb
create app/parts/views/widget_part/index.html.erb
```

Edit action(s) then call it from a view

```
<%= WidgetPart => :index %>
```


# Exceptions

* Handled in `app/controllers/exceptions.rb`

```
def show
  @product = Product.find_by_product_id(params[:id])
  raise NotFound unless @product
  display @product
end
```

* `exceptions.not_found` method
* render out in that view what you want to show for that error

# Router is Powerful

Example from Yehudas slides at RailsConf

```
Merb::Router.prepare do |r|
  r.match(%r{/login/(.*)}, :user_agent => /MSIE/).
  to(:controller => "acount",
     :action => "login",
     :id => "[1]"
end 
```

# Pressing the GO button

* Simple

```
merb -p 3000 -e development
```

* Fires up a mongrel instance through merb
* Hit it just like regular old rails app

# Hooking up to webserver

* Merb implements a Rack adaptor
* Allows you to use different types of application server depending on your needs. Example:

```
merb -p 3000 -e production -a thin
```

* Mongrel, Thin, Ebb, Eventd Mongrel, Passenger
* Connect Apache/Nginx -> Merb (not) Apache -> Mongrel -> Merb

# Resources

* [merbivore.com](http://merbivore.com)
* [wiki.merbivore.com](http://wiki.merbivore.com)
* Great videos [mwrc2008.confreaks.com](http://mwrc2008.confreaks.com)
* [merbunity.com](http://merbunity.com)
* [merborials.com](http://merborials.com)
* [github.com/search?q=merb](http://github.com/search?q=merb)
* [www.slideshare.net/search/slideshow?q=merb](http://www.slideshare.net/search/slideshow?q=merb)
* [www.slideshare.net/search/slideshow?q=datamapper](http://www.slideshare.net/search/slideshow?q=datamapper)

# The million dollar question

Should I use it ?

