Lex Analysis
============

In this chapter, we will demostrate the ``cat(1)`` utility in Unix system.

.. code-block:: lex

    %%

    {LINE} { ++nline; }
    {WORD} { ++nword; nchar += yyleng; }
