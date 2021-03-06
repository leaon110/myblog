android 下面的基线升级介绍   马哥的淘宝店:https://shop592330910.taobao.com/

基线升级一般有2中方式，
* git rebase的方式
* git cherry-pick 的方式(这个效果和git rebase 类似)
* git merge的方式


不同的公司可能采取不同的方式。

两种各有优缺点  
git rebase的方式 其中的git log历史会是一条直线走下去的，不会有分叉。马哥的淘宝店:https://shop592330910.taobao.com/   
git merge的方式 其中的git log 历史中会有分叉，有自己公司的提交，和其他公司(比如高通)的提交相互穿插的。马哥的淘宝店:https://shop592330910.taobao.com/      


git rebase方式 一般最后会采用强制 git push -f的方式推送到服务器上。之前的历史会冲掉，所以一般会先做个备份分支。   
git merge 来说安全一些，不会强制git push到服务器，冲掉之前的git log历史。merge一般是不会改变之前的历史提交的。（rebase是会改变之前历史的）

git rebase 会稍微的复杂一些。
git merge  相对于来说简单一些。（一般我们看很多的开源项目都比较喜欢git merge的方式，例如kernel，我们通过git log能看到很多 Merge 打头的提交）



#基线升级 git rebase

git rebase 一般步骤介绍
```
第一步

repo init  -m  branch_start_point.xml && repo sync           #这个是当时创建分支保存的那个点。 马哥的淘宝店:https://shop592330910.taobao.com/

```
 

```
第二步

创建标签 repo forall -c "git tag start_point"          #这个是当时创建分支保存的那个点。  马哥的淘宝店:https://shop592330910.taobao.com/

```
 

 

```
第三步

repo init  -m   dev.xml  && repo sync        # 这个假设是我们的开发分支。    马哥的淘宝店:https://shop592330910.taobao.com/

```
 

```
第四步 

创建标签  repo forall -c "git tag end_point"  #这个是开发分支最新的那个点。   马哥的淘宝店:https://shop592330910.taobao.com/




```
 

```
第五步

repo init –m   qcom_base.xml   &&  repo sync

基于这个第五步这个点上 git checkout 出来一个新的分支，new                马哥的淘宝店:https://shop592330910.taobao.com/

```


```

第六步
git rebase  --onto  new   start_point   end_point 

repo forall –c  “  git rebase  --onto new   start_point   end_point     ”    马哥的淘宝店:https://shop592330910.taobao.com/



```


举例 A - B - C - D 这几个，A之前那个是我们的start point，D是我们的end point。
A之前的是高通公司的上次基线。A之前的就都是高通公司的提交了。A之后的（包括A）都是自己公司的提交。  马哥的淘宝店:https://shop592330910.taobao.com/

之后高通基线释放了一次更新。  
假设              
这个是之前一次基线的我们的开发分支的提交历史情况     
qcom1  - qcom2  - qcom3 - qcom 4 - A - B - C - D #这样几个提交   

这次高通升级了，他们在qcom4之后多几个提交的。     
qcom1  - qcom2  - qcom3 - qcom 4 - qcom5 - qcom6 -qcom 7      


我通过git rebase之后 就是说 要把我们公司自己的提交 A - B - C - D 这四个放到 qcom7 后面连起来     马哥的淘宝店:https://shop592330910.taobao.com/    
qcom1  - qcom2  - qcom3 - qcom 4 - qcom5 - qcom6 -qcom 7 -  A - B - C - D     




具体拿个高通的modem仓库升级举例    
下面这个历史是高通上次 r00446.1 基线的历史。     
```
* 5d9c028721b - Commit label r00446.1 - Post-CS6 0.0.446.1                                       — QC Publisher (tag: start)
* 3a7938edc31 - Commit label r004361.1 - Post-CS5 0.0.4361.1                                     — QC Publisher - (12 days ago)
* 57ab46a6d39 - Commit label r00425.1 - Post-CS4 0.0.425.1                                       — QC Publisher - (12 days ago)
* 4969cbee834 - Commit label r00418.2 - Post-CS3 0.0.418.2                                       — QC Publisher - (12 days ago)
* cc98e139704 - Commit label r004032.1 - Post-CS2 0.0.4032.1                                     — QC Publisher - (12 days ago)
* a3e87e4a191 - Commit label r004031.1 - Post-CS2 0.0.4031.1                                     — QC Publisher - (12 days ago)
* 21394e21a15 - Commit label r00391.1 - Post-CS 0.0.391.1                                        — QC Publisher - (12 days ago)
* 285c683e3de - Commit label r00375.10a - CS 0.0.375.10a                                         — QC Publisher - (12 days ago)
* f8a82bb4771 - Commit label r00355.2 - Post-CS4 0.0.355.2                                       — QC Publisher - (12 days ago)
* 2eff551636f - Commit label r00333.1a - Pre-CS3 0.0.333.1a                                      — QC Publisher - (12 days ago)
* e8e2ffec9aa - Commit label r00322.2 - Pre-CS 0.0.322.2                                         — QC Publisher - (12 days ago)
* abb243c22ca - Commit label r00304.1 - Pre-CS 0.0.304.1                                         — QC Publisher - (12 days ago)

```
我们需要在5d9c028721b做个start的标记，一般可以采用git tag start 5d9c028721b的方式。  马哥的淘宝店:https://shop592330910.taobao.com/    



