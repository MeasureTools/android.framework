<!-- MarkdownTOC -->

- [Android Framework](#android-framework)
- [How to set a GPIO](#how-to-set-a-gpio)
- [How to read from a GPIO](#how-to-read-from-a-gpio)
- [How to read from a ValueSupplier via the framework](#how-to-read-from-a-valuesupplier-via-the-framework)
	- [How to log a ValueSupplier via the framework](#how-to-log-a-valuesupplier-via-the-framework)
	- [License](#license)
	- [Acknowledgments](#acknowledgments)

<!-- /MarkdownTOC -->

# Android Framework

This is an open source framework to ease the use of this [android system services](https://github.com/MeasureTools/android.system-services) and ease the logging of the data returned by the services.

# How to set a GPIO

```java
int i = 10;
com.grimgal.android.framework.GPIO gpio =
      com.grimgal.android.framework.GPIO.create(i);
{
	gpio.setOut();
}
if (gpio.isOut()) {
	gpio.setValue(false); // Sets the GPIO to logical false
	// Do something in-between
	gpio.setValue(true);  // Sets the GPIO to logical true
}
else {
	// Error handling
}
```

# How to read from a GPIO

```java
int i = 10;
com.grimgal.android.framework.GPIO gpio =
      com.grimgal.android.framework.GPIO.create(i);
{
	gpio.setIn();
}
if (gpio.isIn()) {
	boolean value = gpio.getValue();
	// Do something with the value
}
else {
	// Error handling
}
```

# How to read from a ValueSupplier via the framework

```java
IValueSupplierManager valueSupplierManager =
      IValueSupplierManager.
      Stub.
      asInterface(
      	ServiceManager.
      	getService("VALUE_SUPPLIER_MANAGER")
      );
byte[] bytes = valueSupplierManager.
               value("$SUP_NAME"); // e.g. ODROID_XU3_FAN_USAGE
ByteBuffer wrapped = ByteBuffer.wrap(bytes)
float value = wrapped.getFloat(); // Beware, not all suppliers return a float.
                                  // Some return a double, int, long, char etc.
```

## How to log a ValueSupplier via the framework

```java
String log_directory = "my/log/directory/"; // Beware, write access isn't requestet 
                                            // by the frameworks and needs to be requestet 
                                            // by the app!
String prefix = "my_log_for_suppliert_X";
String supplier = "X";
try {
	com.grimgal.android.framework.Framework.init(log_directory, prefix);
	IValueSupplierManager ivsm = IValueSupplierManager.Stub.asInterface(ServiceManager.getService("VALUE_SUPPLIER_MANAGER"));
	if (ivsm == null) {
		com.grimgal.android.framework.Log.e("supplier","ivsm was null");
	}
	int found_index = -1;
	String[] suppliers = ivsm.list();
	com.grimgal.android.framework.Log.v("AppLauncher","Found " +suppliers.length+" Suppliers");
	for(int k =0 ; k < suppliers.length ; k++) {
		com.grimgal.android.framework.Log.v("supplier",suppliers[k]);
		if (suppliers[k].equals(supplier)) {
			found_index = k;
		}
	}
	if (found_index == -1) {
		// Error handling
	}
	// Log the value supplied by the value suppliert k each 100ms (10Hz)
	com.grimgal.android.framework.ValueLog.run(
		suppliers[found_index],                      // Supplier name
		100,                                         // Period
		java.util.concurrent.TimeUnit.MILLISECONDS   // Unit of the period
	);
}
catch (IOException e) {
	// Error handling
}
catch (RemoteException e2) {
	// Error handling
}
catch (Exception e3) {
	// Error handling
}
```

## License

This project is licensed under Apache 2.0 - see the [LICENSE](LICENSE) file for details.

## Acknowledgments

* TU Dortmund [Embedded System Software Group](https://ess.cs.tu-dortmund.de/EN/Home/index.html) which made this release possible
