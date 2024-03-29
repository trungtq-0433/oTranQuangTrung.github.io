In the case that you wish to sort by some complex value, such as the result of a SQL function, you may do so using scopes. 
In your model, define scopes whose names line up with the name of the virtual field you wish to sort by, as so:

```
class Person < ActiveRecord::Base
  scope :sort_by_reverse_name_asc, lambda { order("REVERSE(name) ASC") }
  scope :sort_by_reverse_name_desc, lambda { order("REVERSE(name) DESC") }
...

<%= sort_link(@q, :reverse_name) %>
```

Group OR in ransack
https://qiita.com/acro5piano/items/9f792d58f0647892d612
```
app/controllers/users_controller.rb
def index
  @q = User.search(params[:q]))
  @q.build_grouping unless @q.groupings.any?
  @users = @q.result
end

```
```
= search_form_for @q do |f|
  = f.search_field :name_cont
  = f.grouping_fields do |g|
    = g.hidden_field :m, value: 'or'
    = g.search_field :email_cont
    = g.search_field :address_cont

```
