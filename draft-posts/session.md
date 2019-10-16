https://github.com/redis-store/redis-rails#session-storage
```
Rails.application.config.session_store :redis_store,
  servers: ["redis://#{ENV['REDIS_HOST']}:6379/session"],
  expire_after: 1.day,
  key: "_#{Rails.application.class.parent_name.downcase}_session",
  secure: !Rails.env.development?
```
