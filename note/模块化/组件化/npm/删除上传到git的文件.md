学习React的时候，不小心把node_modules文件夹和dist文件夹也一起git push到了远程仓库，在网上搜了不少资料，终于得出最佳的解决方案。现在记录下来，希望能帮到更多人及时解决该问题。

1.先在.gitignore文件上编写一下代码

node_modules/
dist/
2.在命令行进入仓库目录，删除github仓库上.gitignore上新加的选项

git rm -r --cached .
3.然后重新添加要提交的选项

git add .
4.接着commit，简要说明一下commit的内容

git commit -m 'remove node_modules and dist'
5.最后在git push 到远程仓库上就可以了。
