# Modern_Cpp11
Modern C++11 Features
Smart pointers in C++
A pointer is used to store the address of another variable. In other words, a pointer extracts the information of a resource that is outside the program (heap memory).
We use a copy of the resource, and to make a change, it is done in the copied version. To change the content of the original resource, smart pointers are used.
Problems faced with normal pointers
Below is an example that depicts the problem with normal pointers.
Created a class Rectangle with two data members (length and breadth). A function fun() dynamically creates an object of Rectangle.

    1. #include <iostream>  
    2. using namespace std;  
    3.   
    4. class Rectangle { // created a class Rectangle  
    5. private:  
    6.     int length; // data member as length of rectangle  
    7.     int breadth; // data member as breadth of rectangle  
    8.   
    9. };
       
    10. void fun() // the function to indicate the problem with normal pointer   
    11. {
    12.       
    13.     Rectangle* p = new Rectangle(); // Create a dynamic object p   
    14. }  
    15.   
    16. int main()  
       {  
          // Infinite Loop  
          while (1) { // Run an infinite loop that will allocate p   
              fun();  
          }  
       }  
When the function fun() ends, the object p gets destroyed. Since we did not delete p, the memory remains allocated, and this allocated resource will not be available to other variables.
In the main(), we execute an infinite loop that will keep creating p objects and allocate resources. This problem turns out into a memory leak to which smart pointers is a solution.

Smart pointers
We will implement smart pointers such that they will release the memory of unused resources.
Create a class with a pointer, overloaded operators(->, *) and destructors.
The destructor will be automatically called when its object goes out of the scope, and automatically the dynamically allocated memory will be deleted.
    1. #include <iostream>  
    2. using namespace std;  
    3.   
    4. class SmartPtr { // Create the class to implement smart Pointer  
    5.     int* ptr; // Actual pointer  
    6. public:  
    7. // Create an explicit constructor   
    8.     explicit SmartPtr(int* p = NULL) { ptr = p; }  
    9.   
    10.     // Destructor to deallocate the resource used   
    11.     ~SmartPtr() { delete (ptr); }  
    12.   
    13.     // Overloading dereferencing operator  
    14.     int& operator*() { return *ptr; }  
    15. };  
    16.   
    17. int main()  
    18. {  
    19.     SmartPtr ptr(new int());  
    20.     *ptr = 100;  
    21.     cout << *ptr;  
    22.   
    23.     // We don't need to call delete ptr: when the object  
    24.     // ptr goes out of scope, the destructor for it is automatically  
    25.     // called, and destructor does delete ptr.  
    26.   
    27.     return 0;  
    28. }  

The above example works only for int. We will create a template that will work for every data type.
    1. #include <iostream>  
    2. using namespace std;  
    3.   
    4. template <class T> // Create a template class   
    5. class SmartPtr {  
    6.     T* ptr; // Actual pointer  
    7. public:  
    8.     // Constructor  
    9.     explicit SmartPtr(T* p = NULL) { ptr = p; }  
    10.   
    11.     // Destructor  
    12.     ~SmartPtr() { delete (ptr); }  
    13.   
    14.     // Overloading dereferncing operator  
    15.     T& operator*() { return *ptr; }  
    16.   
    17.     // Overloading arrow operator so that  
    18.     // members of T can be accessed  
    19.     // like a pointer (useful if T represents  
    20.     // a class or struct or union type)  
    21.     T* operator->() { return ptr; }  
    22. };  
    23.   
    24. int main()  
    25. {  
    26.     SmartPtr<int> ptr(new int());  
    27.     *ptr = 100;  
    28.     cout << *ptr;  
    29.     return 0;  
    30. }  
Types of smart pointers
    • Unique_ptr 
This type of object stores only a single object. To assign a different object, current object is deallocated.
    1. #include <iostream>  
    2. #include <memory>  
    3. using namespace std;  
    4.   
    5.   
    6. class Rectangle { // Create the class  
    7.     // Data members  
    8.     int length; // length of rectangle   
    9.     int breadth; // breadth of rectangle   
    10.   
    11. public:  
    12.     Rectangle(int l, int b)  
    13.     { // parameterised constructor  
    14.         length = l;  
    15.         breadth = b;  
    16.     }  
    17.   
    18.     int area()  
    19.     { // calculate area  
    20.         return length * breadth; // return the area   
    21.     }  
    22. };  
    23.   
    24. int main()  
    25. {  
    26.   
    27.     unique_ptr<Rectangle> P1(new Rectangle(20, 5));  
    28.     cout << P1->area() << endl; // This'll print 100  
    29.   
    30.     // unique_ptr<Rectangle> P2(P1);  
    31.     unique_ptr<Rectangle> P2;  
    32.     P2 = move(P1);  
    33.   
    34.     // This'll print 100  
    35.     cout << P2->area() << endl;  
    36.   
    37.     return 0;  
    38. }  

    • Sharing Pointers- std::Shared_ptr 
