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
##app/views/products/index.html.erb
##app/models/product.rb
class Product < ActiveRecord::Base
validates :title, :description, :image_url, presence: true
validates :price, numericality: {greater_than_or_equal_to: 0.01}
validates :title, uniqueness: true
validates :image_url, allow_blank: true, format: {
with:
%r{\.(gif|jpg|png)$}i,
message: 'must be a URL for GIF, JPG or PNG image.'
}
end
#商品目录显示
<pre>
rails generate controller Store index
</pre>

##routes
<pre>
root to: 'store#index', as: 'store'
</pre>
##app/controllers/store_controller.rb
<pre>
def index
@products = Product.order(:title)
end
</pre>

rm public/index.html

##显示顺序偏好
default_scope :order => 'title'
##app/views/store/index.html.erb

##app/assets/stylesheets/store.css.scss

##加入侧边栏
###app/views/layouts/application.html.erb
###app/assets/stylesheets/application.css.scss

#购物车
<pre>
rails generate scaffold cart
rake db:migrate
</pre>

##current_cart 方法
<pre>
class ApplicationController < ActionController::Base
   protect_from_forgery


   private

   def current_cart
       Cart.find(session[:cart_id])
       rescue ActiveRecord::RecordNotFound
       cart = Cart.create
       session[:cart_id] = cart.id
       cart
       end
end
</pre>

##将产品放到购物车
<pre>
rails generate scaffold line_item product_id:integer cart_id:integer
rake db:migrate
</pre>

app/models/cart.rb
<pre>
has_many :line_items, dependent: :destroy
</pre>

app/models/line_item.rb
<pre>
belongs_to :product
belongs_to :cart
</pre>

app/models/product.rb
<pre>
  has_many :line_items
  before_destroy :ensure_not_referenced_by_any_line_item
  attr_accessible :description, :image_url, :price, :title
  validates :title, :description, :image_url, presence: true
  validates :price, numericality: {greater_than_or_equal_to: 0.01}
  validates :title, uniqueness: true
  validates :image_url, allow_blank: true, format: {
    with:    %r{\.(gif|jpg|png)\Z}i,
    message: 'must be a URL for GIF, JPG or PNG image.'
  }
  validates :title, length: {minimum: 10}

  private

    # ensure that there are no line items referencing this product
    def ensure_not_referenced_by_any_line_item
      if line_items.empty?
        return true
      else
        errors.add(:base, 'Line Items present')
        return false
      end
    end
end
</pre>

##添加一个按钮

