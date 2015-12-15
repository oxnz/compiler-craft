Introduction
************

.. epigraph::

    No matter where you go, there you are.

    -- Buckaroo Banzai

compiler-craft is a project intend to demostrate how a compiler is constructed.
And there will be a bunch of examples demostrated.

The book will be separated into three parts:

Basic Theory
============

.. math::

   alpha_t(i) = P(O_1, O_2, ... O_t, q_t = S_i lambda)

.. code:: python

    def greet():
        print 'hello world'

This part will introduce the basic theory have developed in the past and what's
being used nowadays. The basics will cover lex and parse. In lex part, we will
discuss regulare expressions and state machine. In parse part, we will discuss
from simple such as recursive decendent to high level such as LR(k) and LALR(1)
parser. We will talk about both front end and back end. Front end concludes token
recognition, grammer validate, semantic process. While the backend concludes
code generation, optimization. Back end we will both use hand crafted and llvm
as backend.

Compiler Construction
=====================

This part will demostrate how to implementation several pratical languages.
And the same time, we will compare our language to the already existed langauges
such as c, c++, java, etc. In order to fully understand the different languages,
we will discuss from system language such as c, java to some script languages such
as shell, javascript.

Advanced Topics
===============

This part will demostrate the high order syntax parse and compile, such as class,
closure, lambda, etc. And in this part, we will also talk about industry production
level compiler such as gcc and llvm.

* Note:

    * doc/build is auto-generated
    * doc/source is manual-crafted
    * examples is the demo directory
    * exp and parser is the front-end
    * backend is not included for now

