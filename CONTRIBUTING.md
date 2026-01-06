# 贡献指南

感谢您对这个项目的兴趣！这个项目是基于 [sulv-hugo-papermod](https://github.com/xyming108/sulv-hugo-papermod) 的二次开发版本，原始项目基于 MIT 许可证。

## 如何使用

1. 克隆此仓库：
   ```
   git clone https://github.com/yourusername/your-repo-name.git
   ```

2. 进入目录并初始化子模块：
   ```
   cd your-repo-name
   git submodule update --init
   ```

3. 安装 [Hugo](https://gohugo.io/) (Extended 版本)

4. 启动本地服务器：
   ```
   hugo server -D
   ```

5. 在浏览器中访问 `http://localhost:1313`

## 个性化配置

1. 修改 [config.yml](./config.yml) 中的站点信息
2. 更换 [content/about.md](./content/about.md) 中的关于页面内容
3. 更新 [content/_index.md](./content/_index.md) 中的首页信息
4. 替换 [assets/img/](./assets/img/) 目录下的图像文件

## 许可证

此项目遵循原始项目的 MIT 许可证。请保留原始作者的署名信息。

## 问题和支持

如果遇到问题，请提交 Issue。欢迎提交 Pull Request！