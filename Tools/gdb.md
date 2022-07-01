# GDB

|Command|Description|
|---|---|
|```b $function```|Set breakpoint at function|
|```c```|Continue (Step)|
|```r```|run|

## GEF
|```gef```|Run GEF|
|```x/i $memoryaddress```|Examine the instruction|
|```x/dw $memory_address```|Displays the value at the given memory address|
|```print $eax```|Print the value of the eax register|
|```print $eip```|Print the value of the eip register|
|```print $ebp```|Print the value of the eip register|
|```print```|Can also print mathematical functions e.x ```print $ebp-0x10``` will print the value of the EBP register minus 0x10|
|```pattern create $length```|Creates pattern of $length bytes|
|```pattern offset $offset```|Finds pattern in pattern created by ```pattern create```|