再看我们的开发分支的历史      马哥的淘宝店:https://shop592330910.taobao.com/    
```

* b299ad990db - [SHA-1356][CSP]Fix some modem stats free                                         — 研发某某人 (tag: end, shgit/dev) - (2 days ago)
* 19e07ef237b - [SHA-1356][CSP]Judge lte sig strength by rsrp instead of rssi                    — 研发某某人 - (2 days ago)
* 1c68bd229dc - [SHA-1356][CSP]Make call detail info persist                                     — 研发某某人 - (3 days ago)
* 9ce5e6e71ac - [SHAR-3222][CSP] Modem: Fix Fdd sig drop when tx off                             — 研发某某人 - (4 days ago)
* 1094b5cccea - [SHAR-3506][CSP] Modem: CA config lead crash                                     — 研发某某人 - (4 days ago)
* 82d22ff8f0e - [SHAR-28][CSP] Modem: Modify Gsm static NV                                       — 研发某某人 - (7 days ago)
* b13744a6313 - [SHAR-3703][CSP] set PS supplementary service domain preference to 1             — 研发某某人 - (6 days ago)
* d20f9dc03cd - [SHAR-1341][csp] modify gsm1800 lte b3 tds b39 and lte b39 qm13126 port config   — 研发某某人 - (3 weeks ago)
* 0d8a1976514 - [SHAR-28][CSP] Modem: remove gsm fbrx and modify gsm static nv                   — 研发某某人 - (2 weeks ago)
* 3e9a3ec2928 - [SHAR-32][CSP] Modem: Modify GSM PA value                                        — 研发某某人 - (4 weeks ago)
中间省略一大部分提交历史
* 97a7fe15ad0 - [SHAR-32][CSP] Modem: RF ca bring up,update rx2 rx3 and gsm wtr port             — 研发某某人 - (4 months ago)
* c7042b70fd2 - [SHAR-37][csp]modem : adjust antenna switch band configuration                   — 研发某某人 - (4 months ago)
* 79a4026acce - [SHAR-32][CSP] Modem: RF ca bring up,delete rx2,rx3                              — 研发某某人 - (5 months ago)
* ca009c17f44 - [SHAR-37][csp]modem: rfc antenna initial settings                                — 研发某某人 - (5 months ago)
* 9cce83227bc - [SHAR-32][CSP] Modem: RF ca bring up                                             — 研发某某人 - (5 months ago)
* 124691dd56a - [SHAR-28][CSP] Modem: RF Bring up code                                           — 研发某某人 - (5 months ago)
* fd27c311a84 - [SHAR-3][AMSS] fix modem build env                                               — 研发某某人 - (5 months ago)
* 5b07995a297 - [SHAR-4][csp] initial mbn configuration and build scripts                        — 研发某某人 - (5 months ago)
* c6ade72c576 - [SHAR-3][AMSS] set modem build env                                               — 研发某某人 - (5 months ago)
* 5d9c028721b - Commit label r00446.1 - Post-CS6 0.0.446.1                                       — QC Publisher (tag: start) - (10 days ago)
* 3a7938edc31 - Commit label r004361.1 - Post-CS5 0.0.4361.1                                     — QC Publisher - (12 days ago)
* 57ab46a6d39 - Commit label r00425.1 - Post-CS4 0.0.425.1                                       — QC Publisher - (12 days ago)
* 4969cbee834 - Commit label r00418.2 - Post-CS3 0.0.418.2                                       — QC Publisher - (12 days ago)
* cc98e139704 - Commit label r004032.1 - Post-CS2 0.0.4032.1                                     — QC Publisher - (12 days ago)
* a3e87e4a191 - Commit label r004031.1 - Post-CS2 0.0.4031.1                                     — QC Publisher - (12 days ago)
* 21394e21a15 - Commit label r00391.1 - Post-CS 0.0.391.1                                        — QC Publisher - (12 days ago)
* 285c683e3de - Commit label r00375.10a - CS 0.0.375.10a                                         — QC Publisher - (12 days ago)
* f8a82bb4771 - Commit label r00355.2 - Post-CS4 0.0.355.2                                       — QC Publisher - (12 days ago)
* 2eff551636f - Commit label r00333.1a - Pre-CS3 0.0.333.1a                                      — QC Publisher - (12 days ago)
* e8e2ffec9aa - Commit label r00322.2 - Pre-CS 0.0.322.2                                         — QC Publisher - (12 days ago)
* abb243c22ca - Commit label r00304.1 - Pre-CS 0.0.304.1                                         — QC Publisher - (12 days ago)
* 07404bef2a1 - Commit label r002781.1 - FC 0.0.2781.1                                           — QC Publisher - (12 days ago)
* c08322001fa - Commit label r00278.1 - FC 0.0.278.1                                             — QC Publisher - (12 days ago)
* 704b0689e09 - Commit label r00252.1 - FC 0.0.252.1                                             — QC Publisher - (12 days ago)
* c38db517801 - Commit label r00227.1 - ES3 0.0.227.1                                            — QC Publisher - (12 days ago)
* 9b84f778a25 - Commit label r00205.1 - ES2 0.0.205.1                                            — QC Publisher - (12 days ago)
* 7aeffcc4872 - Commit label r00160.1 - ES1 0.0.160.1                                            — QC Publisher - (12 days ago)

```
同样的我们在最后那个提交我们做了个end的tag标记          马哥的淘宝店:https://shop592330910.taobao.com/



