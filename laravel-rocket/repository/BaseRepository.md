# BaseRepository
    Định nghĩa các function cơ bản để xửa lý dữ liệu với model

## Methods

- `public function allByFilterQuery($filter, $order = null, $direction = null)`
    ### Description
        - Trả về query builder theo filter

    ### Params 
        - array  $filter
        - string $order
        - string $direction

    ### Return 
        \Illuminate\Database\Query\Builder
    
- `public function all($order = null, $direction = null)`
    ### Description
        - Trả về toàn bộ models

    ### Params 
        - string $order
        - string $direction

    ### Return
        \LaravelRocket\Foundation\Models\Base[] | \Traversable|array |\Illuminate\Database\Eloquent\Collection

- `public function allEnabled($order = null, $direction = null)`
    ### Description
        - Trả về toàn bộ models enable (trường `is_enabled` = 1 nếu có)

    ### Params 
        - string $order
        - string $direction

    ### Return
        \LaravelRocket\Foundation\Models\Base[] | \Traversable|array |\Illuminate\Database\Eloquent\Collection

- `public function allByFilter($filter, $order = null, $direction = null)`
    ### Description
        - Trả về toàn bộ models theo filter 

    ### Params 
        - array  $filter
        - string $order
        - string $direction

    ### Return
        \LaravelRocket\Foundation\Models\Base[] | \Traversable|array |\Illuminate\Database\Eloquent\Collection
        
- `public function get($order, $direction, $offset, $limit, $before = 0, $after = 0)`
    ### Description
        - Trả về các models theo limit số lượng 
        - Mặc định order by Id với ASC

    ### Params 
        - string $order
        - string $direction
        - int    $offset
        - int    $limit
        - mixed  $before
        - mixed  $after

    ### Return
        \LaravelRocket\Foundation\Models\Base[] | \Traversable|array |\Illuminate\Database\Eloquent\Collection

- `public function getByFilter($filter, $order, $direction, $offset, $limit, $before = 0, $after = 0)`
    ### Description
        - Trả về các models theo filter và limit số lượng  
        - Mặc định order by Id với ASC

    ### Params 
        - array  $filter
        - string $order
        - string $direction
        - int    $offset
        - int    $limit
        - mixed  $before
        - mixed  $after

    ### Return
        \LaravelRocket\Foundation\Models\Base[] | \Traversable|array |\Illuminate\Database\Eloquent\Collection

- `public function getEnabled($order, $direction, $offset, $limit, $before = 0, $after = 0)`
    ### Description
        - Trả về các models enable (trường `is_enabled` = 1 nếu có) và limit số lượng  
        - Mặc định order by Id với ASC

    ### Params 
        - string $order
        - string $direction
        - int    $offset
        - int    $limit
        - mixed  $before
        - mixed  $after

    ### Return
        \LaravelRocket\Foundation\Models\Base[] | \Traversable|array |\Illuminate\Database\Eloquent\Collection

- `public function countByFilter($filter)`
    ### Description
        - Trả về số lượng các models phù hợp filter

    ### Params 
        - array $filter

    ### Return
        int

- `public function countEnabled()`
    ### Description
        - Trả về số lượng các models enable

    ### Params 
        - 

    ### Return
        int

- `function firstByFilter($filter)`
    ### Description
        - Trả về model đầu tiên phù hợp filter

    ### Params 
        - array $filter

    ### Return
        int

- `public function updateByFilter($filter, $values)`
    ### Description
        - Update các models phù hợp filter bằng values
        - Trả về số lượng models được update

    ### Params 
        - array $filter
        - array $values

    ### Return
        int
 
- `public function getSQLByFilter($filter)`
    ### Description
        - Trả câu lệnh sql từ filter

    ### Params 
        - array $filter

    ### Return
        string

- `public function deleteByFilter($filter)`
    ### Description
        - Xoá các models theo filter

    ### Params 
        - array $filter

    ### Return
        
- `public function getModelClassName()`
    ### Description
        - Trả về tên class của model

    ### Params 
        

    ### Return
        string

- `public function getBlankModel()`
    ### Description
        - Trả về model rỗng

    ### Params 
        

    ### Return
        \LaravelRocket\Foundation\Models\Base | \Illuminate\Database\Query\Builder

- `public function getBaseQuery()`
    ### Description
        - Trả về query builder

    ### Params 
        

    ### Return
        \Illuminate\Database\Query\Builder

- `public function firstOrNew($attributes, $values = [])`
    ### Description
        - Trả về model đầu tiên phù hợp hoặc tạo mới model nếu chưa có

    ### Params 
        - array $attributes
        - array $values

    ### Return
        \Illuminate\Database\Eloquent\Model
 
- `public function firstOrCreate($attributes, $values = [])`
    ### Description
        - Trả về model đầu tiên phù hợp hoặc tạo mới model nếu chưa có

    ### Params 
        - array $attributes
        - array $values

    ### Return
        \Illuminate\Database\Eloquent\Model

- `public function updateOrCreate($attributes, $values = [])`
    ### Description
        - Trả về model sau khi update nếu tồn tại hoặc tạo mới model nếu chưa có

    ### Params 
        - array $attributes
        - array $values

    ### Return
        \Illuminate\Database\Eloquent\Model

- `public function rules()`
    ### Description
        - Trả về rules cho validate

    ### Params 
        

    ### Return
        array

- `public function messages()`
    ### Description
        - Trả về messages cho validate

    ### Params 
        

    ### Return
        array

- `public function pluck($collection, $value, $key = null)`
    ### Description
        - Trả về collection với giá trị của column ở $value

    ### Params 
        - \Illuminate\Support\Collection $collection
        - string                         $value
        - string|null                    $key

    ### Return
        \Illuminate\Support\Collection