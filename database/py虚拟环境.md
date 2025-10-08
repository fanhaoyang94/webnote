## py 虚拟环境

1. 安装目录

   D:/
   ├── Python39/
   │   └── python.exe
   ├── Scripts/
   │   └── pip.exe
   └── Lib/                   # python 内置的所有的模块以及 sitepackages 文件夹
       └── site-packages/     # 通过 pip 安装的所有的第三方包
           ├── random
           ├── re 
           ├── requests v1.5 
           ├── bs4 
           └── xpath

2. 创建虚拟环境后会 copy 一份环境

   eg：项目 A
   D:/
   └── A/
       └── .venv/
            ├── Scripts/                        # Scripts/ (Windows) 或 bin/ (Linux/Unix)
            │   └── pip.exe
            └── Lib/                            # 依赖系统解释器
                └── python3.9/                  # 虚拟环境依赖的所有的第三方包
                    └── site-packages/          # 通过 pip 安装的所有的第三方包
                        ├── random
                        ├── re 
                        ├── requests v1.5 
                        ├── bs4 
                        └── xpath

当项目结束后执行

```
# 执行过后会将当前虚拟环境依赖的所有的第三方包即版本信息写入requirements.txt
pip freeze > requirements.txt
```

创建项目的建议：
在项目的根目录创建一个虚拟环境文件夹 `.venv ` 此目录中不要做任何操作
