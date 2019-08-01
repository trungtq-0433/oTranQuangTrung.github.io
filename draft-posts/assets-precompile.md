Chỉ compile những file được require trong thư mục application.js
Những file ko có require thì cần require bằng tay ở assets.rb
Rails.application.config.assets.precompile += %w[influence/*.js]
