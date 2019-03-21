# FIleUploadLocalService
Service này được dùng để upload file trên local

## Example
```
$image = $request->image

$path        = $image->getPathname();
$mediaType   = $image->getClientMimeType();
$filename    = $image->getClientOriginalName();
$attributes = []; 

$this->upload($path, $mediaType, $filename, $attributes)
```
## Properties
Service này ko có property

## Methods
###`$this upload($srcPath, $mediaType, $filename, $attributes)`
Upload file trên local
#### Parameters
Type | Parameters | Description
:---: | --- | ------------- 
string | $srcPath | Lấy đường dẫn của file, tham khảo https://php.net/manual/en/splfileinfo.getpathname.php
string | $mediaType | Lấy mime type của file 
string | $filename | lấy tên của file khi upload 
array | $attributes['uploadDirectory'] | Không bắt buộc, giá trị mặc định là `config('file.storage.local.path')` <br> Đường dẫn tới thư mục chứa file 
array | $attributes['baseUrl'] | Không bắt buộc, giá trị mặc định là `config('file.storage.local.url')` <br> Đường dẫn base

####Return value
```
[
    'success' => boolean, 
    'url' => string
]
```
___

###`$this delete($attributes)`
Xóa file trên local

Type | Parameters | Description
:---: | --- | ------------- 
array | $attributes['uploadDirectory'] | Đường dẫn tới thưc mục của file 
array | $attributes['key'] | Tên của file 

####Return value
`[
    'success' => boolean
]`
                       
