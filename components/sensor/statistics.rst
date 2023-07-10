Statistics
==========

.. seo::
    :description: Instructions for setting up a Statistics Sensor

The ``statistics`` sensor platform quickly generates summary statistics from another sensor's measurements over a sliding window of observations. This sensor computes the following summary statistics: minimum, maximum, mean (average), variance, standard deviation, covariance, and trend (the slope of the line of best fit). It also provides the count of the number of valid (not ``NaN``) measurements currently in the sliding window. This sensor is efficient both computationally and in memory usage. The summary statistics for each observation in the sliding window are stored in external PSRam if available. Otherwise, it uses the internal memory of the device.

Each summary statistic sensor is optional, and the component only stores the measurement information necessary for the configured sensors.

Note that several of these statistics can instead be individually gathered using :ref:`sensor-filters`. This component allows you to quickly generate multiple summary statistics from the same source sensor, and they are computed in a more effecient manner than the filters for large sliding windows.

To use the sensor, first, provide the source sensor and then configure the desired statistics.

The min, max, mean, and standard deviations sensors inherit the source sensor's ``accuracy_decimals``, ``device_class``, ``entity_category``, ``icon``, and ``unit_of_measurement``. Their ``state_class`` is ``measurement``.

The variance, covariance, and trend sensors inherit the source sensor's ``accuracy_decimals``, ``entity_class``, and ``icon``. The ``unit_of_measurement`` for each are automatically transformed based on the source sensor's ``unit_of_measurement``. Their ``state_class`` is ``measurement``.

The count sensor does not inherit any properties from the source sensor, and its ``state_class`` is ``total``.

.. code-block:: yaml

    # Example configuration entry
    sensor:
      - platform: statistics
        source_id: source_measurement_sensor
        window_size: 15
        send_every: 5
        send_first_at: 3
        group_type: sample
        count:
          name: "Count of Valid Sensor Measurements"    
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
        variance:
          name: "Sensor Sample Variance"
        covariance:
          name: "Sensor Sample Covariance"
        trend:
          name: "Sensor Trend"

      # Use any other sensor component to gather statistics for
      - platform: ...
        id: source_measurement_sensor

Configuration variables:
------------------------

- **type** (**Required**, string): One of ``sliding_window``, ``chunked_sliding_window``, ``continuous``, or ``chunked_continuous``.
- **average_type** (*Optional*, string): How each measurement is weighted, one of ``simple`` or ``time_weighted``. Defaults to ``simple``.
- **group_type** (*Optional*, string): The type of the set of sensor measurements, one of ``sample`` or ``population``. Defaults to ``sample``.
- **time_unit** (*Optional*, string): The time unit used for the covariance and trend sensors, one of
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

``sliding_window`` type options:

- **window_size** (**Required**, int): The number of *measurements* over which to calculate the summary statistics when pushing out a
  value.
- **send_every** (**Required**, int): How often the sensor statistics should be pushed out. For example, if set to 15, then the statistic sensors will be publish updates after every 15 *measurements*.
- **send_first_at** (*Optional*, int): By default, the first *measurement's* statistics on boot is immediately
  published. With this parameter you can specify how many *measurements* should be collected before the first statistics are sent.
  Must be smaller than or equal to ``send_every``
  Defaults to ``1``.

``chunked_sliding_window`` type options:

- **window_size** (**Required**, int): The number of *chunks* over which to calculate the summary statistics when pushing out a value.
- **chunk_size** (*Optional*, int): Note that exactly one of ``chunk_size`` or ``chunk_duration`` must be present.
- **chunk_duration** (*Optional*, :ref:`config-time`): Note that exactly one of ``chunk_size`` or ``chunk_duration`` must be present.
- **send_every** (**Required**, int): How often the sensor statistics should be pushed out. For example, if set to 15, then the statistic sensors will be publish updates after every 15 *chunks*.
- **send_first_at** (*Optional*, int): By default, the first *chunk's* statistics on boot is immediately
  published. With this parameter you can specify how many *chunks* should be collected before the first statistics are sent.
  Must be smaller than or equal to ``send_every``
  Defaults to ``1``.

``continuous`` type options:

- **window_size** (*Optional*, int): The number of *measurements* after which all statistics are reset. Set to ``0`` to disable automatic resets. Note that at least one of ``window_duration`` and ``window_size`` must be configured. If both are configured, whichever causes a reset first will do so.
- **window_duration** (*Optional*, :ref:`config-time`): Time duration after which all statistics are reset. Note that at least one of ``window_duration`` and ``window_size`` must be configured. If both are configured, whichever causes a reset first will do so.
- **send_every** (**Required**, int): How often the sensor statistics should be pushed out. For example, if set to 15, then the statistic sensors will be publish updates after every 15 *measurements*.
- **send_first_at** (*Optional*, int): By default, the first *measurement's* statistics on boot is immediately
  published. With this parameter you can specify how many *measurements* should be collected before the first statistics are sent.
  Must be smaller than or equal to ``send_every``.
  Defaults to ``1``.

``chunked_continuous`` type options:

- **chunk_size** (*Optional*, int): Note that exactly one of ``chunk_size`` or ``chunk_duration`` must be present.
- **chunk_duration** (*Optional*, :ref:`config-time`): Note that exactly one of ``chunk_size`` or ``chunk_duration`` must be present.
- **window_size** (*Optional*, int): The number of *chunks* after which all statistics are reset. Set to ``0`` to disable automatic resets. Note that at least one of ``window_duration`` and ``window_size`` must be configured. If both are configured, whichever causes a reset first will do so.
- **window_duration** (*Optional*, :ref:`config-time`): Time duration after which all statistics are reset. Note that at least one of ``window_duration`` and ``window_size`` must be configured. If both are configured, whichever causes a reset first will do so.
- **restore** (*Optional*, boolean): Whether to store the intermediate statistics on the device so that they can be restored upon power cycle or reboot. Warning: this option can wear out your flash. Defaults to ``false``.
- **send_every** (**Required**, int): How often the sensor statistics should be pushed out. For example, if set to 15, then the statistic sensors will be publish updates after every 15 *chunks*.
- **send_first_at** (*Optional*, int): By default, the first *chunk's* statistics on boot is immediately
  published. With this parameter you can specify how many *chunks* should be collected before the first statistics are sent.
  Must be smaller than or equal to ``send_every``.
  Defaults to ``1``.

See Also
--------

- :ref:`sensor-filters`
- `DABA Lite algorithm (IBM's sliding window aggregators) <https://github.com/IBM/sliding-window-aggregators/blob/master/cpp/src/DABALite.hpp>`__
- `Linear Trend Estimation (Wikipedia) <https://en.wikipedia.org/wiki/Linear_trend_estimation>`__
- `Bessel's Correction (Wikipedia) <https://en.wikipedia.org/wiki/Bessel%27s_correction>`__
- :apiref:`statistics/statistics.h`
- :ghedit:`Edit`
