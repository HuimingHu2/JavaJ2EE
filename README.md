TestSpringboot healthcare enroll notes by 20210130
=====================
# The challenge

Using Spring Boot or Go, and your database of choice (PostgreSQL, MySQL, MongoDB -- any you'd like), develop a microservice for tracking the status of enrollees in a health care program.
- Enrollees must have an id, name, and activation status (true or false), and a birth date
- Enrollees may have a phone number (although they do not have to supply this)
- Enrollees may have zero or more dependents
- Each of an enrollee's dependents must have an id, name, and birth date

The application we will be building will need to be able to do these things:
- Add a new enrollee
- Modify an existing enrollee
- Remove an enrollee entirely
- Add dependents to an enrollee
- Remove dependents from an enrollee
- Modify existing dependents
=============

=================20210131
load some test data before using Junit, using postman, or insert statement in mysql
--------- for example
post
http://localhost:8080/healthcare/enrollee

--------1, works
{
    "enrolleeId" : "aa",
   "name" : "John",
   "birthDate":"1989-09-04",
   "activeStatus": true,
   "hasDependents": true
}
-------- 2, works
{
    "enrolleeId" : "bb",
   "name" : "Peter",
   "birthDate":"1994-07-10",
   "activeStatus": true,
   "hasDependents": true
}

---------- 3, works
{
    "enrolleeId" : "cc",
   "name" : "Suz",
   "birthDate":"1993-12-25",
   "activeStatus": true,
   "phoneNum":"222-333-4444",
   "hasDependents": true
}
==============junit, 1/31/2021

---------------- step 01, pass
	   @Test
	   public void createEnrollee() throws Exception {
	      String uri = "/healthcare/enrollee";
	      Enrollee enrollee = new Enrollee();
	      enrollee.setEnrolleeId("aa1");
	      enrollee.setName("Albert");
	      enrollee.setActiveStatus(true);
	      enrollee.setHasDependents(false);
	      enrollee.setBirthDate(new SimpleDateFormat("yyyy-MM-dd").parse("1999-02-10"));

	      String inputJson = super.mapToJson(enrollee);
	      MvcResult mvcResult = mvc.perform(MockMvcRequestBuilders.post(uri)
	         .contentType(MediaType.APPLICATION_JSON_VALUE)
	         .content(inputJson)).andReturn();
	      
	      int status = mvcResult.getResponse().getStatus();
	      assertEquals(201, status);
	      String content = mvcResult.getResponse().getContentAsString();
	      assertEquals(content, "");
	   }
---------------- step 02, pass
	   @Test
	   public void updateEnrollee() throws Exception {
	      String uri = "/healthcare/enrollee/3";
	      Enrollee enrollee = new Enrollee();
	      enrollee.setEnrolleeId("cc");
	      enrollee.setName("Jessica");
	      enrollee.setActiveStatus(true);
	      enrollee.setHasDependents(true);
	      enrollee.setBirthDate(new SimpleDateFormat("yyyy-MM-dd").parse("1997-03-14"));
	      String inputJson = super.mapToJson(enrollee);
	      MvcResult mvcResult = mvc.perform(MockMvcRequestBuilders.put(uri)
	         .contentType(MediaType.APPLICATION_JSON_VALUE)
	         .content(inputJson)).andReturn();
	      
	      int status = mvcResult.getResponse().getStatus();
	      assertEquals(200, status);
	      String content = mvcResult.getResponse().getContentAsString();
	      assertEquals(content, "");
	   }
--------------- step 03, pass
	   @Test
	   public void deleteEnrollee() throws Exception {
	      String uri = "/healthcare/enrollees/2";
	      MvcResult mvcResult = mvc.perform(MockMvcRequestBuilders.delete(uri)).andReturn();
	      int status = mvcResult.getResponse().getStatus();
	      assertEquals(200, status);
	      String content = mvcResult.getResponse().getContentAsString();
	      assertEquals(content, "");
	   }
--------------- step 04, pass
only use Junit
--------------- step 05, pass
only use Junit
--------------- step 06, pass	
only use Junit   
----------------

------------workspace, and src
TestSpringboot_healthcare
TestSpringboot_healthcare_workspace
---------------cmd
mvn clean install
mvn spring-boot:run
=============

--------------- mysql
CREATE DATABASE enrollee;

show databases;

=========works
create table enrolleenorm(
	id INT PRIMARY KEY AUTO_INCREMENT,
   enrollee_id VARCHAR(20) DEFAULT '' NOT NULL,
   name VARCHAR(40) NOT NULL,
   active_status boolean DEFAULT FALSE NOT NULL,
   has_dependents boolean DEFAULT TRUE,
   birth_date DATE DEFAULT '1000-01-01' NOT NULL,
   phone_num VARCHAR(20)
);

-------------- step 04, application.properties
# DATASOURCE (DataSourceAutoConfiguration & DataSourceProperties)
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/enrollee?useSSL=false
spring.datasource.username=root
spring.datasource.password=pat1108

# Hibernate

# The SQL dialect makes Hibernate generate better SQL for the chosen database
spring.jpa.properties.hibernate.dialect = org.hibernate.dialect.MySQL5InnoDBDialect

# Hibernate ddl auto (create, create-drop, validate, update)
spring.jpa.hibernate.ddl-auto = update

logging.level.org.hibernate.SQL=DEBUG
logging.level.org.hibernate.type=TRACE

