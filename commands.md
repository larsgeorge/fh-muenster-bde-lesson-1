## Kommandos
Die folgenden Kommandos werden in der Cloudera QuickStart VM in einem Terminal Fenster ausgeführt. Dazu muss in der Menüzeile am oberen Bildschirmrand auf der linken Seite auf das Terminal Icon geklickt werden. Die folgenden Kommandos werden im Kontext erklärt.

Die VM läuft in der Vorgabe _ohne_ Cloudera Manager, sondern startet alle Dienste über die Linux _Services_ Infrastruktur. Um zu sehen welche Prozesse gestartet sind, kann man beispielsweise den folgen Befehl verwenden:

```
$ sudo service --status-all
```

Hadoop kommt mir mehrere Kommandozeilen Skripten, benannt `hadoop`, `hdfs`, `yarn` und so weiter. Führt man diese _ohne_ zusätzliche Parameter aus, so werden alle verfugbaren Optionen ausgegeben:

```
$ hadoop
```

Die Optionen teilen sich in Klassen, oder Gruppen. Eine davon ist `fs` für Dateisystemoperation. Ruft man das Skript nur mit dem Gruppennamen auf, werden dessen Optionen angezeigt. Man kann ausserdem für jeden Unterbefehl eine Hilfe aufrufen:

```
$ hadoop fs
$ hadoop fs -help tail
$ hadoop fs -help test
$ hadoop fs -help touchz

$ hadoop fs -usage
```

Nun schauen wir uns die Verzeichnisse in HDFS an:

```
$ hadoop fs -ls
```

Dies gibt das Homeverzeichnis des aktuellen Nutzers an (`"cloudera"`). Mit dem Slash (`"/"`) wird das Hauptverzeichnis explizit ausgegeben, und auch unser Homeverzeichnis is explizit zu erreichen:


```
$ hadoop fs -ls /
$ hadoop fs -ls /user
$ hadoop fs -ls /user/cloudera
```

Kopieren wir jetzt eine Datei in unser Homeverzeichnis, schauen nach ob es angekommen ist, und geben im Anschluss dessen Inhalt auf der Konsole wieder aus:

```
$ ll eclipse/
$ less eclipse/about.html
$ hadoop fs -put eclipse/about.html
$ hadoop fs -ls

Found 1 items
-rw-r--r--   1 cloudera cloudera      19876 2015-10-22 10:07 about.html

$ hadoop fs -cat about.html

<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN"
    "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
...
```

Wenn Sie noch einmal versuchen die gleiche Datei nach HDFS zu kopieren:

```
$ hadoop fs -put eclipse/about.html
put: `about.html': File exists
```

Warum die Nachricht?

Weitere Befehlsgruppen und deren Kommandos:

```
$ hdfs
$ hdfs dfs
$ yarn
$ mapred
```

Hadoop kommt mit einigen weiteren Gruppen, welche für verschiedene Dienste zuständig sind. Eine davon erlaubt es das Dateisystem selbst zu administrieren und zu überprüfen:

```
$ hdfs dfsadmin
$ hdfs fsck
$ hdfs fsck about.html
$ hdfs fsck /user/cloudera/about.html

Connecting to namenode via http://quickstart.cloudera:50070
FSCK started by cloudera (auth:SIMPLE) from /127.0.0.1 for path /user/cloudera/about.html at Thu Oct 22 10:10:05 PDT 2015
.Status: HEALTHY
 Total size:    19876 B
 Total dirs:    0
 Total files:    1
 Total symlinks:        0
 Total blocks (validated):    1 (avg. block size 19876 B)
 Minimally replicated blocks:    1 (100.0 %)
 Over-replicated blocks:    0 (0.0 %)
 Under-replicated blocks:    0 (0.0 %)
 Mis-replicated blocks:        0 (0.0 %)
 Default replication factor:    1
 Average block replication:    1.0
 Corrupt blocks:        0
 Missing replicas:        0 (0.0 %)
 Number of data-nodes:        1
 Number of racks:        1
