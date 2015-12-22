---
root: false
name: 缓存管理（Cache）
sort: 7
---

# 缓存管理（Cache）

`blade-cache` 实现简单的缓存管理。它不依赖 `Blade`，如果你也需要可以直接使用，
底层是 `FIFO`，`LFU`，`LRU` 算法模型的实现。

## 使用示例

```java
public class CacheTest {

	@Test
	public void testLRU(){
		CacheManager cm = CacheManager.getInstance();
		
		Cache<String, Object> cache = cm.newLRUCache();
		cache.set("name:1", "jack");
		cache.set("name:2", "jack2");
		
		System.out.println(cache.get("name:2"));
		
	}
	
}
```

## Hash类型数据

```java
public class CacheTest {
	
	@Test
	public void testHashCache(){
		CacheManager cm = CacheManager.getInstance();
		
		Cache<String, Object> cache = cm.newLRUCache();
		cache.hset("user:list", "a1", "123");
		cache.hset("user:list", "a2", "456");
		cache.hset("user:list", "a3", "789");
		
		System.out.println(cache.hget("user:list", "a1"));
		System.out.println(cache.hget("user:list", "a2"));
		System.out.println(cache.hget("user:list", "a3"));
		
	}
	
}
```

## 自定义设置

```java
public class CacheTest {
	
	@Test
	public void testAutoClean(){
		CacheManager cm = CacheManager.getInstance();
		// 设置定时清理的频率
		cm.setCleanInterval(1000);
		
		Cache<String, Object> cache = cm.newLRUCache();
		cache.set("name:1", "jack");
		cache.set("name:2", "jack2");
		
		System.out.println(cache.get("name:2"));
		
		try {
			Thread.sleep(3000);
		} catch (InterruptedException e) {
			e.printStackTrace();
		}
		System.out.println(cache.get("name:2"));
	}
	
}
```