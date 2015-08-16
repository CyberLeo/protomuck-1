# File Naming #
  * Files containing primitives begin with a prefix of p`_`. Supporting code shared by primitives and internal function calls of the game belong in a similarly named file without the p`_` prefix.
  * Always use one of the existing p`_` files if what you're working on belongs in one of the existing categories. New files generally shouldn't be added unless you're adding a major feature.
  * All p`_` files should define the following shared variables:
```
extern struct inst *oper1 = NULL, *oper2 = NULL, *oper3 = NULL, *oper4 = NULL;
extern struct inst temp1, temp2, temp3;
```

# Implicit Variables #
`PRIM_PROTOTYPE` define the following pass-in variables, which are available to all primitives:
```
dbref player
dbref program
int mlev
struct inst *pc
struct inst *arg
int *top
struct frame *fr
```

# Coding Conventions #
  * Functions defining primitives should have a "void" return type, begin with a prefix of prim`_`, and a supplied parameter of `(PRIM_PROTOTYPE)`.
  * Always check the number of args on the stack before doing anything else. This is done with CHECKOP(#). Even if your primitive accepts no parameters, you should still use CHECKOP(0).
  * If your primitive needs to access data on the stack, you need to `POP()` them off the stack. `POP()` returns a `struct inst` pointer which can be stored in one of the pre-defined `oper` variables.
```
oper1 = POP();
oper2 = POP();
```
  * Every item from the stack you `POP()` **must** be fed to `CLEAR(struct *inst)` before the function exits. Failure to do so introduces memory leaks to the server!
  * Call `CHECKOFLOW(#)` after your last `POP()` if you are going to return more items to the stack than you are taking away. This generates an abort if it would be illegal for you to push the items. It's preferable to do this **before** the main body of your code begins; if you wait until you're ready to push, you're just wasting processing time.
  * Type check the `->type` of every `oper` you `POP()` from the stack and `abort_interp("someerror (#)")` if you get something you don't expect. The error message should indicate the input parameter with the invalid data type, and it should number them from left to right. (number 1 would be the **last** stack item you `POP()`'d)
  * If you need to perform any data integrity checks (beyond data types), this should also be done before the main body of the primitive where possible. The same rules for using `abort_interp("someerror (#)")` apply.
  * Use the `DoNullInd(struct *shared_string)` macro to safely retrieve the value of `oper->data.string` when the `->type` is `PROG_STRING`. This returns an empty string instead of a null pointer (which you should never dereference) if the string is empty.
  * If your primitive returns data to the stack (most should), this should done via one of the following macros:
```
PushObject(int dbref)
PushInt(int integer)
PushFloat(long integer)
PushLock(struct *boolexp)
PushTrueLock()
PushMark()
PushStrRaw(struct shared_string *ss)
PushString(char *string)
PushNullStr()
PushArrayRaw(struct inst *stk_array)
PushNullArray()
PushInst(struct inst *instruction) /* for pushing a raw inst */
```
  * `Push`ing data to the stack between calls to `CLEAR()` can have unexpected results. `CLEAR()` first, then `Push`.