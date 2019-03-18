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
Upload file trên s3
#### Parameters
Type | Parameters | Description
:---: | --- | ------------- 
string | $srcPath | Lấy đường dẫn của file, tham khảo https://php.net/manual/en/splfileinfo.getpathname.php
string | $mediaType | Lấy mime type của file 
string | $filename | lấy tên của file khi upload 
array | $attributes['region'] | Không bắt buộc, giá trị mặc định là `config('file.storage.s3.region')` <br> Đường dẫn tới thư mục chứa file 
array | $attributes['buckets'] | Không bắt buộc

####Return value
```
[
    'success' => boolean, 
    'url' => string,
    'bucket'  => $bucket,
    'key'     => $filename,
    'region'  => $region,
]
```
___

###`$this delete($attributes)`
Xóa file trên s3

Type | Parameters | Description
:---: | --- | ------------- 
array | $attributes['region'] | region của bucket  
array | $attributes['bucket'] | tên bucket  
array | $attributes['key'] | Tên của file 

####Return value
```
[
    'success' => boolean
]
```
                       
