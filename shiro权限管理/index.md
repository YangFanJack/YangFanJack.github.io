# shiro权限管理


# shiro权限管理

Shiro的核心就是用来完成权限管理的。权限管理包括用户身份认证和授权两部分，简称认证授权。

只要有人参与的系统都需要进行权限管理。

## 什么是Shiro？

Shiro是apache旗下的一个开源框架，它将软件系统的安全认认证相关的功能抽取出来，实现用户身份认证、权限授权、加密、会话管理等功能，组成了一个通用的安全认证框架。

![Shiro核心架构图](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521225542.png)



- **Security Manager：**包含Authenticator认证器，Authorizer授权器，Session Manager会话管理器，Cache Manager缓存管理器，Session DAO会话数据访问对象，Pluggable Realms可插拔域
- **cryptography：**密码器（sha256，md5）
- **subject：**主体（Shiro集成的东西，宿主）

## Shiro中用户的身份认证

- **subject：主体**

  访问系统的用户，主体可以是用户、程序等，进行认证都称为主体。主体在进行身份认证时需要身份信息Principal和凭证信息credential

- **Principal：身份信息**

  身份认证的标识，如用户名、手机号、邮箱地址等。一个主体可以有多个身份信息，但是只能有一个主身份信息。

- **credential：凭证信息**

  只有主体自己知道的安全信息，如密码、证书等

**认证流程：**

![image-20210730101307429](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521225641.png)



**代码**

```
[users]
zhangsan=123
lisi=1234
wangwu=12345
zhaoliu=123456
public class TestAuhenticator
{
    public static void main( String[] args )
    {
        //创建安全管理器对象
        DefaultSecurityManager securityManager = new DefaultSecurityManager();
        //给安全管理器设置realm
        securityManager.setRealm(new IniRealm("classpath:shiro.ini"));
        //SecurityUtils安全工具类
        SecurityUtils.setSecurityManager(securityManager);
        //拿到主体subject
        Subject subject = SecurityUtils.getSubject();
        //模拟登录，创建令牌
        UsernamePasswordToken token = new UsernamePasswordToken("zhangsan", "123");
        //用户登录
        try {
            System.out.println("Authentication Status:"+subject.isAuthenticated());
            subject.login(token);
            System.out.println("Authentication Status:"+subject.isAuthenticated());
        }
        catch (UnknownAccountException e){
            e.printStackTrace();
            System.out.println("认证失败，用户名不存在！");
        }
        catch (IncorrectCredentialsException e){
            e.printStackTrace();
            System.out.println("认证失败，密码错误！");
        }

    }
}
```

**源码剖析**

- 最终执行用户名校验，在 SimpleAccountRealm类（继承于AuthorizingRealm） doGetAuthenticationInfo() 这个方法中完成用户名校验：查看是否有这个用户

- 最终密码的校验，在 AuthenticatingRealm类 assertCredentialsMath() 这个方法中完成密码校验的

- 通过剖析源码可知，如果要把Realm放在数据库中，只需要继承AuthorizingRealm类，然后重写 doGetAuthenticationInfo() 方法即可

- 几个重要Realm类的继承关系图：

  ![image-20210731093920423](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521225652.png)

- 总结：

  AuthenticatingRealm中有认证方法：doGetAuthenticationInfo()

  AuthorizingRealm中有授权方法：doGetAuthorizationInfo()

  所以，当实际上操作数据库时，我们自定义的Realm类实际上是继承AuthorizingRealm类，然后重写 doGetAuthenticationInfo() 和 doGetAuthorizationInfo() 两个方法。

## 自定义Realm

