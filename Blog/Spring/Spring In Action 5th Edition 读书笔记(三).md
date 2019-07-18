# Spring In Action 5th Edition 读书笔记 (三)



## Chapter 3 Working with data



### 1.摘录与解读

```html
	Spring JDBC support is rooted in the JdbcTemplate class. JdbcTemplate provides a means by which developers can perform SQL operations against a relational databasewithout all the ceremony and boilerplate typically required when working with JDBC.
	解读：Spring JDBC将开发者从编写大量冗余的语句中解放出来，从而提升了开发效率。倘若使用JDBC，我们除了编写业务逻辑代码外还需要创建Connection，创建Statement(PreparedStatement)，以及一系列关闭工作，与之对应的还有一系列繁琐的异常处理，而Spring JDBC则帮我们省去了这些麻烦，我们可以通过JdbcTemplate来简洁地操作数据库。
```

```html
	Although  the  interface  captures  the  essence  of  what  you  need  an  ingredient  reposi-tory to do, you’ll still need to write an implementation of IngredientRepository that uses JdbcTemplate to query the database.	
	......
	Java 8’s method references and lambdas are convenient when working with JdbcTemplate as an  alternative  to  an  explicit  RowMapper  implementation.  
	解读：使用Spring JDBC来进行DAO的话，我们需要提供一个Repository的接口，并且自行用JdbcTemplate来实现接口中的方法。JdbcTemplate的常用方法为queryForObject(),queryForList(),query(),update()等，这些方法中除了要提供一个String类型的sql语句外，往往还需要一个参数来实现数据库中的数据到Java对象的映射，这个参数经常是Class对象或是RomMapper方法。在Java 8以后，我们可以实现一个private的映射方法，然后用方法引用来将该方法作为参数。当然也可以使用匿名类的形式来实现。以下给出书中实现两种方式的例子。
```

```java
@Override
public Ingredient findOne(String id) {  
    return jdbc.queryForObject("select id, name, type from Ingredient where		 									id=?",this::mapRowToIngredient, id);
}
private Ingredient mapRowToIngredient(ResultSet rs, int rowNum) 
    				throws SQLException {  
    return new Ingredient(rs.getString("id"),
                          rs.getString("name"),      						    							  Ingredient.Type.valueOf(rs.getString("type")));
}
```

```java
@Override
public Ingredient findOne(String id) {
    return jdbc.queryForObject(
    	"select id, name, type from Ingredient where id=?",      							new RowMapper<Ingredient>() {
        	public Ingredient mapRow(ResultSet rs, int rowNum)
                throws SQLException {
                return new Ingredient(
                    rs.getString("id"),
                    rs.getString("name"),              			        	                   			   Ingredient.Type.valueOf(rs.getString("type")));
            	};
        }, id);
}
```

```html
	If there’s a file named schema.sql in the root of the application’s classpath, then the SQL in that file will be executed against the database when the application starts.
	......
	Fortunately,Spring Boot will also execute a file named data.sql from the root of the classpath when the application starts.
	解读：我们在src/main/resources/下放置的名为schema.sql的文件将在应用启动时被自动解析从而执行其中的SQL语句，该文件的主要目的是用以创建数据库的模式。而在src/main/resources/下放置的名为data.sql的文件也会被自动解析执行，该文件的主要目的是往应用创建的数据库中插入初始化数据。
```

```html
	SimpleJdbcInsert  has  a  couple  of  useful  methods  for  executing  the  insert:execute() and executeAndReturnKey(). Both accept a Map<String,Object>, where the map keys correspond to the column names in the table the data is inserted into.The map values are inserted into those columns.
	解读：Spring JDBC实现数据插入的方式主要有两种，一种是使用update()方法，这种方法十分繁杂并不推荐；而更为推荐的是使用SimpleJdbcInsert,下面给出创建SimpleJdbcInsert的示例代码。有了SimpleJdbcInsert以后，我们就可以使用它的execute()和executeAndReturnKey()方法来执行插入操作。其中后者将返回一个Number对象，通过调用该对象的longValue()方法我们可以得到插入对象的ID值。这两个方法都接收一个Map<String,Object>作为参数，因此我们需要提前将要存储的数据转为这种形式。书中作者使用了Jaskson的ObjectMapper的converValue()来实现快速地将领域对象转为Map,使用这种方法的前提是领域对象的所有字段名与数据库列名相同。
```

