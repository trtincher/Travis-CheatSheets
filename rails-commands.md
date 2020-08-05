# Rails Commands

### Kill Rails Server
```
#In your terminal find PID with :
 lsof -wni tcp:3000

 #then kill the process with
 kill -9 PID

# or to kill all servers with rails in the name

killall -9 rails

```

### Set up Awesome Print for Terminal
[Awesome Print Docs](https://gauravsohoni.wordpress.com/2017/07/26/set-up-awesome_print-as-default-rails-console-formatter/)

### Rails Start-Up Commands
- Make new rails app_api
```rb
                            -optional flags depending on setup
rails new name_app_api --api -d postgresql --skip-gi
```

- cd into name_app_api\
- create data base
```rb
rails db:create
-you will see
Created database 'new_app_api_development'
Created Database 'new_app_api_test'
```

### Scaffolding
Rails is so developer friendly that it even has a command to help scaffold an app with following:

- Controller
- Model
- A Migration
- Routes
- Views for every standard controller action (index, edit, - show, new)

Let's scaffold Locations with lat and lng as decimals, and also a name column.

```
$ rails g scaffold location lat:decimal lng:decimal name
```

This has added boilerplate files and code to

- db/migrate
- app/models
- config/routes.rb
- app/controllers

### Generating Migrations
- to generate a migration
```rb
                        -Create is key word followed by name
rails generate migration CreateTodos
or
rails g migration CreateTodos
```
- this creates a schema
- update migration
```rb
-class names must be capitalized and singular
class CreateTodos < ActiveRecord::Migration
  def change
     create_table :todos do |t|
        t.string :title
        t.boolean :completed
     end
  end
end
```
- run migration
```rb
rails db:migrate
```
- This creates a schema in schema.rb
- NEVER CHANGE SCHEMA DIRECTLY, ALWAYS THROUGH A MIGRAGTION

#### Column Manipulation
##### Add
- create new migration with Add key word
```rb
rails g migration AddColumnDescriptionToTodos
```
- In migration file use add_column method
```rb
class AddDescriptionToTodos < ActiveRecord::Migration
  def change
    # Method    Table   Column       Datatype
    add_column :todos, :description, :string
  end
end
```
- Run migrate `rails db:migrate`
### Migration Edits
- Undoing migration:
`rails db:rollback`
- Rollback to beginning: `rails db:migrate Version=0` 
- - or any previous version
- Delete migration: `rails destroy migration NameOfMigration`
- - or `rails d migration NameOfMigration`


##### Rename
- create new migration with Rename key word
```rb
rails g migration RenameDescriptionColumn
```
- use rename_column method
```rb
def change
  rename_column :todos, :description, :details
end
```
`rails db:migrate`
##### Remove
- create new migration with Remove key word
```rb
rails g migration RemoveDescriptionColumn 
```
- use remove_column method
```rb
def change
  remove_column :todos, :details, :string
end
```
`rails db:migrate`

#### Table Manipulation
##### Change Name
- Create Migration
```rb
rails g migration ChangeTableName
```
- Use rename_table method
```rb
class ChangeTableName < ActiveRecord::Migration[6.0]
  def change
    rename_table :characters, :matrix_characters
  end
end
```
`rails db:migrate`

### Rails Console commands
- to enter console
``` rb
rails dbconsole
```
- see psql commands 

### Create Models
- The model generator made both the book.rb file and the table migration file:
```rb
rails generate model name_of_file
```
- then fill out your migration and run migration
##### or
- once you have a schema
- make file with singular name of schema in app/modles
- example => todo.rb
- make class in todo.rb
```rb
            - inherites from ApplicationRecord
class Todo <  ApplicaitonRecord
end
```
#### Adding Relationships to models
- schema folder should look like this:
```rb
  enable_extension "plpgsql"

  create_table "products", force: :cascade do |t|
    t.string "title"
    t.integer "price"
  end

  create_table "reviews", force: :cascade do |t|
    t.integer "rating"
    t.string "content"
    t.integer "product_id"
  end
end
```
- Here Todo Model contains user_id column
```rb
IN THE USER MODEL, SPECIFY THE MODEL’S RELATION TO THE Todo MODE
class User < ApplicationRecord
  has_many :todos
End

IN THE Todo MODEL, SPECIFY THE MODEL’S RELATION TO THE USER MODEL

class Todo < ApplicationRecord
  belongs_to :user
end

```

### Active Record Commands
- to inter AR rails console
- `rails console  ` or `rails c`
- Basic Commands
```rb
Todo.all
    - finds all Todo
Todo.count
    - counts Todos
    - can pass in column Todo.count(id)
Todo.new(title: "First todo", completed: true)
    - creates new Todo
Todo.save
    - saves new Todo
Todo.create(title: "Something", completed: false)
    - creates and saves
Todo.update()
Todo.update_all()
Todo.destroy
```
- Related Items
```rb
User -> Todos
User.find(1).tods.all

Todos -> User
Todo.where(user_id: 1) or
Todo.where(user_id: user.id)
```

### Seeding the Database
- Inside `db/seeds.rb`
- Single Entry
```rb
Model.create({ column: data, column: data })
```
- Bulk Entry
```rb
User.create([{ name: "Neff" }, { name: "Jacc" }, { name: "Snoop" }])
```
- To create four individual Todo entries: (note that these have the foreign keys):
```rb
Todo.create(title: "Accentuate the Positive", completed: false, user_id: 1)
Todo.create(title: "Eliminate the Negative", completed: false, user_id: 1)
Todo.create(title: "Latch on to the Affirmative", completed: false, user_id: 2)
Todo.create(title: "Don't mess with Mister In-Between", completed: false, user_id: 3)
```
- Run Seed File `rails db:seed`

Can drope databases and re-seed with : `rails db:reset`

### Routes
We can format our data this way with the .to_json method that takes a hash as an argument that we can use to include the temperatures.
```rb
def index
  @locations = location.all

  render json: @locations.to_json(inclide: :temperatures)
end


def show
  render json: @location.to_json(include: :temperatures)
end
```

#### Nested routes
We will want to nest our create action inside the locations routes.
```rb
Rails.application.routes.draw do
  resources :temperatures, only: [:index]
  resources :locations, only: [:index, :show] do
    resources :temperatures, only: [:create]
  end
end
```
When we rails routes, we will get this:
```
               Prefix Verb URI Pattern                                    Controller#Action
         temperatures GET  /temperatures(.:format)                        temperatures#index
location_temperatures POST /locations/:location_id/temperatures(.:format) temperatures#create
            locations GET  /locations(.:format)                           locations#index
             location GET  /locations/:id(.:format)                       locations#show
```
Our create action URI has changed to reflect that we are creating a Temperature only in relation to a Location. The param we received is location_id

We will want to add the incoming location_id to our new temperature record:
```rb
@temperature.location_id = params[:location_id]
```
Create a new Temperature using temperature_params and on the new temperature, set the id column to the location_id from the url

Also remove location: @temperature, it's a pain that will try to force a redirect and will give us errors in Postman if it stays.



#### Side Note
##### Hirb Gem for better table formatting
- Add gem 'hirb' to our Gemfile
- Run bundle install to install the gem
- Now run rails c
- In the console type: require 'hirb'
- In the console type: Hirb.enable
- or
- put require 'hirb'; Hirb.enable in your ~/.irbrc