FSCK ended at Thu Oct 22 10:10:05 PDT 2015 in 2 milliseconds

The filesystem under path '/user/cloudera/about.html' is HEALTHY
```   

Weitere parameter erlauben die Ausgabe der Blockinformationen für eine Datei:

```
$ hdfs fsck /user/cloudera/about.html -files -blocks -locations

Connecting to namenode via http://quickstart.cloudera:50070
FSCK started by cloudera (auth:SIMPLE) from /127.0.0.1 for path /user/cloudera/about.html at Thu Oct 22 10:10:35 PDT 2015
/user/cloudera/about.html 19876 bytes, 1 block(s):  OK
0. BP-989008105-127.0.0.1-1433846136903:blk_1073742217_1393 len=19876 repl=1 [DatanodeInfoWithStorage[127.0.0.1:50010,DS-dcf35ebd-2f86-4fe6-8001-67f8db5e89d4,DISK]]

Status: HEALTHY
 Total size:    19876 B
 Total dirs:    0
...
```

Für eine (fiktive) grössere Datei sieht man noch besser wie die Blöcke binär bis zur gesetzten Grösse (128MB) gefüllt und dann geschlossen werden. Der letzte Block wird nur mit dem restlichen Daten beschrieben, es gibe also kein Padding:

```
$ hdfs fsck /user/cloudera/out4/web-large.log -files -blocks -locations

Connecting to namenode via http://quickstart.cloudera:50070
FSCK started by cloudera (auth:SIMPLE) from /127.0.0.1 for path /user/cloudera/out4/web-large.log at Thu Nov 06 06:22:05 PST 2014
/user/cloudera/out4/web-large.log 1561266402 bytes, 12 block(s): OK
0. BP-1256644290-127.0.0.1-1413121757039:blk_1073742414_1594 len=134217728 repl=1 [127.0.0.1:50010]
1. BP-1256644290-127.0.0.1-1413121757039:blk_1073742415_1595 len=134217728 repl=1 [127.0.0.1:50010]
2. BP-1256644290-127.0.0.1-1413121757039:blk_1073742416_1596 len=134217728 repl=1 [127.0.0.1:50010]
3. BP-1256644290-127.0.0.1-1413121757039:blk_1073742417_1597 len=134217728 repl=1 [127.0.0.1:50010]
4. BP-1256644290-127.0.0.1-1413121757039:blk_1073742418_1598 len=134217728 repl=1 [127.0.0.1:50010]
5. BP-1256644290-127.0.0.1-1413121757039:blk_1073742419_1599 len=134217728 repl=1 [127.0.0.1:50010]
6. BP-1256644290-127.0.0.1-1413121757039:blk_1073742420_1600 len=134217728 repl=1 [127.0.0.1:50010]
7. BP-1256644290-127.0.0.1-1413121757039:blk_1073742421_1601 len=134217728 repl=1 [127.0.0.1:50010]
8. BP-1256644290-127.0.0.1-1413121757039:blk_1073742422_1602 len=134217728 repl=1 [127.0.0.1:50010]
9. BP-1256644290-127.0.0.1-1413121757039:blk_1073742424_1604 len=134217728 repl=1 [127.0.0.1:50010]
10. BP-1256644290-127.0.0.1-1413121757039:blk_1073742425_1605 len=134217728 repl=1 [127.0.0.1:50010]
11. BP-1256644290-127.0.0.1-1413121757039:blk_1073742427_1607 len=84871394 repl=1 [127.0.0.1:50010]

