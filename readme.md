# A lightweight and nice syntax for handling errors in FileMaker

FM Pilot adds a nice syntax for handling error in FileMaker by using custom functions.

## Handling error from a script step

Instead of using Get(LastError), we have shortcut @IsError to handle any errors from a script step. As you can see in the example below, if an error occurs while setting the data to the First Name field, a dialog will prompt out.

```filemaker
Set Field [ Contacts::First Name ; "Phu Huynh" ] 
If [ @IsError ] 
	Perform Script [ Specified: From list ; “Show Custom Dialog” ; Parameter:    ]
End If
```

## Handling error from another script

Suppose we have a script that returns a custom error like this.

```filemaker
Exit Script [ Text Result: #Error.Custom ( -1 ; "Your custom error message" ) ] 
```

With FM Pilot, you can easily handle the error with a few lines of code.

```filemaker
Perform Script [ Specified: From list ; “Do something” ; Parameter:    ]
If [ @IsError ] 
	Perform Script [ Specified: From list ; “Show Custom Dialog” ; Parameter:    ]
End If
```

## Supporting Last Error and Custom Error

In case you would like to return a last error from a script step or a script. 

- Return last error from a script step

```filemaker
Set Field [] 
If [ @IsError ] 
	Exit Script [ Text Result: #LastError ] 
End If
```

- Return last error from a script

```filemaker
Perform Script [ Specified: From list ; “Do something” ; Parameter:    ]
If [ @IsError ] 
	Exit Script [ Text Result: #LastError ] 
End If
```

- To return a custom error, you simply replace the exit script step in the example above with the following line.

```filemaker
Exit Script [ Text Result: #Error.Custom ( -1 ; "Your custom error message" ) ] 
```

The first parameter is the error code, and the second one is the error description.

## Simple way to get error code and error description

Two custom functions that support getting an error code and description.

```filemaker
@Error.Code
@Error.Message
```

An example use case

```filemaker
Set Field [] 
If [ @IsError ] 
	Show Custom Dialog [ "Error code: " &  @Error.Code ; @Error.Message ] 
End If
```

## Verifying script parameters and auto converting to local variables 

Supports JSON parameter only.

```filemaker
Set Variable [ $elementRequired ; Value: List ( "firstName" ; "lastName" ) ] 
Set Variable [ $elementOptional ; Value: List ( "age" ; "" ) ] 
If [ not @IsValid ] 
	Exit Script [ Text Result: #LastError ] 
End If
```

The above code verifies the required parameters firstName and lastName. If they're not passed to the script parameter, it will throw an error with description. The `age` is optional. If no error has been thrown, the following local variables will be created.

```filemaker
$firstName
$lastName
$age (if it's included in the script parameter)
```

## Auto converting a script result to local variables

When you call a script, and then use @IsError function to check the result, if the script returns a JSON without an error, the key-values pair will be converted to local variables. Let's have a look at this example.

A script returns a JSON string like this

```filemaker
Exit Script [ Text Result: JSONSetElement ( "{}" ; [ "firstName" ; "Phu" ; JSONString] ; [ "lastName" ; "Huynh" ; JSONString ]  ) ] 
```

Using @IsError function to check the result

```filemaker
Perform Script [ Specified: From list ; “Do something” ; Parameter:    ]
If [ @IsError ] 
	Perform Script [ Specified: From list ; “Show Custom Dialog” ; Parameter:    ]
End If
```

If there was no error, you would get the below local variables.

```filemaker
$firstName
$lastName
```

## Adding custom message when a script parameter is missing

You are now able to use a custom message returned when a script parameter is missing.

```filemaker
Set Variable [ $elementRequired ; Value: List ( #Para.MakeCustomMessage ( "firstName" ; "First name cannot be nil." ) ; "number" ; "lastName" ) ] 
Set Variable [ $elementOptional ; Value: List ( "age" ; "" ) ] 
If [ not @IsValid ] 
    Exit Script [ Text Result: #LastError ] 
End If
```
The default message is 'Missing parameter firstName', but for now you can use the function `#Para.MakeCustomMessage` to overwrites it, which is more friendly to users.

## Conclusion

I used FM Pilot in a lot projects, and it saved a lot of development time. You can fork this repo and do whatever you want.

Thanks for reading. Let me know if you have any questions, comments or feedback on Twitter.
