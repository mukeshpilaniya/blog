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
        - Declaring two-dimensional arrays  
            `var array [4][2]int`
        - Using an array literal to declare and initialize a two dimensional integer array  
            `array := [4][2]int{{10, 11}, {20, 21}, {30, 31}, {40, 41}}`
    - Passing arrays between functions
        - Passing an array between functions can be an expensive operation in terms of memory and performance. 
        - When your variable is an array, this means the entire array, regardless
of its size, is copied and passed to the function.
2. Slice 
    - Slice internals and fundamentals
        - Slices are built around the concept of dynamic arrays that can grow and
shrink 
        - They’re three field data structures that contain the metadata. The three fields are a pointer to the underlying array, the length or the number of elements the slice has access to, and the capacity or the number of elements the slice has
available for growth. 
        - Remember, if you specify a value inside the [ ] operator, you’re creating an array. If
you don’t specify a value, you’re creating a slice.
            - Create an array of three integers.  
                `array := [3]int{10, 20, 30}`
            -  Create a slice of integers with a length and capacity of three.  
                `slice := []int{10, 20, 30}`
    - Creating and Initializing
        - When you use make, one
option you have is to specify the length of the slice.
        - Declaring a slice of strings by length  
            `slice := make([]string, 5)`
        - Declaring a slice of integers by length and capacity  
            `slice := make([]int, 3, 5)`  
            Contains a length of 3 and has a capacity of 5 elements. Trying to create a slice with a capacity that’s smaller than the length is not allowed
        - Declaring a slice with a slice literal  
            `slice := []string{"Red", "Blue", "Green", "Yellow", "Pink"}`
    - NIL and empty slices
        - Declaring an nil slice.  
            `var slice []int`  
             A nil slice is created
by declaring a slice without any initialization.
        - Declaring an empty slice  
            `slice := make([]int, 0)`  
            `slice := []int{}`
        - NIL useful when you want to represent a slice that doesn’t exist, such as when an exception
occurs in a function that returns a slice.
        - Empty slices are useful when you want to represent an empty collection, such as when
a database query returns zero results .
        - Regardless of whether you’re using a nil slice or an empty slice, the built-in functions
append, len, and cap work the same.
    - Working with slices
        - Slices are called such because you can slice a portion of the underlying array to create
a new slice.
            ```
            slice := []int{10, 20, 30, 40, 50}
            newSlice := slice[1:3]
            ```
            Changes made to the shared section of the underlying array by one slice can be
seen by the other slice.
        - Calculating Length and Capacity of Slice  
            For slice[i:j:k] or [2:3:4]  
            Length: j - i or 3 - 2 = 1  
            Capacity: k - i or 4 - 2 = 2
        - To use append, you need a source slice and a value that is to be appended. When
your append call returns, it provides you a new slice with the changes.The append function will always increase the length of the new slice. 
            ```
            func main() {
                slice := []int{10,20,30,40,50}
                newSlice := slice[1:3]
                fmt.Println(slice)
                fmt.Println(newSlice)
                newSlice =append(newSlice,60)
                fmt.Println(slice)
                fmt.Println(newSlice)
            }
            [10 20 30 40 50]
            [20 30]
            [10 20 30 60 50]
            [20 30 60]
            ```
            Because there was available capacity in the underlying array for newSlice, the
append operation incorporated the available element into the slice’s length and
assigned the value. Since the original slice is sharing the underlying array, slice also changed.
        - When there’s no available capacity in the underlying array for a slice, the append function will create a new underlying array, copy the existing values that are being referenced, and assign the new value  
            ```
            func main() {
                slice := []int{10,20,30,40}
                newSlice :=append(slice,60)
                fmt.Println(slice)
                fmt.Println(newSlice)
            }
            [10 20 30 40]
            [10 20 30 40 60]
            ```
        - By having the option to set the capacity of a new slice to be the same as the length,
you can force the first append operation to detach the new slice from the underlying
array. Detaching the new slice from its original source array makes it safe to change.
            - newSlice len=1 and capacity=2
                ```
                func main() {
                    slice := []string{"Apple", "Orange", "Plum", "Banana", "Grape"}
                    newSlice :=slice[2:3:4]
                    fmt.Println(slice)
                    fmt.Println(newSlice)
                    newSlice =append(newSlice,"mango")
                    fmt.Println(slice)
                    fmt.Println(newSlice)
                }
                [Apple Orange Plum Banana Grape]
                [Plum]
                [Apple Orange Plum mango Grape]
                [Plum mango]
                ```
            - newSlice len=1 and capacity=1
                ```
                func main() {
                    slice := []string{"Apple", "Orange", "Plum", "Banana", "Grape"}
                    newSlice :=slice[2:3:3]
                    fmt.Println(slice)
                    fmt.Println(newSlice)
                    newSlice =append(newSlice,"mango")
                    fmt.Println(slice)
                    fmt.Println(newSlice)
                }
                [Apple Orange Plum Banana Grape]
                [Plum]
                [Apple Orange Plum Banana Grape]
                [Plum mango]
                ```
    - Iterating over slices
        - Go has a special keyword
called range that you use in conjunction with the keyword for to iterate over slices.
            ```
            //Create a slice of integers.
            // Contains a length and capacity of 4 elements.
            slice := []int{10, 20, 30, 40}
            
            // Iterate over each element and display each value.
            for index, value := range slice {
                fmt.Printf("Index: %d Value: %d\n", index, value)
            }
            ```
        - The first value
is the index position and the second value is a copy of the value in that index position. It’s important to know that range is making a copy of the value, not returning a reference
        - If you don’t need the index value, you can use the underscore character to discard
the value.
            ```
            // Create a slice of integers.
            // Contains a length and capacity of 4 elements.
            slice := []int{10, 20, 30, 40}
            
            // Iterate over each element and display each value.
            for _, value := range slice {
                fmt.Printf("Value: %d\n", value)
            }
            ```
        - The keyword range will always start iterating over a slice from the beginning. If you
need more control iterating over a slice, you can always use a traditional for loop.
            ```
            // Create a slice of integers.
            // Contains a length and capacity of 4 elements.
            slice := []int{10, 20, 30, 40}
            
            // Iterate over each element starting at element 3.
            for index := 2; index < len(slice); index++ {
                fmt.Printf("Index: %d Value: %d\n", index, slice[index])
            }
            ```
