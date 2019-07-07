1. `config/initializers/routing_draw.rb`
```
# Adds draw method into Rails routing
# It allows us to keep routing splitted into files
class ActionDispatch::Routing::Mapper
  def draw routes_name
    instance_eval(File.read(Rails.root.join("config/routes/#{routes_name}.rb")))
  end
end
```

2. mkdir folder config/routes
3. create file admin.rb, user.rb
3. in `config/routes.rb` file
```
draw :admin
draw :user
```
