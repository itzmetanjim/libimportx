# libimportx user guide



Python
------

```python
from libimportx import*

# To import a file
mymodule=importx("file.js")
print(mymodule.myfunction("args"))
print(mymodule.variable)
mymodule.variable="another_value"

# To export this file
def myfunction(arg): #defining something to export
    print(arg);
    return arg
variable="value"

exportx() # Blocks if importx'ed, otherwise returns False
print("Running standalone")
```

JavaScript
----------

```js
var {importx,exportx}=require("libimportx")

// To import a file
var mymodule=await importx("file.py") //In JS you need await for some things
console.log(await mymodule.myfunction())
console.log(await mymodule.variable)
mymodule.variable="another_value" //no await here


// To export this file
function myfunction(arg){
    console.log(arg)
    return arg
}
let variable="value"

module.exports={variable,myfunction}
if (!exportx()){ //Does NOT block, returns whether it was importx'ed or not
    console.log("Running standalone")
}
```


