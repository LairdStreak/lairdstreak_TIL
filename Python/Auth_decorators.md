
# Using Python Decorators for Authentication

### A Refresher

Python decorators are bits of syntactic sugar that allow you to execute complementary code every time a function is executed. Given this function...

def super_useful_decorator(fn):

    def wrapper():
        print "hello world"
        fn()

    return wrapper

...this:

    def hello_world():
        return 42
    
    super_useful_decorator(hello_world())()

...is exactly the same as this:

    @super_useful_decorator
    def hello_world():
        return 42

    hello_world()

### Who Cares?

At this point, most decorator tutorials wax lyrical about how python functions are first class and then spend a lot of time talking about closures. This is  _interesting_, but not necessarily immediately  _useful_. Here instead, is the cliffnotes version:

> **Function decorators let you do things automatically when you call a function.**

Keeping this in mind, let's move on to an actual use case for decorators.

### Authenticating API Calls

Say you're setting up a REST API which requires an authenticated token from the user:

    def token_is_valid(token):
        # Replace with a call to your token validation service
        return token=='42'
    
    def authenticate_access(token):
        return token_is_valid(token)
    
    def rest_function_1(token, input_1, input_2)
        rv = {}
    
        if authenticate_access(token):
            rv['code'] = 200
        else:
            rv['code] = 401
            rv['payload'] = input_1+input_2
    
        return rv
    
    def rest_function_2(token, input_1, input_2)
        rv = {}
    
        if authenticate_access(token):
            rv['code'] = 200
        else:
            rv['code'] = 401
            rv['payload'] = input_1*input_2
    
        return rv

This is perfectly workable code, but there are a couple of problems with it.

-   **Boredom**: Authentication code has to be written for each REST API call. This is boring for the programmer.
-   **Brittleness**: Changing the authentication code in any way means that every REST API function has to be altered.
-   **Repetition**: This follows from the last problem, but bears repetition (heh). Saying the same thing over and over in authentication code is dangerous: sooner or later, you're going to say it wrong and let a bunch of people use your service without authentication.

Python decorators are a pretty elegant solution to this problem:

    def token_is_valid(token):
        # No seriously, replace this with your own validation code
        return token=='42'
    
    def authenticate_access(fn):
        def wrapfn(*args, **kwargs):
            rv = {}
            token = args[0]
            if token_is_valid(token):
                rv['code'] = 200
                rv['payload'] = fn(*args, **kwargs)
            else:
                rv['code'] = 401
            return rv
    
        return wrapfn
    
    @authenticate_access
    def rest_function_1(token, input_1, input_2):
        return input_1+input_2
    
    @authenticate_access
    def rest_function_2(token, input_1, input_2):
        return input_1*input_2

Let's try executing this:

In [1]: rest_function_1('43', 2, 2)
Out[1]: {'code': 401}

In [2]: rest_function_1('42', 2, 2)
Out[2]: {'code': 200, 'payload': 4}

In [3]: rest_function_2('42', 32, 32)
Out[3]: {'code': 200, 'payload': 1024}

Neat.

As an additional bonus, there is now clear separation of concerns between authentication and whatever it is the REST API functions are supposed to be doing. The code just  _looks_  cleaner.

### Conclusion

If you are using Python, you should be making heavy use of decorators.


