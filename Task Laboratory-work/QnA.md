# QnA

## Содержание 

- [Apache Spark](#apache-spark)
- [Hive](#hive)
- [MapReduce](#mapreduce)
- [Потоковая обработка (Apache Storm)](#потоковая-обработка-apache-storm)
- [Архитектуры BigData решений](#архитектуры-bigdata-решений)


## Apache Spark

[содержание](#)

### С чего начать первое знакомство с Apache Spark?

Рекомендуется начать с установки IDEA Community Edition, создания sbt проекта и подключения Apache Spark библиотеки последней версии https://mvnrepository.com/artifact/org.apache.spark/spark-core. В среде разработки вы можете реализовать классическое приложение, создать scratch файл или запустить scala консоль для более интерактивной разработки.

После освоения Spark команд на локальных данных, отработайте навыки импорта/экспорта данных в распределённую файловую систему и запуска Apache Spark приложений на одной из доступных виртуальных машин с Hadoop экосистемой:
- https://mapr.com/try-mapr/sandbox/,
- https://www.cloudera.com/downloads/hortonworks-sandbox/hdp.html 

или на одном из серверов в облаке:
- https://docs.microsoft.com/ru-ru/azure/hdinsight/spark/apache-spark-overview,
- https://aws.amazon.com/ru/emr/features/spark/,
- https://cloud.google.com/learn/what-is-apache-spark.


В подготовленной администратором среде вам должны быть доступны команды `spark-shell` для интерактивного программного взаимодействия, `spark-shell` для запуска задачи, `spark-sql` для интерактивного выполнения Spark SQL запросов. Также доступен `spark-class` скрипт, который используется всеми вышеперечисленными командами, для более тонкой настройки запуска. 



### Как подключить Apache Spark библиотеку к проекту на Java, Scala?

Как и любую другую библиотеку в зависимости от менеджера зависимостей в конфигурационном файле. Например, для Maven это будет файл `maven.conf`. Существуют сайты репозитариев с функцией поиска библиотек, например mvnrepository.com. На нём на странице найденной библиотеки приводится инструкция о том, как её добавить. 

###  Какой командой можно загрузить текстовый файл в Spark?

```
sc.textFile(“/user/user1/example.txt”)
```
### Какие виды источников данных могут быть использованы в Spark?

В первую очередь Spark предназначен для обработки файлов в DFS (распределённой файловой системе). К нему также можно подключить различные облачные хранилища. Для тестирования на локальной машине возможно обращение к локальной файловой системе с префиксом `file://` в пути. В Spark Streaming возможно использование коннекторов с системами очередей, например к Apache Kafka и реляционным базам данных. 

Более полный список из документации: Connecting to SQL Databases using JDBC, Amazon Redshift, Amazon S3, Amazon S3 Select, Azure Blob Storage, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure Cosmos DB, Azure SQL Data Warehouse, Cassandra, Couchbase, ElasticSearch, Images, Import Hive Tables, MongoDB, Neo4j, Oracle, Avro Files, CSV Files, JSON Files, LZO Compressed Files, Parquet Files, Redis, Riak Time Series, Snowflake, Zip Files, Apache Kafka, Amazon Kinesis, Optimized S3 File Source with SQS, Azure Event Hubs, Databricks Delta Tables, Read and Write Streaming Avro Data with DataFrames.

###  С каким типом данных (c какой абстракцией) работает Spark?

Resilient Distributed Dataset (RDD), DataSet, DataFrame. 

###  Как указать в качестве источника файл на локальной файловой системе?

В пути к файлу должен присутствовать префикс `file://` (указание схемы “file” в формате URI).

### Какие два вида операций доступны в Apache Spark? В чём их различие?

Операции трансформаций и операции действия (Операции, которые только объявляют какие действия с данными нужно сделать и операции, которые обязательно приводят к запуску расчётов, в том числе действий, от которых такая операции зависит). 

###  К какому типу операций относятся функции высшего порядка map, filter, reduce?

К трансформациям, за исключением reduce.

###  Что является моделью описания обработки данных?

Направленный ациклический граф (DAG).

### Какие функции доступны для сохранения данных?

Из RDD — saveAsObjectFile, saveAsTextFile. Из DataFrame и DataSet — write.

### Чем определяется распределение данных по кластеру?

Количеством, размером файлов и размером блока в распределённой файловой системе. При обработке указанным параллелизмом операции.

### Как распределён список размера N на M узлах с указанием параллелизма 3?

Наверное, по N/M элементов. При чём здесь параллелизм.

### Можно ли реализовать итеративный алгоритм на Apache Spark?

Да, это одна из мотиваций создания фреймворка.

### Чем отличаются функции join и union?

Join работает со списками пар ключ/значение, семантика операции требует наличие идентификатора значения. Uninon не требует допущений относительно элементов коллекции, работает с коллекциями как с мультимножествами, возвращая мультимножество (не убирает дубликаты). 

### Является ли результат выполнения операции union множеством?

Нет, операция ведёт себя как объединение мультимножеств или как конкатенация списков. Дубликаты не убираются.

### Когда следует использовать кэширование?

Когда посчитанные на определённом этапе данные в DAG будут использованы далее в нескольких местах.

### Какие виды хранения существуют для распределённых данных?

Непонятный вопрос. (in memory, ...)

### Каким способом можно сгруппировать данные?

С помощью функций `aggregate`, `reduce`, `fold`.

### Чем отличаются функции aggregate от reduce?

Тип возвращаемого значения может отличаться от типа входных значений.

### Какая трансформация будет выполняться дольше groupBy->reduce или reduceByKey? Почему?

Трансформация groupBy->reduce будет выполняться дольше, так как фреймворк не будет выполнять частичную редукцию до передачи данных по сети. Это приведёт к большему объёму переданных по сети данных и большей нагрузке reduce обработчиков.

### Где (на каком узле) выполняется операция collect?

Операция выполняется на driver узле, с которого была запущена задача, например посредством `spark-submit` или `spark-shell`.

### Приведите пример, в котором нужно использовать flatMap.

В классической задаче подсчёта частоты встречаемости слов в тексте этап создания слов из строк файла реализуется с помощью  flatMap. `“text.flatMap(line => line.split(“ ”)).map(word => (word, 1)).reduceByKey((a, b) => a + b)”`

### На каких узлах и в каком количестве выполняется функция, переданная в mapPartitions?

Функция выполняемая для одной партиции гарантированно выполняется на одном узле в одном Java процессе.

### Что произойдёт при выключении одного из узлов при обработке map операции? В середине обработки конвейера из трансформаций?

Будет выполнено несколько попыток перезапуска.

### Как поведёт себя система в случае отказа узла при установленном флаге checkpoint?

Оператор, после которого вызвана функция checkpoint, будет восстанавливать состояние из контрольной точки, а не путём пересчёта данных с нулевой отметки.

### Каким образом получить список пар ключ-значение, отсортированный по ключу?

```
list.sortBy(x => x._1)
```

### Чем отличается операция fold от reduce в Spark?

В fold можно добавить начальное значение.

### Как выглядит простейшая программа на Apache Spark?

```
sc.map(x=>x+1).take(5) ?
```

### Как объявить анонимную функцию? 

```
e => print(e) или (a,b) => a + b
```

### Для чего используются анонимные функции и какими свойствами они обладают?

Функции без имени в scala обладают теми же возможностями, что и обычные функции с именами. Обычно используются для коротких выражений, выполнение которых производится не главным потоком или отложено во времени. В Spark разделение между описанием действием и тем, как оно будет вычисляться присутствует естественным образом в DAG.

### Что такое замыкание?

Это функция, в которой происходит обращение к переменным лексического контекста объявленными за пределами тела функции. В этом случае функция “захватывает” переменную и должна при передаче по сети или сохранении на диск сериализоваться вместе с данными переменной.

### Где выполняется пользовательская функция в локальном режиме и в режиме выполнения на кластере?

Функция в локально режиме выполняется в JVM driver процесса, в распределённом режиме рабочий процесс выполнения определяется планировщиком Spark.

### Какие виды процессов существуют в Spark кластере?

Существует 2 вида процессов: driver и worker.

### Верно ли, что переменная аккумулятор может быть использована для суммирования результатов, полученных в процессе выполнения задачи?

Да. Пример `sc.parallelize(Array(1,2,3,4)).foreach(x => accum += x)` .

### Можно ли считывать переменную аккумулятор на рабочих узлах?

Нет. Аккумулирующую переменную можно считать только в driver процессе.

### Где выполняется команды вывода для rdd.map(println), rdd.foreach(println), rdd.take(5).foreach(println)?

Первая команда выполнится, если будет вызвана операция-действие, вторая на разных узлах, где лежат данные, третья соберёт и напечатает данные в driver.

### Каким способом могут быть разосланы данные, например словарь, по всем узлам для дальнейшего использования в функциях трансформации?

С помощью broadcast переменной. `val broadcastVar = sc.broadcast(Array(1,2,3)) `



## Hive

[содержание](#)

### Какие типы таблиц существуют в Hive?

Существует два типа таблиц: управляемая (managed) и внешняя (external). Hive управляет и данными, и схемой управляемой таблицы. Данные внешней таблицы находятся за пределами ответственности Hive.

### Подходит ли Hive роль OLTP (обработка транзакций в реальном времени)?

Нет, Hive не даёт возможности вставки и обновления на уровне строк. И, следовательно, обрабатывать данные в реальном времени.

### Можно ли переименовывать таблицы после создания?

Да. `ALTER TABLE old_name RENAME TO new_name`.

### Можно ли изменить тип данных колонки?

Да, используя параметр `REPLACE в ALTER TABLE. ALTER TABLE table_name REPLACE COLUMNS ...`

### Дайте определение metastore.

Это реляционная база данных для хранения метаданных таблиц, партиций, hive баз данных.

### Для чего писать свой SerDe класс?

В зависимости от характера данных пользователя встроенные SerDe реализации могут быть неподходящими для их формата.

### Для чего создавался Hive? Какую роль он играет в экосистеме Hadoop?

Hive является интерфейсом к данным в HDFS, позволяющим выполнять подобные SQL запросы к данным на языке HiveQL. Его используют для получения и анализа данных.

### В какой директории по умолчанию хранятся табличные данные?
```
hdfs://namenode_server/user/hive/warehouse
```

### В каких трёх режимах может быть запущен Hive?
Локальном (Local), Распределённом (Distributed), Псевдораспределённом (Pseudodistributed)

### Существует ли понятие типа данных в Hive?
Да, например `float`, `int`, `ARRAY`.

### Существует ли тип данных для даты?
Да, `TIMESTAMP`. Дата хранится в формате `java.sql.timestamp`.

### Какие составные типы данных есть в Hive?
`ARRAY`, `MAP`, `STRUCT`

### Можем ли мы запустить линукс консольную команду из Hive интерпретатора?
Используя восклицательный знак. Например, “!pwd”

### Для чего нужны Hive переменные?
Через переменные вы можете передавать значения в Hive запросы при их запуске.

### Могут ли запросы выполняться из скриптов? Как?
```
hive> source /path/file.hql
```

### Какую роль играет файл `_hiverc` в Hive?

В файле хранится список комманд, запускаемых при старте интерпретатора Hive. Например, там может быть установлен строгий режим выполнения задачи (strict mode).

### Какие разделители используются по умолчанию для разграничения строк и столбцов?
Для строк символ новой строки “\n”, для столбцов (полей) символы “\001”, “\002”, “\003”.

### В какой момент происходит валидация схемы - во время чтения и/или во время записи?
Только время чтения.

### Можно ли командах SHOW использовать шаблоны поиска? 
Да, `SHOW DATABASES LIKE ‘p.*’`

### Как вывести все базы данные Hive, начинающиеся с буквы ‘p’?
```
SHOW DATABASES LIKE ‘p.*’
```

### Для чего используется команда USE?
Для выбора текущей базы данных, для которой будут выполняться последующие запросы.

### Как удалить `DBPROPERTY`?
Удалить `DBPROPERTY` нельзя.

### Что означает строка `“set hive.mapred.mode = strict;”` ?
Она переключает MapReduce задачи в строгий режим. В строгом режиме запросы не могут выполняться над партиционированными таблицами без слова WHERE. Это предотвращает выполнение больших задач длительное время.

### Каким образом проверить существование конкретной партиции?
```
SHOW PARTITIONS table_name PARTITION(partition_column=’partition_value’)
```

### Какие Java классы отвечают за кодирование данных в файлы, хранящих таблицы Hive?
`org.apache.hadoop.mapred.TextInputFormat`

### Какие Java классы отвечают за раскодирование данных в файлы, хранящих таблицы Hive?
`org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat`

### Что произойдет при попытке удаления таблицы с выражением IF EXISTS?
Hive выкинет ошибку, если таблица не существует.

### Что произойдёт с данными, если поменять путь к партиции таблицы?
Данные останутся в прежнем место, их нужно перемещать отдельной командой.

### Как вставить дополнительные столбцы в таблицу перед существующей колонкой?
```
ALTER TABLE table_name CHANGE COLUMN new_col INT BEFORE x_col
```

### Экономит ли место архивированная таблица в HDFS?
Нет, только уменьшает нагрузку на NameNode

### Можно ли исключить партицию таблицы из результата запроса?
Да, выставив свойство `ENABLE OFFLINE в ALTER TABLE`.

### Каким образом при загрузке данных в Hive указать путь к HDFS файлу или к локальному файлу линукс системы.
Для указания локального файла требуется дополнительное выражение local.

### Что делает следующий запрос `“INSERT OVERWRITE TABLE employees PARTITION (country, state) SELECT …, se.cntry, se.st FROM stage_employees se;”`?
Он создаёт раздел для таблицы employees со значениями из колонок SELECT выражения.

### Что такое генерирующая функция в Hive?
Генерирующая функция принимает один столбец и разворачивает его в несколько.

### В чём разница между LIKE и RLIKE операторами Hive?
`LIKE` ведёт себя аналогично обычному SQL LIKE. Например, `street_name like ‘%Chi’`. RLIKE позволяет использовать регулярные выражения. Например, `street_name RLIKE ‘.*(Chi|Oho).*’`.

### Можно ли выполнить декартово произведение двух таблиц в Hive.
Нет. Но почему?

### Как преобразовать строку “51.2” в число с плавающей точкой?
```
cast(price as FLOAT)
```

### Может ли имя представления (view) совпадать с именем таблицы?
Нет, имена таблиц и представлений уникальны для одной базы данных

### Можно ли загрузить данные в представление?
Нет

### Какими накладными расходами чревато создание индексов?
Индексы занимают дополнительной пространство и требуют дополнительной обработки данных при создании

### Назовите команду для вывода списка индексов.
```
SHOW INDEX ON table_name
```

### Что такое бакетирование?
Шардинг? Значения колонки хешируются в разные бакеты, количество которых определено пользователем. Это один из способов избежать создания нескольких или вложенных партиций.

### Для чего предназначена команда /* streamtable(table_name) */?
Это подсказка для Hive, что перед запросом данные должны быть сначала помещены в память. Это техника оптимизации запроса.

### Может ли партиция быть заархивирована? Какие существуют преимущества и недостатки?
Да, партиция может быть заархивирована. Преимущество в том, что количество файлов, учитываемых NameNode и заархивированный файл может быть предметом запроса на hiveql. Недостаток в том, что выполнение запроса будет производиться менее эффективно.

### Что такое UDF?
UDF является Java программой, которая создаётся для определённой задачи, которая не может быть решена стандартными функциями Hive. В программе можно определить тип аргумента и вернуть соответствующий результат.

### Следующее выражение не выполнилось - `LOAD DATA LOCAL INPATH ‘{env:HOME}/country/state/ OVERWRITE INTO TABLE address;’`. В чём причина?
В качестве аргумента `LOCAL INPATH` должен передаваться путь к файлу, а не к директории.

### Как указать создателя таблицы при создании таблицы?
```
TBLPROPERTIES(‘creator’ = ‘Joan’)
```


## MapReduce

[содержание](#)

### Как работает механизм репликации в HDFS?
Chain репликация выполняется при параметре `dfs.replication` в `hdfs-size.xml` большим, чем 1. Данные записываются в локальный блок HDFS, затем транслируются в блок в соседнем узле той же стойки, затем в блок на узле из другой стойки. Запись заканчивается, когда, начиная с последнего узла цепочки до первого, проходит подтверждение об успешной записи.

### За счёт какой оптимизации в MapReduce минимизируется объём переданных данных по сети?
Выполнения обработки на том же узле, на котором хранятся данные.
 
### Необходимо ли указывать тип входного и выходного формата при запуске MapReduce?
Нет, значение по умолчанию “text”.

### Можем ли мы переименовать выходной файл?
Да, реализуя класс MultipleFormatOutput.

### Что подразумевается под перемешиванием и сортировкой в MapReduce?
Сразу после выполнения map задачи полученные результаты сортируются перед и после передачи на сторону reduce. По принятой терминологии этот процесс называется shuffle (перемешивание).

### Объясните, как происходит процесс сброса на диск (spilling) во время MapReduce.
Выдача map задачи сначала записывается в циклический буфер в памяти, а при его заполнении на заданный процент данные сбрасываются на диск. По умолчанию размер буфера `mapreduce.task.io.sort.mb` равен 100МБ, а пороговое значение `mapreduce.map.sort.spill.percent` заполнения 80%.

### Что такое распределённый кэш в фреймворке MapReduce?
Распределённый кэш — это дополнительная утилита фреймворка для хранения файлов, необходимых для выполнения задачи. После регистрации в кэше для данной задачи файлов, фреймворк сделает их доступными локально на каждом узле, на котором будет исполняться задача. Так, что вы сможете получить доступ из Mapper и Reducer.

### Что такое Combiner? В каких случаях его следует использовать?
Это функция аналогичная reduce для частичной агрегации данных на стороне Map до передачи данных по сети.

### Почему результаты Map задач сохраняются на локальном диске, а не в HDFS?
Результаты Map являются промежуточными в MapReduce задачи и после выполнения могут быть удалены. Поэтому смысла в их репликации нет.

### Что произойдёт, если map задача прервётся из-за ошибки до того, как её результаты будут переданы в reducer?
Будет создана новая задача на другом узле для расчёта результата заново.

### В чём заключается роль MapReduce Partitioner?
Задачей этого класса является распределение пар ключ/значение по партициям, число которых равно количеству Reduce процессов.  Каждая партиция будет обработана своим reduce процессом. Распределения производится хэш-функцией над одним или группой ключей. В поставку Hadoop для этой цели входит класс HashPartitioner.

### Как мы можем быть уверены, что значения определённого ключа попадут в один reducer?
Мы можем контролировать куда попадут пары, используя класс распределяющий по партициям.

### В чём разница между InputSplit и HDFS блоком?
Объект класс InputSplit хранит логическое разделение исходных данных, а размер HDFS блока определяет физическое разделение.

### Что такое InputFormat?
Наследник InputFormat определяет то, как: проверяет путь к входным файлам на корректность, исходные данные делятся на логические части InputSplit, создаёт объект класса RecordReader для чтения записей из логической части.

### Для чего используется TextInputFormat?
Этот класс является классом по умолчанию для формата входных данных. Согласно ему исходный файл читается по строкам, что формирует пары номер строки/содержимое строки для Map функции.

### Какую роль играет RecordReader?
Логическое разбиение задаёт поле для работы map функции, но не определяет то, как эти данные читать (парсить). Задача преобразования входных данных в пары ключ/значение передаётся RecordReader-у, который создаётся классом InputFormat.

### Какие конфигурационные параметры необходимы для запуска MapReduce задачи?
Путь к входным файлам, путь к выходным файлам, формат входных данных, формат выходных данных, класс содержащий map  функцию, класс содержащий reduce функцию, jar c этими классами плюс driver класс.

### Когда вам следует использовать SequenceFileInputFormat?
Этот класс позволяет читать данные из файла, записанного в сжатом бинарном формате `SequenceFileOutputFormat`, который применяется для эффективной передачи данных между MapReduce задачами.

### Что такое identity Mapper и identity Reducer?
Это классы Mapper и Reducer по умолчанию, которые использует фреймворк, если пользователем они не переопределены. Map возвращает идентичный результат полученному от RecordReader, Reduce возвращает идентичный результат полученному от shuffle фазы.

### В чём заключается преимущество выполнения join на стороне Map?
Уменьшает накладные расходы в фазе перемешивания и сортировки, а также уменьшает время выполнения задачи.

### Можно ли установить количество Reduce задач равным нулю?
Да, например, в случае, когда вам не нужно проводить редукцию результатов Map. В этом случае результаты буду записаны не локально, а напрямую в HDFS.

### Как планово остановить MapReduce задачу?
С помощью команды `hadoop job -kill JOBID`

### Каким образом можно добавить к исполняемому jar файлу дополнительные ресурсы: медиа файлы, статичные файлы с табличными данными?
Вы можете добавить их в распределённый кэш. Файлы будут скопированы один раз в начале задачи.

### Дайте определение спекулятивному выполнению задач.
Это оптимизация, которая позволяет бороться с медленными обработчиками. В конце Map или Reduce фазы задачи для логического разбиения запускаются не в одном, а нескольких экземплярах. Та задача, которая выигрывает гонку, первой записывает данные локально или в HDFS, а дубликаты уничтожаются.

### Что такое NameNode и DataNode?
NameNode — главный процесс в HDFS, который управляет процессами DataNode, хранит метаданные файлов распределённой файловой системы. DataNode — обеспечивает доступ к локальным данным, контролирует целостность данных, за которые отвечает.

### Имеет ли смысл устанавливаться NameNode и DataNode на машины с одинаковыми характеристиками?
Нет, для хранения метаданных требуется большой объём оперативной памяти и небольшое пространство на жёстком диске. Для хранения данных требуются большие объёмы дискового пространства.

### В чём различие между NAS (Network Attached Storage) и HDFS?
NAS является единой точкой доступа к данным, единой точкой отказа, не позволяет обрабатывать данные без предварительной передачи по сети на узел или узлы обработки.

### Чем отличается реляционное хранилище от HDFS?
Схема проверяется при записи в РБД и при чтении в HDFS
В РБД доступны эффективное чтение и вставка отдельных записей. В HDFS за счёт параллельного чтения с множества узлов достигается очень высокая пропускная способность.
Hadoop наилучшим образом подходит для OLAP, РБД для OLTP.

### За счёт чего HDFS позволяет достичь высокой пропускной способности на чтение?
За счёт модели “один пишет — многие читают”

### Какую задачу решает SecondaryNameNode? (Верно ли, что SecondaryNameNode используется для достижения высокой доступности?)
Это процесс-помощник, который периодически применяет изменения EditLogs к FsImage для предотвращения разрастания EditLogs.

### Какую информацию в HDFS можно считать метаданными?
Метаданные отражают структуру HDFS директорий и файлов. Они включают в себя данные о: владельце, правах, квотах, степени репликации. В NameNode присутствует два файла с метаданными:
 - FsImage — содержит полное описание файловой системы с начала работы процесса,
 - EditLogs — содержит все последние изменения, произведённые в файловой системе

### В чём заключается проблема хранения большого количества небольших файлов в HDFS?
Несмотря на наличие места в HDFS, файловая система может перестать нормально работать из-за нехватки оперативной памяти для хранения метаданных файлов, размер которых равен приблизительно 150 байтам на файл.
Объясните работу механизма Heartbeat.
Это периодический сигнал от DataNode к NameNode, наличие которого сигнализирует о штатной работе и наличии соединения между процессами. По умолчанию интервал между сигналами `dfs.heartbeat.interval` составляет 3 секунды.

### Каким образом можно узнать о статусе работы NameNode в системе? 
Одним из способов является утилита JPS.

### Что такое HDFS блок?
Блок — это непрерывная последовательность байт с данными на диске. HDFS хранит каждый файл блоками, размер которых по умолчанию равен 128 МБ, в отличие от Linux файловой системы с размером блоков 4 КБ. Мотивацией для таких больших размеров блока является снижение накладных расходов на поиск считывающей головкой в жестких дисках место считывания и уменьшения количества метаданных.

### Предположим, что в HDFS хранится файл размером 514 МБ с параметром репликации и размером блока по умолчанию. Какое количество блоков хранится в системе, каков их размер, и сколько всего данных будет храниться на дисках?
При размере блока 128 МБ и репликацией 3, в HDFS будет храниться 4 блока по 128 МБ и один блок 2 МБ, каждый в трёх копиях. Всего на дисках будет храниться 1542 МБ.

### Можно ли скопировать файл в HDFS c другим параметром размера блока, чем тот, что задан администратором HDFS?
Да, при копировании нужно указать опцию “-Ddfs.blocksize=block_size”, где размер указывается в байтах. Например,
```
hadoop fs -Ddfs.blocksize=33554432 -copyFromLocal test.txt /sample_hdfs
```
Проверить атрибуты файла можно командой:
```
hadoop fs -stat %o /sample_hdfs/test.txt
```

### Можно ли поменять размер блока для всех файлов хранимых в HDFS?
Да, можно, в конфигурационном файле `hdfs-site.xml`. Но изменения произойдут только после перезапуска распределённой файловой системы.

### Что такое сканнер блоков HDFS?
DataNode периодически сканирует файлы, хранимые на его узле, на предмет ошибок. Если битые файлы найдены выполняется следующая последовательность действий:
 1. Отправляется отчёт об испорченных блоках в NameNode;
 2. NameNode инициализирует процесс создания новых реплик данных из оставшихся корректных блоков;
 3. После успешной репликации испорченные блоки будут удаляются.
 
Посмотреть отчёт сканнера можно на web странице DataNode в разделе /blockScannerReport.

### За счёт чего достигается отказоустойчивость HDFS?
Отказоустойчивость достигается за счёт репликации, heartbeat мониторинга и перезапуска вылетевших задач.

### Можете ли вы для выбранных файлов в HDFS изменить степень репликации? Покажите, как это сделать на примере.
Да. Например, для файла text.xml с репликацией 1 можно установить количество реплик равное 3 командой:
```
hadoop fs -setrwp -w 3 test.xml
```
Проверить изменения можно командами:
```
hadoop fs -ls  или hadoop fsck test.xml -files
```


### Что означает rack awareness алгоритм?
При репликации копии будут записываться в разные наиболее друг от друга независимые с точки зрения причины отказа оборудования места. Если первая реплика будет сохранена локально, то вторая будет сохранена на другой машине в той же стойке, а третья в машине в другой стойке. Это позволяет улучшить доступность данных по сети и уменьшить вероятность потери данных.

### Опишите последовательность действий алгоритма записи данных в HDFS.
Допустим клиент хочет записать файл в HDFS. Тогда процесс пройдёт следующие этапы: 
- клиент разделит файл на блоки и отправит запрос на запись в NameNode, 
- для каждого блока будет возвращён список из нескольких адресов DataNode (3 при степени репликации 3) в которые можно отправлять соответствующие блоки,
- блок будет скопирован клиентом в один из DataNode, который автоматически запустит репликацию на следующие два в цепочке. 

### Можете ли вы изменить существующий файл?
Нет, в HDFS данные файлов не изменяемые. Можно добавлять данные в конец файла.

### Могут ли несколько пользователей параллельно записывать данные в один HDFS файл?
Нет. HDFS работает согласно модели “пишет один — читают многие”.

### Можно ли считывать из файла, который в данный момент пишется программой другого пользователя?
Да, можно, но HDFS не даёт гарантий, что новые данные, которые другой пользователь по его заявлению успешно записал, будут видны при чтении. Пишущая программа может периодически вызывать hflush для ускорения появления новых данных для чтения другими пользователями.

### Каким образом HDFS отслеживает целостность хранимых данных в кластере?
В распределённом хранилище существует ненулевая вероятность того, что данные могли повредиться при записи или чтение. Поэтому HDFS также хранит контрольные суммы для данных, которые всегда проверяются при чтении данных. Кроме того, периодически каждый DataNode выполняет дежурную операцию обхода данных с проверкой контрольных сумм.

### Для чего используется конфигурация HighAvailability у NameNode? Как она работает?
Для решения проблемы единой точки отказа, которой является процесс NameNode. Был добавлен в Hadoop 2.x. При такой конфигурации в кластере присутствуют два NameNode процесса в активно/пассивном режиме. В случае, если один завершается с ошибкой или становится недоступным — другой переключается в активный режим и подхватывает обслуживание запросов.

### Для чего в HDFS предоставляется возможность архивирования?
Эта возможность была добавлена для решения проблемы, возрастающей со временем нагрузки на память процессом NameNode. Процессом архивации, накопившееся множество мелких файлов, объединяется в небольшое количество крупных. Это позволяет существенно сократить объём метаданных, хранимых для каждого файла.

### Каким образом можно совершить миграцию данных из одного Hadoop кластера в другой?
```
hadoop distcp hdfs://<source NameNode> hdfs://<target NameNode>
```

## Потоковая обработка (Apache Storm)

[содержание](#)

### Что такое Apache Storm?
Система потоковой обработки с открытым кодом. 

### Дайте определение топологии в Apache Storm и назовите её составляющие элементы.
Топология — ориентированный граф описывающий способ обработки данных в Storm. Может трактоваться как описание запроса в более низкоуровневых терминах, чем SQL. Состоит из источников (spouts), вводящих поток записей из внешних систем в систему Storm, и обработчиков (bolts), выполняющих преобразования над потоком записей.

### Какими отличительными свойствами обладают потоковые данные?
Количество данных в потоке потенциально бесконечно, характеристики (интенсивность, распределение) определяются внешним для потоковой системы окружением

### Можно ли обновить топологию в Apache Storm?
Единственным вариантом обновления топологии является остановка старой топологии и запуск новой. В реальный рабочих условиях может потребоваться запуск новой топологии параллельно старой.

### Объясните механизм гарантии доставки сообщений в Apache Storm.
Источник закрепляет за каждым созданным им сообщением уникальный идентификатор и хранит его до тех пор, пока сообщение или производные от него сообщения не пройдут по графу обработки. Перед каждой передачей узел-отправитель генерирует случайное число и отправляет вместе с идентификатором специальному процессу `Acker`. Каждый получатель, после успешной обработки сообщения отправляет те же два числа — идентификатор и сгенерированное случайное число, которые он узнал из сообщения — процессу `Acker` второй раз. По получению сообщения Acker в своей хэш-таблице записывает результат исключающего или между хранимым значением случайным числом и полученным из сообщения под ключом идентификатора. Обработка считает выполненной, если значение ключа идентификатора стало равным нулю.

### Какой механизм обмена сообщениями используется в Apache Storm между рабочими процессами, а какой между потоками процесса? Можно ли его заменить?
По умолчанию используется Netty для передачи сообщений. Раньше использовался ZeroMQ, который может быть включен в настройках. Внутри процесса обмен происходит через циклический буфер, названный разработчиками LMAX Disruptor. 

### Приведите несколько примеров задач, хорошо решаемых потоковыми системами?
Мониторинг сети на предмет аномалий и взломов. Веб аналитика кликов пользователей на интернет-страницах. 



## Архитектуры BigData решений

[содержание](#)

### Объясните, что такое Lambda архитектура.
Lambda architecture = CQRS (batch layer + serving layer) + speed layer
https://martinfowler.com/bliki/CQRS.html

### Чем Kappa архитектура отличается от Lambda архитектуры? В чём преимущества?
Главное отличие в том, что вместо двух фреймворков обработки данных используется один фреймворк потоковой обработки. 

### О чём говорит CAP теорема? Приведите неформальное доказательство.
Вы не можете одновременно добиться трёх свойств от системы: согласованности данных, доступности, распределённости. В реальности распределённость является неотъемлемым свойством, поэтому перед разработчиком системы встаёт выбор между гарантией согласованности данных или гарантией высокой доступности.

### Для чего используется Zookeeper?
Zookeeper используется многими фреймворками (Kafka, Storm, Flink, Hive) для хранения конфигурационной информации, метаданных о системе. Отсутствие единой точки отказа, гарантия согласованности данных, отказоустойчивость делают его хорошим кандидатом для хранения небольшого, но жизненно важного объёма информации о состоянии системы.  Хранение данных в памяти позволяет быстро отдавать данные.

