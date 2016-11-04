# Name
lua-resty-filecache is an file write module with cache


#Synopsis


```
    lua_package_path "/path/to/lua-resty-filecache/lib/?.lua;;";
    init_worker_by_lua_block {
        -- Import the module
        local filecache = require("resty.filecache")
        -- Start timer, regularly clean up the handle.
        filecache.start_cleanup_timer(600) --600 seconds no write operation, close the file handle.
    }
    
    server {
        listen 199 default; 
        location /writelog {
            content_by_lua_block {
                local time = ngx.localtime()
                local date = string.sub(time,1,4) .. string.sub(time,6,7) .. string.sub(time, 9,10)
                local hour = string.sub(time, 12,13) 
                local filename = string.format("/tmp/logs/myserver/%s/%s.log", date, hour)
                
                local filecache = require("resty.filecache")
                ngx.req.read_body()
                local log = ngx.req.get_body_data()
                log = log .. "\n"
                local err = filecache.write(filename, log)
                if err then 
                    ngx.log(ngx.ERR, "write log to [", filename, "] failed! err:", tostring(err))
                end
            }
        }
    }
```

# Test Write Log
```
curl http://127.0.0.1:199/writelog -d "testlog"  
```
# View the log
```
tail -f /tmp/logs/myserver/$yyyymmdd/$hh.log
```

#Author
Xiaojie Liu. jie123108@163.com
