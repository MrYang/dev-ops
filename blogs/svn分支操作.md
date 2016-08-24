### svn 分支操作

1. 创建分支   
  
  `svn copy http://svn_server/repo/trunk http://svn_server/repo/branches/branche_feature -m 'create branche'`  
2. 切换到分支开发，**注意，所有的开发都在分支上进行**
      
      `svn switch svn://ip/repo/branches/bracche_feature`
3. 从主干合并代码，**即把主干的代码合并到分支上**
	  
	  `cd branche_feature`
	  
	  `svn merge http://svn_server/repo/trunk`
	  
	  如果需要预览该刷新操作，可以使用svn mergeinfo命令，如：
	  
	  `svn mergeinfo http://svn_server/repo/trunk --show-revs eligible`
 	  
	  或使用`svn merge --dry-run`选项以获取更为详尽的信息。
4. 提交从主干合并的代码
	  
	  `svn commit -m '从主干更新的代码提交'`
5. 重复3,4步骤
6. 提交分支代码
	  
	  `svn commit -m '提交新功能'`
7. 功能开发完，主干合并分支的代码，**即把分支的代码合并到主干上**
  
  `svn merge -r 分支版本号:HEAD 分支的URL   #HEAD为当前主干上的最新版本`
   
   示例：
  
  `cd trunk`
  
  `svn merge -r 12:HEAD svn://192.168.1.177/tags/beta_2009_12_24`
  
  `cd trunk`
  
  `svn merge --reintegrate http://svn_server/xxx_repository/branches/br_feature001`
  
  分支合并到主干中完成后应当删该分支，因为在SVN中该分支已经不能进行刷新也不能合并到主干。
  
  合并版本并将合并后的结果应用到现有的分支上
  
  `svn -r 148:149 merge http://svn_server/xxx_repository/trunk`
8. 建立tags
  
  `svn copy svn://xx.com/repo/trunk svn://xx.com/repo/tags/RB-1.0 `
  
  `svn copy http://svn_server/xxx_repository/trunk http://svn_server/xxx_repository/tags/release-1.0 -m "1.0 released"`
  
  删除分支或tags
  
  `svn rm http://svn_server/xxx_repository/branches/br_feature001`
  
  `svn rm http://svn_server/xxx_repository/tags/release-1.0`