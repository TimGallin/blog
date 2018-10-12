

###传统的认证系统###
用户必须向客户端（或者其他第三方应用）共享他的身份信息以开放访问用户保护资源的权限给这些应用，这会导致如下问题：

1. 第三方应用必须保存用户的身份信息以供未来其他用途，通常是保存了用户的明文账号密码
2. 服务端必须支持密码认证，尽管密码中可能存在安全问题
3. 第三方应用获取了过于宽泛的权限来访问用户资源，用户无法限制第三方访问权限的范围和有效期
4. 用户无法单独撤销其对某一个应用的授权，除非更改密码，而这将撤销对所有应用的授权
5. 任何一个应用被破解泄露都将导致用户的所有资源泄露

###OAUTH2.0协议角色构成###
>**Roles**
>OAUTH2.0可以抽象为四个角色共同认证系统

1. **Resource Owner**
资源的拥有者，可以向第三方应用授权，通常指代用户

2. **Client**
第三方应用，在接收Resource Owner的授权后，可以向认证服务器请求认证以获取**Access Token**，在获取后到token后可以向资源服务器申请访问资源

3. **Authorization Server**
认证服务器，可以向应用提供Access Token授权

4. **Resource Server**
资源服务器，管理保存用户资源，可以向应用提供资源访问


###协议工作流###
>**Protocal Flow**
>Authorization Server和Resource Server可以在同一台物理机上，也可以分开部署

     +--------+                               +---------------+
     |        |--(A)- Authorization Request ->|   Resource    |
     |        |                               |     Owner     |
     |        |<-(B)-- Authorization Grant ---|               |
     |        |                               +---------------+
     |        |
     |        |                               +---------------+
     |        |--(C)-- Authorization Grant -->| Authorization |
     | Client |                               |     Server    |
     |        |<-(D)----- Access Token -------|               |
     |        |                               +---------------+
     |        |
     |        |                               +---------------+
     |        |--(E)----- Access Token ------>|    Resource   |
     |        |                               |     Server    |
     |        |<-(F)--- Protected Resource ---|               |
     +--------+                               +---------------+

###许可类型###
>**Authorization Grant**

1. **Authrozation Code**
认证码作为用户与应用的中介，应用请求认证服务器认证，认证服务将用户重定向认证页面，用户对认证页面进行身份信息授权，提交后认证服务进行校验，根据应用请求认证服务时提供的重定向URI，将认证码返回给应用。 应用通过该认证码可以向认证服务器的其他service请求token
**Flow：**

```	
	+----------+                              +---------------+
	|  Client  |--(A)-Authorization Request ->| Authorization |
	|          |      (Redirect-Uri)	      |     Server    |	
	+----------+                              |               |
											  |               |
	+----------+							  |               |
	| Resource |<-(B)-----Grant Prompt--------|			   |
	|   Owner  |                              |               |
	|          |--(C)----Authorization------->|			   |
	+----------+							  |			   |
											  |               |
	+----------+							  |               |
	|  Client  |<-(D)-Authorization Code------|               |
	|          |							  |               |
	|          |--(E)-Auth Code-------------->|               |
	|          |                              |               |
	|          |<-(F)-Access Token------------|               |
	+----------+							  +---------------+	
```

2. **Implicit**
