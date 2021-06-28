# Arduino PWM frequency library

This is a fork (backup) of the well-known "PWM frequency library" initially created by Sam Knight; see http://code.google.com/p/arduino-pwm-frequency-library/downloads/list for further information on the original project. As Sam stated in [his post](https://forum.arduino.cc/t/pwm-frequency-library/114988), "the internet is full of partial examples and code snippets for changing PWM frequency", which makes it quite hard for beginners to get started with custom PWM settings, and it was the same for me. This library is great for its simplicity and a good starting point for creating prototype software for the Arduino. Since the Google Code Hosting service retired in 2016 I moved the code into this GitHub repository.

## Global functions

| Method | Description |
|--------|-------------|
| `InitTimers()` | Initializes all timers. Needs to be called before changing the timers frequency or setting the duty on a pin |
| `InitTimersSafe()` | Same as `InitTimers()` except timer 0 is not initialized in order to preserve time keeping functions. |
| `pwmWrite(uint8_t pin, uint8_t val)` | Same as `analogWrite()`, but it only works with initialized timers. Continue to use `analogWrite()` on uninitialized timers |
| `SetPinFrequency(int8_t pin, int32_t frequency)` | Sets the pin's frequency (in Hz) and returns a bool for success | 
| `SetPinFrequencySafe(int8_t pin, int32_t frequency)` | Same as `SetPinFrequency` except it does not affect timer 0 |

## Timer functions

The library also has five functions for each Timer object. I could not get the code size down to what I felt was reasonable so I ditched C++ classes and did some fancy macro work instead. Each of these functions are technically preprocessor macros with nice self explanatory names that swap out for more cryptic functions inside the library header just before compile time. For timer 1 the functions are:

| Method | Description |
|--------|-------------|
| `Timer1_GetFrequency()` | Gets the timer's frequency in Hz. |
| `Timer1_SetFrequency(int frequency)` | Sets the timer's frequency in Hz. |
| `Timer1_GetPrescaler()` | Gets the value (not bits) of the prescaler. Don't know what this means? Don't worry about it, just use `SetFrequency(int frequency)`. |
| `Timer1_SetPrescaler(enum value)` | Sets the prescaler. |
| `Timer1_GetTop()` | Gets the timer register's maximum value. |
| `Timer1_SetTop(int top)` | Sets the timer register's maximum value. |
| `Timer1_Initialize()` | Initializes the timer. |

The prescaler is inconsistent among different timers. I figured using enumerators was the best solution because most types of invalid input will be caught at compile time. For a normal timer, use one of these as a parameter: `ps_1`, `ps_8`, `ps_64`, `ps_256`, `ps_1024`. If those give a type error, then the timer you are using is one of the inconsistent ones, and you should use `psalt_1`, `psalt_8`, `psalt_32`, `psalt_64`, `psalt_128`, `psalt_256`, or `psalt_1024` instead.