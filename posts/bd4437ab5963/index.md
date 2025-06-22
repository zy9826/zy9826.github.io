# 常用指令记录


<!--more-->

## hugo
- hugo创建新文章
  ``` bash
  hugo new posts/xxx.md
  hugo new content posts/xxx.md
  ```
- hugo启动项目
  ``` bash
  hugo server -D 
  # build草稿文章
  hugo server --buildDrafts 
  # 实时预览
  hugo server -D --disableFastRender 
  ```
- md增加全角缩进`&emsp;`, 半角缩进`&ensp;`

## iptables
- 查看INPUTt表: `iptables -L INPUT -n --line-numbers`
- 追加到INPUT表: `iptables -A INPUT -s 42.192.6.197 -j DROP`

## git
- git远程删除分支`git push origin --delete remotebranch` 和 `git push origin :remotebranch`

less用法   
less可以使用鼠标滚轮翻页，-N显示行号 -e显示百分比
| **less** | **向上翻页** | **向下翻页** |
| -------- | ------------ | ------------ |
| **一页** | k            | j 空格       |
| **半页** | u (up)       | d (down)     |
| **一行** | b            | f 回车       |


---

> 作者: Yu  
> URL: https://blog.zy9826.site/posts/bd4437ab5963/  

