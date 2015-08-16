
```
struct inst {                   /* instruction */
    short   type;
    short   line;
    union {
        ....
        struct shared_string *string;   /* strings                          */
        ....
    } data
}

struct shared_string {              /* for sharing strings in programs */
    int     links;                  /* number of pointers to this struct */
    int     length;                 /* byte length of string data */
    int     wclength;               /* length in wide characters (UTF-8) */
    char    data[1];                /* shared string data */
};


```


  * `oper->data.string->data` is the pointer to the actual C string.
  * The C string should be fetched via `DoNullInd(struct shared_string *ss)` more often than not. string->data should contain a null pointer in the event of an empty string; `DoNullInd(oper->data.string)` will return an empty string (technically `'\0'`) in such a circumstance, preventing a NULL pointer from being accidentally dereferenced.
  * wclength is the cached UTF-8 length hint. It should always be accessed via the `wcharlen(struct shared_string *ss)` function.
    * If -2, the wclength is uncached and `wcharlen` will look it up.
    * If -1, `data` will contain byte sequences that are not legal UTF-8. This can be used to avoid wasting any cycles on trying to treat it like valid UTF-8. This is a cached value, and further calls to `wcharlen` will not attempt to calculate the UTF-8 length. If you are accepting data from an external source and want to ensure that the data is not treated as UTF-8, you can "cheat" and manually store -1.
      * IDEA: Set wclength to -3 for strings imported from a socket. Have `wcharlen` treat -3 the same as -1, and provide a separate prim that treats -3 as -2. (say, `wcharlen_sockstr`) This can be done when the encoding should reliably be valid UTF-8, such as when it's advertised via a HTTP header.
    * If `(length == wclength || wclength == 0)`, the string is both valid ASCII-7 and valid UTF-8.
    * If `(length > wclength)`, the string is valid UTF-8 but not valid ASCII-7.
  * Despite `data` being defined as a 1-byte array, the game will actually `malloc` (length + 1) bytes beginning at the memory space pointed to by `data`. If you need to extend `struct shared_string`, you **must not** add additional members after `data`.