# DateTimeHelper
    Định nghĩa các helper function cơ bản để xửa lý date time

## Methods

- `public function timezoneForStorage()`
    ### Description
        - Trả về default TimeZone

    ### Params 

    ### Return 
        \DateTimeZone

- `public function setPresentationTimeZone($timezone = null)`
- `public function clearPresentationTimeZone()`
- `public function getPresentationTimeZoneString()`
- `public function timezoneForPresentation()`
- `public function now(\DateTimeZone $timezone = null)`
    ### Description
        - trả về now theo timezone

    ### Params 
        - \DateTimeZone $timezone

    ### Return 
        \Carbon\Carbon

- `public function fromTimestamp($timeStamp, \DateTimeZone $timezone = null)`
    ### Description
        - Convert Unix TimeStamp to Carbon(DateTime)

    ### Params 
        - int           $timeStamp
        - \DateTimeZone $timezone

    ### Return 
        \Carbon\Carbon  

- `public function dateTime($dateTimeStr, \DateTimeZone $timezoneFrom = null, \DateTimeZone $timezoneTo = null)`
    ### Description
        - Get DateTime Object from string

    ### Params 
        - string        $dateTimeStr
        - \DateTimeZone $timezoneFrom
        - \DateTimeZone $timezoneTo

    ### Return 
        \Carbon\Carbon 
 
- `public function dateTimeWithFormat($format, $dateTimeStr, \DateTimeZone $timezoneFrom = null, \DateTimeZone $timezoneTo = null)`
    ### Description
        - Get DateTime Object from string

    ### Params 
        - string        $format
        - string        $dateTimeStr
        - \DateTimeZone $timezoneFrom
        - \DateTimeZone $timezoneTo

    ### Return 
        \Carbon\Carbon 

- `public function formatDate($dateTime, \DateTimeZone $timezone = null)`
    ### Description
        - format date to `Y-m-d`

    ### Params 
        - \DateTime     $dateTime
        - \DateTimeZone $timezone

    ### Return 
        string

- `public function formatTime($dateTime, \DateTimeZone $timezone = null)`
    ### Description
        - format time to `H:i`

    ### Params 
        - \DateTime     $dateTime
        - \DateTimeZone $timezone

    ### Return 
        string

- `public function formatDateTime($dateTime, $format = 'Y-m-d H:i', \DateTimeZone $timezone = null)`
    ### Description
        - format datetime, default `Y-m-d H:i`

    ### Params 
        - \DateTime|null $dateTime
        - string         $format
        - \DateTimeZone  $timezone

    ### Return 
        string
