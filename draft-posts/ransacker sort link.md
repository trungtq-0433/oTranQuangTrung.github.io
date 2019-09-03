In the case that you wish to sort by some complex value, such as the result of a SQL function, you may do so using scopes. 
In your model, define scopes whose names line up with the name of the virtual field you wish to sort by, as so:

```
class Person < ActiveRecord::Base
  scope :sort_by_reverse_name_asc, lambda { order("REVERSE(name) ASC") }
  scope :sort_by_reverse_name_desc, lambda { order("REVERSE(name) DESC") }
...

<%= sort_link(@q, :reverse_name) %>
```
