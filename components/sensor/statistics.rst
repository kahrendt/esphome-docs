Statistics
==========

.. seo::
    :description: Instructions for setting up a Statistics Sensor

The ``statistics`` sensor platform quickly generates summary statistics from another sensor’s measurements. See :ref:`statistics-description` for information about the available summary statistics.

The component calculates statistics over a sliding window or a resettable continuous window. See :ref:`window-types` for details about each possible type.

Each summary statistic sensor is optional, and the component only stores the measurement information necessary for the enabled sensors. This component uses external memory (PSram on an ESP32) if available.

You could also use :ref:`sensor-filters` to compute some of the available summary statistics over a sliding window. In contrast, this component allows you to generate multiple summary statistics from the same source sensor. Additionally, it calculates them more efficiently than sensors filters.

To use the component, first, provide the source sensor and then configure the window settings and the desired statistics.

.. code-block:: yaml

    # Example configuration entry
    sensor:
      - platform: statistics
        source_id: source_measurement_sensor
        window:
          type: sliding
          window_size: 15
          send_every: 5
          send_first_at: 3
        average_type: time_weighted
        group_type: sample
        count:
          name: "Count of Valid Sensor Measurements"    
        covariance:
          name: "Sensor Sample Covariance"          
        duration:
          name: "Sample Duration"
        max:
          name: "Sensor Maximum"   
        min:
          name: "Sensor Minimum"
        mean:
          name: "Sensor Average"
        std_dev: 
          name: "Sensor Sample Standard Deviation"
        trend:
          name: "Sensor Trend"          
        variance:
          name: "Sensor Sample Variance"

      # Use any other sensor component to gather statistics for
      - platform: ...
        id: source_measurement_sensor

Configuration variables:
------------------------

- **type** (**Required**, enum): One of ``sliding``, ``chunked_sliding``, ``continuous``, or ``chunked_continuous``.
- **average_type** (*Optional*, enum): How each measurement is weighted, one of ``simple`` or ``time_weighted``. Defaults to ``simple``.
- **group_type** (*Optional*, enum): The type of the set of sensor measurements, one of ``sample`` or ``population``. Defaults to ``sample``.
- **time_unit** (*Optional*, enum): The time unit used for the covariance and trend sensors, one of
  ``ms``, ``s``, ``min``, ``h`` or ``d``. Defaults to ``s``.
- **count** (*Optional*): The information for the count sensor.

  - **name** (**Required**, string): The name for the count sensor.
  - **id** (*Optional*, :ref:`config-id`): Set the ID of this sensor for use in lambdas.
  - All other options from :ref:`Sensor <config-sensor>`.  

- **covariance** (*Optional*): The information for the covariance sensor.

  - **name** (**Required**, string): The name for the covariance sensor.
  - **id** (*Optional*, :ref:`config-id`): Set the ID of this sensor for use in lambdas.
  - All other options from :ref:`Sensor <config-sensor>`.  

- **duration** (*Optional*): The information for the duration sensor.

  - **name** (**Required**, string): The name for the duration sensor.
  - **id** (*Optional*, :ref:`config-id`): Set the ID of this sensor for use in lambdas.
  - All other options from :ref:`Sensor <config-sensor>`.    

- **max** (*Optional*): The information for the maximum sensor.

  - **name** (**Required**, string): The name for the maximum sensor.
  - **id** (*Optional*, :ref:`config-id`): Set the ID of this sensor for use in lambdas.
  - All other options from :ref:`Sensor <config-sensor>`.

- **mean** (*Optional*): The information for the mean (average) sensor.

  - **name** (**Required**, string): The name for the mean sensor.
  - **id** (*Optional*, :ref:`config-id`): Set the ID of this sensor for use in lambdas.
  - All other options from :ref:`Sensor <config-sensor>`.

- **min** (*Optional*): The information for the minimum sensor.

  - **name** (**Required**, string): The name for the minimum sensor.
  - **id** (*Optional*, :ref:`config-id`): Set the ID of this sensor for use in lambdas.
  - All other options from :ref:`Sensor <config-sensor>`.

