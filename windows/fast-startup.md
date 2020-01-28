# Windows 8/10 - Difference between Shutdown and Restart with "Fast Startup"

A shutdown in Windows 8 or 10 is not really a shut down, the state of RAM is written to hard disk and then read back into RAM when you start your system again.

> In Windows this is counter intuitive because of Fast Startup . . .
> 
> *Shutdown:*
> The contents of the RAM on your PC is written to your hard drive, then when you start your PC, those contents are read back into RAM, this is very similar to the old Hibernation in previous versions of Windows.
> 
> *Restart:*
> Your PC is actually shut down and the contents of RAM are flushed, then your PC starts up again with a fresh version of Windows.
> 
> This is the reason why large Windows 10 updates require a restart, rather than a shut down.
> 
> It is not well known, that it is very healthy for your PC to perform a restart every now and then to flush out your System RAM, because constantly using shut down over time can result in a slow sluggish system . . . 

## Reference

[What is the difference between Shutdown and Restart?](https://answers.microsoft.com/en-us/windows/forum/all/what-is-the-difference-between-shutdown-and/fa211af9-a13d-43da-9f8d-a5d2973a5c77)

[What Is Windows 10 Fast Startup And How to Disable It](https://www.petri.com/how-to-disable-windows-10-fast-startup-and-why-you-might-want-to)