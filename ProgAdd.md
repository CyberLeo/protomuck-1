
```
struct inst {                   /* instruction */
    short   type;
    short   line;
    union {
        ....
        struct prog_addr *addr;         /* the result of 'funcname          */
        ....
    } data;
};

struct prog_addr {              /* for 'addres references */
    int     links;              /* number of pointers */
    dbref   progref;            /* program dbref */
    struct inst *data;          /* pointer to the code */
};
```

  * It has nothing to do with addition. Honest.