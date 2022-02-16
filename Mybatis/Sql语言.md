## sql语言

  ![avatar](https://images0.cnblogs.com/blog/33509/201304/28234015-f1cc175bc15c439d94abf7cb1c52ab97.png)
  
  ### 1. 分组求和
    根据类型分组后，按数量求和：
        SELECT type,sum(number) as count FROM `t_wwgroup` GROUP BY type;
  
