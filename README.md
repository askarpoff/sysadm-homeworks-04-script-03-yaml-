# Домашнее задание к занятию "4.3. Языки разметки JSON и YAML" DEVOPS-21 Карпов Андрей


## Обязательная задача 1
Мы выгрузили JSON, который получили через API запрос к нашему сервису:
```
    { "info" : "Sample JSON output from our service\t",
        "elements" :[
            { "name" : "first",
            "type" : "server",
            "ip" : 7175 
            }
            { "name" : "second",
            "type" : "proxy",
            "ip : 71.78.22.43
            }
        ]
    }
```
  Нужно найти и исправить все ошибки, которые допускает наш сервис
  1. Экранировать t;
  2. У "elements" : пробел не там;
  3. "ip" : 7175  - совсем не похоже на ip;
  4. Между элементами запятая
  5. "ip : 71.78.22.43 - незакрытая кавычка и ip значение строковое - нужны кавычки;
  
```
   {
   	"info": "Sample JSON output from our service\\t",
   	"elements": [{
   		"name": "first",
   		"type": "server",
   		"ip": "71.75.0.1"
   	}, 
    {
   		"name": "second",
   		"type": "proxy",
   		"ip": "71.78.22.43"
   	}]
   }
```

## Обязательная задача 2
В прошлый рабочий день мы создавали скрипт, позволяющий опрашивать веб-сервисы и получать их IP. К уже реализованному функционалу нам нужно добавить возможность записи JSON и YAML файлов, описывающих наши сервисы. Формат записи JSON по одному сервису: `{ "имя сервиса" : "его IP"}`. Формат записи YAML по одному сервису: `- имя сервиса: его IP`. Если в момент исполнения скрипта меняется IP у сервиса - он должен так же поменяться в yml и json файле.

### Ваш скрипт:
```python
#!/usr/bin/env python3

import socket
import time
import json
import yaml

#hosts={'drive.google.com':'74.125.205.194', 'mail.google.com':'108.177.14.18', 'google.com':'74.125.205.113'}
hosts={}
with open("hosts.json", "r") as fp_json:
    hosts=json.load(fp_json)

while(True):
    for hostname in hosts.keys():
        host_ip=socket.gethostbyname(hostname)
        print(hostname,host_ip)
        if (host_ip==hosts[hostname]):
            print (f'IP не изменился: {hostname} {host_ip}')
        else:
            print('\x1b[1;31;40m' + 'Внимание!' + '\x1b[0m')
            print (f'IP изменился: {hostname} был {hosts[hostname]} - стал {host_ip}')
            hosts[hostname]=host_ip
            ip_changed = True

    if ip_changed:
        with open("hosts.json", "w") as fp_json:
            json.dump(hosts, fp_json, indent=2)
        with open("hosts.yaml", "w") as fp_yaml:
            yaml.dump(hosts, fp_yaml, explicit_start=True, explicit_end=True)
                
    time.sleep(5)
```

### Вывод скрипта при запуске при тестировании:
```
drive.google.com 142.251.1.194
IP не изменился: drive.google.com 142.251.1.194
mail.google.com 64.233.161.19
Внимание!
IP изменился: mail.google.com был 64.233.161.83 - стал 64.233.161.19
google.com 173.194.220.102
Внимание!
IP изменился: google.com был 173.194.220.139 - стал 173.194.220.102
drive.google.com 142.251.1.194
IP не изменился: drive.google.com 142.251.1.194
mail.google.com 64.233.161.17
Внимание!
IP изменился: mail.google.com был 64.233.161.19 - стал 64.233.161.17
google.com 173.194.220.113
Внимание!
IP изменился: google.com был 173.194.220.102 - стал 173.194.220.113
drive.google.com 142.251.1.194
IP не изменился: drive.google.com 142.251.1.194
mail.google.com 64.233.161.18
Внимание!
IP изменился: mail.google.com был 64.233.161.17 - стал 64.233.161.18
google.com 173.194.220.100
Внимание!
IP изменился: google.com был 173.194.220.113 - стал 173.194.220.100
drive.google.com 142.251.1.194
IP не изменился: drive.google.com 142.251.1.194
mail.google.com 64.233.161.83
Внимание!
IP изменился: mail.google.com был 64.233.161.18 - стал 64.233.161.83
google.com 173.194.220.101
Внимание!
IP изменился: google.com был 173.194.220.100 - стал 173.194.220.101
^CTraceback (most recent call last):
  File "/home/debian/4.py", line 31, in <module>
    time.sleep(5)
KeyboardInterrupt

```

### json-файл(ы), который(е) записал ваш скрипт:
```json
{
  "drive.google.com": "142.251.1.194",
  "mail.google.com": "64.233.161.83",
  "google.com": "173.194.220.101"
}

```

### yml-файл(ы), который(е) записал ваш скрипт:
```yaml
---
drive.google.com: 142.251.1.194
google.com: 173.194.220.101
mail.google.com: 64.233.161.83
...

```