In shared_ptr, more than one object can point to a single pointer at the same instance of time. A reference counter is maintained for denoting the object using the use_count() method.
    1. #include <iostream>  
    2. #include <memory>  
    3. using namespace std;  
    4.   
    5. class Rectangle { // Create the class  
    6.     // Data members  
    7.     int length; // length of rectangle   
    8.     int breadth; // breadth of rectangle   
    9.   
    10. public:  
    11.     Rectangle(int l, int b)  
    12.     { // parameterised constructor  
    13.         length = l;   
    14.         breadth = b;  
    15.     }  
    16.   
    17.     int area()  
    18.     { // calculate area of rectangle   
    19.         return length * breadth; // return area   
    20.     }  
    21. };  
    22.   
    23. int main()  
    24. {  
    25.   
    26.     shared_ptr<Rectangle> P1(new Rectangle(20, 5)); // create shared //ptr P1  
    27.     // This'll print 100  
    28.     cout << P1->area() << endl;  
    29.     // Create shared ptr P2  
    30.     shared_ptr<Rectangle> P2;  
    31.     P2 = P1;  
    32.   
    33.     // This'll print 100  
    34.     cout << P2->area() << endl;  
    35.   
    36.     // This'll now not give an error,  
    37.     cout << P1->area() << endl; // prints 100  
    38.   
    39. // reference counter of P2 is 2     
    40.     cout << P1.use_count() << endl; // prints 2   
    41.     return 0;  
    42. }  
      
    • Weak_ptr 
Weak_ptr is similar to the shared pointer. The difference is that it does not maintain a reference counter and there is no strong hold of the object on the pointer. This property may result in a deadlock as different objects will try to hold the pointer.












Move Semantics:
The copy constructors in C++ work with the l-value references and copy semantics(copy semantics means copying the actual data of the object to another object rather than making another object to point the already existing object in the heap). While move constructors work on the r-value references and move semantics(move semantics involves pointing to the already existing object in the memory).
On declaring the new object and assigning it with the r-value, firstly a temporary object is created, and then that temporary object is used to assign the values to the object. Due to this the copy constructor is called several times and increases the overhead and decreases the computational power of the code. To avoid this overhead and make the code more efficient we use move constructors.

Std::async-
std::async

 
C++
 
Concurrency support library
 
Defined in header <future> 





(1)



template< class Function, class... Args >
std::future<typename std::result_of<typename std::decay<Function>::type(
        typename std::decay<Args>::type...)>::type>
    async( Function&& f, Args&&... args );

(since C++11) 
(until C++17) 


template< class Function, class... Args >
std::future<std::invoke_result_t<std::decay_t<Function>, std::decay_t<Args>...>>
    async( Function&& f, Args&&... args );

(since C++17) 
(until C++20) 


template< class Function, class... Args >
[[nodiscard]] 
std::future<std::invoke_result_t<std::decay_t<Function>, std::decay_t<Args>...>>
    async( Function&& f, Args&&... args );

(since C++20) 




(2)


template< class Function, class... Args >
std::future<typename std::result_of<typename std::decay<Function>::type(
        typename std::decay<Args>::type...)>::type>
    async( std::launch policy, Function&& f, Args&&... args );


(since C++11) 
(until C++17) 

template< class Function, class... Args >
std::future<std::invoke_result_t<std::decay_t<Function>, std::decay_t<Args>...>>
    async( std::launch policy, Function&& f, Args&&... args );


(since C++17) 
(until C++20) 

template< class Function, class... Args >
[[nodiscard]] 
std::future<std::invoke_result_t<std::decay_t<Function>, std::decay_t<Args>...>>
    async( std::launch policy, Function&& f, Args&&... args );


(since C++20) 






