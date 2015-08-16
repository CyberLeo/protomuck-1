
```
struct inst {                   /* instruction */
    short   type;
    short   line;
    union {
        ....
        struct muf_proc_data *mufproc;  /* Data specific to each procedure  */
        ....
    } data;
};

struct muf_proc_data {
    char *procname;
    int vars;
    int args;
    const char **varnames;
};

```