# ImageService
Service này được dùng để xử lý ảnh

## Properties
Service này ko có property

## Methods
###`$this convert($src, $dst, $format, $size, $needExactSize = false, $backgroundColor='#FFFFFF')`
Convert ảnh theo option
#### Parameters
Type | Parameters | Description
:---: | --- | ------------- 
string | $src | Đường dẫn ảnh
string | $dst | Đường dẫn lưu ảnh 
string | $format | Định dạng mới của ảnh  
array | $size | size của ảnh 
float | $size[0] | width
float | $size[1] | height

####Return value
```
[
    'width' => float, 
    'height' => float
]
```
___

###`$this getImageSize($src)`
Lấy width và height của 1 ảnh

Type | Parameters | Description
:---: | --- | ------------- 
string | $src | Đường dẫn của ảnh  

####Return value
`[
    'width' => float, 
    'height' => float
]`
                       
