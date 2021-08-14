## git
### git有哪些主流的工作流
#### 1.集中式工作流
类似于集中式版本控制，以中央仓库作为项目所有修改的单点实体，在git中我们使用master
分支作为主干分支，所有修改都提交到master上，在集中式工作流中我们只使用master。
#### 2.功能分支工作流
不在master分支上做开发，每个功能模块基于一个专门的分支。功能开发促成了Pull Request 
工作流，每个PR让技术负责人review代码，检查无误后merge到master分支上。
#### 3.Git flow工作流
远程仓库作为开发者的交互中心，同时围绕master、release、develop、feature
feature是统称不止这一个）四种分支协作，完成多环境、多任务的代码管理。
#### 4.Github工作流