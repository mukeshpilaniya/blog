1. Array 
    - Array Internals and fundamentals
        - An array in Go is a fixed-length data type that contains a contiguous block of elements
    of the same type.
        - Arrays are valuable data structures because the memory is allocated sequentially. 
        - Having memory in a contiguous form can help to keep the memory you use stay loaded
    within CPU caches longer.
        - An array is a value in Go. This means you can use it in an assignment operation. The
variable name denotes the entire array and, therefore, an array can be assigned to
other arrays of the same type. 
        - When an array is initialized in Go, each
individual element that belongs to the array is initialized to its zero value.
    - Declaring and Initializing
        - Declare an integer array of five elements  
            `var array [5]int` 
        - Array literals allow you to declare the number of elements you need and specify values for those
elements  
        - Declaring and Initialize an array using an array literal  
            `array := [5]int{10, 20, 30, 40, 50}`
        - Declaring and Initialize an array with Go calculating size  
            `array := [...]int{10, 20, 30, 40, 50}`
        - Declaring an array initializing specific elements  
            `array := [5]int{1: 10, 2: 20}`
        - 