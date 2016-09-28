# Rails API Relationships Diagnostic

Place your responses inside the fenced code-blocks where indivated by comments.

1.  Describe a reason why a join tables may be valuable.

```sh
  Join tables allow the possibility of many-to-many relationships (doctors have
  many patients through appointments, books have many borrowers through loans,
  etc.).
```

1.  Provide a database table structure and explain the Entity Relationship that
describes a many-to-many relationship for `Profiles`, `Movies` and `Favorites`
(Think of Netflix). A `Profile` has a `given_name`, `surname` and `email` and a
`Movies` have `title`, `release_date`, and `length` and `Favorites` would be the
join table with references to `Movies` and `Profiles`.

```sh
  Profiles
  ________
  given_name:string
  surname:string
  email:string

  Movies
  ______
  title:string
  release_date:date
  length:integer

  Favorites
  _________
  movie:references
  profile:references
  (These columns would actually be movie_id and profile_id)
```

1.  For the above example, what needs to be added to the Model files?

```rb
class Profile < ActiveRecord::Base
  has_many :favorites, dependent: :destroy
  has_many :movies, through: :favorites
end
```

```rb
class Movie < ActiveRecord::Base
  has_many :favorites, dependent: :destroy
  has_many :profiles, through: :favorites
end
```

```rb
class Favorite < ActiveRecord::Base
  belongs_to :movie, inverse_of: :favorites
  belongs_to :profile, inverse_of: :favorites
end
```

1.  What is the purpose of a serializer? What would our `Profile` serializer look
like to show all movies favorited by a profile on
`http://localhost:3000/profiles/1`

```sh
  Serializers show the information the developer wants to show and hides the other
  information.
```

```rb
class ProfileSerializer < ActiveModel::Serializer
  attributes :movies
end
```

1.  What would the command be to _scaffold_ out a **join table** for Favorites from
the above `Movies` and `Profiles`.

```sh
  bundle exec rails g scaffold Favorites movie:references profile:references
```

1.  What is `Dependent: Destroy` and where/why would we use it?

```sh
  Dependent: Destroy allows us to delete all rows of the join table that contain
  the member being deleted on one of the tables being joined. For example, we
  can delete Jurassic Park from our database. If we delete it without Dependent:
  Destroy, every profile that had Jurassic Park as a Favorite Movie would now
  have a Favorite that had a nil movie entry. If we delete it with Dependent:
  Destroy, these profiles would simply have one less Favorite, the one that was
  Jurassic Park. It would work the same way when deleting a specific Profile.
```

1.  Think of **ANY** example where you would have a one-to-many relationship as well
as a many-to-many relationship in an application. You only need to list the
description about the resources and how they relate to one another.

```sh
  One-to-many: A sports team can have many players, but a player only belongs to
    one sports team at a time. (Patriots have Tom Brady, Jimmy Garoppolo, etc.
    but Tom Brady only plays for the Patriots.)
  Many-to-many: Without borrowing from the three examples we have been working
    with, a store can sell many different products, and those products can be
    sold in many different stores. (Walmart sells toasters, blenders, phones, etc.
    and toasters can be bought at Walmart, Target, Sears, etc.)
```
