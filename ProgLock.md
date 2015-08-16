
```
struct inst {                   /* instruction */
    short   type;
    short   line;
    union {
        ....
        struct boolexp *lock;           /* boolean lock expression          */
        ....
    } data;
};

struct boolexp {
    boolexp_type type;
    struct boolexp *sub1;
    struct boolexp *sub2;
    dbref   thing;
    struct plist *prop_check;
};

#define TRUE_BOOLEXP ((struct boolexp *) 0)

struct plist {
    unsigned short flags;
    short height;               /* satisfy the avl monster.  */
    union pdata_u data;
    struct plist *left, *right, *dir;
    char key[1];
};

union pdata_u {
    char *str;
    struct boolexp *lok;
    int val;
    double fval;
    dbref ref;
    long pos;
};

```

  * You are wise to stay away from the internals of locks if you value your sanity. None of us will blame you.