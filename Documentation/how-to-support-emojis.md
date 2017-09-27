
# How to support emojis (utf8mb4)

### A little background

On MySQL, the max key length is 767 bytes. We need pay attention to all the keys on the database that are based specially on the **VARCHAR** type.

For example:

- *utf8*, each character is 3 bytes. So a varchar(255) = 255 * 3 = 765 bytes (**OK**, under the limit).

- *utf8mb4*, each character is 4 bytes. So a varchar(255) = 255 * 4 = 1020 bytes (**NOT OK**)

While running a migration you won’t see MySQL 5.7 complaining about this, and everything will be created as you would expect. Except for the keys that break the limit. (MySQL fails that silently?)

### Options

2 options may be considered:

- Option 1: Do you really need 255 characters on that column?

    A good example of this is the PHP nodes/backend package. We have backend_roles table with the column slug varchar(255) and a reference to this column on the backend_users table, on the column user_role varchar(255). Do we really need a role slug to be 255 characters? We can reduce it to 191 characters and everything would be ok (191 * 4 = 764 bytes).

    This would represent a slug like this:
    35qh8PXbQeYxtfv5k3ZtaZChucgHm4GuWSFCum80oa4JQYBSFfEqn9ffEK378MIbmhVpGbhpVnLx5mk9MlLfVK05f3yrydwVBddMKoecA4rzFiaqWcrzrgf2yCH8GnmbEqC4Dk7ZZkVV7VEci32n0X1DqtmhDluuOjwkPrIxXeYsbotvgtkZ1bW6SEp0leB

    Enough? :)

- Option2: We really need 255 characters!

    If we really need all 255 characters, we can consider using this innodb setting:
    http://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html#sysvar_innodb_large_prefix

### How to enable utf8mb4 support on Vapor?

If your project is deployed to Vapor Cloud, then `utf8mb4` will be the default encoding on `VARCHAR` fields.