接下来我们看到高通这次新升级的基线历史。我们可以看到比上次多了一个最后一个提交 709f36e60d8 - Commit label r00446.1.135694.1 - Post-CS6 0.0.446.1.135694.1
```text

* 709f36e60d8 - Commit label r00446.1.135694.1 - Post-CS6 0.0.446.1.135694.1                     — QC Publisher (HEAD -> new) - (3 days ago)
* 5d9c028721b - Commit label r00446.1 - Post-CS6 0.0.446.1                                       — QC Publisher (tag: start) - (10 days ago)
* 3a7938edc31 - Commit label r004361.1 - Post-CS5 0.0.4361.1                                     — QC Publisher - (12 days ago)
* 57ab46a6d39 - Commit label r00425.1 - Post-CS4 0.0.425.1                                       — QC Publisher - (12 days ago)
* 4969cbee834 - Commit label r00418.2 - Post-CS3 0.0.418.2                                       — QC Publisher - (12 days ago)
* cc98e139704 - Commit label r004032.1 - Post-CS2 0.0.4032.1                                     — QC Publisher - (12 days ago)
* a3e87e4a191 - Commit label r004031.1 - Post-CS2 0.0.4031.1                                     — QC Publisher - (12 days ago)
* 21394e21a15 - Commit label r00391.1 - Post-CS 0.0.391.1                                        — QC Publisher - (12 days ago)
* 285c683e3de - Commit label r00375.10a - CS 0.0.375.10a                                         — QC Publisher - (12 days ago)
* f8a82bb4771 - Commit label r00355.2 - Post-CS4 0.0.355.2                                       — QC Publisher - (12 days ago)
* 2eff551636f - Commit label r00333.1a - Pre-CS3 0.0.333.1a                                      — QC Publisher - (12 days ago)
* e8e2ffec9aa - Commit label r00322.2 - Pre-CS 0.0.322.2                                         — QC Publisher - (12 days ago)
* abb243c22ca - Commit label r00304.1 - Pre-CS 0.0.304.1                                         — QC Publisher - (12 days ago)
* 07404bef2a1 - Commit label r002781.1 - FC 0.0.2781.1                                           — QC Publisher - (12 days ago)
* c08322001fa - Commit label r00278.1 - FC 0.0.278.1                                             — QC Publisher - (12 days ago)
* 704b0689e09 - Commit label r00252.1 - FC 0.0.252.1                                             — QC Publisher - (12 days ago)
* c38db517801 - Commit label r00227.1 - ES3 0.0.227.1                                            — QC Publisher - (12 days ago)
* 9b84f778a25 - Commit label r00205.1 - ES2 0.0.205.1                                            — QC Publisher - (12 days ago)
* 7aeffcc4872 - Commit label r00160.1 - ES1 0.0.160.1                                            — QC Publisher - (12 days ago)


```


最后我们执行 git rebase --onto new start end 命令   马哥的淘宝店:https://shop592330910.taobao.com/
```text

$ git rebase --onto new start end  
First, rewinding head to replay your work on top of it...  马哥的淘宝店:https://shop592330910.taobao.com/
Applying: [SHAR-3][AMSS] set modem build env
省略很多
Applying: [SHAR-3222][CSP] Modem: Fix Fdd sig drop when tx off
Applying: [SHA-1356][CSP]Make call detail info persist
Applying: [SHA-1356][CSP]Judge lte sig strength by rsrp instead of rssi
Applying: [SHA-1356][CSP]Fix some modem stats free


```
我们可以看到一系列的applying输出，表明在打patch的。这次我们比较幸运是没有冲突的，当有冲突我们可以停下来解决冲突，然后继续git rebase --continue


执行完git rebase之后 会切换到一个匿名分支上的。 马哥的淘宝店:https://shop592330910.taobao.com/


```text
马哥的淘宝店:https://shop592330910.taobao.com/

$ git --no-pager ll                                                                                       
* 7e97cd4e4f1 - [SHA-1356][CSP]Fix some modem stats free                                         — 研发某某人 (HEAD) - (2 days ago)
中间省略很多提交
* 9555a23168e - [SHAR-28][CSP] Modem: RF Bring up code                                           — 研发某某人 - (5 months ago)
* afe71d51b14 - [SHAR-3][AMSS] fix modem build env                                               — 研发某某人 - (5 months ago)
* 4656cc922ed - [SHAR-4][csp] initial mbn configuration and build scripts                        — 研发某某人 - (5 months ago)
* 4ef2d960d65 - [SHAR-3][AMSS] set modem build env                                               — 研发某某人 - (5 months ago)
* 709f36e60d8 - Commit label r00446.1.135694.1 - Post-CS6 0.0.446.1.135694.1                     — QC Publisher (new, r00446.1.135694.1) - (3 days ago)
* 5d9c028721b - Commit label r00446.1 - Post-CS6 0.0.446.1                                       — QC Publisher (tag: start,r00446.1) - (10 days ago)
* 3a7938edc31 - Commit label r004361.1 - Post-CS5 0.0.4361.1                                     — QC Publisher - (12 days ago)
* 57ab46a6d39 - Commit label r00425.1 - Post-CS4 0.0.425.1                                       — QC Publisher - (12 days ago)
* 4969cbee834 - Commit label r00418.2 - Post-CS3 0.0.418.2                                       — QC Publisher - (12 days ago)
* cc98e139704 - Commit label r004032.1 - Post-CS2 0.0.4032.1                                     — QC Publisher - (12 days ago)
* a3e87e4a191 - Commit label r004031.1 - Post-CS2 0.0.4031.1                                     — QC Publisher - (12 days ago)
* 21394e21a15 - Commit label r00391.1 - Post-CS 0.0.391.1                                        — QC Publisher - (12 days ago)
* 285c683e3de - Commit label r00375.10a - CS 0.0.375.10a                                         — QC Publisher - (12 days ago)
* f8a82bb4771 - Commit label r00355.2 - Post-CS4 0.0.355.2                                       — QC Publisher - (12 days ago)
* 2eff551636f - Commit label r00333.1a - Pre-CS3 0.0.333.1a                                      — QC Publisher - (12 days ago)
* e8e2ffec9aa - Commit label r00322.2 - Pre-CS 0.0.322.2                                         — QC Publisher - (12 days ago)
* abb243c22ca - Commit label r00304.1 - Pre-CS 0.0.304.1                                         — QC Publisher - (12 days ago)
* 07404bef2a1 - Commit label r002781.1 - FC 0.0.2781.1                                           — QC Publisher - (12 days ago)
* c08322001fa - Commit label r00278.1 - FC 0.0.278.1                                             — QC Publisher - (12 days ago)
* 704b0689e09 - Commit label r00252.1 - FC 0.0.252.1                                             — QC Publisher - (12 days ago)
* c38db517801 - Commit label r00227.1 - ES3 0.0.227.1                                            — QC Publisher - (12 days ago)
* 9b84f778a25 - Commit label r00205.1 - ES2 0.0.205.1                                            — QC Publisher - (12 days ago)
* 7aeffcc4872 - Commit label r00160.1 - ES1 0.0.160.1                                            — QC Publisher - (12 days ago)

```
通过以上操作我们看到 我们 已经我把我们自己公司的所有提交 都放到新高通基线的最后了。这就是基线升级。



