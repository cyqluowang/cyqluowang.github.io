title: Redis
date: 2017-02-22 17:47:34
tags: spring
---
1、添加依赖配置
```
 <dependency>
     <groupId>org.springframework.boot</groupId>
     <artifactId>spring-boot-starter-redis</artifactId>
 </dependency>
```
2、编写配置文件,设置redis的一些配置
```
@Configuration
@EnableCaching//重要  不能忘记， 不然@Cacheable无效
@PropertySource("classpath:config/redis.properties")
public class RedisConfig  extends CachingConfigurerSupport{
    @Autowired
    private Environment env;//读取redis.properties中设置的变量
    @Bean
    public RedisConnectionFactory redisConnectionFactory() {
        JedisPoolConfig jedisPoolConfig = new JedisPoolConfig();
        JedisConnectionFactory jedisConnectionFactory = new JedisConnectionFactory();//连接配置
        jedisConnectionFactory.setHostName(env.getProperty("redis.host").trim());//主机
        jedisConnectionFactory.setPort(Integer.parseInt(env.getProperty("redis.port").trim()));//端口
        jedisConnectionFactory.setPassword(env.getProperty("redis.password").trim());//密码
        jedisConnectionFactory.setUsePool(true);//使用池
        jedisConnectionFactory.setPoolConfig(jedisPoolConfig);
        return jedisConnectionFactory;
    }


    /**
     * 重写生成key的策略
     * 包名+类名+方法名+参数
     * @return
     */
    @Override
    @Bean
    public KeyGenerator keyGenerator() {
        return new KeyGenerator() {
            @Override
            public Object generate(Object target, Method method, Object... params) {
                StringBuilder sb = new StringBuilder();
                sb.append(target.getClass().getName());
                sb.append(method.getName());
                for (Object obj : params) {
                    sb.append(obj.toString());
                }
                return sb.toString();
            }
        };
    }

    /**
     * 管理缓存
     * values 缓存存储的集合名  就是这个值存在哪里
     * 设置了value  在使用后就会同步过期时间。
     * 否则采用默认过期时间
     * @param redisTemplate
     * @return
     */
    @SuppressWarnings("rawtypes")
    @Bean
    public CacheManager cacheManager(RedisTemplate redisTemplate) {
        RedisCacheManager rcm = new RedisCacheManager(redisTemplate);
        //设置缓存过期时间
         rcm.setDefaultExpiration(6000);//秒
        //设置value的过期时间
        Map<String,Long> map=new HashMap();
        map.put("valuetest",6L);
        rcm.setExpires(map);
        return rcm;
    }


    /**
     * RedisTemplate配置
     * @param factory
     * @return
     */
    @Bean
    public RedisTemplate<String, String> redisTemplate(RedisConnectionFactory factory) {
        StringRedisTemplate template = new StringRedisTemplate(factory);
        Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
        ObjectMapper om = new ObjectMapper();
        om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
        jackson2JsonRedisSerializer.setObjectMapper(om);
        template.setValueSerializer(jackson2JsonRedisSerializer);
        template.afterPropertiesSet();
        return template;
    }
}

```

3、使用，方式一（代码使用）
```
@Repository
public class RedisBaseDao {

    @Autowired
    RedisTemplate redisTemplate;//设置过期时间，如果不需要不用注入

    //实例化ValueOperations时需要名为redisTemplate的实例，这里根据名称注入，byName
    @Resource(name="redisTemplate")
    protected ValueOperations<Serializable, Object> valueOperations;


    //设置，缓存
    public void addValue(String key, Object value){
        valueOperations.set(key, value);
        //设置过期时间
        redisTemplate.expire(key,10, TimeUnit.SECONDS);

    }
    //缓存取值
    public Object getValue(String key){
        return valueOperations.get(key);
    }
}
```

4、使用方式二（注解使用）
```
@Cacheable(value ="valuetest")
    public String getCacheableTest(String i){
        System.out.println("运行么？。。。。。。。");
        return "我是缓存的测试，不知道成功不成功";
    }
```
## 注解说明
1、同一个类中调用不会有缓存的效果，要不同的类
2、自动会取，设置值。
3、 value、cacheNames：两个等同的参数（cacheNames为Spring 4新增，作为value的别名），用于指定缓存存储的集合名。由于Spring 4中新增了@CacheConfig，因此在Spring 3中原本必须有的value属性，也成为非必需项了
 4、 key：缓存对象存储在Map集合中的key值，非必需，缺省按照函数的所有参数组合作为key值，若自己配置需使用SpEL表达式，比如：@Cacheable(key = "#p0")：使用函数第一个参数作为缓存的key值，更多关于SpEL表达式的详细内容可参考官方文档

5、 @CacheConfig：主要用于配置该类中会用到的一些共用的缓存配置。在这里@CacheConfig(cacheNames = "users")：配置了该数据访问对象中返回的内容将存储于名为users的缓存对象中，我们也可以不使用该注解，直接通过@Cacheable自己配置缓存集的名字来定义。

6、 @Cacheable：配置了findByName函数的返回值将被加入缓存。同时在查询时，会先从缓存中获取，若不存在才再发起对数据库的访问。该注解主要有下面几个参数：

  7、 condition：缓存对象的条件，非必需，也需使用SpEL表达式，只有满足表达式条件的内容才会被缓存，比如：@Cacheable(key = "#p0", condition = "#p0.length() < 3")，表示只有当第一个参数的长度小于3的时候才会被缓存，若做此配置上面的AAA用户就不会被缓存，读者可自行实验尝试。
 8、 unless：另外一个缓存条件参数，非必需，需使用SpEL表达式。它不同于condition参数的地方在于它的判断时机，该条件是在函数被调用之后才做判断的，所以它可以通过对result进行判断。
9、 keyGenerator：用于指定key生成器，非必需。若需要指定一个自定义的key生成器，我们需要去实现org.springframework.cache.interceptor.KeyGenerator接口，并使用该参数来指定。需要注意的是：该参数与key是互斥的

10、 cacheManager：用于指定使用哪个缓存管理器，非必需。只有当有多个时才需要使用
 11、 cacheResolver：用于指定使用那个缓存解析器，非必需。需通过org.springframework.cache.interceptor.CacheResolver接口来实现自己的缓存解析器，并用该参数指定。

除了这里用到的两个注解之外，还有下面几个核心注解：

- @CachePut：配置于函数上，能够根据参数定义条件来进行缓存，它与@Cacheable不同的是，它每次都会真是调用函数，所以主要用于数据新增和修改操作上。它的参数与@Cacheable类似，具体功能可参考上面对@Cacheable参数的解析
- @CacheEvict：配置于函数上，通常用在删除方法上，用来从缓存中移除相应数据。除了同@Cacheable一样的参数之外，它还有下面两个参数：

    - allEntries：非必需，默认为false。当为true时，会移除所有数据
    - beforeInvocation：非必需，默认为false，会在调用方法之后移除数据。当为true时，会在调用方法之前移除数据。

