## sql语言

  ![avatar](https://images0.cnblogs.com/blog/33509/201304/28234015-f1cc175bc15c439d94abf7cb1c52ab97.png)
  
  类型-type; 数量-number; 摘要-remark; 表名：t_wwgroup
  
  ### 1. 分组求和
    根据类型分组后，按数量求和：
        SELECT type,sum(number) as count FROM `t_wwgroup` GROUP BY type;
        
    返回结果：
    ![avatar](https://images0.cnblogs.com/blog/33509/201304/28234054-ff92ae14bfe74da98c4deb8d7c78f2f8.png)
        
  
  
