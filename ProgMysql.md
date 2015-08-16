
```
struct inst {                   /* instruction */
    short   type;
    short   line;
    union {
        ....
#ifdef SQL_SUPPORT                      /*----------------------------------*/
            struct muf_sql *mysql;      /* A MySQL connection               */
#endif                                  /*----------------------------------*/
        ....
    } data;
};

#ifdef SQL_SUPPORT
struct muf_sql {       /* struct for MUF SQL connections */
    MYSQL *mysql_conn; /* The connection descriptor struct */
    int connected;     /* Bool indicating if connected or not */
    int timeout;       /* Timeout for this connection */
    int links;         /* number of instances of this connection */
};
#endif
```

  * The `MYSQL` type is defined by MySQL's C API. <mysql.h>