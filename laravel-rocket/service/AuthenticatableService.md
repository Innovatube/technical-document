# AuthenticatableService
Service này được dùng để upload file trên local

## Properties
Type | Name | Description
:---: | -------- | ------------- 
string | $resetEmailTitle | Title of reset email 
string | $resetEmailTemplate | Template of reset email

## Methods
###`$this signInById($id)`
Login bằng ID của user
#### Parameters
Type | Parameters | Description
:---: | --- | ------------- 
integer | $id | User ID

####Return value
```
User instance
```
___

###`$this getGuardName()`
Cần phải override sau khi extends service này

Type | Parameters | Description
:---: | --- | ------------- 

####Return value
```
string
```
                       
###`$this signIn($input)`
Login bằng email và password
#### Parameters
Type | Parameters | Description
:---: | --- | ------------- 
array | $input | Array chứa email, password, remember_me
string | $input['email'] | Email đăng nhập 
string | $input['password'] | Password đăng nhập 
boolean | $input['remember_me'] | Lựa chọn có nhớ đăng nhập

####Return value
```
User instance
```

###`$this signUp($input)`
Đăng ký tài khoản đăng nhập
#### Parameters
Type | Parameters | Description
:---: | --- | ------------- 
array | $input | Array chứa email, password, remember_me
string | $input['email'] | Email đăng nhập 
string | $input['password'] | Password đăng nhập 

####Return value
```
User instance
```

###`$this signOut()`
Log out 
#### Parameters
Type | Parameters | Description
:---: | --- | -------------  

####Return value
```
boolean
```

###`$this sendPasswordResetEmail($email)`
Gửi email reset password
#### Parameters
Type | Parameters | Description
:---: | --- | ------------- 
string | $email | Email đăng nhập  

####Return value
```
void
```

###`$this resetPassword($email, $password, $token)`
Gửi email reset password
#### Parameters
Type | Parameters | Description
:---: | --- | ------------- 
string | $email | Email đăng nhập  
string | $password | Password mới  
string | $token | verify token   

####Return value
```
boolean
```

###`$this isSignedIn()`
Kiểm tra có login không
#### Parameters
Type | Parameters | Description
:---: | --- | -------------    

####Return value
```
boolean
```
          