下面也是一种简单的git rebase的方式   马哥的淘宝店:https://shop592330910.taobao.com/
```text
#首先checkout到我们的开发分支上,dev分支上

#然后执行
git rebase <高通新基线分支r00446.1.135694.1>

这个顺序出来的git log历史 就是 先是高通的提交，后面都是我们自己公司的提交。这个顺序是我们想要的。
```

如果你分支顺序弄反了，git log的历史也就反过来了。   马哥的淘宝店:https://shop592330910.taobao.com/
```text

#首先checkout到  高通新基线分支

#然后执行
git rebase <我们的开发分支 dev>

这个顺序出来的git log历史是先是dev分支的所有提交历史，最后一个提交是高通新基线的那个提交。
（对我们这个例子高通新基线只多了一个提交，所以这里我们最后只看到这1个）

```
马哥的淘宝店:https://shop592330910.taobao.com/


#基线升级 git cherry-pick 

当然除了使用git rebase 命令我们还可以使用git cherry-pick 

```bash
$ git cherry-pick c6ade72c576^..b299ad990db

#其中c6ade72c576是我们公司的第一个提交 。c6ade72c576^ 表示这个提交的前一个提交。     马哥的淘宝店:https://shop592330910.taobao.com/
#b299ad990db是我们公司的最后一个提交。

#这个首先我们在checkout 到高通新基线，然后把我们的提交都cherry-pick到高通新基线后面       马哥的淘宝店:https://shop592330910.taobao.com/


     
```

```text

$ git cherry-pick c6ade72c576^..b299ad990db  
[new 0c359bfd39f] [SHAR-3][AMSS] set modem build env
[new aa215b1d329] [SHAR-4][csp] initial mbn configuration and build scripts    马哥的淘宝店:https://shop592330910.taobao.com/
中间也省略很多log。
[new a068317e390] [SHA-1356][CSP]Make call detail info persist
[new 454451a3363] [SHA-1356][CSP]Judge lte sig strength by rsrp instead of rssi  马哥的淘宝店:https://shop592330910.taobao.com/
[new ebef8e4e415] [SHA-1356][CSP]Fix some modem stats free

```




#基线升级   git merge

下面我们看看git merge的操作

我们先checkout到我们的新基线哪个提交上，     马哥的淘宝店:https://shop592330910.taobao.com/
```text

* 709f36e60d8 - Commit label r00446.1.135694.1 - Post-CS6 0.0.446.1.135694.1                     — QC Publisher (r00446.1.135694.1) - (3 days ago)  
* 5d9c028721b - Commit label r00446.1 - Post-CS6 0.0.446.1                                       — QC Publisher (tag: start, r00446.1) - (10 days ago)
* 3a7938edc31 - Commit label r004361.1 - Post-CS5 0.0.4361.1                                     — QC Publisher - (12 days ago) 马哥的淘宝店:https://shop592330910.taobao.com/
* 57ab46a6d39 - Commit label r00425.1 - Post-CS4 0.0.425.1                                       — QC Publisher - (12 days ago) 马哥的淘宝店:https://shop592330910.taobao.com/
* 4969cbee834 - Commit label r00418.2 - Post-CS3 0.0.418.2                                       — QC Publisher - (12 days ago) 马哥的淘宝店:https://shop592330910.taobao.com/
* cc98e139704 - Commit label r004032.1 - Post-CS2 0.0.4032.1                                     — QC Publisher - (12 days ago)
* a3e87e4a191 - Commit label r004031.1 - Post-CS2 0.0.4031.1                                     — QC Publisher - (12 days ago)
* 21394e21a15 - Commit label r00391.1 - Post-CS 0.0.391.1                                        — QC Publisher - (12 days ago)
* 285c683e3de - Commit label r00375.10a - CS 0.0.375.10a                                         — QC Publisher - (12 days ago)
* f8a82bb4771 - Commit label r00355.2 - Post-CS4 0.0.355.2                                       — QC Publisher - (12 days ago)
* 2eff551636f - Commit label r00333.1a - Pre-CS3 0.0.333.1a                                      — QC Publisher - (12 days ago)
* e8e2ffec9aa - Commit label r00322.2 - Pre-CS 0.0.322.2                                         — QC Publisher - (12 days ago)
* abb243c22ca - Commit label r00304.1 - Pre-CS 0.0.304.1                                         — QC Publisher - (12 days ago)
* 07404bef2a1 - Commit label r002781.1 - FC 0.0.2781.1                                           — QC Publisher - (12 days ago)
* c08322001fa - Commit label r00278.1 - FC 0.0.278.1                                             — QC Publisher - (12 days ago) 马哥的淘宝店:https://shop592330910.taobao.com/


```
然后直接执行git merge dev分支。就是把dev分支直接合并到当前这个新基线分支上。
```text

$ git --no-pager ll                                                                                           
*   c49b5d92582 - Merge remote-tracking branch 'shgit/sdm845_o_dev_20171101' into n                — Minghui Ma (HEAD -> n) - (3 minutes ago)  马哥的淘宝店:https://shop592330910.taobao.com/
|\  
| * b299ad990db - [SHA-1356][CSP]Fix some modem stats free                                         — 研发某某人 (tag: end, dev) - (2 days ago)      马哥的淘宝店:https://shop592330910.taobao.com/
| * 19e07ef237b - [SHA-1356][CSP]Judge lte sig strength by rsrp instead of rssi                    — 研发某某人 - (2 days ago)
| * 1c68bd229dc - [SHA-1356][CSP]Make call detail info persist                                     — 研发某某人 - (3 days ago)
| * 9ce5e6e71ac - [SHAR-3222][CSP] Modem: Fix Fdd sig drop when tx off                             — 研发某某人 - (4 days ago)
| * 1094b5cccea - [SHAR-3506][CSP] Modem: CA config lead crash                                     — 研发某某人 - (4 days ago)
| * 82d22ff8f0e - [SHAR-28][CSP] Modem: Modify Gsm static NV                                       — 研发某某人 - (7 days ago)
| * 中间省略很多log
| * 79a4026acce - [SHAR-32][CSP] Modem: RF ca bring up,delete rx2,rx3                              — 研发某某人 - (5 months ago)
| * ca009c17f44 - [SHAR-37][csp]modem: rfc antenna initial settings                                — 研发某某人 - (5 months ago)
| * 9cce83227bc - [SHAR-32][CSP] Modem: RF ca bring up                                             — 研发某某人 - (5 months ago)
| * 124691dd56a - [SHAR-28][CSP] Modem: RF Bring up code                                           — 研发某某人 - (5 months ago)
| * fd27c311a84 - [SHAR-3][AMSS] fix modem build env                                               — 研发某某人 - (5 months ago)
| * 5b07995a297 - [SHAR-4][csp] initial mbn configuration and build scripts                        — 研发某某人 - (5 months ago)
| * c6ade72c576 - [SHAR-3][AMSS] set modem build env                                               — 研发某某人 - (5 months ago)
* | 709f36e60d8 - Commit label r00446.1.135694.1 - Post-CS6 0.0.446.1.135694.1                     — QC Publisher (r00446.1.135694.1) - (3 days ago)  马哥的淘宝店:https://shop592330910.taobao.com/
|/  
* 5d9c028721b - Commit label r00446.1 - Post-CS6 0.0.446.1                                       — QC Publisher (tag: start, r00446.1) - (10 days ago)  马哥的淘宝店:https://shop592330910.taobao.com/
* 3a7938edc31 - Commit label r004361.1 - Post-CS5 0.0.4361.1                                     — QC Publisher - (12 days ago)
* 57ab46a6d39 - Commit label r00425.1 - Post-CS4 0.0.425.1                                       — QC Publisher - (12 days ago)
* 4969cbee834 - Commit label r00418.2 - Post-CS3 0.0.418.2                                       — QC Publisher - (12 days ago)
* cc98e139704 - Commit label r004032.1 - Post-CS2 0.0.4032.1                                     — QC Publisher - (12 days ago)
* a3e87e4a191 - Commit label r004031.1 - Post-CS2 0.0.4031.1                                     — QC Publisher - (12 days ago)
* 21394e21a15 - Commit label r00391.1 - Post-CS 0.0.391.1                                        — QC Publisher - (12 days ago)
* 285c683e3de - Commit label r00375.10a - CS 0.0.375.10a                                         — QC Publisher - (12 days ago)
* f8a82bb4771 - Commit label r00355.2 - Post-CS4 0.0.355.2                                       — QC Publisher - (12 days ago)
* 2eff551636f - Commit label r00333.1a - Pre-CS3 0.0.333.1a                                      — QC Publisher - (12 days ago)
* e8e2ffec9aa - Commit label r00322.2 - Pre-CS 0.0.322.2                                         — QC Publisher - (12 days ago)
* abb243c22ca - Commit label r00304.1 - Pre-CS 0.0.304.1                                         — QC Publisher - (12 days ago)
* 07404bef2a1 - Commit label r002781.1 - FC 0.0.2781.1                                           — QC Publisher - (12 days ago)
* c08322001fa - Commit label r00278.1 - FC 0.0.278.1                                             — QC Publisher - (12 days ago)
* 704b0689e09 - Commit label r00252.1 - FC 0.0.252.1                                             — QC Publisher - (12 days ago)
* c38db517801 - Commit label r00227.1 - ES3 0.0.227.1                                            — QC Publisher - (12 days ago)
* 9b84f778a25 - Commit label r00205.1 - ES2 0.0.205.1                                            — QC Publisher - (12 days ago)
* 7aeffcc4872 - Commit label r00160.1 - ES1 0.0.160.1                                            — QC Publisher - (12 days ago)马哥的淘宝店:https://shop592330910.taobao.com/

```
通过git merg的方式 我们看到git log 历史发现了分叉的现象
5d9c028721b 和 709f36e60d8 这里出现了分叉。

