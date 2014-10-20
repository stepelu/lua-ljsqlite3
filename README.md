LJSQLite3
=========

SQLite databases require no preliminary setup (installation and configuration or a database server), offer competitive performance and are portable as each database is fully contained in a single file. Additionally the command-line shell can be used to facilitate common tasks. Thanks to all these features SQLite databases are ideal to store and manipulate scientific data. We refer to http://sqlite.org for more information on SQLite.

Using the MIT licensed LJSQLite3 library data can be conveniently exchanged between Lua and SQLite databases. Moreover arbitrary operations can be performed on SQLite databases directly from Lua.

The following example presents the main features of this library:
```lua
local sql = require "ljsqlite3"
local conn = sql.open("") -- Open a temporary in-memory database.
  
-- Execute SQL commands separated by the ';' character:
conn:exec[[
CREATE TABLE t(id TEXT, num REAL);
INSERT INTO t VALUES('myid1', 200);
]]
  
-- Prepared statements are supported:
local stmt = conn:prepare("INSERT INTO t VALUES(?, ?)")
for i=2,4 do
  stmt:reset():bind('myid'..i, 200*i):step()
end
  
-- Command-line shell feature which here prints all records:
conn "SELECT * FROM t"
--> id    num
--> myid1 200
--> myid2 400
--> myid3 600
--> myid4 800
  
local t = conn:exec("SELECT * FROM t") -- Records are by column.
-- Access to columns via column numbers or names:
assert(t[1] == t.id)
-- Nested indexing corresponds to the record number:
assert(t[1][3] == 'myid3')
  
-- Convenience function returns multiple values for one record:
local id, num = conn:rowexec("SELECT * FROM t WHERE id=='myid3'")
print(id, num) --> myid3 600
 
-- Custom scalar function definition, aggregates supported as well.
conn:setscalar("MYFUN", function(x) return x/100 end)
conn "SELECT MYFUN(num) FROM t"
--> MYFUN(num)
--> 2
--> 4
--> 6
--> 8
 
conn:close() -- Close stmt as well.
```

Info: http://scilua.org/ljsqlite3.html