- **std_dev** (*Optional*): The information for the standard deviation sensor.

  - **name** (**Required**, string): The name for the standard deviation sensor.
  - **id** (*Optional*, :ref:`config-id`): Set the ID of this sensor for use in lambdas.
  - All other options from :ref:`Sensor <config-sensor>`.

- **trend** (*Optional*): The information for the trend sensor.

  - **name** (**Required**, string): The name for the trend sensor.
  - **id** (*Optional*, :ref:`config-id`): Set the ID of this sensor for use in lambdas.
  - All other options from :ref:`Sensor <config-sensor>`.

- **variance** (*Optional*): The information for the variance sensor.

  - **name** (**Required**, string): The name for the variance sensor.
  - **id** (*Optional*, :ref:`config-id`): Set the ID of this sensor for use in lambdas.
  - All other options from :ref:`Sensor <config-sensor>`.


``sliding`` type options:
********************************

- **window_size** (**Required**, int): The number of *measurements* over which to calculate the summary statistics when pushing out a
  value.
- **send_every** (**Required**, int): How often the sensor statistics should be pushed out. For example, if set to 15, then the statistic sensors will be publish updates after every 15 *measurements*.
- **send_first_at** (*Optional*, int): By default, the first *measurement's* statistics on boot is immediately
  published. With this parameter you can specify how many *measurements* should be collected before the first statistics are sent.
  Must be smaller than or equal to ``send_every``
  Defaults to ``1``.

``chunked_sliding`` type options:
****************************************

- **window_size** (**Required**, int): The number of *chunks* over which to calculate the summary statistics when pushing out a value.
- **send_every** (**Required**, int): How often the sensor statistics should be pushed out. For example, if set to 15, then the statistic sensors will be publish updates after every 15 *chunks*.
- **send_first_at** (*Optional*, int): By default, the first *chunk's* statistics on boot is immediately
  published. With this parameter you can specify how many *chunks* should be collected before the first statistics are sent.
  Must be smaller than or equal to ``send_every``
  Defaults to ``1``.
- **chunk_size** (*Optional*, int): The number of *measurements* to be stored in a chunk before inserting into the window. Note that exactly one of ``chunk_size`` or ``chunk_duration`` must be present.
- **chunk_duration** (*Optional*, :ref:`config-time`): The duration of *measurements* to be stored in a chunk before inserting into the window. Note that exactly one of ``chunk_size`` or ``chunk_duration`` must be present.


``continuous`` type options:
***********************************

- **window_size** (*Optional*, int): The number of *measurements* after which all statistics are reset. Set to ``0`` to disable automatic resets. Note that at least one of ``window_duration`` and ``window_size`` must be configured. If both are configured, whichever causes a reset first will do so.
- **window_duration** (*Optional*, :ref:`config-time`): Time duration after which all statistics are reset. Note that at least one of ``window_duration`` and ``window_size`` must be configured. If both are configured, whichever causes a reset first will do so.
- **send_every** (**Required**, int): How often the sensor statistics should be pushed out. For example, if set to 15, then the statistic sensors will be publish updates after every 15 *measurements*.
- **send_first_at** (*Optional*, int): By default, the first *measurement's* statistics on boot is immediately
  published. With this parameter you can specify how many *measurements* should be collected before the first statistics are sent.
  Must be smaller than or equal to ``send_every``.
  Defaults to ``1``.

``chunked_continuous`` type options:
*******************************************

- **window_size** (*Optional*, int): The number of *chunks* after which all statistics are reset. Set to ``0`` to disable automatic resets. Note that at least one of ``window_duration`` and ``window_size`` must be configured. If both are configured, whichever causes a reset first will do so.
- **window_duration** (*Optional*, :ref:`config-time`): Time duration after which all statistics are reset. Note that at least one of ``window_duration`` and ``window_size`` must be configured. If both are configured, whichever causes a reset first will do so.
- **send_every** (**Required**, int): How often the sensor statistics should be pushed out. For example, if set to 15, then the statistic sensors will be publish updates after every 15 *chunks*.
- **send_first_at** (*Optional*, int): By default, the first *chunk's* statistics on boot is immediately
  published. With this parameter you can specify how many *chunks* should be collected before the first statistics are sent.
  Must be smaller than or equal to ``send_every``.
  Defaults to ``1``.
