Qwiic PIR Motion Binary Sensor
==============================

.. seo::
    :description: Instructions for setting up the Qwiic PIR Motion binary sensor.
    :image: qwiic_pir.jpg

The Qwiic PIR Motion binary sensor allows you to use your Qwiic PIR (`EKMC4607112K based <https://www.sparkfun.com/products/17374>`__, `EKMB1107112 based <https://www.sparkfun.com/products/17375>`__, `firmware documentation <https://github.com/sparkfun/Qwiic_PIR>`__)
sensors from SparkFun with ESPHome.

.. figure:: images/qwiic_pir.jpg
    :align: center
    :width: 30.0%

    SparkFun Qwiic PIR sensor.
    (Credit: `Sparkfun <https://www.sparkfun.com/products/17374>`__, image cropped and compressed)

.. _Sparkfun: https://www.sparkfun.com/products/17374

The SparkFun Qwiic PIR Motion binary sensor uses Sharp PIR sensors to detect motion. It communicates over I²C. There are two models currently available. One uses the `Sharp EKMC4607112K sensor <https://cdn.sparkfun.com/assets/7/2/a/4/3/EKMC460711xK_Spec.pdf>`__, and the other uses the `Sharp EKMB1107112 sensor <https://cdn.sparkfun.com/assets/c/e/8/7/5/EKMB110711x_Spec.pdf>`__. 

You can configure a debounce time to reduce noise and false detections. You can also define an optional binary sensor that reports the raw state of the PIR sensor with no debouncing.

To use the sensor, first set up an :ref:`I²C Bus <i2c>` and connect the sensor to the specified pins.

.. code-block:: yaml

    # Example configuration entry
    binary_sensor:
      - platform: qwiic_pir
        name: "Qwiic PIR Motion Sensor"
        debounce: 500ms
        raw:
            name: "Qwiic PIR Raw Motion Sensor"

Configuration variables:
------------------------

- **name** (**Required**, string): The name of the motion sensor.
- **id** (*Optional*, :ref:`config-id`): Manually specifiy the ID used for code generation.
- **debounce** (*Optional*, :ref:`config-time`): The debounce time to reduce noise and false detections. Defaults to ``750ms``.
- **raw** (*Optional*) The information for the raw motion binary sensor.

    - **name** (**Required**, string): The name for the raw motion binary sensor.
    - **id** (*Optional*, :ref:`config-id`): Manually specifiy the raw binary sensor's ID used for code generation.
    -  All other options from :ref:`Binary Sensor <config-binary_sensor>`.

-  All other options from :ref:`Binary Sensor <config-binary_sensor>`.

See Also
--------
- :doc:`/components/binary_sensor/index`
- :apiref:`qwiic_pir/qwiic_pir.h`
- :ghedit:`Edit`
