Khi joins 2 bảng, mặc định điều kiện join là ID.
Tuy nhiên trong 1 số trường hợp, ta muốn thêm điều kiện trong join
```ruby
.joins("LEFT JOIN office_campaigns ON office_campaigns.campaign_id = campaigns.id
      AND office_campaigns.office_id = #{office_id}")
```