The function template async runs the function f asynchronously (potentially in a separate thread which might be a part of a thread pool) and returns a std::future that will eventually hold the result of that function call. 
1) Behaves as if (2) is called with policy being std::launch::async | std::launch::deferred.
2) Calls a function f with arguments args according to a specific launch policy policy. 
    • If the async flag is set (i.e. (policy & std::launch::async) != 0), then async executes the callable object f on a new thread of execution (with all thread-locals initialized) as if spawned by std::thread(std::forward<F>(f), std::forward<Args>(args)...), except that if the function f returns a value or throws an exception, it is stored in the shared state accessible through the std::future that async returns to the caller. 
    • If the deferred flag is set (i.e. (policy & std::launch::deferred) != 0), then async converts f and args... the same way as by std::thread constructor, but does not spawn a new thread of execution. Instead, lazy evaluation is performed: the first call to a non-timed wait function on the std::future that async returned to the caller will cause the copy of f to be invoked (as an rvalue) with the copies of args... (also passed as rvalues) in the current thread (which does not have to be the thread that originally called std::async). The result or exception is placed in the shared state associated with the future and only then it is made ready. All further accesses to the same std::future will return the result immediately. 
    • If neither std::launch::async nor std::launch::deferred, nor any implementation-defined policy flag is set in policy, the behavior is undefined.
