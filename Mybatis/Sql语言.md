## sql语言

  ![avatar](https://images0.cnblogs.com/blog/33509/201304/28234015-f1cc175bc15c439d94abf7cb1c52ab97.png)
  
  类型-type; 数量-number; 摘要-remark; 表名：t_wwgroup
  
  ### 1. 分组求和
    根据类型分组后，按数量求和：
        SELECT type,sum(number) as count FROM `t_wwgroup` GROUP BY type;
        
   返回结果：
   ![avatar](https://images0.cnblogs.com/blog/33509/201304/28234054-ff92ae14bfe74da98c4deb8d7c78f2f8.png)
   
  ### 2. Group By 和 Order By
    根据类型分组后，按数量求和并根据求和后的数排序：
        SELECT type ,sum(number) as count From t_wwgroup GROUP BY type ORDER BY sum(number)  ASC；
        
   返回结果：
   ![avatar](https://images2015.cnblogs.com/blog/33509/201605/33509-20160507160034812-1769640827.png)
   
  ### 3. Group By中Select指定的字段限制
    在select指定的字段要么就要包含在Group By语句的后面，作为分组的依据；要么就要被包含在聚合函数中。     
        SELECT type ,sum(number) as count from  t_wwgroup GROUP BY type ORDER BY type DESC
        错误示范：
        SELECT type ,sum(number) as count, **remark** from  t_wwgroup GROUP BY type ORDER BY type DESC
        
  ### 4. 多列分组 Group By All
    根据类型和摘要分组后求和
        SELECT type ,remark, sum(number) as count from  t_wwgroup GROUP BY type , remark ORDER BY type DESC
        
   “多列分组”实际上就是就是按照多列（类别+摘要）合并后的值进行分组，示例4中可以看到“a, a2001, 13”为“a, a2001, 11”和“a, a2001, 2”两条记录的合并。
   
   返回结果：
   ![avatar](https://images0.cnblogs.com/blog/33509/201304/28234156-7fb9d1f258ad4faaa26decfddc3723fc.png)
   
 ### 5. Group By与聚合函数
   3中提到group by语句中select指定的字段必须是“分组依据字段”，其他字段若想出现在select中则必须包含在聚合函数中，常见的聚合函数如下表：
   
   sum(列名)	    求和	　　　
   
   max(列名)	    最大值	　　　
   
   min(列名)	    最小值	　　　　
   
   avg(列名)	    平均值	　　　　
   
   first(列名)	  第一条记录	仅Access支持
   
   last(列名)	    最后一条记录	仅Access支持
   
   count(列名)	  统计记录数	注意和count(*)的区别
   
   #### 5.1 分组后求各组平均值
        select type, avg(number) AS numAvg from A group by type; 
  
