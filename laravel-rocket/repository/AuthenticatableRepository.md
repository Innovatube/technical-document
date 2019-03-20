# AuthenticatableRepository
    - Định nghĩa các function trao đổi dữ liệu với authenticatable model 
    - Extends `SingleKeyModelRepository`

## Methods

- `public function findByEmail($email)`
    ### Description
        - Trả về model theo trường email

    ### Params 
        - string $email

    ### Return 
        AuthenticatableBase|null

- `public function updateRawPassword($user, $password)`
    ### Description
        - Update raw password cho $user model

    ### Params 
        - AuthenticatableBase $user
        - string              $password

    ### Return 
        AuthenticatableBase