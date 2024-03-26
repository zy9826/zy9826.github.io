# Nginx常用操作


记录Nginx操作
&lt;!--more--&gt;


### 查看访问最频繁的前100个IP
```awk
awk &#39;{print $1}&#39; access.log | sort -n |uniq -c | sort -rn | head -n 100
```


---

> 作者: Yu  
> URL: https://blog.zy9826.site/posts/9190390222c2/  

