  Selinux安全上下文

 **USER：ROLE：TYPE[LEVEL[：CATEGORY]]**

- USER
- 
         1) user identity：类似Linux系统中的UID，提供身份识别，用来记录身份；安全上下文的一部分；
         2) 三种常见的 user:

             • user_u ：普通用户登录系统后的预设；
             • system_u ：开机过程中系统进程的预设；
             • root ：root 登录后的预设；
        3) 在 targeted policy 中 users 不是很重要；
        4) 在strict policy 中比较重要，所有预设的 SELinux Users 都是以 “_u” 结尾的，root 除外。

-  ROLE
-  
        1) 文件、目录和设备的role：通常是 object_r；
        2) 程序的role：通常是 system_r；
        3) 用户的role：targeted policy为system_r； strict policy为sysadm_r、staff_r、user_r；用户的role，类似系统中的GID，不同角色具备不同的的权限；用户可以具备多个role；但是同一时间内只能使用一个role；        

        4) 使用基于RBAC(Roles Based Access Control) 的strict和mls策略中，用来存储角色信息

-  TYPE
-  
        1) type：用来将主体(subject)和客体(object)划分为不同的组，给每个主体和系统中的客体定义了一个类型；为进程运行提供最低的权限环境；
        2) 当一个类型与执行中的进程相关联时，其type也称为domain；
        3) type是SElinux security context 中最重要的部位，是 SELinux Type Enforcement 的心脏，预设值以_t结尾；

        LEVEL和CATEGORY：定义层次和分类，只用于mls策略中
             • LEVEL：代表安全等级,目前已经定义的安全等级为s0-s15,等级越来越高
             • CATEGORY：代表分类，目前已经定义的分类为c0-c1023

![](Selinux.png)