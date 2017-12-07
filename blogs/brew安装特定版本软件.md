
### 举个例子，假设安装filebeat5.3.2

1. `git clone https://github.com/Homebrew/homebrew-core` 到本地
2. 到github上找到filebeat.rb 的历史记录，找到5.3.2版本的commit，为`cb8474bbabf00aafbc209e860710b6672fd810e1`
3. 到本地仓库 `git checkout cb8474bbabf00aafbc209e860710b6672fd810e1`
4. 执行命令 `brew install /gitpath/homebrew-core/Formula/filebeat.rb`