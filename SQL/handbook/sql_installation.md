# SQLite Handbook (Windows + CSV Import)

## 0) What we're using

-   **OS:** Windows 10/11
-   **Shell:** PowerShell (or Windows Terminal)
-   **DB:** SQLite (`sqlite3` CLI)

------------------------------------------------------------------------

## 1) Install SQLite on Windows

- Go to the official site: [SQLite Download Page](https://www.sqlite.org/download.html)  
- Get the **sqlite-tools zip** (contains `sqlite3.exe`). 
- Extract to a folder `C:\Tools\sqlite`
- Add that folder to your **PATH**: -> Start -> *Edit the system environment variables* -> *Environment Variables...* -> Under *User variables* -> select **Path** -> *Edit* -> *New* -> `C:\Tools\sqlite` -> OK.

**PowerShell:**
``` powershell
sqlite3 -version
```

------------------------------------------------------------------------

## 2) Create/Open a database

**PowerShell:**
``` powershell
cd "C:\Users\Eric\Desktop\Projects\CS50x\Lecture\Lecture_7\database"
sqlite3 favoritesdb
```

-   favoritedb just a file name. You can rename it to whatever your human language is.

Create data base file in the folder

**sql:**
``` sql
.database
```

Exit any time with:

**sql:**
``` sql
.quit
```

------------------------------------------------------------------------

## 3) Import a CSV

**sql:**
``` sql
.mode csv
.import "C:/Users/Eric/Desktop/Projects/CS50x/Lecture/Lecture_7/favorites.csv" favorites
```

-   Forward slashes `/` recommended in paths.
-   `favorites` = table name.
-   To skip headers (if supported):

**sql:**
``` sql
.import --skip 1 "C:/.../favorites.csv" favorites
```

**Check:**

**sql:**
``` sql
.headers on
.mode column
SELECT COUNT(*) AS rows FROM favorites;
SELECT * FROM favorite LIMIT 10;
```

------------------------------------------------------------------------

## 4) Path & Windows quirks

-   Use `/` or `\` in paths.
-   If `.import` fails, check if table exists or if headers are included.
-   Everything defaults to TEXT unless schema is pre-created.

------------------------------------------------------------------------

## 5) GUI option

Install **DB Browser for SQLite** if you want a visual tool.

------------------------------------------------------------------------

[← Back to Index](../index.md)