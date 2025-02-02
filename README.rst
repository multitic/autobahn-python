Autobahn\|Python
================

WebSocket & WAMP for Python on Twisted and asyncio.

| |Version| |Downloads| |Build Status| |Coverage|

--------------

| **Quick Links**: `Source Code <https://github.com/crossbario/autobahn-python>`__ - `Documentation <http://autobahn.ws/python>`__ - `WebSocket Examples <http://autobahn.ws/python/websocket/examples.html>`__ - `WAMP Examples <http://autobahn.ws/python/wamp/examples.html>`__
| **Community**: `Mailing list <http://groups.google.com/group/autobahnws>`__ - `StackOverflow <http://stackoverflow.com/questions/tagged/autobahn>`__ - `Twitter <https://twitter.com/autobahnws>`__ - `IRC #autobahn/chat.freenode.net <https://webchat.freenode.net/>`__
| **Companion Projects**: `Autobahn|JS <https://github.com/crossbario/autobahn-js/>`__ - `Autobahn|Cpp <https://github.com/crossbario/autobahn-cpp>`__ - `Autobahn|Testsuite <https://github.com/crossbario/autobahn-testsuite>`__ - `Crossbar.io <http://crossbar.io>`__ - `WAMP <http://wamp.ws>`__

Introduction
------------

**Autobahn\|Python** is a subproject of `Autobahn <http://autobahn.ws/>`__ and provides open-source
implementations of

-  `The WebSocket Protocol <http://tools.ietf.org/html/rfc6455>`__
-  `The Web Application Messaging Protocol (WAMP) <http://wamp.ws/>`__

for Python 2 and 3, and running on `Twisted <http://twistedmatrix.com/>`__ and `asyncio <http://docs.python.org/3.4/library/asyncio.html>`__.

You can use **Autobahn\|Python** to create clients and servers in Python speaking just plain WebSocket or WAMP.

**WebSocket** allows `bidirectional real-time messaging on the Web <http://tavendo.com/blog/post/websocket-why-what-can-i-use-it/>`__ and beyond, while `WAMP <http://wamp.ws/>`__ adds real-time application communication on top of WebSocket.

**WAMP** provides asynchronous **Remote Procedure Calls** and **Publish & Subscribe** for applications in *one* protocol running over `WebSocket <http://tools.ietf.org/html/rfc6455>`__. WAMP is a *routed* protocol, so you need a **WAMP Router** to connect your **Autobahn\|Python** based clients. We provide `Crossbar.io <http://crossbar.io>`__, but there are `other options <http://wamp.ws/implementations/#routers>`__ as well.

Features
--------

-  framework for `WebSocket <http://tools.ietf.org/html/rfc6455>`__ and `WAMP <http://wamp.ws/>`__ clients and servers
-  compatible with Python 2.6, 2.7, 3.3 and 3.4
-  runs on `CPython <http://python.org/>`__, `PyPy <http://pypy.org/>`__ and `Jython <http://jython.org/>`__
-  runs under `Twisted <http://twistedmatrix.com/>`__ and `asyncio <http://docs.python.org/3.4/library/asyncio.html>`__ - implements WebSocket
   `RFC6455 <http://tools.ietf.org/html/rfc6455>`__ and Draft Hybi-10+
-  implements `WebSocket compression <http://tools.ietf.org/html/draft-ietf-hybi-permessage-compression>`__
-  implements `WAMP <http://wamp.ws/>`__, the Web Application Messaging Protocol
-  high-performance, fully asynchronous implementation
-  best-in-class standards conformance (100% strict passes with `Autobahn Testsuite <http://autobahn.ws/testsuite>`__: `Server Test Report <http://autobahn.ws/python/testreport/servers/index.html>`__ / `Client Test Report <http://autobahn.ws/python/testreport/clients/index.html>`__)
-  message-, frame- and streaming-APIs for WebSocket
-  supports TLS (secure WebSocket) and proxies
-  Open-source (`MIT license <https://github.com/crossbario/autobahn-python/blob/master/LICENSE>`__)

Show me some code
-----------------

To give you a first impression, here are two examples. We have lot more `in the repo <https://github.com/crossbario/autobahn-python/tree/master/examples>`__.

WebSocket Echo Server
~~~~~~~~~~~~~~~~~~~~~

Here is a simple WebSocket Echo Server that will echo back any WebSocket
message received:

.. code:: python

    from autobahn.twisted.websocket import WebSocketServerProtocol
    # or: from autobahn.asyncio.websocket import WebSocketServerProtocol

    class MyServerProtocol(WebSocketServerProtocol):

        def onConnect(self, request):
            print("Client connecting: {}".format(request.peer))

        def onOpen(self):
            print("WebSocket connection open.")

        def onMessage(self, payload, isBinary):
            if isBinary:
                print("Binary message received: {} bytes".format(len(payload)))
            else:
                print("Text message received: {}".format(payload.decode('utf8')))

            # echo back message verbatim
            self.sendMessage(payload, isBinary)

        def onClose(self, wasClean, code, reason):
            print("WebSocket connection closed: {}".format(reason))

To actually run above server protocol, you need some lines of `boilerplate <http://autobahn.ws/python/websocket/programming.html#running-a-server>`__.

WAMP Application Component
~~~~~~~~~~~~~~~~~~~~~~~~~~

Here is a WAMP Application Component that performs all four types of
actions that WAMP provides:

#. **subscribe** to a topic
#. **publish** an event
#. **register** a procedure
#. **call** a procedure

.. code:: python

    from autobahn.twisted.wamp import ApplicationSession
    # or: from autobahn.asyncio.wamp import ApplicationSession

    class MyComponent(ApplicationSession):

        @inlineCallbacks
        def onJoin(self, details):

            # 1. subscribe to a topic so we receive events
            def onevent(msg):
                print("Got event: {}".format(msg))

            yield self.subscribe(onevent, 'com.myapp.hello')

            # 2. publish an event to a topic
            self.publish('com.myapp.hello', 'Hello, world!')

            # 3. register a procedure for remote calling
            def add2(x, y):
                return x + y

            self.register(add2, 'com.myapp.add2');

            # 4. call a remote procedure
            res = yield self.call('com.myapp.add2', 2, 3)
            print("Got result: {}".format(res))

Above code will work on Twisted and asyncio by changing a single line
(the base class of ``MyComponent``). To actually run above application component, you need some lines of `boilerplate <http://autobahn.ws/python/wamp/programming.html#running-components>`__ and a `WAMP Router <http://crossbar.io>`__.

.. |Version| image:: https://img.shields.io/pypi/v/autobahn.svg
   :target: https://pypi.python.org/pypi/autobahn

.. |Downloads| image:: https://img.shields.io/pypi/dm/autobahn.svg
   :target: https://pypi.python.org/pypi/autobahn

.. |GitHub Stars| image:: https://img.shields.io/github/stars/crossbario/autobahn-python.svg?style=social&label=Star
   :target: https://github.com/crossbario/autobahn-python

.. |Master Branch| image:: https://img.shields.io/badge/branch-master-orange.svg
   :target: https://travis-ci.org/crossbario/autobahn-python.svg?branch=master

.. |Build Status| image:: https://travis-ci.org/crossbario/autobahn-python.svg?branch=master
   :target: https://travis-ci.org/crossbario/autobahn-python

.. |Coverage| image:: https://img.shields.io/codecov/c/github/crossbario/autobahn-python/master.svg
   :target: https://codecov.io/github/crossbario/autobahn-python
