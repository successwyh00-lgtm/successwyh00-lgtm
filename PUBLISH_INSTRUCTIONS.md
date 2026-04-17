目标：发布 GitHub Profile README 到账号 successwyh00-lgtm

GitHub 个人主页 README 需要创建一个与用户名同名的公开仓库：
- 仓库名：successwyh00-lgtm

当前已准备好的文件：
- README.md
- PROFILE_SHORT.md
- CLIENT_INTAKE.md

若提供 GitHub Token，可按以下方式自动发布：

1. 配置 git 凭据
   git config --global credential.helper store

2. 创建远程仓库（需 API token）
   POST https://api.github.com/user/repos
   body:
   {
     "name": "successwyh00-lgtm",
     "description": "GitHub profile and service intro",
     "private": false,
     "auto_init": false
   }

3. 推送本地目录
   cd /home/wuyahao/successwyh00-lgtm
   git init
   git add README.md PROFILE_SHORT.md CLIENT_INTAKE.md
   git commit -m "docs: add GitHub profile README and service info"
   git branch -M main
   git remote add origin https://github.com/successwyh00-lgtm/successwyh00-lgtm.git
   git push -u origin main

需要的最小权限：
- GitHub Personal Access Token
- scope: repo（classic token 即可）

Token 可到这里创建：
https://github.com/settings/tokens
