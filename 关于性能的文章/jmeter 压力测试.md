## jmeter 压力测试

1. 新建测试计划->线程组
2. 设置线程组线程数，启动时间，循环次数
3. 添加Sampler->http 请求，设置请求地址，参数
4. 添加想要的监听器

保存为test.jmx 文件

执行命令`jmeter -n -t test.jmx -l report.jtl -e -o report`


把测试结果输入到reprot.jtl， report 文件夹中，report 文件夹存网页形式的压力测试结果

手动编辑 test.jmx 文件，修改线程数，循环次数，测试不同压力下系统的吞吐量，响应时间，tps等