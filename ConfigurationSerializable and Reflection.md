### 實作ConfigurationSerializable以及熟悉Reflection
用`config`存取物品時很常用到這兩個`method`：
* `config.set(path, item);`
* `ItemStack item = config.getItemStack(path);`

那我就在想
> 如果自己寫的物件也用這種方法存取我就不用自己寫`load()`和`save()`了。

於是翻了一下[`ItemStack`][item_doc]的文件發現它實作了[`interface ConfigurationSerializable`][cs_doc]，這樣就可以用`serialize`將物件存到`config`裡，然後用它給的方法將`config`裡物件`deserialize`拿出來，**有夠方便...**，以下就來實作。

[item_doc]: https://hub.spigotmc.org/javadocs/spigot/org/bukkit/inventory/ItemStack.html
[cs_doc]: https://hub.spigotmc.org/javadocs/spigot/org/bukkit/configuration/serialization/ConfigurationSerializable.html
***
首先我先簡單寫了一個類別`Human`
```Java
public class Human {

    private String name;
    private int age;
    
    public Human(String name, int age){
      this.name = name;
      this.age = age;
    }
    
    public String getName(){
      return name;
    }
    
    public int getAge(){
      return age;
    }
    
}
```
然後將它實作`ConfigurationSerializable`  

    public class Human implements ConfigurationSerializable

這邊會發現它會要你實作`serialize()`這個方法，有兩種做法：
* 一個一個填
```Java
@Override
public Map<String, Object> serialize() {
  //宣告一個空的map
  Map<String, Object> map = new HashMap<>();
    
  //將成員存到map
  map.put("name",name);
  map.put("age",age);
    
  return map;
}
```
* 用`reflection`抓
```Java
@Override
public Map<String, Object> serialize() {
  //宣告一個空的map
  Map<String, Object> map = new HashMap<>();
    
  try {
    //取得所有成員
    for (Field f : this.getClass().getDeclaredFields()) 
      //將成員存到map
      map.put(f.getName(), f.get(this));
  }catch(Exception e) {
    e.printStackTrace();
  }
    
  return map;
}
```
這邊我才發現用`reflection`來抓有多麼方便，假如之後我新增了一個身高成員`private double height`，那麼用第一個方法的話就要再寫`map.put("height",height)`，假如之後新增更多成員就要再寫，很麻煩...；但是用第二種方式就不用再動`serialize()`了。
***
序列化寫好了之後，**一定**要有一個反序列化的方法(沒有的話會丟錯誤)，`ConfigurationSerializable`提供了幾種方法，以下選一個就好：
* **靜態方法 `deserialize(Map<String, Object>)`**

      public static Human deserialize(Map<String, Object> map)

* **靜態方法 `valueOf(Map<String, Object>)`**

      public static Human valueOf(Map<String, Object> map)
* **建構子**

      public Human(Map<String, Object> map)

這邊我就用**建構子**來實作反序列化，跟上面一樣，這邊有兩種做法：
* 一個一個寫
```Java
public Human(Map<String, Object> map) {
  this.name = (String) map.get("name");
  this.age = (Integer) map.get("age");
}
```
* 用`reflection`寫
```Java
public Human(Map<String, Object> map) {
  try {
    for (String key : map.keySet()) {
      if (key.equalsIgnoreCase("=="))
        continue;
      //取得指定成員
      Field f = this.getClass().getDeclaredField(key);
      f.setAccessible(true);
      //設值
      f.set(this, map.get(key));
    }
  }catch(Exception e) {
    e.printStackTrace();
  }
}
```
用`reflection`寫會發現有這一行`if (key.equalsIgnoreCase("=="))`是因為從`config`裡拿出來的是一個`map`它會包含這個`key(==)`所以要寫這行，不然會丟沒有這個成員的錯誤；用其他方法的話就依樣畫葫蘆就好。
***
最後的最後，一個非常重要的：**必須要註冊這個`Class`**，寫在插件開啟`onEnable()`中即可

    public void onEnable() {
      ConfigurationSerialization.registerClass(Human.class);
    }

這樣這個類別就可以就可以使用`config.set(path, human)`存物件，`Human human = (Human) config.get(path)`取物件了。
***
#### 完整Human.class

```Java
public class Human implements ConfigurationSerializable {
  
    private String name;
    private int age;
    
    public Human(String name, int age){
      this.name = name;
      this.age = age;
    }
    
    public Human(Map<String, Object> map) {
      try {
        for (String key : map.keySet()) {
          if (key.equalsIgnoreCase("=="))
            continue;
          //取得指定成員
          Field f = this.getClass().getDeclaredField(key);
          f.setAccessible(true);
          //設值
          f.set(this, map.get(key));
        }
      }catch(Exception e) {
        e.printStackTrace();
      }
    }
    
    public String getName(){
      return name;
    }
    
    public int getAge(){
      return age;
    }
    
    @Override
    public Map<String, Object> serialize() {
      //宣告一個空的map
      Map<String, Object> map = new HashMap<>();
    
      try {
        //取得所有成員
        for (Field f : this.getClass().getDeclaredFields()) {
          //將成員存到map
          map.put(f.getName(), f.get(this));
        }
      }catch(Exception e) {
        e.printStackTrace();
      }
    
      return map;
    }
}
```
#### 測試
1. 存`Human`物件到`config`，這裡使用`/sethuman`指令去存
```Java
Human h = new Human("class70635", 24);
getConfig().set("human_save", h);
saveConfig();
```
2. 此時`config.yml`  
```
human_save:
  ==: Human
  name: class70635
  age: 24
```
3. 用`/gethuman`指令取得`Human`物件
```Java
Human h = (Human) getConfig().get("human_save");
p.sendMessage(h.getName() + ", " + h.getAge() + "歲");
```
4. 輸出
```
class70635, 24歲
```








