---
title: Security身份认证之UserDetailsService
date: 2019-05-08 22:11:41
tags: springSecurity
categories: springSecurity
---
之前我们采用了配置文件的方式从数据库中读取用户进行登录。虽然该方式的灵活性相较于静态账号密码的方式灵活了许多，但是将数据库的结构暴露在明显的位置上，绝对不是一个明智的做法。本文通过Java代码实现UserDetailsService接口来实现身份认证。


##### 1.1 UserDetailsService在身份认证中的作用


Spring Security中进行身份验证的是AuthenticationManager接口，ProviderManager是它的一个默认实现，但它并不用来处理身份认证，而是委托给配置好的AuthenticationProvider，每个AuthenticationProvider会轮流检查身份认证。检查后或者返回Authentication对象或者抛出异常。


验证身份就是加载响应的UserDetails，看看是否和用户输入的账号、密码、权限等信息匹配。此步骤由实现AuthenticationProvider的DaoAuthenticationProvider（它利用UserDetailsService验证用户名、密码和授权）处理。包含 GrantedAuthority 的 UserDetails对象在构建 Authentication对象时填入数据。
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20180406152002371.png)

##### 1.2 配置UserDetailsService

###### 1.2.1 更改Spring-Security.xml中身份的方式，使用自定义的UserDetailsService。
```xml
<security:authentication-manager> 
	<security:authentication-provider user-service ref="favUserDetailService">     
	</security:authentication-provider>
</security:authentication-manager>
<bean id="favUserDetailService" class="com.favccxx.favsecurity.security.FavUserDetailService" />
```

######  1.2.2 新建FavUserDetailsService.java，实现UserDetailsService接口。为了降低学习的难度，这里并没有与数据库进行集成，而是采用模拟从数据库中获取用户的方式进行身份验证。示例代码如下：
```java
package com.favccxx.favsecurity.security;
import java.util.ArrayList;
import java.util.Collection;
import java.util.List;
import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;
import org.springframework.security.core.GrantedAuthority;
import org.springframework.security.core.authority.SimpleGrantedAuthority;
import org.springframework.security.core.userdetails.User;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.core.userdetails.UsernameNotFoundException;
 
public class FavUserDetailService implements UserDetailsService {
	private static final Logger logger = LogManager.getLogger(FavUserDetailService.class);  
	/**  * 根据用户名获取用户 - 用户的角色、权限等信息   */
	public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {      
		UserDetails userDetails = null;     
		try {           
			com.favccxx.favsecurity.pojo.User favUser = new com.favccxx.favsecurity.pojo.User();            
			favUser.setUsername("favccxx");        
			favUser.setPassword("favccxx");         
			Collection<GrantedAuthority> authList = getAuthorities();         
			userDetails = new User(username, favUser.getPassword().toLowerCase(),true,true,true,true,authList);     
		}catch (Exception e) {         
			e.printStackTrace();        
		}       
		return userDetails; 
	}
	/**  * 获取用户的角色权限,为了降低实验的难度，这里去掉了根据用户名获取角色的步骤     * @param    * @return   */ 
	private Collection<GrantedAuthority> getAuthorities(){        
		List<GrantedAuthority> authList = new ArrayList<GrantedAuthority>();       
		authList.add(new SimpleGrantedAuthority("ROLE_USER"));      
		authList.add(new SimpleGrantedAuthority("ROLE_ADMIN"));     
		return authList;    
	}
}

```

###### 1.2.3 启动应用服务器，只要用户名和密码不全是favccxx，就会产生下面的错误。

![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20180406152945823.png)

用户名和密码都输入favccxx，则登陆成功

![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20180406153002554.png)

##### 1.3 跟踪UserDetailsService。

身份认证的调用流程图如下，用户可下载Spring Security源代码跟踪调试。
![](https://fuzui.oss-cn-shenzhen.aliyuncs.com/img/20180406153020212.png)


<br>
本文转自:[Spring Security身份认证之UserDetailsService](https://blog.csdn.net/shehun1/article/details/45394405)