- **chunk_size** (*Optional*, int): The number of *measurements* to be stored in a chunk before inserting into the window. Note that exactly one of ``chunk_size`` or ``chunk_duration`` must be present.
- **chunk_duration** (*Optional*, :ref:`config-time`): The duration of *measurements* to be stored in a chunk before inserting into the window. Note that exactly one of ``chunk_size`` or ``chunk_duration`` must be present.
- **restore** (*Optional*, boolean): Whether to store the intermediate statistics on the device so that they can be restored upon power cycle or reboot. Warning: this option can wear out your flash. Defaults to ``false``.

.. _window-types:

Window Types
------------

There are two categories of windows. The first category is a sliding window. A sliding window has a pre-defined capacity of ``window_size`` measurements. The component inserts sensor measurements until it has inserted ``window_size`` total. Before this component inserts another sensor measurement, it removes the oldest measurement in the window.

The second category is a continuous window. This category of windows has a pre-defined capacity of ``window_size`` measurements or a pre-defined duration ``window_duration``. The component inserts sensor measurements until it inserts ``window_size`` total or the difference between the timestamps of the oldest and most recent sensor measurements exceeds ``window_duration``. Then, this component removes **all** of the sensor measurements in the window.

Instead of inserting individual measurements, the component can combine several sensor measurements into a chunk. When this chunk exceeds ``chunk_size`` sensor measurements or ``chunk_size`` duration, this component adds that chunk to the window. This approach saves memory for sliding windows, as memory does not hold every individual sensor measurement but only stores several sensor measurements combined. For continuous windows, this improves accuracy for significantly large windows.

If you want to collect statistics from a significant number of measurements (potentially unlimited), use a ``chunked_continuous`` type. It uses slightly more memory and is slower but is numerically accurate. A ``continuous`` type uses very little memory and is extremely fast. However, it can lose accuracy with significantly large windows.

.. list-table:: Sliding Window Type Comparison
    :header-rows: 1 

    * - 
      - ``sliding``
      - ``chunked_sliding``
    * - Capacity set by count
      - yes
      - yes
    * - Capacity set by duration
      - no
      - indirectly
    * - Memory usage
      - high (for large windows)
      - medium (if chunk size is large)
    * - CPU usage
      - very low
      - very low
    * - Accurate Long-Term
      - yes
      - yes



.. list-table:: Continuous Window Type Comparison
    :header-rows: 1

    * -
      - ``continuous``
      - ``chunked_continuous``
    * - Capacity set by count
      - yes
      - yes
    * - Capacity set by duration
      - yes
      - yes
    * - Memory usage
      - very low
      - low (for large windows)
    * - CPU usage
      - very low
      - low
    * - Accurate Long-Term
      - potentially no (for large windows)
      - yes


.. _statistics-description:

Statistics Description
----------------------

- ``count`` sensor:

  - Counts the number of sensor measurements in the window that are not ``NaN``.
  - By default, its ``state_class`` is ``total``.

- ``covariance`` sensor:

  - Gives the covariance of the sensor masurements in the window with respect to time.
  - If ``group_type`` is ``sample``, and ``average_type`` is ``simple``, then it uses Bessel's correction to give an unbiased estimator.
  - If ``group_type`` is ``sample``, and ``average_type`` is ``time_weighted``, then it uses reliability weights to give an unbiased estimator.  
  - By default, its ``state_class`` is ``measurement``.
  - By default, it inherits ``accuracy_decimals``, ``entity_category``, and ``icon`` from the source sensor.
  - The ``unit_of_measurement`` is the source sensor's unit multiplied by the configured ``time_unit``. For example, if the source sensor is in ``Pa`` and ``time_unit`` is in seconds, the unit is ``Pa⋅s``.
  
