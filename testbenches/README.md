# Best practices for RTL testbenches
* Separate stimuli and checker
* Apply stimuli synchronously, after clock in the event queue
* Allow the event queue to resolve before checking
