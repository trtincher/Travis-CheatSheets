# Rails One To Many Creation


```rb
# Make Rails API
rails new products_reviews_app_api --api -d postgresql --skip-git

# Create Database
rails db:create


#check out nested_scaffold gem @
```
[nested scaffold gem docs](https://github.com/amatsuda/nested_scaffold)

```rb
#Make Scaffolds
rails generate scaffold products name price:integer img
rails g scaffold review title content author
#Run Migrate
rails db:migrate

#Make seed data in seeds.rb
Product.creat([...])
#Run seed data
rails db:seed

#check rails routes
rails routes

#Nest Routes
#in cofig/routes.rb
Rails.applicaiton.routes.draw do
    resources :products do
        resources :reviews
    end
end
#check routes
rails routes
#reviews is now only accessable through products
/products/:product_id/reviews(.:formate)

#Add Foreign Key
#good pracatice is to do this through a migration
rails g migration AddProductIdToReviews
#edit migration
class AddProductIdToReviews < ActiveRecord::Migration[6.0]
    def change
        add_column :reviews, :product_id, :integer
    end
end
#run migration
rails db:migrate

#Add relations to models
class Product < ApplicationRecord
    has_many :reviews
end

class Review < ApplicationRecord
    belongs_to :product
end

#test association
rails c
Product.find(1).reviews
#nothing has been seeded yet but it exits
=> #<ActiveRecord::Associations::CollectionProxy []>

#Add product reviews via rails c
Product.find(1).reviews.create(title: "first review", content: "not a bad product", author: "Terminator 2")
Product.find(1).reviews.create(title: "second review", title: "middling product I'd say", author: "Chingy Right Thurr")

#check reviews
Product.find(1).reviews
Review.where(product_id: 1)

#Trimming Your routes
#can use only: and except: methods
Rails.applicaiton.routes.draw do
    resources :products, only: [:index, :show]do
        resources :reviews, except: [:show, :update, :destroy]
    end

    resources :reviews, only: [:update, :destroy]
end

#after trims Controllers look like this
###################################################
class ProductsController < ApplicationController
  before_action :set_product, only: [:show]

  # GET /products
  def index
    @products = Product.all

    render json: @products, status: 200
  end

  # GET /products/1
  def show
    @product = Product.find(params[:id])

    render json: @product, status: 200
  end

  private
    # Use callbacks to share common setup or constraints between actions.
    def set_product
      @product = Product.find(params[:id])
    end

    # Only allow a trusted parameter "white list" through.
    def product_params
      params.require(:product).permit(:name, :price, :img)
    end
end
#######################################################################
class ReviewsController < ApplicationController
  before_action :set_review, only: [ :update, :destroy]

  # GET /reviews
  def index
    @reviews = Review.where(product_id: params[:product_id])

    render json: @reviews
  end

  # POST /reviews
  def create
    @review = Review.new(review_params)
    @review.product_id = params[:product_id]

    if @review.save
      render json: @review, status: :created
    else
      render json: @review.errors, status: :unprocessable_entity
    end
  end

  # PATCH/PUT /reviews/1
  def update
    if @review.update(review_params)
      render json: @review
    else
      render json: @review.errors, status: :unprocessable_entity
    end
  end

  # DELETE /reviews/1
  def destroy
    @review.destroy
  end

  private
    # Use callbacks to share common setup or constraints between actions.
    def set_review
      @review = Review.find(params[:id])
    end

    # Only allow a trusted parameter "white list" through.
    def review_params
      params.require(:review).permit(:title, :content, :author)
    end
end

#CORS Cofiguration
#in gemfile on line 26 uncomment
gem 'rack-cors'
#run
bundle
#in config/initializers/cors.rb
#allow * origins
Rails.application.config.middleware.insert_before 0, Rack::Cors do
  allow do
    origins '*'

    resource '*',
      headers: :any,
      methods: [:get, :post, :put, :patch, :delete, :options, :head]
  end
end



```
