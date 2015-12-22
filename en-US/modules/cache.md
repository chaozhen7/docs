---
root: false
name: Cache Manage
sort: 7
---

# Cache Manager

`blade-cache` implement a simple cache management. It does not rely on `Blade`, if you need can be used directly,
The bottom is a `FIFO`, `LFU` and `LRU` algorithm implementation of the model.

## Use Sample

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

## Hash Type Data

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

## Custom Setting

```java
public class CacheTest {
	
	@Test
	public void testAutoClean(){
		CacheManager cm = CacheManager.getInstance();
		// Automate the frequency of cleaning
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