```
public class CustomRealm extends AuthorizingRealm {
    /**
     * 授权
     * @param principalCollection
     * @return
     */
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
        return null;
    }

    /**
     * 认证
     * @param authenticationToken
     * @return
     * @throws AuthenticationException
     */
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
        String principal = (String) authenticationToken.getPrincipal();
        String credentials = new String((char[]) authenticationToken.getCredentials());
        System.out.println("************************************");
        System.out.println(principal);
        System.out.println(credentials);
        if("zhangsan".equals(principal)){
            return new SimpleAuthenticationInfo("zhangsan","123",this.getName());
        }
        return null;
    }
}
public class TestAuhenticator
{
    public static void main( String[] args )
    {
        //创建安全管理器对象
        DefaultSecurityManager securityManager = new DefaultSecurityManager();
        //给安全管理器设置realm
//        securityManager.setRealm(new IniRealm("classpath:shiro.ini"));
        securityManager.setRealm(new CustomRealm());
        //SecurityUtils安全工具类
        SecurityUtils.setSecurityManager(securityManager);
        //拿到主体subject
        Subject subject = SecurityUtils.getSubject();
        //模拟登录，创建令牌
        UsernamePasswordToken token = new UsernamePasswordToken("zhangsan", "123");
        //用户登录
        try {
            System.out.println("Authentication Status:"+subject.isAuthenticated());
            subject.login(token);
            System.out.println("Authentication Status:"+subject.isAuthenticated());
        }
        catch (UnknownAccountException e){
            e.printStackTrace();
            System.out.println("认证失败，用户名不存在！");
        }
        catch (IncorrectCredentialsException e){
            e.printStackTrace();
            System.out.println("认证失败，密码错误！");
        }

    }
}
```

## 密码加密

**MD5算法**

- 一般用来加密 & 签名
- MD5加密不可逆
- MD5算法生成结果始终是一个16进、32位长度的字符串
- 一般在后端代码的业务层完成加密操作如果要进行用户名
- 使用MD5算法，输入只会有唯一输出。所以为了防止有人恶意穷举试探密码，我们需要给密码加盐后再MD5机密

**Shiro中的MD5加密**

直接看代码

```
public class CustomRealm extends AuthorizingRealm {
    /**
     * 授权
     * @param principalCollection
     * @return
     */
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
        return null;
    }

    /**
     * 认证
     * @param authenticationToken
     * @return
     * @throws AuthenticationException
     */
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
        String principal = (String) authenticationToken.getPrincipal();
        String credentials = new String((char[]) authenticationToken.getCredentials());
        System.out.println("************************************");
        System.out.println(principal);
        System.out.println(credentials);
        if("zhangsan".equals(principal)){
            return new SimpleAuthenticationInfo("zhangsan","123", ByteSource.Util.bytes("abc*"),this.getName());
        }
        return null;
    }
}
public class TestAuhenticator
{
    public static void main( String[] args )
    {
        //创建安全管理器对象
        DefaultSecurityManager securityManager = new DefaultSecurityManager();
        //新建自定义realm对象
        CustomRealm realm = new CustomRealm();
        //设置realm使用hash凭证检测器
        HashedCredentialsMatcher credentialsMatcher = new HashedCredentialsMatcher();
        credentialsMatcher.setHashAlgorithmName("md5");
        credentialsMatcher.setHashIterations(1024);//设置加盐散列次数
        realm.setCredentialsMatcher(credentialsMatcher);

        //给安全管理器设置realm
//        securityManager.setRealm(new IniRealm("classpath:shiro.ini"));
        securityManager.setRealm(realm);
        //SecurityUtils安全工具类
        SecurityUtils.setSecurityManager(securityManager);
        //拿到主体subject
        Subject subject = SecurityUtils.getSubject();
        //模拟登录，创建令牌
        UsernamePasswordToken token = new UsernamePasswordToken("zhangsan", "123");
        //用户登录
        try {
            System.out.println("Authentication Status:"+subject.isAuthenticated());
            subject.login(token);
            System.out.println("Authentication Status:"+subject.isAuthenticated());
        }
        catch (UnknownAccountException e){
            e.printStackTrace();
            System.out.println("认证失败，用户名不存在！");
        }
        catch (IncorrectCredentialsException e){
            e.printStackTrace();
            System.out.println("认证失败，密码错误！");
        }

    }
}
```

## Shiro中的授权操作

**关键对象**

- Who对What进行How操作

- Who是访问资源的主体，What是系统的资源，How是针对资源的操作

- 授权流程：

  ![image-20210801100955232](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521225715.png)

  

