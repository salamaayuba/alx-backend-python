Assync Comprehension.
What’s New in Python: Asynchronous Comprehensions / Generators
/ Python / By Mike / February 14, 2017 / Python 3
Python 3.6 added the ability to create Asynchronous Comprehensions and Asynchronous Generators. You can read about asynchronous comprehension in PEP 530 while the asynchronous generators are described in PEP 525. The documentation states that you can now create asynchronous list, set and dict comprehensions and generator expressions. Their example looks like this:

result = [i async for i in aiter() if i % 2]
Basically you just need to add Python’s new async keyword into your expression and call a callable that has implemented __aiter__. Trying to follow this syntax will actually result in a SyntaxError though:

>>> result = [i async for i in range(100) if i % 2]
  File "<stdin>", line 1
    result = [i async for i in range(100) if i % 2]
                    ^
SyntaxError: invalid syntax
</stdin>

This is actually be definition. If you look in PEP 530, you will see that it states the following: Asynchronous comprehensions are only allowed inside an async def function. Of course, you cannot put Python’s await in a comprehension either as that keyword is to be used only inside of an async def function’s body. Just for fun, I tried defining an async def function to see if I could make my idea work:

import asyncio
async def test(): 
    return [i async for i in range(100) if i % 2]
loop = asyncio.get_event_loop()
loop.run_until_complete(test())
If you run this code, you will get a TypeError: ‘async for’ requires an object with __aiter__ method, got range. What you really want to do is call another async def function instead of calling range directly. Here’s an example:

import asyncio
async def numbers(numbers):
    for i in range(numbers):
        yield i
        await asyncio.sleep(0.5)
async def main():
    odd_numbers = [i async for i in numbers(10) if i % 2]
    print(odd_numbers)
if __name__ == '__main__':
    event_loop = asyncio.get_event_loop()
    try:
        event_loop.run_until_complete(main())
    finally:
        event_loop.close()
Technically the numbers function is an asynchronous generator that is yielding values to our asynchronous list comprehension.

Wrapping Up
Creating an asynchronous list comprehension is quite a bit different than creating a regular list comprehension. As you can see, it takes a lot more code to make it work. Other than adding the ability to do more asynchronously out of the box, the new asynchronous generator is actually supposed to be 2x faster than an equivalent implemented as an asynchronous iterator according to PEP 525. While I currently don’t have a use case for this new functionality, it is really neat to see what I can do now and I’ll be filing these new concepts away for some future application.
