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

with open("hosts.json", "r") as fp_json:
    hosts=json.load(fp_json)

while(True):
    ip_changed=False
    i=0
    for hostname in hosts:
        for k, v in hostname.items():
            host_ip=socket.gethostbyname(k)
        if (host_ip==v):
            print (f'IP не изменился: {k} {host_ip}')
        else:
            print('\x1b[1;31;40m' + 'Внимание!' + '\x1b[0m')
            print (f'IP изменился: {k} был {v} - стал {host_ip}')
            hosts[i][k]=host_ip
            ip_changed = True
        i+= 1

    if ip_changed:
        with open("hosts.json", "w") as fp_json:
           json.dump(hosts, fp_json, indent=2)
        with open("hosts.yaml", "w") as fp_yaml:
            yaml.dump(hosts, fp_yaml, explicit_start=True, explicit_end=True)
                
    time.sleep(5)
    ```

### Вывод скрипта при запуске при тестировании:
```

![image](https://user-images.githubusercontent.com/108946489/196809901-63f58efb-3ec3-4422-898f-6944c21977fe.png)
```

### json-файл(ы), который(е) записал ваш скрипт:
```json
[
  {
    "drive.google.com": "142.250.150.194"
  },
  {
    "mail.google.com": "64.233.165.19"
  },
  {
    "google.com": "64.233.162.101"
  }
]
```

### yml-файл(ы), который(е) записал ваш скрипт:
```yaml
---
- drive.google.com: 142.250.150.194
- mail.google.com: 64.233.165.19
- google.com: 64.233.162.101
...
```