这个时候如果在使用 git rebase -i 5d9c028721b 就会把历史搞成一条直线了。


我们在试试 git merge 高通基线分支

```text
# 首先我们要checkout到我们的开发dev分支上。然后执行git merge qcom
$ git --no-pager ll       
*   e82f092d067 - Merge branch 'qcom' into HEAD                                                    — 马哥的淘宝店:https://shop592330910.taobao.com/ (HEAD) - (20 seconds ago)
|\  
| * 709f36e60d8 - Commit label r00446.1.135694.1 - Post-CS6 0.0.446.1.135694.1                     — QC Publisher (r00446.1.135694.1, qcom) - (3 days ago) 马哥的淘宝店:https://shop592330910.taobao.com/
* | b299ad990db - [SHA-1356][CSP]Fix some modem stats free                                         — 研发某某人 (tag: end,dev) - (2 days ago)  马哥的淘宝店:https://shop592330910.taobao.com/
* | 19e07ef237b - [SHA-1356][CSP]Judge lte sig strength by rsrp instead of rssi                    — 研发某某人 - (2 days ago)
* | 1c68bd229dc - [SHA-1356][CSP]Make call detail info persist                                     — 研发某某人 - (3 days ago)
* | 9ce5e6e71ac - [SHAR-3222][CSP] Modem: Fix Fdd sig drop when tx off                             — 研发某某人 - (4 days ago)
* | 中间省略很多log
* | 124691dd56a - [SHAR-28][CSP] Modem: RF Bring up code                                           — 研发某某人 - (5 months ago)
* | fd27c311a84 - [SHAR-3][AMSS] fix modem build env                                               — 研发某某人 - (5 months ago)
* | 5b07995a297 - [SHAR-4][csp] initial mbn configuration and build scripts                        — 研发某某人 - (5 months ago)
* | c6ade72c576 - [SHAR-3][AMSS] set modem build env                                               — 研发某某人 - (5 months ago) 马哥的淘宝店:https://shop592330910.taobao.com/
|/  
* 5d9c028721b - Commit label r00446.1 - Post-CS6 0.0.446.1                                       — QC Publisher (tag: start, r00446.1) - (10 days ago) 马哥的淘宝店:https://shop592330910.taobao.com/
* 3a7938edc31 - Commit label r004361.1 - Post-CS5 0.0.4361.1                                     — QC Publisher - (12 days ago)马哥的淘宝店:https://shop592330910.taobao.com/
* 57ab46a6d39 - Commit label r00425.1 - Post-CS4 0.0.425.1                                       — QC Publisher - (12 days ago)马哥的淘宝店:https://shop592330910.taobao.com/
* 4969cbee834 - Commit label r00418.2 - Post-CS3 0.0.418.2                                       — QC Publisher - (12 days ago)马哥的淘宝店:https://shop592330910.taobao.com/
* cc98e139704 - Commit label r004032.1 - Post-CS2 0.0.4032.1                                     — QC Publisher - (12 days ago)马哥的淘宝店:https://shop592330910.taobao.com/
* a3e87e4a191 - Commit label r004031.1 - Post-CS2 0.0.4031.1                                     — QC Publisher - (12 days ago)马哥的淘宝店:https://shop592330910.taobao.com/
* 21394e21a15 - Commit label r00391.1 - Post-CS 0.0.391.1                                        — QC Publisher - (12 days ago)马哥的淘宝店:https://shop592330910.taobao.com/
* 285c683e3de - Commit label r00375.10a - CS 0.0.375.10a                                         — QC Publisher - (12 days ago)马哥的淘宝店:https://shop592330910.taobao.com/
* f8a82bb4771 - Commit label r00355.2 - Post-CS4 0.0.355.2                                       — QC Publisher - (12 days ago)马哥的淘宝店:https://shop592330910.taobao.com/
* 2eff551636f - Commit label r00333.1a - Pre-CS3 0.0.333.1a                                      — QC Publisher - (12 days ago)马哥的淘宝店:https://shop592330910.taobao.com/
* e8e2ffec9aa - Commit label r00322.2 - Pre-CS 0.0.322.2                                         — QC Publisher - (12 days ago)马哥的淘宝店:https://shop592330910.taobao.com/
* abb243c22ca - Commit label r00304.1 - Pre-CS 0.0.304.1                                         — QC Publisher - (12 days ago)马哥的淘宝店:https://shop592330910.taobao.com/
* 07404bef2a1 - Commit label r002781.1 - FC 0.0.2781.1                                           — QC Publisher - (12 days ago)马哥的淘宝店:https://shop592330910.taobao.com/
* c08322001fa - Commit label r00278.1 - FC 0.0.278.1                                             — QC Publisher - (12 days ago)马哥的淘宝店:https://shop592330910.taobao.com/
* 704b0689e09 - Commit label r00252.1 - FC 0.0.252.1                                             — QC Publisher - (12 days ago)马哥的淘宝店:https://shop592330910.taobao.com/
* c38db517801 - Commit label r00227.1 - ES3 0.0.227.1                                            — QC Publisher - (12 days ago)马哥的淘宝店:https://shop592330910.taobao.com/
* 9b84f778a25 - Commit label r00205.1 - ES2 0.0.205.1                                            — QC Publisher - (12 days ago)马哥的淘宝店:https://shop592330910.taobao.com/
* 7aeffcc4872 - Commit label r00160.1 - ES1 0.0.160.1                                            — QC Publisher - (12 days ago)马哥的淘宝店:https://shop592330910.taobao.com/

```
这个时候我们看到 高通最新的那个提交“Commit label r00446.1.135694.1” 放到了我们自己的提交的后面了。  马哥的淘宝店:https://shop592330910.taobao.com/

