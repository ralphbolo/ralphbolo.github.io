---
layout: post
title:  "Rebuilding Rails: ActiveRecord"
date:   2018-06-13 21:12:39 -0400
categories: Rails
---

ActiveRecord is a gem that provides abstractions for representing business objects, business logic, database queries and persistence. It allows the programmer to quickly build the model in the MVC architecture, the accompanying table in the database and the mapping of objects to the database.

Rebuilding ActiveRecord consisted of building the base class, database connection adapters and query generation.

The general pattern in most of the core gems in Rails consist of having a base class for your gem usually base.rb and different modules for each features of the gem.

For example.
``` ruby 
#/lib/active_record/base.rb
module ActiveRecord
  class Base
    ...
  end
end 
```

``` ruby
#/lib/active_record/connection_adapter.rb
module ActiveRecord
  module ConnectionAdapter
    class SqliteAdapter
      ...
    end
  end
end 
```

``` ruby
#/lib/active_record/relation.rb
module ActiveRecord
  class Relation
    ...
  end
end
```

Another pattern used in rails is the adapter pattern. The adapter, in this case, allows the programmer to safely switch databases and makes sure the common methods can be used safely. For example in ActiveRecord the database connection uses an adapter pattern.

Ex. usage
``` ruby
ActiveRecord::Base.establish_connection(
  adapter:  "mysql2",
  host:     "localhost",
  username: "myuser",
  password: "mypass",
  database: "somedatabase"
)
```

    
The simplified class structure looks something like ...

``` ruby
module ActiveRecord
  module ConnectionAdapter
    class AbstractAdapter
      
      #abstract methods
      
    end

    class SqliteAdapter < AbstractAdapter
      #concrete methods
    end

    class MySqlAdapter < AbstractAdapter
      #concrete methods
    end
  end
end 
```