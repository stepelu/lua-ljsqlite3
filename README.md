LJSQLite3
=========

Using this MIT licensed library data can be conveniently exchanged between Lua and SQLite databases. Moreover arbitrary operations can be performed on SQLite databases directly from Lua.

Code snippet example:

```lua
local sql = require "ljsqlite3"
local conn = sql.open("") -- Open an temporary in-memory database.
 
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

The LJSQLite3 library is part of the <a href="http://www.scilua.org">SciLua</a> framework which aims to offer a framework for numerical computing which combines the ease of use of scripting languages (Matlab, R, ...) with the high performance of compiled languages (C/C++, Fortran, ...). Please refer to the project's homepage for more information.