If more than one flag is set, it is implementation-defined which policy is selected. For the default (both the std::launch::async and std::launch::deferred flags are set in policy), standard recommends (but doesn't require) utilizing available concurrency, and deferring any additional tasks.
In any case, the call to std::async synchronizes-with (as defined in std::memory_order) the call to f, and the completion of f is sequenced-before making the shared state ready. If the async policy is chosen, the associated thread completion synchronizes-with the successful return from the first function that is waiting on the shared state, or with the return of the last function that releases the shared state, whichever comes first. If std::decay<Function>::type or each type in std::decay<Args>::type is not constructible from its corresponding argument, the program is ill-formed. 
Parameters
f 
- 
Callable object to call 
args... 
- 
parameters to pass to f 
policy 
- 
bitmask value, where individual bits control the allowed methods of execution 
Bit 
Explanation 
std::launch::async 
enable asynchronous evaluation 
std::launch::deferred 
enable lazy evaluation 

Return value
std::future referring to the shared state created by this call to std::async. 
Exceptions
Throws std::system_error with error condition std::errc::resource_unavailable_try_again if the launch policy equals std::launch::async and the implementation is unable to start a new thread (if the policy is async|deferred or has additional bits set, it will fall back to deferred or the implementation-defined policies in this case), or std::bad_alloc if memory for the internal data structures could not be allocated. 
Notes
The implementation may extend the behavior of the first overload of std::async by enabling additional (implementation-defined) bits in the default launch policy. 
Examples of implementation-defined launch policies are the sync policy (execute immediately, within the async call) and the task policy (similar to async, but thread-locals are not cleared) 
If the std::future obtained from std::async is not moved from or bound to a reference, the destructor of the std::future will block at the end of the full expression until the asynchronous operation completes, essentially making code such as the following synchronous: 
std::async(std::launch::async, []{ f(); }); // temporary's dtor waits for f()
std::async(std::launch::async, []{ g(); }); // does not start until f() completes
(note that the destructors of std::futures obtained by means other than a call to std::async never block) 
Example
Run this code
#include <iostream>
#include <vector>
#include <algorithm>
#include <numeric>
#include <future>
#include <string>
#include <mutex>
 
std::mutex m;
struct X {
    void foo(int i, const std::string& str) {
        std::lock_guard<std::mutex> lk(m);
        std::cout << str << ' ' << i << '\n';
    }
    void bar(const std::string& str) {
        std::lock_guard<std::mutex> lk(m);
        std::cout << str << '\n';
    }
    int operator()(int i) {
        std::lock_guard<std::mutex> lk(m);
        std::cout << i << '\n';
        return i + 10;
    }
};
 
template <typename RandomIt>
int parallel_sum(RandomIt beg, RandomIt end)
{
    auto len = end - beg;
    if (len < 1000)
        return std::accumulate(beg, end, 0);
 
    RandomIt mid = beg + len/2;
    auto handle = std::async(std::launch::async,
                             parallel_sum<RandomIt>, mid, end);
    int sum = parallel_sum(beg, mid);
    return sum + handle.get();
}
 
int main()
{
    std::vector<int> v(10000, 1);
    std::cout << "The sum is " << parallel_sum(v.begin(), v.end()) << '\n';
 
    X x;
    // Calls (&x)->foo(42, "Hello") with default policy:
    // may print "Hello 42" concurrently or defer execution
    auto a1 = std::async(&X::foo, &x, 42, "Hello");
    // Calls x.bar("world!") with deferred policy
    // prints "world!" when a2.get() or a2.wait() is called
    auto a2 = std::async(std::launch::deferred, &X::bar, x, "world!");
    // Calls X()(43); with async policy
    // prints "43" concurrently
    auto a3 = std::async(std::launch::async, X(), 43);
    a2.wait();                     // prints "world!"
    std::cout << a3.get() << '\n'; // prints "53"
} // if a1 is not done at this point, destructor of a1 prints "Hello 42" here
Possible output: 
The sum is 10000
43
world!
53
Hello 42

std::mutex

 
C++
 
Concurrency support library
 
std::mutex
 
Defined in header <mutex> 


class mutex;

(since C++11) 



The mutex class is a synchronization primitive that can be used to protect shared data from being simultaneously accessed by multiple threads. 
mutex offers exclusive, non-recursive ownership semantics: 
    • A calling thread owns a mutex from the time that it successfully calls either lock or try_lock until it calls unlock. 
    • When a thread owns a mutex, all other threads will block (for calls to lock) or receive a false return value (for try_lock) if they attempt to claim ownership of the mutex. 
    • A calling thread must not own the mutex prior to calling lock or try_lock. 
The behavior of a program is undefined if a mutex is destroyed while still owned by any threads, or a thread terminates while owning a mutex. The mutex class satisfies all requirements of Mutex and StandardLayoutType. 
std::mutex is neither copyable nor movable. 
Member types
Member type 
Definition 
native_handle_type(not always present) 
implementation-defined 
Member functions
(constructor)
constructs the mutex 
(public member function) 
(destructor)
destroys the mutex 
(public member function) 
operator=
[deleted]
not copy-assignable 
(public member function) 
Locking 
lock
locks the mutex, blocks if the mutex is not available 
(public member function) 
try_lock
tries to lock the mutex, returns if the mutex is not available 
(public member function) 
unlock
unlocks the mutex 
(public member function) 
Native handle 
native_handle
returns the underlying implementation-defined native handle object 
(public member function) 
Notes
std::mutex is usually not accessed directly: std::unique_lock, std::lock_guard, or std::scoped_lock (since C++17) manage locking in a more exception-safe manner. 
Example
This example shows how a mutex can be used to protect an std::map shared between two threads.
Run this code
#include <iostream>
#include <map>
#include <string>
#include <chrono>
#include <thread>
#include <mutex>
 
std::map<std::string, std::string> g_pages;
std::mutex g_pages_mutex;
 
void save_page(const std::string &url)
{
    // simulate a long page fetch
    std::this_thread::sleep_for(std::chrono::seconds(2));
    std::string result = "fake content";
 
    std::lock_guard<std::mutex> guard(g_pages_mutex);
    g_pages[url] = result;
}
 
int main() 
{
    std::thread t1(save_page, "http://foo");
    std::thread t2(save_page, "http://bar");
    t1.join();
    t2.join();
 
    // safe to access g_pages without lock now, as the threads are joined
    for (const auto &pair : g_pages) {
        std::cout << pair.first << " => " << pair.second << '\n';
    }
}
Output: 
http://bar => fake content
http://foo => fake content
std::lock_guard

 
C++
 
Concurrency support library
 
std::lock_guard
 
Defined in header <mutex> 


template< class Mutex >
class lock_guard;

(since C++11) 



The class lock_guard is a mutex wrapper that provides a convenient RAII-style mechanism for owning a mutex for the duration of a scoped block. 
When a lock_guard object is created, it attempts to take ownership of the mutex it is given. When control leaves the scope in which the lock_guard object was created, the lock_guard is destructed and the mutex is released. 
The lock_guard class is non-copyable. 
Template parameters
Mutex 
- 
the type of the mutex to lock. The type must meet the BasicLockable requirements 
Member types
Member type 
Definition 
mutex_type 
Mutex 
Member functions
(constructor)
constructs a lock_guard, optionally locking the given mutex 
(public member function) 
(destructor)
destructs the lock_guard object, unlocks the underlying mutex 
(public member function) 
operator=
[deleted]
not copy-assignable 
(public member function) 
Notes
std::scoped_lock offers a replacement for lock_guard that provides the ability to lock multiple mutexes using a deadlock avoidance algorithm. 
(since C++17)
Example
Run this code
#include <thread>
#include <mutex>
#include <iostream>
 
int g_i = 0;
std::mutex g_i_mutex;  // protects g_i
 
void safe_increment()
{
    const std::lock_guard<std::mutex> lock(g_i_mutex);
    ++g_i;
 
    std::cout << "g_i: " << g_i << "; in thread #"
              << std::this_thread::get_id() << '\n';
 
    // g_i_mutex is automatically released when lock
    // goes out of scope
}
 
int main()
{
    std::cout << "g_i: " << g_i << "; in main()\n";
 
    std::thread t1(safe_increment);
    std::thread t2(safe_increment);
 
    t1.join();
    t2.join();
 
    std::cout << "g_i: " << g_i << "; in main()\n";
}
Possible output: 
g_i: 0; in main()
g_i: 1; in thread #140487981209344
g_i: 2; in thread #140487972816640
g_i: 2; in main()
