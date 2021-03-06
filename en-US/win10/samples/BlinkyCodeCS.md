<h3> Let's look at the code </h3>
<div class="row">
    <p>The code for this sample is pretty simple. We use a timer, and each time the 'Tick' event is called, we flip the state of the LED.</p>
    <h3> Timer Code </h3>
    <div class="col-md-6 col-sm-12 col-no-padding">

        <p>Here is how you set up the timer in C#:</p>
    </div>
    <div class="col-md-6 col-sm-12">
        {% highlight C# %}
            public MainPage()
            {
                // ...

                timer = new DispatcherTimer();
                timer.Interval = TimeSpan.FromMilliseconds(500);
                timer.Tick += Timer_Tick;
                InitGPIO();
                if (pin != null)
                {
                    timer.Start();
                }

                // ...
            }

            private void Timer_Tick(object sender, object e)
            {
                if (pinValue == GpioPinValue.High)
                {
                    pinValue = GpioPinValue.Low;
                    pin.Write(pinValue);
                    LED.Fill = redBrush;
                }
                else
                {
                    pinValue = GpioPinValue.High;
                    pin.Write(pinValue);
                    LED.Fill = grayBrush;
                }
            }
        {% endhighlight %}
    </div>
</div>
<h3> Initialize the GPIO Pin </h3>
<div class="row">
    <div class="col-md-6 col-sm-12 col-no-padding">
        <p>To drive the GPIO pin, first we need to initialize it. Here is the C# code (notice how we leverage the new WinRT classes in the Windows.Devices.Gpio namespace):</p>
        <p> Let's break this down a little</p>
            <ul class="inline-list">
                <li>First, we use <code>GpioController.GetDefault()</code> to get the GPIO controller.</li>
                <li>If the device does not have a GPIO controller, this function will return <code>null</code>.</li>
                <li>Then we attempt to open the pin by calling <code>GpioController.OpenPin()</code> with the <code>LED_PIN</code> value.</li>
                <li>Once we have the <code>pin</code>, we set it to be off (High) by default using the <code>GpioPin.Write()</code> function.</li>
                <li>We also set the <code>pin</code> to run in output mode using the <code>GpioPin.SetDriveMode()</code> function.</li>
            </ul>
    </div>
    <div class="col-md-6 col-sm-12">
        {% highlight C# %}
            using Windows.Devices.Gpio;

            private void InitGPIO()
            {
                var gpio = GpioController.GetDefault();

                // Show an error if there is no GPIO controller
                if (gpio == null)
                {
                    pin = null;
                    GpioStatus.Text = "There is no GPIO controller on this device.";
                    return;
                }

                pin = gpio.OpenPin(LED_PIN);
                pinValue = GpioPinValue.High;
                pin.Write(pinValue);
                pin.SetDriveMode(GpioPinDriveMode.Output);

                GpioStatus.Text = "GPIO pin initialized correctly.";

            }
        {% endhighlight %}
    </div>
</div>
<h3> Modify the state of the GPIO pin </h3>
<div class="row">
<p>Once we have access to the <code>GpioOutputPin</code> instance, it's trivial to change the state of the pin to turn the LED on or off.</p>
    <div class="col-md-6 col-sm-12 col-no-padding">
        <p>To turn the LED on, simply write the value <code>GpioPinValue.Low</code> to the pin:</p>
    </div>
    <div class="col-md-6 col-sm-12">
        {% highlight C# %}
            pin.Write(GpioPinValue.Low);
        {% endhighlight %}
    </div>
</div>
<div class="row">
    <div class="col-md-6 col-sm-12 col-no-padding">
        <p>and of course, write <code>GpioPinValue.High</code> to turn the LED off:</p>
    </div>
    <div class="col-md-6 col-sm-12">
        {% highlight C# %}
            pin.Write(GpioPinValue.High);
        {% endhighlight %}
    </div>
</div>








Remember that we connected the other end of the LED to the 3.3 Volts power supply, so we need to drive the pin to low to have current flow into the LED.