所以说这个git merge时候 分支是顺序也是很重要的。

其实结果是一样的，也就是说你怎么merge，最后的代码应该是一样的。只是git log的历史看着顺序不太一样。

同样的的采用git rebase的方式 也是需要关系顺序的。  马哥的淘宝店:https://shop592330910.taobao.com/


#基线升级 冲突解决策略


## 升级冲突之二进制文件冲突解决策略

今天我们来看个 升级有冲突的一个情况  ，这次冲突的文件还是一个二进制的文件。

下面是我们开发分支的最新的提交，暂且叫做dev分支，   
我们可以看到 Commit label r00455.2 - Post-CS7 0.0.455.2是最新的高通的提交。   

```text

* d15c629 - [SHAR-1651][Drv][WLAN] Modify RF bin for pvt machine                             — 马哥的淘宝店:https://shop592330910.taobao.com/ (HEAD, dev) - (11 days ago)
* 9818689 - [SHAR-4346][Drv][WLAN] Modify the tmp bdf name to bawlant.bin                    — 马哥的淘宝店:https://shop592330910.taobao.com/ - (4 weeks ago)
* 87233f6 - [SHAR-4346][Drv][WLAN] Add tmp_bdf file to compitable dvt machine                — 马哥的淘宝店:https://shop592330910.taobao.com/ - (5 weeks ago)
* eab031d - [SHAR-1651][Drv][WLAN] Modify RF bdf bin for PVT product                         — 马哥的淘宝店:https://shop592330910.taobao.com/ - (5 weeks ago)
* e7caad1 - [SHAR-1651][Drv][WLAN] Modify for RF bin to optimize power test                  — 马哥的淘宝店:https://shop592330910.taobao.com/ - (6 weeks ago)
* 269c0a7 - [SHAR-1651][Drv][WLAN] Modify RF bin file of 20171227 for factory test           — 马哥的淘宝店:https://shop592330910.taobao.com/ - (2 months ago)
* bbcda88 - [SHAR-1651][Drv][WLAN] Replace bin for RF 20171226 optimize parameters           — 马哥的淘宝店:https://shop592330910.taobao.com/ - (2 months ago)
* b621875 - [SHAR-1651][Drv][WLAN] Modify for RF test bin                                    — 马哥的淘宝店:https://shop592330910.taobao.com/ - (3 months ago)
* 881b1c0 - [SHAR-223] WLAN RF paremeters update                                             — 马哥的淘宝店:https://shop592330910.taobao.com/ - (4 months ago)
* 53b284b - Commit label r00455.2 - Post-CS7 0.0.455.2                                       — QC Publisher - (4 weeks ago)
* 18cd798 - Commit label r00446.1 - Post-CS6 0.0.446.1                                       — QC Publisher - (6 weeks ago)
* 6b5ada6 - Commit label r004361.1 - Post-CS5 0.0.4361.1                                     — QC Publisher - (6 weeks ago)
* b86ba16 - Commit label r00425.1 - Post-CS4 0.0.425.1                                       — QC Publisher - (6 weeks ago)
* 2dd6908 - Commit label r00418.2 - Post-CS3 0.0.418.2                                       — QC Publisher - (6 weeks ago)
* 9c2aea2 - Commit label r004031.1 - Post-CS2 0.0.4031.1                                     — QC Publisher - (6 weeks ago)
* 62976a7 - Commit label r00391.1 - Post-CS 0.0.391.1                                        — QC Publisher - (6 weeks ago)
* 0c2bf63 - Commit label r00375.10a - CS 0.0.375.10a                                         — QC Publisher - (6 weeks ago)
* 5409905 - Commit label r00355.2 - Post-CS4 0.0.355.2                                       — QC Publisher - (6 weeks ago)
* 089bfa5 - Commit label r00333.1a - Pre-CS3 0.0.333.1a                                      — QC Publisher - (6 weeks ago)
* c70ada6 - Commit label r00322.2 - Pre-CS 0.0.322.2                                         — QC Publisher - (6 weeks ago)
* 41de9fd - Commit label r00304.1 - Pre-CS 0.0.304.1                                         — QC Publisher - (6 weeks ago)
* b31005a - Commit label r00278.1 - FC 0.0.278.1                                             — QC Publisher - (6 weeks ago)
* 380080c - Commit label r00252.1 - FC 0.0.252.1                                             — QC Publisher - (6 weeks ago)
* d0f5347 - Commit label r00227.1 - ES3 0.0.227.1                                            — QC Publisher - (6 weeks ago)
* cd51300 - Commit label r00205.1 - ES2 0.0.205.1                                            — QC Publisher - (6 weeks ago)
* 39aae3a - Commit label r00160.1 - ES1 0.0.160.1                                            — QC Publisher - (6 weeks ago)


```

