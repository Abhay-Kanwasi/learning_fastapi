# Learning Fastapi
Putting my learning of fastapi as lessons here


Concurrency and async / await¶
Details about the async def syntax for path operation functions and some background about asynchronous code, concurrency, and parallelism.

### In a hurry?¶
#### TL;DR:

If you are using third party libraries that tell you to call them with await, like:


```results = await some_library()```
Then, declare your path operation functions with async def like:

```
@app.get('/')
async def read_results():
    results = await some_library()
    return results
```

>Note You can only use await inside of functions created with async def.

If you are using a third party library that communicates with something (a database, an API, the file system, etc.) and doesn't have support for using await, (this is currently the case for most database libraries), then declare your path operation functions as normally, with just def, like:

```
@app.get('/')
def results():
    results = some_library()
    return results
```

If your application (somehow) doesn't have to communicate with anything else and wait for it to respond, use async def, even if you don't need to use await inside.

If you just don't know, use normal def.

Note: You can mix def and async def in your path operation functions as much as you need and define each one using the best option for you. FastAPI will do the right thing with them.

Anyway, in any of the cases above, FastAPI will still work asynchronously and be extremely fast.

But by following the steps above, it will be able to do some performance optimizations.

## Technical Details¶
Modern versions of Python have support for "asynchronous code" using something called "coroutines", with async and await syntax.

Let's see that phrase by parts in the sections below:

- Asynchronous Code
- async and await
- Coroutines

## Asynchronous Code¶
Asynchronous code just means that the language 💬 has a way to tell the computer / program 🤖 that at some point in the code, it 🤖 will have to wait for something else to finish somewhere else. Let's say that something else is called "slow-file" 📝.

So, during that time, the computer can go and do some other work, while "slow-file" 📝 finishes.

Then the computer / program 🤖 will come back every time it has a chance because it's waiting again, or whenever it 🤖 finishes all the work it had at that point. And it 🤖 will see if any of the tasks it was waiting for have already finished, doing whatever it had to do.

Next, it 🤖 takes the first task to finish (let's say, our "slow-file" 📝) and continues whatever it had to do with it.

That "wait for something else" normally refers to I/O operations that are relatively "slow" (compared to the speed of the processor and the RAM memory), like waiting for:
- the data from the client to be sent through the network
- the data sent by your program to be received by the client through the network
- the contents of a file on the disk to be read by the system and given to your program
- the contents your program gave to the system to be written to disk
- a remote API operation
- a database operation to finish
- a database query to return the results
etc.
As the execution time is consumed mostly by waiting for I/O operations, they call them "I/O bound" operations.

It's called "asynchronous" because the computer / program doesn't have to be "synchronized" with the slow task, waiting for the exact moment that the task finishes, while doing nothing, to be able to take the task result and continue the work.

Instead of that, by being an "asynchronous" system, once finished, the task can wait in line a little bit (some microseconds) for the computer / program to finish whatever it went to do, and then come back to take the results and continue working with them.

For "synchronous" (contrary to "asynchronous") they commonly also use the term "sequential", because the computer / program follows all the steps in sequence before switching to a different task, even if those steps involve waiting.


## Run the code¶
All the code blocks can be copied and used directly (they are actually tested Python files).

```
uv run fastapi dev
```
Using it in your editor is what really shows you the benefits of FastAPI, seeing how little code you have to write, all the type checks, autocompletion, etc

## Install FastAPI¶
The first step is to set up your project and add FastAPI.

Install uv, then create a project and add FastAPI:

```
uv init awesome-project --bare
cd awesome-project
uv add "fastapi[standard]"
```

uv add creates the project's virtual environment in .venv, adds FastAPI to pyproject.toml, and creates uv.lock so the same package versions can be installed lat