- ``duration`` sensor:

  - Gives the sum of the durations between each measurements' timestamps in the window.
  - By default, its ``state_class`` is ``measurement``.  
  - The ``unit_of_measurement`` is in millseconds (ms).

- ``max`` sensor:

  - The maximum value of measurements from the source sensor in the window.
  - By default, its ``state_class`` is ``measurement``.  
  - By default, it inherits ``accuracy_decimals``, ``device_class``, ``entity_category``, ``icon``, and ``unit_of_measurement`` from the source sensor.

- ``mean`` sensor:

  - The mean/average value of measurements from the source sensor in the window.
  - By default, its ``state_class`` is ``measurement``.  
  - By default, it inherits ``accuracy_decimals``, ``device_class``, ``entity_category``, ``icon``, and ``unit_of_measurement`` from the source sensor.

- ``min`` sensor:

  - The minimum value of measurements from the source sensor in the window.
  - By default, its ``state_class`` is ``measurement``.  
  - By default, it inherits ``accuracy_decimals``, ``device_class``, ``entity_category``, ``icon``, and ``unit_of_measurement`` from the source sensor.

- ``std_dev`` sensor:

  - The standard deviation of measurements from the source sensor in the window.
  - If ``group_type`` is ``sample``, and ``average_type`` is ``simple``, then it uses Bessel's correction to give an unbiased estimator.
  - If ``group_type`` is ``sample``, and ``average_type`` is ``time_weighted``, then it uses reliability weights to give an unbiased estimator.  
  - By default, its ``state_class`` is ``measurement``.  
  - By default, it inherits ``accuracy_decimals``, ``device_class``, ``entity_category``, ``icon``, and ``unit_of_measurement`` from the source sensor.

- ``trend`` sensor:

  - Gives the slope of the line of best fit for the source sensor measurements in the window versus their timestamps.
  - By default, its ``state_class`` is ``measurement``.  
  - By default, it inherits ``accuracy_decimals``, ``entity_category``, and ``icon`` from the source sensor.
  - The ``unit_of_measurement`` is the source sensor's unit divided by the configured ``time_unit``. For example, if the source sensor is in ``Pa`` and ``time_unit`` is in seconds, the unit is ``Pa/s``.
  
- ``variance`` sensor:

  - Gives the variance of measurements from the source sensor in the window.
  - If ``group_type`` is ``sample``, and ``average_type`` is ``simple``, then it uses Bessel's correction to give an unbiased estimator.
  - If ``group_type`` is ``sample``, and ``average_type`` is ``time_weighted``, then it uses reliability weights to give an unbiased estimator.
  - By default, its ``state_class`` is ``measurement``.  
  - By default, it inherits ``accuracy_decimals``, ``entity_category``, and ``icon`` from the source sensor.
  - The ``unit_of_measurement`` is the source sensor's unit squared. For example, if the source sensor is in ``Pa``, the unit is ``(Pa)²``.
  

``sensor.statistics.reset`` Action
----------------------------------

This :ref:`Action <config-action>` allows you to reset all the statistics by clearing all stored measurements in the window. 
For example, you could use a time-based automation to reset all the statistics sensors at midnight.

.. code-block:: yaml

    on_...:
      - sensor.statistics.reset:  my_statistics_component  

See Also
--------

- :ref:`sensor-filters`
- `DABA Lite algorithm (IBM's sliding window aggregators) <https://github.com/IBM/sliding-window-aggregators/blob/master/cpp/src/DABALite.hpp>`__
- `Linear Trend Estimation (Wikipedia) <https://en.wikipedia.org/wiki/Linear_trend_estimation>`__
- `Bessel's Correction (Wikipedia) <https://en.wikipedia.org/wiki/Bessel%27s_correction>`__
- `Reliability Weights (Wikipedia) <http://en.wikipedia.org/wiki/Weighted_arithmetic_mean#Weighted_sample_variance>`__
- :apiref:`statistics/statistics.h`
- :ghedit:`Edit`
