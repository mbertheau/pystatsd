.. _reference-chapter:

=============
API Reference
=============

The ``StatsClient`` provides accessors for all the types of data the statsd_
server supports.

.. note::

    Each public API method supports a ``rate`` parameter, but statsd doesn't
    always use it the same way. See the :ref:`types-chapter` for more
    information.


.. _StatsClient:

``StatsClient``
===============

::

    StatsClient(host='localhost', port=8125, prefix=None, batch_len=1)

Create a new ``StatsClient`` instance with the appropriate connection and
prefix information.

* ``host``: the hostname or IPv4 address of the statsd_ server.

* ``port``: the port of the statsd server.

* ``prefix``: a prefix to distinguish and group stats from an application or
  environment.

* ``batch_len``: how many stats to batch before flushing to the statsd_ server.
  See :ref:`flush`.


.. _incr:

``incr``
========

::

    StatsClient().incr(stat, count=1, rate=1)

Increment a :ref:`counter <counter-type>`.

* ``stat``: the name of the counter to increment.

* ``count``: the amount to increment by. Typically an integer. May be negative,
  but see also :ref:`decr`.

* ``rate``: a sample rate, a float between 0 and 1. Will only send data this
  percentage of the time. The statsd server will take the sample rate into
  account for counters.


.. _decr:

``decr``
========

::

    StatsClient().decr(stat, count=1, rate=1)

Decrement a :ref:`counter <counter-type>`.

* ``stat``: the name of the counter to decrement.

* ``count``: the amount to decrement by. Typically an integer. May be negative
  but that will have the impact of incrementing the counter. See also
  :ref:`incr`.

* ``rate``: a sample rate, a float between 0 and 1. Will only send data this
  percentage of the time. The statsd server will take the sample rate into
  account for counters.


.. _timing:

``timing``
==========

::

    StatsClient().timing(stat, delta, rate=1)

Record :ref:`timer <timer-type>` information.

* ``stat``: the name of the timer to use.

* ``delta``: the number of milliseconds whatever action took. Should always be
  milliseconds.

* ``rate``: a sample rate, a float between 0 and 1. Will only send data this
  percentage of the time. The statsd server does *not* take the sample rate
  into account for timers.


.. _timer:

``timer``
=========

::

    with StatsClient().timer(stat, rate=1):
        pass

::

    @StatsClient().timer(stat, rate=1)
    def foo():
        pass

Automatically ecord timing information for a managed block or function call.
See also the :ref:`chapter on timing <timing-chapter>`.

* ``stat``: the name of the timer to use.

* ``rate``: a sample rate, a float between 0 and 1. Will only send data this
  percentage of the time. The statsd server does *not* take the sample rate
  into account for timers.


.. _gauge:

``gauge``
=========

::

    StatsClient().gauge(stat, value, rate=1)

Set a :ref:`gauge <gauge-type>` value.

* ``stat``: the name of the gauge to set.

* ``value``: the current value of the gauge.

* ``rate``: a sample rate, a float between 0 and 1. Will only send data this
  percentage of the time. The statsd server does *not* take the sample rate
  into account for gauges. Use with care.

.. note::

   Gauges were added to the statsd server in commit 0ed78be_. If you try to use
   this method with an older version of the server, the data will not be
   recorded.


.. _duration:

``duration``
=========

::

    StatsClient().duration(stat, value, rate=1)

Set a :ref:`duration <duration-type>` value.

* ``stat``: the name of the duration to set.

* ``value``: the current value of the duration.

* ``rate``: a sample rate, a float between 0 and 1. Will only send data this
  percentage of the time. The statsd server does *not* take the sample rate
  into account for durations. Use with care.

.. note::

   Durations were added to the statsd server in commit 56d7781_. If you try to use
   this method with an older version of the server, the data will not be
   recorded.


.. _flush:

``flush``
=========

::

    StatsClient().flush()

Flush batched stats data to the statsd_ server.

.. note::

   If you create a StatsClient instance with a ``batch_len`` greater than 1,
   you should find a way to call ``flush`` at the end of whatever your
   application is doing, because the client will only send every time the
   number of stats is zero modulo ``batch_len``, which may introduce a systemic
   bias to your stats.


.. _statsd: https://github.com/etsy/statsd
.. _0ed78be: https://github.com/etsy/statsd/commit/0ed78be7
