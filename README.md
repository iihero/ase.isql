# Simple Usage of Java Isql client for ASE

<a rel="nofollow"  href="https://info.flagcounter.com/R9Kc"><img src="https://s01.flagcounter.com/countxl/R9Kc/bg_FFFFFF/txt_000000/border_CCCCCC/columns_2/maxflags_10/viewers_0/labels_1/pageviews_1/flags_0/percent_0/" alt="Flag Counter" border="0" alt="Flag Counter" ></a>

## 1. Build:

mvn clean package   
There will be ase.isql-1.0.2-spring-boot.jar and jconn4-16.0.03.11.30030.jar in target directory.  The spring-boot jar already packaged the jconn related classes inside. 

## 2. Run: 

Only ase.isql-1.0.2-spring-boot.jar is required.

java -jar ase.isql-1.0.2-spring-boot.jar -S jdbc:sybase:Tds:localhost:30015/MYDB -E -U \<username\> -P <password\> [-I sql_input_file]

sample:

### 2.1 No input file

java -jar ase.isql-1.0.2-spring-boot.jar -S jdbc:sybase:Tds:localhost:30015/MYDB -E -U \<username\> -P <password\>

Enter a query:
1 > select 1

------------------ Result set 1 -----------------------

Columns:
[ 1]    1
1 rows Affected.

Enter a query:
1 > quit

### 2.2 Pipe SQL command into the connection command

**echo sp_helpdb tempdb**  | java -jar ase.isql-1.0.2-spring-boot.jar  -S "jdbc:sybase:Tds:localhost:30015/MYDB?ENABLE_SSL=true&SSL_TRUST_ALL_CERTS=true"  -U * -P *

```
Enter a query:
1 >
------------------ Result set 1 -----------------------

Columns:        name    db_size owner   dbid    created durability      lobcomplvl      inrowlen        status
[ 1]    tempdb       124.0 MB   sa         2    Mar 22, 2022    no_recovery              0          NULL        select into/bulkcopy/pllsort, trunc log on chkpt, abort tran on log full, mixed log and data, allow wide dol rows
1 rows Selected.

------------------ Result set 2 -----------------------

Columns:        name    attribute_class attribute       int_value       char_value      comments
[ 1]    tempdb  buffer manager  cache binding           1       tempdb_cache    null
1 rows Selected.


------------------ Result set 3 -----------------------

Columns:        device_fragments        size    usage   created free_kbytes
[ 1]    master        24.0 MB    -- unused by any segments --   Mar  6 2019  2:10AM                24480
[ 2]    tempdbdev            100.0 MB   data and log    Mar  6 2019  2:11AM               100736
2 rows Selected.
```



### 2.3 With input file

The content of abc.sql is:  

exec sp_savespace_lob 16384

java -jar ase.isql-1.0.2-spring-boot.jar -S jdbc:sybase:Tds:localhost:30015/MYDB -E -U \<username\> -P <password\> -I abc.sql

output:

```
Warning: Row size (17743 bytes) could exceed row size limit, which is 16300 bytes.
...... 
sp_chgattribute SMP_CLOUDBUILD_SIGNING_PROFILE, "dealloc_first_txtpg", 1
'dealloc_first_txtpg' attribute of object 'SMP_CLOUDBUILD_SIGNING_PROFILE' changed to 1.
alter table SMP_CLOUDBUILD_SIGNING_PROFILE modify CERTIFICATE_FILE_DATA in row (16384)
Warning: ALTER TABLE operation MODIFY IN ROW did not affect column 'CERTIFICATE_FILE_DATA'.

Warning: Row size (32767 bytes) could exceed row size limit, which is 16300 bytes.

SQLSTATUS = 0
sp_chgattribute SMP_CLOUDBUILD_SIGNING_PROFILE, "dealloc_first_txtpg", 1
'dealloc_first_txtpg' attribute of object 'SMP_CLOUDBUILD_SIGNING_PROFILE' changed to 1.
alter table SMP_CLOUDBUILD_SIGNING_PROFILE modify IOS_PROVISIONING_FILE_DATA in row (16384)
Warning: ALTER TABLE operation MODIFY IN ROW did not affect column 'IOS_PROVISIONING_FILE_DATA'.

Warning: Row size (32767 bytes) could exceed row size limit, which is 16300 bytes.

SQLSTATUS = 0
Unexpected exception : SqlState: 010P4 java.sql.SQLWarning: 010P4: An output parameter was received and ignored., ErrorCode: 0
2028 rows Affected.
```

### 2.4 With Customized Properties in the Server Url

java -jar ase.isql-1.0.2-spring-boot.jar  -S "jdbc:sybase:Tds:localhost:30015/MYDB?ENABLE_SSL=true&SSL_TRUST_ALL_CERTS=true" -U * -P * 

Here,  if you don't want to add : ?ENABLE_SSL=true&SSL_TRUST_ALL_CERTS=true,  just use "-E" instead. It will automatically add these two connection properties. 

### 2.5 Option for Quit the command immediately

By default,  the isql will continue to execute the later sql statement even there is error for the current statement.  To force quit,  please add "-z" in the command line option. 

```
c:\work\tools\database\ase.isql\target>java -jar ase.isql-1.0.2-spring-boot.jar -z -S "jdbc:sybase:Tds:localhost:30015/MYDB?ENABLE_SSL=true&SSL_TRUST_ALL_CERTS=true" -U * -P *

Enter a query:
1 > select * from 1
Msg 102, Level 15, State 181:
, Server HA005PQ, Line 1, Status 181, TranState 1:
Incorrect syntax near '1'.


c:\work\tools\database\ase.isql\target
```

The above example shows the directly quit behavior.