```java
//创建SimpleJdbcInsert
@Autowired  
public JdbcOrderRepository(JdbcTemplate jdbc) {    
    this.orderInserter = new SimpleJdbcInsert(jdbc)        	
        .withTableName("Taco_Order")        
        .usingGeneratedKeyColumns("id");
    
    this.orderTacoInserter = new SimpleJdbcInsert(jdbc)        
        .withTableName("Taco_Order_Tacos"); 
    
    this.objectMapper = new ObjectMapper();
}
```

```java
//使用Jakson的ObjectMapper来创建Map(并不推荐)
private long saveOrderDetails(Order order) {    
    @SuppressWarnings("unchecked")    
    Map<String, Object> values =        
        objectMapper.convertValue(order, Map.class);    
    values.put("placedAt", order.getPlacedAt());
    
    long orderId =        
        orderInserter            
       		.executeAndReturnKey(values)            
        	.longValue();    
    	return orderId;
}
```

```html
	One  of  the  most  interesting  and  useful  features  provided  by  Spring  Data  for  all  of these projects is the ability to automatically create repositories, based on a repository specification interface.
	解读：Spring Data的众多项目(主要有：Data-JPA,Data-MongoDB,Data-Neo4j,Data-Redis,Data-Cassandra)都有一个共同的特征是：我们只需要提供Repository接口，它将自动帮我们实现。
```



### 2.重要注解总结

**@ModelAttribute：**

​	Spring MVC提供的注解，标注在控制器的方法上，表示该方法将会在Controller的每个方法执行前被执行，如果方法返回值并不是void，则将返回值直接注入Model中，默认属性名为返回值类型名首字母小写，也可以通过@ModelAttribute(value=)来指定属性名；该注解也可以标注在方法的形参上，表示该形参的数据将从Model中获取(这种情况下往往存在用@ModelAttribute标注的方法，具体见下示例代码)，而不会与请求参数相绑定；该注解也能标注在方法的返回值上，表示将返回值注入Model。

```java
@Controller 
public class HelloWorldController { 
    @ModelAttribute("user") 
    public User addAccount() { 
        return new User("jz","123"); 
     } 

    @RequestMapping(value = "/helloWorld") 
    public String helloWorld(@ModelAttribute("user") User user) { 
           user.setUserName("jizhou"); 
           return "helloWorld"; 
    } 
}
```

**@SessionAttribute(types=,value=)：**

​	Spring MVC提供的注解，标注在类上,会将该类中所有types包含的类型的对象以及value包含的对象注入Session中。



本章中涉及了大量Spring Data JPA的注解，在此略作总结。

**@Entity：**

​	标注在类上，表明该类是一个JPA实体类。

**@Table(name=)：**

​	标注在类上，往往与@Entity并列使用，将JPA实体类与数据库中的表对应。

**@Column(name=)：**

​	标注在类的字段上，@Table默认类的字段名即表的列名，可以用@Column来指定类的某个字段来映射表的某一列。

**@Id：**

​	标注在类的字段上，指定类的该字段是表的主键。

**@GeneratedValue(strategy=)：**

​	标注在类的字段上，和@Id一起使用来指定主键的生成策略，strategy有四个选项：GenerationType.AUTO，GenerationType.SEQUENCE，GenerationType.IDENTITY，Generation.TABLE。

**@PrePersist:**

​	标注在类的方法上，在一个新实体持久化前调用该方法，一般用来存储实体中不改变的信息。

**@ManyToMany(targetEntity=)：**

​	标注在类的集合字段上，表示该字段与另一张表是多对多关系。

**@Query(value=,nativeQuery=)：**

​	标注在JPA的Repository接口的方法上，用SQL语句或JPQL，SPEL来进行查询，nativeQuery为true时表示使用sql语句。JPA提供两种方式操作数据库，一种是用JPA规定的接口方法名，另一种是用@Query使用查询语句进行操作。

**@Modifying：**

​	标注在JPA的Repository接口的方法上，和@Query一起使用，表示该方法起到修改作用。

**@Param：**

​	标注在标记了@Query的方法的形参上，表示将该参数传入查询语句用于查询。