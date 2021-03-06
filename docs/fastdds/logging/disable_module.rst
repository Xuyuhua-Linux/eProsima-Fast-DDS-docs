.. include:: ../../03-exports/aliases.include
.. include:: ../../03-exports/aliases-api.include
.. include:: ../../03-exports/roles.include

.. _dds_layer_log_disable:

Disable Logging Module
======================

Setting the :ref:`dds_layer_log_verbosity_level`, translates into entries not being added to the log queue if the
entry's level has lower importance than the set one.
This check is performed when calling the macros defined in :ref:`dds_layer_log_logging`.
However, it is possible to fully disable each macro (and therefore each verbosity level individually) at build time.

* |logInfo| is fully disabled by either:

    * Setting CMake option |LOG_NO_INFO| to ``ON`` (default for Single-Config generators if ``CMAKE_BUILD_TYPE``
      is other than ``Debug``).
    * Defining macro |HAVE_LOG_NO_INFO| to ``1``.

* |logWarning| is fully disabled by either:

    * Setting CMake option |LOG_NO_WARNING| to ``ON``.
    * Defining macro |HAVE_LOG_NO_WARNING| to ``1``.

* |logError| is fully disabled by either:

    * Setting CMake option |LOG_NO_ERROR| to ``ON``.
    * Defining macro |HAVE_LOG_NO_ERROR| to ``1``.

Applying either of the previously described methods will set the macro to be empty at configuration time, thus allowing
the compiler to optimize the call out.
This is done so that all the debugging messages present on the library are optimized out at build time if not building
for debugging purposes, thus preventing them to impact performance.

``INTERNAL_DEBUG`` CMake option activates log macros compilation, so the arguments of the macros are compiled.
However:

* It does not activate the log Warning and Error messages, i.e. the messages are not written in the log queue.
* |logInfo| has a special behaviour to simplify working with Multi-Config capability IDEs.
  If CMake option |LOG_NO_INFO| is ``OFF``, or the C++ definition |HAVE_LOG_NO_INFO| is ``0``, then logging is enabled
  only for ``Debug`` configuration.
  In this scenario, setting |FASTDDS_ENFORCE_LOG_INFO| to ``ON`` will enable |logInfo| even on non ``Debug``
  configurations.
  This is specially useful when using the *Fast DDS*' logging module in an external application which links with
  *Fast DDS* compiled in ``Release``.
  In that case, applications wanting to use all three levels of logging can simply add the following code prior to
  including any Fast DDS header:

  .. code-block:: c

     #define HAVE_LOG_NO_INFO 0
     #define FASTDDS_ENFORCE_LOG_INFO 1


.. warning::

    ``INTERNAL_DEBUG`` can be automatically set to ``ON`` if CMake option ``EPROSIMA_BUILD`` is set to ``ON``.
