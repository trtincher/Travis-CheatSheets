# RoR Authentication with Cookies and Session

[YouTube tutorial Link](https://www.youtube.com/playlist?list=PLgYiyoyNPrv_yNp5Pzsx0A3gQ8-tfg66j)

## Setup

```
//in terminal
rails new authentication_app --database
```

Don't use the api only flag. You need all the dependences to use session.

- look up how to add these dependences back in

```
cd authentication_app

tat
(?look this up)

rails db:create
```

Go to rubygems.org (insures you have the latest version)

- look up bcrypt

```
vim Gemfile

//in Gemfile add
gem 'bcrypt', //pull versuib from rubygems.org
gem 'rack-cors', :require => 'rack/cors'

//in terminal
bundle

```

Add config

```
//in terminal
touch config/initializers/cors.rb

touch config/initializers/session_store.rb

```

In config/initializers/cors.rb

```rb
# inserts middleware
# whitelists only certain domains
Rails.application.config.middleware.insert_before 0, Rack::Cors do
    # allow development block
    allow do
        #localhost may change depending on frontend environment
        origins "http://localhost:3000"
        #adds resources allowed
        #credentials: true allows frontend to communicate with backend very important!
        resource "*", headers: :any, methods: [:get, :post, :put, :patch, :delete, :options, :head], credentials: true
    end

    #allow production block
    allow do
        #use produciton url here
        origins "http://jdh-authentication-app-react.herokuapp.com"
        #adds resources allowed
        #credentials: true allows frontend to communicate with backend very important!
        resource "*", headers: :any, methods: [:get, :post, :put, :patch, :delete, :options, :head], credentials: true
    end
end
```

In config/initializers/session_store.rb

```rb
#cookie store
#key is session name
#domain is production

Rails.application.config.session_store :cookie_store, key: "_authentication_app", domain: "jdh_authentication_app_api.herokuapp.com"
```

In config/routes.rb

```rb
Rails.application.routes.draw do
    root to: "static#home"
end

```

create controller

```
touch app/controllers/static_controller.rb
```

in app/controllers/static_controller.rb

```rb
class StaticController < ApplicationController
    def home
        render json: {status: "It's working" }
    end
end

```

test server

```
rails s
```

## Create Models

User model

```
rails g model User email password_digest
```

- 'password_digest' is important
  - with this rails knows you want password confirmation

```
//check migration

//run migration
rails db:migrate
```

in app/model/user.rb

```rb
class User < ApplicationRecord
    has_secure_password

    validates_presence_of :email
    validates_uniqueness_of :email
end
```

test

```
//in terminal

rails c

User.create!(email: "z@dev.com", password: "asdfasdf", password_confirmation: "asdfasdf")
```

- password gets incripted
- bcript uses secret key and uses as 'salt'

in config/routes.rb

```rb
Rails.application.routes.draw do
  #add this
  resources :sessions, only: [:create]

  root to: "static#home"
end
```

create sessions controller

```
touch app/controllers/sessions_controller.rb
```

in sessions_controller.rb

```rb
class SessionsController < ApplicationController
    #post request
    def create
        user= User
        .find_by(email: params["user"]["email"])
        .try(:authenticate, params["user"]["password"])

        if user
            #this creats the cookie
            session[:user_id] = user.id
            render json: {
                status: :created,
                #for readablility
                logged_in: true,
                #send back user for frontend
                user: user
            }
        else
            #send error on non authentication
            render json: {status: 401}
        end
    end

end
```

in config/routes.rb

```rb
Rails.application.routes.draw do
  resources :sessions, only: [:create]

  #add this
  resources :registrations, only: [:create]

  root to: "static#home"
end
```

create registrations controller

```
touch app/controllers/registrations_controller.rb
```

in registrations_controller.rb

```rb
class RegistrationsController < Application Controller
    def create
        user = User.create!(
            email: params['user']['email']
            password: params['user']['password']
            #you need this on the backend but not nessisarly on the frontend just have params['user']['password'] again
            password_confirmation: params['user']['password_confirmation']
        )

        if user
            session[:user_id] = user.id
            render json: {
                status: :created,
                user: user
            }
        else
            render json: { status: 500}
        end
    end
end
```

in app/controllers/aplication_controller.rb

```rb
class ApplicationController < ActionController::Base
    #this logic is happing somewhere so don't need it here
    skip_before_action :verify_authenticity_token
end
```

Test it out

```
//in terminal
rails s

//in new terminal tab
curl --header "Content-Type: application/json" \
--request POST \
--data '{"user": {"email": "z@dev.com", "password": "asdfasdf"}}' \
http://localhost:3000/sessions
```

ERRORS:

```
2020-09-15 12:43:25 -0500: HTTP parse error, malformed request (): #<Puma::HttpParserError: Invalid HTTP format, parsing fails.>

syntax error
--request POST was --request Post
```

Now need ability to check as see if user is logged in or not and the ability to log them out

```
//in config/routes.rb

Rails.application.routes.draw do
  resources :sessions, only: [:create]
  resources :registrations, only: [:create]
  delete :logout, to: "sessions#logout"
  get :logged_in, to: "sessions#logged_in"

  root to: "static#home"
end
```

add current_user_concern

```
touch app/controllers/concerns/current_user_concern.rb
```

in current_user_concern.rb

```rb
module CurrentUserConcern
    extend ActiveSupport::Concern

    included do
        before_action :set_current_user
    end

    def set_current_user
        if session[:user_id]
            @current_user = User.find(session[:user_id])
        end
    end
end
```

add get and delete route to session controller

```rb
#in app/controllers/sessions_controller.rb
class SessionsController < ApplicationController
    include CurrentUserConcern
    #post request
    def create
        ...[collapsed]
    end

    def logged_in
        if @current user
            render json: {
                logged_in: true,
                user: @current_user
            }
        else
            render json: {
                logged_in: false
            }
        end
    end

    def logout
        reset_session
        render json: {status: 200, logged_out: true}
    end
end
```
