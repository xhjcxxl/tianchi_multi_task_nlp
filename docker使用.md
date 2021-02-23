# 中文预训练模型泛化能力挑战赛-docker提交流程

## 前期工作

1.下载docker，并安装好，能够正常运行出现界面就可以了，之后就不要动了

2.阿里云的镜像仓库开通之后，就可以了，就不要动了

3.在本地代码跑通之后，得到预测结果之后，找到submission文件夹，把需要提交的结果打包成result.zip(不要用天池的submit.zip，那个会识别不了)

## 提交流程

1.使用power shell打开，然后找到submission（有result.zip那个文件夹）

2.登录阿里云仓库：`docker login --username=用户名 registry.cn-shenzhen.aliyuncs.com`

3.在本地创建镜像：`docker build -t registry.cn-shanghai.aliyuncs.com/tianchi_xxl/tianchi_competitions:1.0 .`

（我的理解是，从服务器中拉取一个镜像，在本地创建, `registry.cn-shanghai.aliyuncs.com/tianchi_xxl/tianchi_competitions`换成自己的阿里云仓库公网地址，1.0是版本号，自己随意定制）

    1.前面几次失败了
        => [internal] load build definition from Dockerfile                                 0.1s
        => => transferring dockerfile: 433B                                                 0.0s
        => [internal] load .dockerignore                                                    0.1s
        => => transferring context: 2B                                                      0.0s
        => ERROR [internal] load metadata for registry.cn-shanghai.aliyuncs.com/tcc-publi  12.9s
        => [auth] tcc-public/python:pull token for registry.cn-shanghai.aliyuncs.com        0.0s
        ------
        > [internal] load metadata for registry.cn-shanghai.aliyuncs.com/tcc-public/python:3:
        ------
        failed to solve with frontend dockerfile.v0: failed to create LLB definition: failed to copy: httpReadSeeker: failed open: failed to do request: Get http://aliregistry-cn-shanghai.oss-cn-shanghai.aliyuncs.com/docker/registry/v2/blobs/sha256/a4/a4cc999cf2aaafbe0b934b74be5a55cb3b8bd9349856131bea3579428b4155bf/data?Expires=1613793425&OSSAccessKeyId=LTAI4FyN99sZuwGQsaDkPxbv&Signature=CtfVq0ChowYJxQ2BhReuocl8HaY%3D&x-oss-traffic-limit=123493535: dial tcp: lookup aliregistry-cn-shanghai.oss-cn-shanghai.aliyuncs.com on 192.168.65.1:53: read udp 192.168.65.3:58624->192.168.65.1:53: i/o timeout

    2.失败了退出这个目录，然后重新进来，再一次拉取镜像；
        docker\tianchi_multi_task_nlp\submission> docker build -t registry.cn-shanghai.aliyuncs.com/tianchi_xxl/tianchi_competitions:1.0 .
        [+] Building 97.0s (7/7) FINISHED
        => [internal] load build definition from 
        ...
        => => naming to registry.cn-shanghai.aliyuncs.com/tianchi_xxl/tianchi_competitions:1.0                                                                                                                       0.0s

4.镜像创建成功之后，docker客户端中的image里面就会有这个镜像

8.标记镜像,然后推送镜像到阿里云仓库

（66cbaa6a3a54 是docker客户端中image中的 IMAGE ID, registry.cn-shanghai.aliyuncs.com/tianchi_xxl/tianchi_competitions:1.0 是先前创建的镜像，为什么会有1.0，1.1是因为我创建了两次镜像）

    docker tag 66cbaa6a3a54 registry.cn-shanghai.aliyuncs.com/tianchi_xxl/tianchi_competitions:1.1
    docker push registry.cn-shanghai.aliyuncs.com/tianchi_xxl/tianchi_competitions:1.1

9.成功之后，阿里云上的镜像版本中就会出现推送的镜像 [地址](https://cr.console.aliyun.com/repository/cn-shanghai/tianchi_xxl/tianchi_competitions/images)

10.然后去比赛官网进行提交就可以了：

    镜像地址：registry.cn-shanghai.aliyuncs.com/tianchi_xxl/tianchi_competitions:1.1
    用户名：用户名
    密码：密码

11.点击提交

## 注意

每一次提交都需要重新构建镜像，然后进行推送，如果submission文件夹没有修改，那么构建出来的镜像是没有变化的，而且还跟以前的镜像保持相同的tag，所以记得更新result的结果，然后进行构建镜像才行

## 改进优化

1.使用对抗(58->61)

2.使用attention(58->63)

- attention + adv(还是61,说明在attention之后进行adv效果并没有啥提升)

3.使用Bert动态融合

4.使用伪标签

5.使用EDA

6.使用模型融合
