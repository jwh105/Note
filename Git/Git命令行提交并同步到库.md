# Git命令行提交并同步到库

1. 打开项目目录，shift+鼠标右键，打开powershell

2. 提交到本地git仓库

   ``` 
   git add -A
   git commit -m '描述文字'
   ```

   *git add 命令参数：*

   ```
   git add . #将目录下所有新增和修改存至缓存区，但不包括删除
   git add -u #将目录下所有修改和删除存至缓存区，但不包扣新增
   git add -A #缓存所有改动
   ```

3. 同步GitHub远程仓库

   ```
   git push origin master
   ```



