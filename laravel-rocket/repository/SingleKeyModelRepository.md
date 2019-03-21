# SingleKeyModelRepository
    Định nghĩa các function trao đổi dữ liệu với model theo 1 key đơn bổ sung cho `BaseRepository`

## Methods

- `public function getPrimaryKey()`
    ### Description
        - Trả về mang cac primary key cua model

    ### Params 

    ### Return 
        array

- `public function find($id)`
    ### Description
        - Trả về model theo id

    ### Params 
        - int $id

    ### Return 
        \LaravelRocket\Foundation\Models\Base|null

- `public function allByIds($ids, $order = null, $direction = null, $reorder = false)`
    ### Description
        - Trả về các models theo mảng id
        - $reorder = true sẽ sắp xếp model trả về theo danh sách id

    ### Params 
        - array       $ids
        - string|null $order
        - string|null $direction
        - bool        $reorder

    ### Return 
        \LaravelRocket\Foundation\Models\Base[] | \Illuminate\Database\Eloquent\Collection

- `public function countByIds($ids)`
    ### Description
        - Trả về số lượng models tìm theo mảng id

    ### Params 
        - array $ids

    ### Return 
        int

- `public function getByIds($ids, $order = null, $direction = null, $offset = null, $limit = null)`
    ### Description
        - Trả về các models tìm theo mảng id với limit số lượng

    ### Params 
        - array       $ids
        - string|null $order
        - string|null $direction
        - int|null    $offset
        - int|null    $limit

    ### Return 
        \LaravelRocket\Foundation\Models\Base[] | \Illuminate\Database\Eloquent\Collection

- `public function create($input)`
    ### Description
        - Trả về model sau khi tạo mới với các giá trị truyền vào

    ### Params 
        - array $input

    ### Return 
        \LaravelRocket\Foundation\Models\Base

- `public function dryUpdate($model, $input)`
    ### Description
        - Trả về model sau khi update nó với các giá trị truyền vào

    ### Params 
        - \LaravelRocket\Foundation\Models\Base $model
        - array                                 $input

    ### Return 
        \LaravelRocket\Foundation\Models\Base
 
- `public function update($model, $input)`
    ### Description
        - Trả về model sau khi update nó với các giá trị truyền vào
        - Xoá cache giá trị model nếu đang enable

    ### Params 
        - \LaravelRocket\Foundation\Models\Base $model
        - array                                 $input

    ### Return 
        \LaravelRocket\Foundation\Models\Base

- `public function save($model)`
    ### Description
        - Trả về model sau khi save các thay đổi đã set trước đó
        - Xoá cache giá trị model nếu đang enable

    ### Params 
        - \LaravelRocket\Foundation\Models\Base $model

    ### Return 
        \LaravelRocket\Foundation\Models\Base
    
- `public function delete($model)`
    ### Description
        - Xoá cache giá trị model nếu đang enable
        - Xoá model trả về true nếu thành công, false nếu không

    ### Params 
        - \LaravelRocket\Foundation\Models\Base $model

    ### Return 
        bool