- 授权方式：

  - 基于角色的访问控制：Role-Based Access Control

    ```
    if(subject.hasRole("admin")){
        //操作资源
    }
    ```

  - 基于资源的访问控制：Resource-Based Access Control

    ```
    if(subject.isPermission("user:create:*")){
        //操作资源
    }
    /**
    权限字符串：资源标识符:操作:资源实例标识符
    	* 用户创建权限：user:create:*
    	* 用户实例001修改的权限：user:create:001
    	* 用户实例001的所有权限：user:*:001
    **/
    ```

## Shiro中的授权实现

**编程式**

```
Subject subject = SecurityUtils.getSubject();
if(subject.hasRole("admin")){
    //有权限
}
else{
    //无权限
}
```

**注解式**

```
@RequireRoles("admin")
public void hello(){
    //有权限
}
```

**JSP标签式**

```
<shiro:hasRole name="admin">
    <!--有权限-->
</shiro:hasRole>
<!--注意：Thymeleaf中使用shiro需要额外集成-->
```

**代码实现**

```
public class CustomRealm extends AuthorizingRealm {
    /**
     * 授权
     * @param principalCollection
     * @return
     */
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
        System.out.println("==================Authorization===================");
        String primaryPrincipal = (String) principalCollection.getPrimaryPrincipal();
        System.out.println(primaryPrincipal);
        SimpleAuthorizationInfo simpleAuthorizationInfo = new SimpleAuthorizationInfo();
        simpleAuthorizationInfo.addRole("admin");
        simpleAuthorizationInfo.addRole("user");
        simpleAuthorizationInfo.addStringPermission("admin:create:*");
        simpleAuthorizationInfo.addStringPermission("user:update:01");
        return simpleAuthorizationInfo;
    }

    /**
     * 认证
     * @param authenticationToken
     * @return
     * @throws AuthenticationException
     */
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
        System.out.println("==================Authentication===================");
        String principal = (String) authenticationToken.getPrincipal();
        String credentials = new String((char[]) authenticationToken.getCredentials());
        System.out.println("************************************");
        System.out.println(principal);
        System.out.println(credentials);
        if("zhangsan".equals(principal)){
            return new SimpleAuthenticationInfo(principal,"44610a20602341c591e0c9b5ecf19ff3", ByteSource.Util.bytes("abc*"),this.getName());
        }
        return null;
    }
}
public class TestAuthenticator
{
    public static void main( String[] args )
    {
        //创建安全管理器对象
        DefaultSecurityManager securityManager = new DefaultSecurityManager();
        //新建自定义realm对象
        CustomRealm realm = new CustomRealm();
        //设置realm使用hash凭证检测器
        HashedCredentialsMatcher credentialsMatcher = new HashedCredentialsMatcher();
        credentialsMatcher.setHashAlgorithmName("md5");
        credentialsMatcher.setHashIterations(1024);//设置加盐散列次数
        realm.setCredentialsMatcher(credentialsMatcher);

        //给安全管理器设置realm
//        securityManager.setRealm(new IniRealm("classpath:shiro.ini"));
        securityManager.setRealm(realm);
        //SecurityUtils安全工具类
        SecurityUtils.setSecurityManager(securityManager);
        //拿到主体subject
        Subject subject = SecurityUtils.getSubject();
        //模拟登录，创建令牌
        UsernamePasswordToken token = new UsernamePasswordToken("zhangsan", "123");
        //用户登录
        try {
            System.out.println("Authentication Status:"+subject.isAuthenticated());
            subject.login(token);
            System.out.println("Authentication Status:"+subject.isAuthenticated());
        }
        catch (UnknownAccountException e){
            e.printStackTrace();
            System.out.println("认证失败，用户名不存在！");
        }
        catch (IncorrectCredentialsException e){
            e.printStackTrace();
            System.out.println("认证失败，密码错误！");
        }

        //进行授权操作
        if(subject.isAuthenticated()){
            System.out.println(subject.hasRole("user"));
            System.out.println(subject.hasAllRoles(Arrays.asList("admin","user")));
            System.out.println(subject.isPermitted("admin:create:*"));
            System.out.println(subject.isPermitted("user:update:02"));
        }

    }
}
```

## 权限模型

![image-20210802215244502](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521225741.png)



## Shiro和SSM的整合

**整合思路**

![image-20210802191222968](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521225748.png)

