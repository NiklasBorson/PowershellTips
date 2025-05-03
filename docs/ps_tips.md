# 10 Powershell Tips and Tricks

## Tip 1: Getting help

Get usage of any command from the command-line.

**Example:**

```Powershell
Get-Help dir
```

## Tip 2: Getting the members of an object

Discover what members (e.g., methods and properties) an object has.

### Example 1: all members of a variable

```Powershell
$s = "Hello"
$s | Get-Member
```

### Example 2: just properties of a variable

```Powershell
$s = "Hello"
$s | Get-Member -MemberType Properties
```

### Example 3: Members of an object output from a command

```Powershell
Get-ChildItem | Get-Member
```

## Tip 3: Getting the type of an object

```Powershell
Example:
$s = "Hello"
$s.GetType()
```

## Tip 4: Declaring types

Two objects may look the same when formatted for the console, yet behave differently if they have different types. Explicitly declaring types can make your code more predictable and give you some automatic error checking and validation.

### Example 1: Why types matter

```Powershell
10 + 2           # Outputs 12
"10" + 2         # Outputs "102" (string concatenation)
[int]"10" + 2    # Outputs 12 (explicit conversion/cast)
```

### Example 2: Declaring a variable's type

```Powershell
[int] $n        # $n is now a variable of type Int32
$n = 2          # ok - 2 is an Int32
$n = "10"       # ok - converts string value "10" to Int32
$n = "Hello"    # Error! Cannot convert value
```

### Example 3: Declaring a parameter's type

```Powershell
function Get-Year([DateTime] $date) { $date.Year }
Get-Year "1/1/2025"      # Outputs 2025
Get-Year "Strawberry"    # Error: Cannot process argument transformation
```

## Tip 5: Declaring script parameters

### Example: Required parameter and optional parameter with default value

Include the following at the top of a .ps1 file:

```Powershell
Param (
    [Parameter(Mandatory=$true)][DateTime] $BirthDate,
    [DateTime] $AsOf = (Get-Date)
)
```

In the above example, the `$AsOf` parameter is optional, so we assign it a default value. In this
case, the default value is the current date as obtained by invoking the `Get-Date` command. The
parentheses around the `Get-Date` command mean that we're _invoking_ the command and assigning its
_output_ to the `$AsOf` parameter. Without the parentheses, we'd be trying to assign the command
itself to the `$AsOf` parameter, which would be an error because it's the wrong type.

## Tip 6: Quick-and-dirty functions

You can define a function directly from the PowerShell prompt. This can be handy when you're working on a script or module, and you want a quick way to invoke your code.

### Example 1: Define a function that invokes your script with a parameter

```Powershell
PS C:\MyScripts> function go { .\Get-Age.ps1 -BirthDate 12/1/1999 }
PS C:\MyScripts> go
```

### Example 2: Define a function that invokes a method from your module

```Powershell
PS C:\MyScripts> function go {
>>     Import-Module .\Dates.psm1
>>     Get-DateNumber ([DateTime]::Now)
>>     Remove-Module Dates
>> }
PS C:\MyScripts> go
```

When you're writing a PowerShell module, it's helpful to have a quick-and-dirty function like this so
you can easily test functions exported by your module.

## Tip 7: Using the generic List class from PowerShell

Arrays in PowerShell (and .NET) are fixed-size. What if you want to build an array by progressively adding items to it, e.g., in a loop? You can use the + operator, but this is inefficient because it actually creates a brand new array each time. A better way is to use the generic List class from .NET.

### Example 1: Creating a List of int

```Powershell
$list = [System.Collections.Generic.List[int]]::new()
$list.Add(1)
$list.Add(2)
$list[0].GetType() # returns Int32
```

### Example 2: Converting a List of int to an array

```Powershell
$list = [System.Collections.Generic.List[int]]::new()
# . . . Add things to the list . . .
$a = $list.ToArray()
$a.GetType() # returns Int32[]
```

### Tip 8: Using hashtables

### Example 1: Creating and initializing a hashtable in one statement

```Powershell
PS C:\> $h = @{ "First" = "Homer"; "Last" = "Simpson" }
PS C:\> $h.Count
2
PS C:\> $h["Last"]
Simpson
```

### Example 2: Creating and empty hashtable and adding to it

```Powershell
PS C:\> $h = @{}
PS C:\> $h.Count
0
PS C:\> $h["First"] = "Homer"
PS C:\> $h["Last"] = "Simpson"
PS C:\> $h.Count
2
PS C:\> $h["Last"]
Simpson
```

### Example 3: Removing items from a hashtable

```Powershell
PS C:\> $h = @{ "First" = "Homer"; "Last" = "Simpson" }
PS C:\> $h.Count
2
PS C:\> $h.Remove("First")
PS C:\> $h.Count
1
```

## Tip 9: String substitution/interpolation

### Example 1: Simple variable substitution

```Powershell
PS C:\> $firstName = "Homer"
PS C:\> $lastName = "Simpson"
PS C:\> "$firstName $lastName"
Homer Simpson
```

### Example 2: Use single quotes to disable string interpolation

```Powershell
PS C:\> '$firstName $lastName'
$firstName $lastName
```

### Example 3: Enclose complex expressions in $()

```Powershell
PS C:\> "$firstName has $($firstName.Length) characters."
Homer has 5 characters.
```

## Tip 10: String formatting

When you want more control than built-in string substitution gives you.

### Example 1: Formatting a value as a 4-digit hexadecimal number

```Powershell
PS C:\> $n = 254
PS C:\> [string]::Format("0x{0:X4}", $n)
0x00FE
```

### Example 2: Using the -f operator

```Powershell
PS C:\> $n = 254
PS C:\> "0x{0:X4}" -f $n
0x00FE
```
