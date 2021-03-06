# deploy-functiongraph-action
部署serverless函数到华为云函数工作流，支持类型为obs,jar,zip压缩包,单个函数文件,或者目录
目前支持linux系统和苹果系统，windows系统暂不支持

## **前置工作**
### 1.云服务资源使用
函数工作流（[FunctionGraph](https://support.huaweicloud.com/functiongraph/index.html)）是华为云提供的一款无服务器（Serverless）计算服务，无服务器计算是一种托管服务，服务提供商会实时为你分配充足的资源，而不需要预留专用的服务器或容量，真正按实际使用付费。
需要提前学习函数工作流服务的基本操作，并创建好自己的函数

### 2.配置获取方式
(1).AK/SK/projectid获取方式请参考 https://support.huaweicloud.com/apm_faq/apm_03_0001.html
(2).endpoint获取方式请参考 https://developer.huaweicloud.com/endpoint?FunctionGraph
(3).函数urn获取方式:选中需要更新的函数进入函数详情，右上角可以看到urn参数

## **参数说明:**
ak:华为云账号的AK字符串，必填
sk:华为云账号的SK字符串，必填
endpoint:函数服务的endpoint，必填
project_id:华为云账号的project_id
function_urn:函数的urn，必填
function_codetype:函数更新方式,目前支持obs,zip,file,dir,jar五中
function_file:函数文件的路径，如果是obs，请填写该文件在OBS上的路径，如果为其他方式，请填写文件或者目录在本地的绝对路径

## **使用样例:**
deploy-functiongraph-action 在github workflow 上的使用样例:
### **参数说明:**
需要在项目的setting--Secret--Actions下添加 AK,SK,PROJECTID,FUNCTIONAME四个参数
### 1、OBS方式
如果函数文件比较大(超过50M),请先将文件上传到OBS，然后将该文件在OBS中的路径配置到function_file上,function_codetype填写为obs
```yaml
 name: deploy serverless function to huaweicloud functiongraph by obs
  uses: huaweicloud/deploy-functiongraph-action@v1.0
  with:
    ak: ${{ secrets.AK }}
    sk: ${{ secrets.SK }}
    endpoint: https://functiongraph.cn-north-4.myhuaweicloud.com
    project_id: ${{ secrets.PROJECTID }}
    function_urn: urn:fss:cn-north-4:${{ secrets.PROJECTID }}:function:default:${{ secrets.FUNCTIONNAME}}:latest
    function_codetype: obs
    function_file: "https://huaweihdnbucket.obs.cn-north-4.myhuaweicloud.com/function/publishmarket/index_obs.zip
```
### 2、java jar包方式
如果函数使用java开发，可以将java工程打包为jar,function_codetype填写为jar,将jar包在本地的路径填写到function_file上，样例如下
```yaml
name: deploy jar to huaweicloud functiongraph
  uses: huaweicloud/deploy-functiongraph-action@v1.0
  with:
    ak: ${{ secrets.AK }}
    sk: ${{ secrets.SK }}
    endpoint: https://functiongraph.cn-north-4.myhuaweicloud.com
    project_id: ${{ secrets.PROJECTID }}
    function_urn: urn:fss:cn-north-4:${{ secrets.PROJECTID }}:function:default:${{ secrets.FUNCTIONNAME}}:latest
    function_codetype: jar
    function_file: /Users/a/Downloads/demoapp.jar
 ```   
### 3、单个函数文件
如果函数为单个文件，可以直接上传改单个函数文件，function_codetype填写为file,将函数文件在本地的路径填写到function_file上，样例如下
```yaml
- name: deploy single function file to huaweicloud functiongraph
  uses: huaweicloud/deploy-functiongraph-action@v1.0
  with:
    ak: ${{ secrets.AK }}
    sk: ${{ secrets.SK }}
    endpoint: https://functiongraph.cn-north-4.myhuaweicloud.com
    project_id: ${{ secrets.PROJECTID }}
    function_urn: urn:fss:cn-north-4:${{ secrets.PROJECTID }}:function:default:${{ secrets.FUNCTIONNAME}}:latest
    function_codetype: file
    function_file: /Users/a/opensource/resource/index.py
 ```   
 ### 4、函数目录
 如果函数文件比较多，可以将函数都集中到一个目录下，将整个目录作为函数上传，function_codetype填写为dir,将函数目录在本地的路径填写到function_file上，样例如下
```yaml
- name: deploy single function file to huaweicloud functiongraph
  uses: huaweicloud/deploy-functiongraph-action@v1.0
  with:
    ak: ${{ secrets.AK }}
    sk: ${{ secrets.SK }}
    endpoint: https://functiongraph.cn-north-4.myhuaweicloud.com
    project_id: ${{ secrets.PROJECTID }}
    function_urn: urn:fss:cn-north-4:${{ secrets.PROJECTID }}:function:default:${{ secrets.FUNCTIONNAME}}:latest
    function_codetype: dir
    function_file: /Users/a/opensource/resource/functions
 ```  
### 5、zip压缩包
如果函数文件比较多，也可以将函数打包成zip，将zip文件作为函数上传，function_codetype填写为zip,将函数目录在本地的路径填写到function_file上，样例如下
```yaml
- name: deploy single function file to huaweicloud functiongraph
  uses: huaweicloud/deploy-functiongraph-action@v1.0
  with:
    ak: ${{ secrets.AK }}
    sk: ${{ secrets.SK }}
    endpoint: https://functiongraph.cn-north-4.myhuaweicloud.com
    project_id: ${{ secrets.PROJECTID }}
    function_urn: urn:fss:cn-north-4:${{ secrets.PROJECTID }}:function:default:${{ secrets.FUNCTIONNAME}}:latest
    function_codetype: zip
    function_file: /Users/a/opensource/resource/index_py.zip
 ```