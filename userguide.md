# libimportx user guide



Python
------

```bash
pip install libimportx
```

```python

from libimportx import*

# To export this file
def myfunction(arg): #defining something to export
    print(arg);
    return arg
variable="value"

exportx() # Blocks if importx'ed, otherwise returns False
print("Running standalone")

# To import a file
mymodule=importx("file.js")
mymodule.myfunction("Calling JS function")
print(mymodule.variable)
mymodule.variable="another_value"
exit()

```

JavaScript
----------
```bash
npm install libimportx
```
```js
var {importx,exportx}=require("libimportx")
// To export this file
function myfunction(arg){
    console.log(arg)
    return arg
}
let variable="value"

module.exports={variable,myfunction}
if (!exportx()){ //Does NOT block, returns whether it was importx'ed or not
    console.log("Running standalone")
    main() // To prevent infinite loops we are putting it here
}

async function main(){
    // To import a file
    var mymodule=await importx("file.py") //In JS you need await for some things
    await mymodule.myfunction("Printed from Python")
    console.log(await mymodule.variable)
    mymodule.variable="another_value" //no await here
    process.exit(0);
}
```


