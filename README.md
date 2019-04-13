# Homework5
## A Simple Server and a Concurrency Experiment

The objective of this homework is to gain familiarity network programming with sockets and 
to get you thinking about the benefits of writing concurrent programs. 

Consider this homework to be a step by step tutorial. A lot of what we're doing here is 
in the book. Unfortunately copying the book won't prepare you to program anything 
yourself. We encourage you to read the textbook, the man pages, and ask questions 
about how to do the assignment. 

## Tools You Will Use
* [curl](https://curl.haxx.se/) - "a tool for transfering data with urls"
* [time](http://man7.org/linux/man-pages/man1/time.1.html) - A utility for measuring the performance of programs
* [preprocessor macros](https://gcc.gnu.org/onlinedocs/gcc-2.95.3/cpp_1.html#SEC29) - For reusing the code you write
* And some BASH scripting

## A Simple Server

Bryant and O'Halloran introduce an echo server on page 947. Your first task is to implement 
this server, but with a twist. Your server will count the number of times it has accepted a 
connection and store them in a log file called `log.txt`. If your server is called 5 times, the 
contents of `log.txt` will be: 
```BASH
1  
2  
3  
4  
5
```

You can invoke your server using the following command:  
`curl localhost:<port number>`  
The response should be the number of times the server has been invoked. The first time you make a request 
it will look like this:  
```BASH
cs361> curl localhost:4000  
1
```
The 1,000th time you call the server it will look like this:  
```BASH
cs361> curl localhost:4000  
1000  
```

But wait, there's more... Curl will not be happy if you don't have a properly formatted HTTP header. 
Reference Bryan and O'Hallaron 11.5.3 to learn how to do this. 

To test your server you can run the following:  
`cs361>for((i=0; i<100; i==)) do curl localhost:4000 done`  
Your log file should list numbers from 1 to 100 in order. 

## An Introduction to Concurrency
For this part of the homework we'll modify the counting server to work with concurrent processes.  
First, however, we need to modify the code from part A. Put all of your function calls required for 
the sequential server into a function called `sequential_server()`. In your main() function modify the 
code to match this:  
```c
  ifndef CONCURRENT  
    sequential_server();
    sleep(1);
  endif
```
`sleep()` puts the process to sleep for 1 second. Try making 100 requests to your server again. How long 
does it take now?  

Now add a preprocessor macro to your file that looks like this:  
```c
  ifdef CONCURRENT
    // Do you remember how to fork()? Do that here. 
    // Inside the child write to the log and and the socket
    sleep(1);
    // Don't block. 
  endif
```
Your parent should not block while we are waiting for the child to terminate, and you must reap all 
zombie children. You can do that how you see fit, but I did it with a signal handler.  

We will test that your server can handle lots of requests. You should be able to reliably serve 300 rapid fire requests.  

## Submission  
We will collect the assignments through gradescope. There won't be an autograder because the cloud instances don't 
have multiple cores to test your concurrency results. We expect the program to work as described.  
There will also be some short answer questions about socket programming for you to answer.  
