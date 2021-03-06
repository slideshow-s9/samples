title: Going Camping - A Web Microframework in 4k Ruby
gradient-colors: black grey


# Going Camping - A Web Microframework in 4k Ruby

[Jeremy McAnally](http://www.jeremymcanally.com) @ GoRuCo (Gotham Ruby Conference) 2007, New York City

(Adapted S6/S9 Version from Original PDF Slide Deck)


# About Jeremy McAnally

* Still in college
* I dig writing
* [Humble Litte Ruby Book](http://infoq.com/minibooks/ruby)
* [Ruby in Practice Book](http://manning.com/mcanally)

# What is Camping?

* A web microframework
* 4 Kilobytes
* Written by _why the lucky stiff

<p style="font-size: 10px; color: white; background-color: black; padding: 5px; border: silver thick groove; -moz-border-radius: 11px;"><code>
 %w[rubygems active_record markaby metaid ostruct].each {|lib| require lib}
 module Camping;C=self;module Models;end;Models::Base=ActiveRecord::Base
 module Helpers;def R c,*args;p=/\(.+?\)/;args.inject(c.urls.detect{|x|x.
 scan(p).size==args.size}.dup){|str,a|str.gsub(p,(a.method(a.class.primary_key
 )[]rescue a).to_s)};end;def / p;File.join(@root,p) end;end;module Controllers
 module Base;include Helpers;attr_accessor :input,:cookies,:headers,:body,
 :status,:root;def method_missing(m,*args,&blk);str=m==:render ? markaview(
 *args,&blk):eval("markaby.#{m}(*args,&blk)");str=markaview(:layout){str
 }rescue nil;r(200,str.to_s);end;def r(s,b,h={});@status=s;@headers.merge!(h)
 @body=b;end;def redirect(c,*args);c=R(c,*args)if c.respond_to?:urls;r(302,'',
 'Location'=>self/c);end;def service(r,e,m,a);@status,@headers,@root=200,{},e[
 'SCRIPT_NAME'];@cookies=C.cookie_parse(e['HTTP_COOKIE']||e['COOKIE']);cook=
 @cookies.marshal_dump.dup;if ("POST"==e['REQUEST_METHOD'])and %r|\Amultipart\
 /form-data.*boundary=\"?([^\";,]+)\"?|n.match(e['CONTENT_TYPE']);return r(500,
 "No multipart/form-data supported.")else;@input=C.qs_parse(e['REQUEST_METHOD'
 ]=="POST"?r.read(e['CONTENT_LENGTH'].to_i):e['QUERY_STRING']);end;@body=
 method(m.downcase).call(*a);@headers["Set-Cookie"]=@cookies.marshal_dump.map{
 |k,v|"#{k}=#{C.escape(v)}; path=/"if v != cook[k]}.compact;self;end;def to_s
 "Status: #{@status}\n#{{'Content-Type'=>'text/html'}.merge(@headers).map{|k,v|
 v.to_a.map{|v2|"#{k}: #{v2}"}}.flatten.join("\n")}\n\n#{@body}";end;def \
 markaby;Class.new(Markaby::Builder){@root=@root;include Views;def tag!(*g,&b)
 [:href,:action].each{|a|(g.last[a]=self./(g.last[a]))rescue 0};super end}.new(
 instance_variables.map{|iv|[iv[1..-1].intern,instance_variable_get(iv)]},{})
 end;def markaview(m,*args,&blk);markaby.instance_eval{Views.instance_method(m
 ).bind(self).call(*args, &blk);self}.to_s;end;end;class R;include Base end
 class NotFound<R;def get(p);r(404,div{h1("#{C} Problem!")+h2("#{p} not found")
 });end end;class ServerError<R;def get(k,m,e);r(500,markaby.div{h1 "#{C} Prob\
 lem!";h2 "#{k}.#{m}";h3 "#{e.class} #{e.message}:";ul{e.backtrace.each{|bt|li(
 bt)}}})end end;class<<self;def R(*urls);Class.new(R){meta_def(:inherited){|c|
 c.meta_def(:urls){urls}}};end;def D(path);constants.each{|c|k=const_get(c)
 return k,$~[1..-1] if (k.urls rescue "/#{c.downcase}").find {|x|path=~/^#{x}\
 \/?$/}};[NotFound,[path]];end end end;class<<self;def escape(s);s.to_s.gsub(
 /([^ a-zA-Z0-9_.-]+)/n){'%'+$1.unpack('H2'*$1.size).join('%').upcase}.tr(' ',
 '+') end;def unescape(s);s.tr('+', ' ').gsub(/((?:%[0-9a-fA-F]{2})+)/n){[$1.
 delete('%')].pack('H*')} end;def qs_parse(qs,d='&;');OpenStruct.new((qs||'').
 split(/[#{d}] */n).inject({}){|hsh,p|k,v=p.split('=',2).map{|v|unescape(v)}
 hsh[k]=v unless v.empty?;hsh}) end;def cookie_parse(s);c=qs_parse(s,';,') end
 def run(r=$stdin,w=$stdout);w<<begin;k,a=Controllers.D "/#{ENV['PATH_INFO']}".
 gsub(%r!/+!,'/');m=ENV['REQUEST_METHOD']||"GET";k.class_eval{include C
 include Controllers::Base;include Models};o=k.new;o.service(r,ENV,m,a);rescue\
 =>e;Controllers::ServerError.new.service(r,ENV,"GET",[k,m,e]);end;end;end
 module Views; include Controllers; include Helpers end;end
</code></p>


# What is Camping?

* It uses Model-View-Controller 
* Active Record for the models
  * You probably know what this is.
* Markaby for the views
  * In a word: Rubylicious.
* Has (or can easily have) most of the good controller goodness that Rails has.

# What is Camping?

* Everything typically lives in one file
* This file has modules inside of it for each piece of the pie
* File must be named for the head module
* `Camping.goes`

# When to use it

* When Rails is too fat
* Right tool for the right job
* You ain't gonna need it!

# Controlling Camping

Classes define actions

```
module Blog::Controllers
  class Index < R '/'
    def get
      # Do something fun
    end
  end
end
```

Define methods on those for request types (i.e., `get` and `post`)

# Controlling Camping

Routes are regular expressions

```
class Edit < R '/(\w+)/edit'
  def get(page_name)
  end
end
```

# The View

Views are constructed using Markaby

```
p "Hello, world!"
div "This is a #{word}!", :id => "example"
```

No more ERb!

```
<p>Hello, world!</p>
<div id="example">
  This is a <%%= word %>!
</div>
```

# The View

* Tags are constructed using Ruby methods
* Blocks build the tag hierarchy
* Attributes are fed as parameters

```
p "Hello, world!"

div :id => "example" do
  span "This is a #{word}!"
end
```

```
div :id => 'pants' do
  ul do
    ['blue', 'red', 'fancy'].each do |item|
      li item
    end
  end
end
```


# Models

Models are just ActiveRecord classes.

```
class Panda
end
```

If you don't know what ActiveRecord is...

```
my_panda = Panda.new
my_panda.name = "Randall"
my_panda.fav_food = "Dirt"
my_panda.save
```

Defaults to SQLite

# Models

Since it's just ActiveRecord, you can use migrations

```
class CreatePan < V 1.0
  def self.up
    create_table :pans do |t|
      t.column :type, :string
      t.column :name, :text
    end
  end
end
```

You can also use other model-enhancing stuff

```
module CMS::Models
  class Item < Base
    acts_as_versioned
  end
end
```

# Putting It Together - Deployment

* Deploying a Camping application is as easy or easier than deploying a Rails application
* It can be deployed in a wide array of environments...
  * Simple Camping server way
  * "Standard" proxied deployment
  * Probably others

# What if...?

* "What if my application gets big and unwieldy?"
* You can break it into separate files...
  * Just require them and include the modules
  * If it gets this big though, think about Rails

# But It Ain't Rails! - ActiveSupport

* You can pull a lot of Rails over to Camping
* ActiveSupport for example...

```
"plants".singularize
# => plant

14.even?
# => true

10.megabytes
# => 10485760
```

# Moving away from SQLite

* Camping defaults to SQLite for its database
* BUT! You can use other RDBMS options.

```
host : 127.0.0.1
port : 3301
server : mongrel
database :
  :adapter: mysql
  :database: myapp
  :hostname: localhost
  :username: user
  :password: passw0rd!
log:
  my.log
```

# Sessions

Like Rails, Camping has sessions built right in

```
require "camping/session"

module Importer
  include Camping::Session
end

# Now I can use @state to
# access sessions values
```

# Testing

* Use Mosquito for bugfree Camping
* Not in the default package, but still available

```
require 'mosquito'
require 'inventory'

Inventory.create
include Inventory::Models

class TestInventory < Camping::FunctionalTest

  def test_view_item
    get '/view/1'
    assert_response :success
  end
end
```

# Form Helpers

* Gregory Brown posted a nice snippet to get a `form` equivalent in Camping
* I'm currently working on extending that to a usable `form_for`

# Serving static files

Static files can be embedded in a Camping application

```
class Style < R '/base.css'
  def get
    @headers['Content-Type'] = 'text/css'
    "body { color: #ddd; }"
  end
end

# In our view...
link :href => R(Style), :rel => 'stylesheet', :type => 'text/css'
```

They can also be read from disk

```
class Index < R '/'
  def get
    File.read('index.html')
  end
end
```

Optimally, your front end web server would handle them

# Before/after filters

Camping allows you to override the `service` method to put logic before and after it handles a request

```
module WikiFilter
  def service(*args)
    @value = "Hello!"
    super(*args)
    @value = nil
  end
end

module Wiki
  include WikiFilter
end
```

# Decamper

* A little application to convert your Camping application to a Rails application
* Status is unknown

# Kindling

* A new library by me that takes the top 5-10 "Railsisms" and lets you use them in Camping
* Currently supports...
  * Easy before/after filters
  * Static file download/upload
  * Easy addition of template handlers, with default support for ERb


