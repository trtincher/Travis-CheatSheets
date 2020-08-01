# Rails Commands

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


#### Side Note
##### Hirb Gem for better table formatting
- Add gem 'hirb' to our Gemfile
- Run bundle install to install the gem
- Now run rails c
- In the console type: require 'hirb'
- In the console type: Hirb.enable