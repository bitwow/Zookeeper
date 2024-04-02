

**ZooKeeper**


**ZooKeeper** – это высокопроизводительная служба координации для распределенных приложений. Он предоставляет простой и надежный способ для обеспечения синхронизации и управления состоянием в распределенных системах. Если же объяснять простыми словами, то ZooKeeper – это программа-оператор, направляющая и следящая за актуальностью конфигурационных данных распределенных приложений.

**Структура иерархии данных в ZooKeeper**

ZooKeeper представляет собой распределенное хранилище ключ-значение (key-value), где пространство ключей образует древовидную иерархию как в файловой системе, а значения могут содержаться в любом узле иерархии (только в листьях).

**Примеры данных в ZooKeeper:**
```
1. Пример конфигурационных параметров:
   
   /config
       /app1
           host=192.168.1.1
           port=8080
       /app2
           host=192.168.1.2
           port=8081
  

2. **Пример состояний узлов**:
   
   /nodes
       /node1
           status=active
       /node2
           status=inactive
 
```

**Протокол ZAB (ZooKeeper Atomic Broadcast)**

Протокол ZAB (ZooKeeper Atomic Broadcast) играет ключевую роль в обеспечении согласованности данных и управлении состоянием в ZooKeeper, обеспечивая надежное и эффективное функционирование распределенных систем.


**Основные команды управления ZooKeeper**

1. **create**: Создание нового znode в ZooKeeper.
   ```java
   import org.apache.zookeeper.*;

   public class ZooKeeperExample {
       private static final String CONNECT_STRING = "localhost:2181";
       private static final int SESSION_TIMEOUT = 3000;

       public static void main(String[] args) throws Exception {
           ZooKeeper zooKeeper = new ZooKeeper(CONNECT_STRING, SESSION_TIMEOUT, new Watcher() {
               @Override
               public void process(WatchedEvent event) {
                   // Обработка событий
               }
           });

           // Создание znode
           String path = "/exampleNode";
           byte[] data = "Example Data".getBytes();
           CreateMode mode = CreateMode.PERSISTENT;
           zooKeeper.create(path, data, ZooDefs.Ids.OPEN_ACL_UNSAFE, mode);

           // Закрытие соединения с ZooKeeper
           zooKeeper.close();
       }
   }
   ```

2. **get**: Получение данных из существующего znode.
   ```java
   byte[] data = zooKeeper.getData("/exampleNode", false, null);
   ```

3. **set**: Установка новых данных для существующего znode.
   ```java
   zooKeeper.setData("/exampleNode", "New Data".getBytes(), -1);
   ```

4. **delete**: Удаление существующего znode.
   ```java
   zooKeeper.delete("/exampleNode", -1);
   ```

5. **ls**: Просмотр списка дочерних znode для указанного пути.
   ```java
   List<String> children = zooKeeper.getChildren("/config", false);
   ```

6. **exists**: Проверка существования znode по указанному пути.
   ```java
   Stat stat = zooKeeper.exists("/exampleNode", false);
   ```

7. **getAcl**: Получение списка прав доступа для указанного znode.
   ```java
   List<ACL> acl = zooKeeper.getACL("/exampleNode", new Stat());
   ```

8. **setAcl**: Установка новых прав доступа для указанного znode.
   ```java
   zooKeeper.setACL("/exampleNode", ZooDefs.Ids.OPEN_ACL_UNSAFE, -1);
   ```

**Блокировки в ZooKeeper**

Блокировки в ZooKeeper позволяют клиентам координировать доступ к общим ресурсам в распределенной среде. Клиенты создают блокировку, создавая временный znode в ZooKeeper. При попытке захватить блокировку, клиент создает временный znode в уникальном пути. Если это происходит успешно, клиент захватывает блокировку. В случае конкуренции за блокировку, только один клиент успешно создает znode и захватывает блокировку. Остальные клиенты ожидают освобождения блокировки. ZooKeeper использует алгоритм FIFO для разрешения конфликтов при захвате блокировок. Освобождение блокировки происходит путем удаления соответствующего znode. Клиенты могут мониторить блокировки, подписываясь на уведомления об изменениях, чтобы мгновенно реагировать на освобождение или захват блокировки другими клиентами. Блокировки в ZooKeeper полезны для согласования доступа к общим ресурсам и управления операциями в распределенной среде, обеспечивая безопасность и согласованность доступа к ресурсам.