这次高通释放了一个新的基线了。叫做Post-CS8了Commit label r00461.1 - Post-CS8 0.0.461.1。   

```text

* b43884c - Commit label r00461.1 - Post-CS8 0.0.461.1                                       — QC Publisher (r00461.1) - (2 weeks ago)
* 53b284b - Commit label r00455.2 - Post-CS7 0.0.455.2                                       — QC Publisher (r00455.2) - (4 weeks ago)
* 18cd798 - Commit label r00446.1 - Post-CS6 0.0.446.1                                       — QC Publisher (r00446.1) - (6 weeks ago)
* 6b5ada6 - Commit label r004361.1 - Post-CS5 0.0.4361.1                                     — QC Publisher - (6 weeks ago)
* b86ba16 - Commit label r00425.1 - Post-CS4 0.0.425.1                                       — QC Publisher - (6 weeks ago)
* 2dd6908 - Commit label r00418.2 - Post-CS3 0.0.418.2                                       — QC Publisher - (6 weeks ago)
* 9c2aea2 - Commit label r004031.1 - Post-CS2 0.0.4031.1                                     — QC Publisher - (6 weeks ago)
* 62976a7 - Commit label r00391.1 - Post-CS 0.0.391.1                                        — QC Publisher - (6 weeks ago)
* 0c2bf63 - Commit label r00375.10a - CS 0.0.375.10a                                         — QC Publisher - (6 weeks ago)
* 5409905 - Commit label r00355.2 - Post-CS4 0.0.355.2                                       — QC Publisher - (6 weeks ago)
* 089bfa5 - Commit label r00333.1a - Pre-CS3 0.0.333.1a                                      — QC Publisher - (6 weeks ago)
* c70ada6 - Commit label r00322.2 - Pre-CS 0.0.322.2                                         — QC Publisher - (6 weeks ago)
* 41de9fd - Commit label r00304.1 - Pre-CS 0.0.304.1                                         — QC Publisher - (6 weeks ago)
* b31005a - Commit label r00278.1 - FC 0.0.278.1                                             — QC Publisher - (6 weeks ago)
* 380080c - Commit label r00252.1 - FC 0.0.252.1                                             — QC Publisher - (6 weeks ago)
* d0f5347 - Commit label r00227.1 - ES3 0.0.227.1                                            — QC Publisher - (6 weeks ago)
* cd51300 - Commit label r00205.1 - ES2 0.0.205.1                                            — QC Publisher - (6 weeks ago)
* 39aae3a - Commit label r00160.1 - ES1 0.0.160.1                                            — QC Publisher - (6 weeks ago)


```

高通一般都是一次给一个提交的，例如最新的 r00461.1 对应 Post-CS8。     
从中我们也可以看出高通的一些命名方式，先是从  ES1 ， ES2 ， ES3，    
然后是FC，  然后是 Pre CS阶段， 然后是CS阶段，后面是Post CS阶段。   

其中标签的命令也是有规律的，  r00160.1， r00205.1 等等，基本上数字是增大的顺序来的。   
每个提交高通都会打上对应的一个TAG标签，命名也就是r00205.1这样的。


1.首先我们checkout到dev分支上

```bash

git checkout dev
```

2.然后执行git rebase r00461.1  
我们这次采用简单的git rebase方式。通常使用的是git rebase --onto new start end的方式
```text
$ git rebase r00461.1                                                             
First, rewinding head to replay your work on top of it...
Applying: [SHAR-223] WLAN RF paremeters update
Using index info to reconstruct a base tree...  

M         wlan/halphy_tools/host/bdfUtil/qca61x0/bdf/bdwlan.bin   

Falling back to patching base and 3-way merge...
warning: Cannot merge binary files: wlan/halphy_tools/host/bdfUtil/qca61x0/bdf/bdwlan.bin (HEAD vs. [SHAR-223] WLAN RF paremeters update)

Auto-merging wlan/halphy_tools/host/bdfUtil/qca61x0/bdf/bdwlan.bin
CONFLICT (content): Merge conflict in wlan/halphy_tools/host/bdfUtil/qca61x0/bdf/bdwlan.bin

error: Failed to merge in the changes.

Patch failed at 0001 [SHAR-223] WLAN RF paremeters update

The copy of the patch that failed is found in: .git/rebase-apply/patch

When you have resolved this problem, run "git rebase --continue".
If you prefer to skip this patch, run "git rebase --skip" instead.
To check out the original branch and stop rebasing, run "git rebase --abort".


```
3.解决二进制文件的冲突

