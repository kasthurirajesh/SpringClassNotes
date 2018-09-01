## Functions of dispacher servlet ?
- It will map the URI --> Method 
- Convert the response into json format or specified format
```java 
@RequestMapping(RequestMethod= “GET” ,  uri= “/hello-world”  )
public String helloWorld{
	return “hello-world”;
}
```
#### Who is configuring the dipacher servlet ?
When we set web-starter, It internally calls the dispacher servlet.  


## Swagger :

Let's say  you have Many apis that you want to expose. For consumer they need to know the **request format and response format** for various apis. We can’t manually write the request format and response format. 

Just imagine google has many apis and it wants expose their api’s that time It really helps. 

It provides each api name, Request type ( Get/ Post / Delete ) , response type (xml / json ) and URL.

We can customize the Info that you want to show in documentation

Add the following the dependencies in pom.xml

```xml
	<!-- swagger dependencies  -->
		<dependency>
			<groupId>io.springfox</groupId>
			<artifactId>springfox-swagger2 </artifactId>
			<version>2.4.0</version>
		</dependency>

		<dependency>
			<groupId>io.springfox</groupId>
			<artifactId>springfox-swagger-ui </artifactId>
			<version>2.4.0</version>
		</dependency>
```
### Create class in base package To see the swagger documentation  
```java
package com.rajesh.restfulWebServices;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;
import springfox.documentation.swagger2.annotations.EnableSwagger2;

@Configuration
@EnableSwagger2
public class SwaggerConfig {

	@Bean
	public Docket api() {
		return new Docket(DocumentationType.SWAGGER_2);
	}
}
```
### Use the Following Links
http://localhost:8080/swagger-ui.html

### Sample Output

![screenshot from 2018-08-22 11-12-20](https://user-images.githubusercontent.com/40027047/44444934-c7f71300-a5fc-11e8-9154-21b12a16c855.png)

## Actuator
Monitoring the application like what are beans are created,Traffic trends,How many requests are 200 or 404 status for a particular method

We need HAL browser because the actuator gives the links. For browsing the links we need the HAL browser. 

### To see the monitoring options you need to enable the security 
```java
management.security.enabled=false
```
### Dependencies required are follows
```xml
<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-actuator</artifactId>
		</dependency>
		
		<dependency>
			<groupId>org.springframework.data</groupId>
			<artifactId>spring-data-rest-hal-browser</artifactId>
		</dependency>
```
We can see the health,metrics, dump, heapdump,..etc.

**Health** shows that whether the server up/down, How much disk space is free out of total space.It also shows the **database** is up/down.   
**Metrics** : Here we can see the individual service Info. How many requests are coming for counter.status.200.users.id=4the following service. And how many are 200 status and how many are 404 status. like that for each individual service we can see the info. 
```java
@GetMapping(value = "/users/{id}")
	public User findOne(@PathVariable Integer id) {
		return userService.findOne(id);
	}
```

### Sample output
counter.status.200.users.id=4  
counter.status.404.users.id=2  
Here 404 error comes because If user not present we return the 404 status.

**Trace** : It will show you the last 100 requests history. Like what is header that user passing,response,time taken ..etc. 


## Filtering 
While sending response to client we don't want to send few fields.Then we need to do filering
```java
public class SomeBean {
	
	private String field1;
	
	@JsonIgnore
	private String field2;	
	private String field3;
}

```
Now response doesnot contain the filed2. 

## Dynamic filtering
Above filtering is the static filtering.For dynamic filtering we need to create the filter as per requirement. 

```java	
@RestController
public class FilteringController {

	// field1,field2
	@GetMapping("/filtering")
	public MappingJacksonValue retrieveSomeBean() {
		SomeBean someBean = new SomeBean("value1", "value2", "value3");

		SimpleBeanPropertyFilter filter = SimpleBeanPropertyFilter.filterOutAllExcept("field1", "field2");

		FilterProvider filters = new SimpleFilterProvider().addFilter("SomeBeanFilter", filter);

		MappingJacksonValue mapping = new MappingJacksonValue(someBean);

		mapping.setFilters(filters);

		return mapping;
	}

	// field2, field3
	@GetMapping("/filtering-list")
	public MappingJacksonValue retrieveListOfSomeBeans() {
		List<SomeBean> list = Arrays.asList(new SomeBean("value1", "value2", "value3"),
				new SomeBean("value12", "value22", "value32"));

		SimpleBeanPropertyFilter filter = SimpleBeanPropertyFilter.filterOutAllExcept("field2", "field3");

		FilterProvider filters = new SimpleFilterProvider().addFilter("SomeBeanFilter", filter);

		MappingJacksonValue mapping = new MappingJacksonValue(list);

		mapping.setFilters(filters);

		return mapping;
	}

}
```
For the filtering URL we are including field1, field2.  
For the iltering-list we are including field2, field3.  

## what is jpa is trying to solve?
 we are using java, data is present in **objects** but we want to store the data in database **tables**.

how do we store data from objects to tables ?. 
Object Relational Mapping (ORM) is a functionality which is used to develop and maintain a relationship between an object and relational database by mapping an object state to database column.

![screenshot from 2018-09-01 11-11-53](https://user-images.githubusercontent.com/15109557/44942847-8aeb0780-add8-11e8-9c17-8aebfee5a1f0.png)



 ```java
 public class Task {
	 private int id;
	 private  String desc;
	 private Date targetDate;
	 private boolean isDone;
	 private List<Employee> employees; 
}
```
```sql
CREATE TABLE task
(
  id     	 INTEGER GENERATED BY DEFAULT AS IDENTITY,
  description    VARCHAR(255),
  is_done    	 BOOLEAN,
  target_date    TIMESTAMP,
  PRIMARYKEY     ( id) 
)
```
 





