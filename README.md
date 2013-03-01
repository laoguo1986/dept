#创建rails程序
<pre>
rails new dept -d=mysql
cd dept
rake db:create
</pre>
#脚手架
<pre>
rails g scaffold Product title:string description:text image_url:string price:decimal
rake db:migrate
</pre>
#自动输入数据
##db/seeds.rb
<pre>
Product.delete_all
Product.create(title: 'CoffeeScript',
  description: 
    %{<p>
        CoffeeScript is JavaScript done right. It provides all of JavaScript's
	functionality wrapped in a cleaner, more succinct syntax. In the first
	book on this exciting new language, CoffeeScript guru Trevor Burnham
	shows you how to hold onto all the power and flexibility of JavaScript
	while writing clearer, cleaner, and safer code.
      </p>},
  image_url:   'cs.jpg',    
  price: 36.00)
# . . .
Product.create(title: 'Programming Ruby 1.9',
  description:
    %{<p>
        Ruby is the fastest growing and most exciting dynamic language
        out there. If you need to get working programs delivered fast,
        you should add Ruby to your toolbox.
      </p>},
  image_url: 'ruby.jpg',
  price: 49.95)
# . . .

Product.create(title: 'Rails Test Prescriptions',
  description: 
    %{<p>
        <em>Rails Test Prescriptions</em> is a comprehensive guide to testing
        Rails applications, covering Test-Driven Development from both a
        theoretical perspective (why to test) and from a practical perspective
        (how to test effectively). It covers the core Rails testing tools and
        procedures for Rails 2 and Rails 3, and introduces popular add-ons,
        including Cucumber, Shoulda, Machinist, Mocha, and Rcov.
      </p>},
  image_url: 'rtp.jpg',
  price: 34.95)
</pre>
##app/assets/images/
拷贝图片到该目录。
##rake db:seed
##app/assets/stylesheets/products.css.scss
<pre>
// Place all the styles related to the Products controller here.
// They will automatically be included in application.css.
// You can use Sass (SCSS) here: http://sass-lang.com/
/* START_HIGHLIGHT */
.products {
  table {
    border-collapse: collapse;
  }

  table tr td {
    padding: 5px;
    vertical-align: top;
  }

  .list_image {
    width:  60px;
    height: 70px;
  }

  .list_description {
    width: 60%;

    dl {
      margin: 0;
    }

    dt {
      color:        #244;
      font-weight:  bold;
      font-size:    larger;
    }

    dd {
      margin: 0;
    }
  }

  .list_actions {
    font-size:    x-small;
    text-align:   right;
    padding-left: 1em;
  }

  .list_line_even {
    background:   #e0f8f8;
  }
  .list_line_odd {
    background:   #f8b0f8;
  }
}
/* END_HIGHLIGHT */
</pre>
##app/views/layouts/application.html.erb
<!DOCTYPE html>
<html>
<head>
<title>Depot</title>
<%= stylesheet_link_tag "application", :media => "all" %>
<%= javascript_include_tag "application" %>
<%= csrf_meta_tags %>
</head>
<body class='<%= controller.controller_name %>'>
<%= yield %>
</body>
</html>
