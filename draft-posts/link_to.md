```
def link_to_new_tab href
    link_to href, href, target: :_blank, rel: "noopener noreferrer" if href.present?
end
```