Status: HEALTHY
Total size: 1561266402 B
Total dirs: 0
Total files: 1
Total symlinks: 0
Total blocks (validated): 12 (avg. block size 130105533 B)
Minimally replicated blocks: 12 (100.0 %)
...
```
    
Die aktuelle Konfiguration gibt Aufschluss wo sich die eigentlichen Daten innerhalb von HDFS befinden (siehe `dfs.datanode.data.dir`):

```
$ ll /etc/hadoop/conf/
$ cat /etc/hadoop/conf/core-site.xml
```

Mit etwas Geschick kann man die Rohdaten direkt im Dateisystem des Betriebssystems finden. Beachten Sie aber das die Verzeichnisnamen bei jedem HDFS verschieden sind (enthalten einen Epoch basierten Zeitstempel). Ausserdem muss `sudo` benutzt werden, denn die Rohdaten gehörem dem Nutzer `hdfs`, wir sind aber als `cloudera` angemeldet:

```
$ sudo ls -laR /var/lib/hadoop-hdfs/cache/hdfs/dfs/data/current/

$ sudo ls -la /var/lib/hadoop-hdfs/cache/hdfs/dfs/data/current
$ sudo ls -la /var/lib/hadoop-hdfs/cache/hdfs/dfs/data/current/BP-989008105-127.0.0.1-1433846136903/current
...
$ sudo ls -la /var/lib/hadoop-hdfs/cache/hdfs/dfs/data/current/BP-989008105-127.0.0.1-1433846136903/current/finalized/subdir0/subdir1
total 164948
drwxr-xr-x 2 hdfs hadoop    12288 Oct 22 10:07 .
drwxr-xr-x 4 hdfs hadoop     4096 Jun  9 03:39 ..
-rw-r--r-- 1 hdfs hadoop   125573 Jun  9 03:39 blk_1073742080
-rw-r--r-- 1 hdfs hadoop      991 Jun  9 03:39 blk_1073742080_1256.meta
-rw-r--r-- 1 hdfs hadoop    35058 Jun  9 03:39 blk_1073742081
-rw-r--r-- 1 hdfs hadoop      283 Jun  9 03:39 blk_1073742081_1257.meta
...
-rw-r--r-- 1 hdfs hdfs         91 Oct 22 09:03 blk_1073742214
-rw-r--r-- 1 hdfs hdfs         11 Oct 22 09:03 blk_1073742214_1390.meta
-rw-r--r-- 1 hdfs hdfs         91 Oct 22 09:03 blk_1073742215
-rw-r--r-- 1 hdfs hdfs         11 Oct 22 09:03 blk_1073742215_1391.meta
-rw-r--r-- 1 hdfs hdfs      19876 Oct 22 10:07 blk_1073742217
-rw-r--r-- 1 hdfs hdfs        163 Oct 22 10:07 blk_1073742217_1393.meta

$ date
Thu Oct 22 10:13:39 PDT 2015

$ sudo ls -la /var/lib/hadoop-hdfs/cache/hdfs/dfs/data/current/BP-989008105-127.0.0.1-1433846136903/current/finalized/subdir0/subdir1/blk_1073742217
-rw-r--r-- 1 hdfs hdfs 19876 Oct 22 10:07 /var/lib/hadoop-hdfs/cache/hdfs/dfs/data/current/BP-989008105-127.0.0.1-1433846136903/current/finalized/subdir0/subdir1/blk_1073742217

$ sudo head -n 5 /var/lib/hadoop-hdfs/cache/hdfs/dfs/data/current/BP-989008105-127.0.0.1-1433846136903/current/finalized/subdir0/subdir1/blk_1073742217
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN"
    "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1"/>
...
```

Anhand des Zeitstempels des Blocks und der aktuellen Zeit kann man in diesem Beispiel den Block finden. Diese sind einfache Dateien im darunterliegenden Dateisystem.

Abschliessend entfernen wir die Datei:

```
$ hadoop fs -rm about.html
15/10/22 10:49:28 INFO fs.TrashPolicyDefault: Namenode trash configuration: Deletion interval = 0 minutes, Emptier interval = 0 minutes.
Deleted about.html
```
