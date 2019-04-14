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
this server, but with a twist. First, your server will return a page stored in the same folder 
called index.html. Second, your server will count the number of times it has accepted a 
connection and store them in a log file called `log.txt` along with the client name and client port. 
If your server is called 5 times, the 
contents of `log.txt` will be: 
```BASH
1 localhost 123456  
2 localhost 123457  
3 localhost 123543  
4 192.178.3.2 123654   
5 localhost 123457  
```
Note - the client name and port will vary by invocation and by the machine you use. For instance, if 
you invoke it from your own computer it will be `localhost`, if its from another computer on your network 
it will have an ip address.  

You can invoke your server using the following command:  
`curl localhost:<port number>`  
The response should be the index.html file. The first time you make a request 
it will look like this:  
```BASH
cs361> curl localhost:4000  
<text of the index.html file>
```
The 1,000th time you call the server it will look like this:  
```BASH
cs361> curl localhost:4000  
<text of the index.html file>
```

But wait, there's more... Curl will not be happy if you don't have a properly formatted HTTP header. 
Reference Bryan and O'Hallaron 11.5.3 to learn how to do this. 

To test your server you can run the following:  
`cs361>for((i=0; i<100; i+=1)) do curl localhost:4000 & done`  
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
`ifndef` is a preprocesser macro meaning "if not defined." When compiling the concurrent server we use the 
`-D CONCURRENT` flag. The ifndef will make sure this section of code is compiled when `-D CONCURRENT` is not 
applied.  
`sleep()` puts the process to sleep for 1 second. Try making 100 requests to your server again. How long 
does it take now? Use the `time` utility to get an exact value.  

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
zombie children. You can do that how you see fit, but I did it with a signal handler. You should also 
limit the number of concurrent processes to 1 parent and 5 children.  

We will test that your server can handle lots of requests. You should be able to reliably serve 300 rapid fire requests.  

Use the `time` utility to measure the run time of the concurrent program. Why is it faster?  

## Submission  
We will collect the assignments through gradescope. There won't be an autograder because the cloud instances don't 
have multiple cores to test your concurrency results. We expect the program to work as described.  
Name your file `server.c`  
Use the makefile provided in this repo.  
There will also be some short answer questions about socket programming for you to answer.  
