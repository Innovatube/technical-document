# FIleUploadLocalService
Service này được dùng để gửi mail

## Properties
Service này ko có property

## Methods
###`$this sendMail($title, $from, $to, $template, $data)`
Gửi email
#### Parameters
Type | Parameters | Description
:---: | --- | ------------- 
string | $title | Title của email
string | $from | Người gửi 
string | $to | Người nhận  
array | $template | Template của email 
array | $data | Data cho template

####Return value
```
boolean
```