非常不幸的是我们第一个patch就有冲突了。我们可以通过git status查看哪个文件冲突了

```text
$ git st                                                                                                         
rebase in progress; onto b43884c
You are currently rebasing.
  (fix conflicts and then run "git rebase --continue")
  (use "git rebase --skip" to skip this patch)
  (use "git rebase --abort" to check out the original branch)

Unmerged paths:
  (use "git reset HEAD <file>..." to unstage)
  (use "git add <file>..." to mark resolution)

	both modified:   wlan/halphy_tools/host/bdfUtil/qca61x0/bdf/bdwlan.bin

no changes added to commit (use "git add" and/or "git commit -a")


```
通过结果我们发现是一个.bin 文件，这是一个二进制的文件，不是个文本文件。  
执行git diff是看不到什么差异的。
```text
$ git diff  
diff --cc wlan/halphy_tools/host/bdfUtil/qca61x0/bdf/bdwlan.bin
index ed3a8d1,cc2d71d..0000000
Binary files differ

```
我们先记录一下这个时候文件的md5值  
f744a3402e260d7fd330cbc964972a5c  wlan/halphy_tools/host/bdfUtil/qca61x0/bdf/bdwlan.bin



我们通过git log查看这个我们这个提交，可以发现确实是修改了这个文件。
```text
commit 881b1c0d7d8454656118a2153b98c44f8a477b0f
Author: 马哥的淘宝店:https://shop592330910.taobao.com/
Date:   Fri Nov 10 14:31:25 2017 +0800

    [SHAR-223] WLAN RF paremeters update
    
    update rf paremeters in bdwlan.bin
    
    Change-Id: Ifbd118b8a584b64bf88635725baeeac309ccd99f

diff --git a/wlan/halphy_tools/host/bdfUtil/qca61x0/bdf/bdwlan.bin b/wlan/halphy_tools/host/bdfUtil/qca61x0/bdf/bdwlan.bin
index e641291..cc2d71d 100755
Binary files a/wlan/halphy_tools/host/bdfUtil/qca61x0/bdf/bdwlan.bin and b/wlan/halphy_tools/host/bdfUtil/qca61x0/bdf/bdwlan.bin differ


```

此时对于二进制文件的冲突，我们有2种策略，也只能是二选一。     
要么选择高通最新的r00461.1 提交 中的这个文件，   
要么选择我们自己这个881b1c0d7d8454656118a2153b98c44f8a477b0f提交 中的这个文件。  

这个时候需要取和研发沟通来决定 选择哪个文件。   （默认此时文件是高通提交中的文件。）

情况（1）此时可以执行
```bash
 git co --ours wlan/halphy_tools/host/bdfUtil/qca61x0/bdf/bdwlan.bin

```  
来选择 采用高通提交的中的这个文件。
如果你是选择高通提交中的这个文件，有可能我们的这个patch就会变成一个空提交，也就是说可以不要我们这个提交啦。

```bash
$ git add wlan/halphy_tools/host/bdfUtil/qca61x0/bdf/bdwlan.bin                                                  

$ git st                                                                                                         
rebase in progress; onto b43884c
You are currently rebasing.
  (all conflicts fixed: run "git rebase --continue")

nothing to commit, working tree clean

$ git rebase --continue                                                                                          
Applying: [SHAR-223] WLAN RF paremeters update
No changes - did you forget to use 'git add'?
If there is nothing left to stage, chances are that something else
already introduced the same changes; you might want to skip this patch.

When you have resolved this problem, run "git rebase --continue".
If you prefer to skip this patch, run "git rebase --skip" instead.
To check out the original branch and stop rebasing, run "git rebase --abort".

```
这个时候提示 No changes。


情况（2）或者 执行

```bash

git co --theirs wlan/halphy_tools/host/bdfUtil/qca61x0/bdf/bdwlan.bin

```
来选择 我们公司自己提交的中的这个文件。  

然后执行 git add
```bash
$ git add wlan/halphy_tools/host/bdfUtil/qca61x0/bdf/bdwlan.bin


$ git st                                                                                                         
rebase in progress; onto b43884c
You are currently rebasing.
  (all conflicts fixed: run "git rebase --continue")

Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	modified:   wlan/halphy_tools/host/bdfUtil/qca61x0/bdf/bdwlan.bin

```
此时文件就会变成绿色，然后执行git rebase --continue，继续进行rebase操作即可。

我们提交中的这个文件的md5值  
6abe9c56383a121c2cbd5e6d4c88accd  wlan/halphy_tools/host/bdfUtil/qca61x0/bdf/bdwlan.bin


通过操作 git checkout --ours来选择高通提交  
通过执行 git checkout --theirs 选择我们自己的提交

怎么理解这个ours，还是thires呢？
```bash
我们要看当前这个历史中的基线是 高通的还是我们的？  ours代表的基线历史中的那一方。

例如rebase的时候，此时历史基线就是高通的，rebase操作是先把基线换成高通的，然后是把我们自己的提交一个一个打上的。

例如cherry pick时候，此时历史是我们自己的，我们pick的提交是高通的，这个时候ours代表我们自己，theirs代表高通的。
如果反过来，历史基线是高通的，我们pick自己的提交，这个时候ours是代表高通的，thires代表我们的。

```

  
## 升级冲突之文本文件冲突解决策略

下次介绍，这个比较简单


更多精彩请关注

马哥的淘宝店:https://shop592330910.taobao.com/


















