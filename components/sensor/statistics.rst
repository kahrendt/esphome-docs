Statistics
==========

.. seo::
    :description: Instructions for setting up a Statistics Sensor

The ``statistics`` sensor platform quickly generates summary statistics from another sensor's measurements over a sliding window of observations. This sensor computes the following summary statistics: minimum, maximum, mean (average), sample variance, sample standard deviation, sample covariance, and trend (the slope of the line of best fit). It also provides the count of the number of valid (not ``NaN``) measurements currently in the sliding window. This sensor is efficient both computationally and in memory usage. The summary statistics for each observation in the sliding window are stored in external PSRam if available. Otherwise, it uses the internal memory of the device.

Each summary statistic sensor is optional, and the component only stores the measurement information necessary for the configured sensors.

Note that several of these statistics can instead be gathered using :ref:`sensor-filters`. This component allows you to quickly generate multiple summary statistics from the same source sensor, and they are computed in a more effecient manner than the filters for large sliding windows.

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
        send_first_at: 10
        count:
          name: "Count of Valid Sensor Measurements"    
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

- **name** (**Required**, string): The name of the sensor.
- **time_unit** (*Optional*, string): The time unit used for the covariance and trend sennsors, one of
  ``ms``, ``s``, ``min``, ``h`` or ``d``. Defaults to ``s``.
- **window_size** (*Optional*, int): The number of values over which to calculate the summary statistics when pushing out a
  value. Defaults to ``15``.
- **send_every** (*Optional*, int): How often a sensor statistics should be pushed out. For
  example, in above configuration each summary statistic is sent after every 5th received sensor value, over the last 15 received values. Defaults to ``15``.
- **send_first_at** (*Optional*, int): By default, the very first raw value on boot is immediately
  published. With this parameter you can specify when the very first value is to be sent.
  Must be smaller than or equal to ``send_every``
  Defaults to ``1``.
- **min** (*Optional*): The information for the minimum sensor.

  - **name** (**Required**, string): The name for the minimum sensor.
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

- **variance** (*Optional*): The information for the sample variance sensor (Bessel's correction is applied).

  - **name** (**Required**, string): The name for the sample variance sensor.
  - **id** (*Optional*, :ref:`config-id`): Set the ID of this sensor for use in lambdas.
  - All other options from :ref:`Sensor <config-sensor>`.

- **std_dev** (*Optional*): The information for the sample standard deviation sensor (Bessel's correction is applied).

  - **name** (**Required**, string): The name for the sample standard deviation sensor.
  - **id** (*Optional*, :ref:`config-id`): Set the ID of this sensor for use in lambdas.
  - All other options from :ref:`Sensor <config-sensor>`.

- **covariance** (*Optional*): The information for the sample covariance sensor (Bessel's correction is applied).

  - **name** (**Required**, string): The name for the sample covariance sensor.
  - **id** (*Optional*, :ref:`config-id`): Set the ID of this sensor for use in lambdas.
  - All other options from :ref:`Sensor <config-sensor>`.

- **trend** (*Optional*): The information for the trend sensor.

  - **name** (**Required**, string): The name for the trend sensor.
  - **id** (*Optional*, :ref:`config-id`): Set the ID of this sensor for use in lambdas.
  - All other options from :ref:`Sensor <config-sensor>`.

- **count** (*Optional*): The information for the count sensor.

  - **name** (**Required**, string): The name for the count sensor.
  - **id** (*Optional*, :ref:`config-id`): Set the ID of this sensor for use in lambdas.
  - All other options from :ref:`Sensor <config-sensor>`.

See Also
--------

- :ref:`sensor-filters`
- `DABA Lite algorithm (IBM's sliding window aggregators) <https://github.com/IBM/sliding-window-aggregators/blob/master/cpp/src/DABALite.hpp>`__
- `Linear Trend Estimation (Wikipedia) <https://en.wikipedia.org/wiki/Linear_trend_estimation>`__
- `Bessel's Correction (Wikipedia) <https://en.wikipedia.org/wiki/Bessel%27s_correction>`__
- :apiref:`statistics/statistics.h`
- :ghedit:`Edit`
