## nginx concat module ##

### 指令 ###

1. __concat__ : [on | off]

    __context__ : HTTP, Server, location

    __default__ : off

    __说明__ : 模块是否执行的开关, 


2. __concat_max_files__ : number

    __context__ : HTTP, Server, location

    __default__ : 10

    __说明__ : 设定一次请求最多合并的文件数 (缺省10个)


3. __concat_unique__ : [on | off]
    
    __context__ : HTTP, Server, location

    __default__ : on

    __说明__ : 是否允许不同文件合并，比如同是合并 js/css 是非法的，设定这个选项为 off 后就允许多个文件，缺省为on

4. __concat_types__ : string

    __context__ : HTTP, Server, location

    __default__ : 
    __说明__ : 允许的content-type类型，目前只是允许 js/css


### 使用 ###

1. 为了不影响整体稳定性，有一个文件不存在的时候仍然返回200，但是导出concat_file_not_found_flag为true，可以根据flag做expires等操作
2. 方便监控增加WARN日志: `http concat judge num_file_not_found: "1"`表示一次concat请求中有一个文件没有找到

### 配置demo ###

源站对没发现文件情况配置expires设置短一些，保证上线完成后可以通过cdn过期回源来自动恢复

```

location ~* / { 
    if ($request_uri ~* /\?\?) {
        expires 30d;
    }   
            
    if ($concat_file_not_found_flag != "0") {
        expires 1m; 
    }   
        
    root /home/work/nginx/html/;
    index index.html index.htm;
    concat on; 
    concat_max_files 20; 
}

```
 
