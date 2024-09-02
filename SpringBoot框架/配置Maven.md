

### 配置Maven

- 找到.m2文件夹  ,检查里面是否有 *settings.xml配置文件*
  
  ![](配置Maven：1.png)  
  
![](配置Maven：2.png)  


- 如果.m2文件夹下面没有setting.xml配置文件,则需要从苍老师文档服务器下载此文件解压出settings.xml文件保存到.m2文件夹下面   

  ![](配置Maven：3.png)  


### 创建SpringBoot工程  
  
1. 创建Spring Initalizer工程 , 修改URL  
  
   ![](配置Maven：4.png)  
  
2. 勾选 Web->Spring Web    然后点击finish
  
3. 创建完工程后 检查*Build*里面是否出现*绿色对勾*, 如果有红色信息代表工程创建出错,此时点击*Maven里面的刷新Maven*  
  
   ![](配置Maven：5.png)  
  
- 如果刷新完Maven后还是不行, 则 *删除.m2文件夹下的repository文件夹* 然后再次刷新maven, 一般可以解决, 如果*仍然没有解决*则*创建一个新工程 重新测试*, 如果还是不行, 则联系项目经理 现场 或远程 一对一解决