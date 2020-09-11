# Many to Many

```rb
# spin up rails
rails new playlists_songs_app_api --api -d postgresql --skip-git
#create db
cd playlists_songs_app_api
rails db:create

#thing tables
rails g scaffold playlist title
rails g scaffold song title artist duration:integer
#relation table
rails g scaffold roster playlist:references song:references plays:integer fav:boolean

#run migrations
rails db:migrate

#add active record relationships
class Playlist < ApplicationRecord
    has_many :rosters

    has_many :songs, through: :rosters
end

class Song < ApplicationRecord
    has_many :rosters

    has_many :playlists, through: :rosters
end
```
