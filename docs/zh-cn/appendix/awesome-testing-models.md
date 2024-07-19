### 模型1

【用途】

用于快速测试存储性能（一般对4K和4M两个文件粒度进行读写）

【模型】

```
<?xml version="1.0" encoding="UTF-8" ?>
<workload name="s3 performance test" description="for s3">

  <storage type="sio" config="endpoint=http://x.x.x.x:port;accesskey=xx;secretkey=xx;path_style_access=true;timeout=60000" />
 
  <workflow>
    <workstage name="create bucket">
      <work type="init" workers="1" config="cprefix=perftest-;containers=r(1,1)" />
    </workstage>
	
	
	<workstage name="test 4k write">
	  <work name="w_1" workers="50" totalOps="100000" driver="driver1">
        <operation type="write" ratio="100" config="cprefix=perftest-;containers=c(1);oprefix=w1-4kib-;objects=s(1,100000);sizes=c(4)KiB" />
      </work>
	  
	  <work name="w_2" workers="50" totalOps="100000" driver="driver2">
        <operation type="write" ratio="100" config="cprefix=perftest-;containers=c(1);oprefix=w2-4kib-;objects=s(1,100000);sizes=c(4)KiB" />
      </work>
	</workstage>

	
	<workstage name="test 4m write">
	  <work name="w_1" workers="50" totalOps="50000" driver="driver1">
        <operation type="write" ratio="100" config="cprefix=perftest-;containers=c(1);oprefix=w1-4mib-;objects=s(1,100000);sizes=c(4)MiB" />
      </work>
	  
	  <work name="w_2" workers="50" totalOps="50000" driver="driver2">
        <operation type="write" ratio="100" config="cprefix=perftest-;containers=c(1);oprefix=w2-4mib-;objects=s(1,100000);sizes=c(4)MiB" />
      </work>
	</workstage>
	
	
	<workstage name="test 4k read">
	  <work name="w_1" workers="50" totalOps="100000" driver="driver1">
        <operation type="read" ratio="100" config="cprefix=perftest-;containers=c(1);oprefix=w1-4kib-;objects=s(1,100000)" />
      </work>
	  
	  <work name="w_2" workers="50" totalOps="100000" driver="driver2">
        <operation type="read" ratio="100" config="cprefix=perftest-;containers=c(1);oprefix=w2-4kib-;objects=s(1,100000)" />
      </work>
	</workstage>

	
	<workstage name="test 4m read">
	  <work name="w_1" workers="50" totalOps="50000" driver="driver1">
        <operation type="read" ratio="100" config="cprefix=perftest-;containers=c(1);oprefix=w1-4mib-;objects=s(1,100000)" />
      </work>
	  
	  <work name="w_2" workers="50" totalOps="50000" driver="driver2">
        <operation type="read" ratio="100" config="cprefix=perftest-;containers=c(1);oprefix=w2-4mib-;objects=s(1,100000)" />
      </work>
	</workstage>
	
  </workflow>
</workload>
```

【注意】

1. totalOps根据性能稳定情况来实时调整
2. 单个workstage的运行时长建议超过15分钟
3. 如果服务器网卡做bond，建议起多个driver，并测试过程中查看服务器上的连接数是否均分

### 模型2

