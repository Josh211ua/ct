# CT

**(Relatively) Easy Unit Testing for C**

## How to use

1. Copy subdirectory `ct` into your project.
2. Add some rules to your makefile. See [Makefile][] for an example.
3. Write some tests. See [msg-test.c][] for an example.
   Test functions are those whose names begin with "cttest".
   The test runner forks before each test, so global state
   from one test will not affect another.
4. Run `make check`

##Setup and Teardown

Optionally, you can define a function named "ctsetup" in your test file. It
will run before each of the tests in that file. This function should expect no
arguments and return void. Each test file has the option to define its own setup function.

You can also define a function named "ctteardown"; it is the same as ctsetup,
except ctteardown runs after each test in the same file.

## Terminal Output

Running `make check` in the example supplied looks like this:

```
make check
cc -Wall -Wextra   -c -o my-nettest.o my-nettest.c
cc -Wall -Wextra   -c -o sample-nettest.o sample-nettest.c
cc -Wall -Wextra   -c -o ct/udp_protocol.o ct/udp_protocol.c
cc -Wall -Wextra   -c -o msg-test.o msg-test.c
cc -Wall -Wextra   -c -o ct/_net_sample-net-test.o ct/_net_sample-net-test.c
cc -Wall -Wextra   -c -o ct/_net_my-net-test.o ct/_net_my-net-test.c
ct/gen msg-test.o ct/_net_sample-net-test.o ct/_net_my-net-test.o > ct/_ctcheck.c.part
ctsetup_msg_test_o
mv ct/_ctcheck.c.part ct/_ctcheck.c
cc -Wall -Wextra   -c -o ct/_ctcheck.o ct/_ctcheck.c
cc -Wall -Wextra   -c -o ct/ct.o ct/ct.c
cc -Wall -Wextra   -c -o ct/netTestRunner.o ct/netTestRunner.c
cc -Wall -Wextra   -c -o msg.o msg.c
cc   ct/_ctcheck.o ct/ct.o ct/netTestRunner.o msg.o msg-test.o ct/_net_sample-net-test.o ct/_net_my-net-test.o my-nettest.o sample-nettest.o ct/udp_protocol.o   -o ct/_ctcheck
ct/_ctcheck
.EFFE.EE

cttestexit_msg_test_o: error (exit status 2)

cttestfailure_msg_test_o: failure
msg-test.c:39: test: 1 == 2

cttestfmt_msg_test_o: failure
msg-test.c:46: test: n == 2
msg-test.c:46: n is 1

cttestsegfault_msg_test_o: error (signal 11)

cttestnetsample_nettest_c_net_sample_net_test_o: error (exit status 6)
nettesta
from-socket: 9
to-socket: 10
sample-nettest.c:19: test: 0
from-socket: 10
to-socket: 9

cttestnetmy_nettest_c_net_my_net_test_o: error (exit status 11)
client1
from-socket: 11
to-socket: 10
client2
from-socket: 12
to-socket: 10

2 failures; 4 errors.
make: *** [check] Error 1
```

## Network Testing

There is a framework to run cttests over a network. 
This framework sets up a connection between two sockets on the computer.

### How to use

0. See sample-nettest.c for an example.
1. Make sure to #include "ct/ct.h" and the appropriate ?_protocol.h file
2. Use a #define to define a connection between functions. 
    For example, #define NETTESTA "nettestb" makes a connection from nettesta to nettestb
3. Functions beginning with "nettest" are network tests. These functions should return an int and take two struct ctnode * as arguments.
4. Run `make check` as you would for regular cttests

### Network Testing Protocols

A UDP protocol is provided to work with nettests. This protocol is defined 
in udp_protcol.\*. There is also the option to define your own protocol. 
A template for defining protocols can be found in my_protocol.\*

## Releases

There will be no releases of this tool. Just clone the latest source from git
and copy it into your project. If you want to update, copy the newer source
into your project.

## History

Inspired by [CUT][] 2.1 by Sam Falvo and Billy Tanksley.
Also with ideas from the [Go testing package][gotesting] and [gotest][].

[CUT]: http://falvotech.com/content/cut/
[Makefile]: https://github.com/kr/ct/blob/master/Makefile
[msg-test.c]: https://github.com/kr/ct/blob/master/msg-test.c
[gotesting]: http://golang.org/pkg/testing/
[gotest]: http://golang.org/cmd/gotest/
