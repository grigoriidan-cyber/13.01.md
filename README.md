# Домашнее задание к занятию «Уязвимости и атаки на информационные системы» Гришин Даниил Алексеевич

---

## Задание 1. Резервное копирование

### 1.1 Сканирование жертвы на порты и службы
Я нашёл вот эти службы: 

Я решил попробовать нарисовать таблицу:    

| Порт       | Статус | Сервис      | версия                                          |
|:----------:|:------:|:------------|:------------------------------------------------|
| 21/tcp     | open   | ftp         | vsftpd 2.3.4                                    |
| 22/tcp     | open   | ssh         | OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)    |
| 23/tcp     | open   | telnet      | Linux telnetd                                   |
| 25/tcp     | open   | smtp        | Postfix smtpd                                   |
| 53/tcp     | open   | domain      | ISC BIND 9.4.2                                  |
| 80/tcp     | open   | http        | Apache httpd 2.2.8 ((Ubuntu) DAV/2)             |
| 111/tcp    | open   | rpcbind     | 2 (RPC #100000)                                 |
| 139/tcp    | open   | netbios-ssn | Samba smbd 3.X - 4.X (workgroup: WORKGROUP)     |
| 445/tcp    | open   | netbios-ssn | Samba smbd 3.X - 4.X (workgroup: WORKGROUP)     |
| 512/tcp    | open   | exec        | netkit-rsh rexecd                               |
| 513/tcp    | open   | login       | OpenBSD or Solaris rlogind                      |
| 514/tcp    | open   | tcpwrapped  | tcpwrapped                                      |
| 1099/tcp   | open   | java-rmi    | GNU Classpath grmiregistry                      |
| 1524/tcp   | open   | bindshell   | Metasploitable root shell                       |
| 2049/tcp   | open   | nfs         | 2-4 (RPC #100003)                               |
| 2121/tcp   | open   | ftp         | ProFTPD 1.3.1                                   |
| 3306/tcp   | open   | mysql       | MySQL 5.0.51a-3ubuntu5                          |
| 5432/tcp   | open   | postgresql  | PostgreSQL DB 8.3.0 - 8.3.7                     |
| 5900/tcp   | open   | vnc         | VNC (protocol 3.3)                              |
| 6000/tcp   | open   | X11         | (access denied)                                 |
| 6667/tcp   | open   | irc         | UnrealIRCd                                      |
| 8009/tcp   | open   | ajp13       | Apache Jserv (Protocol v1.3)                    |
| 8180/tcp   | open   | http        | Apache Tomcat/Coyote JSP engine 1.1             |

### 1.2 Обнаруженные уязвимости

vsftpd 2.3.4 - выполнение команд через бэкдор (Metasploit): https://www.exploit-db.com/exploits/17491

UnrealIRCd 3.2.8.1 - выполнение команд через бэкдор (Metasploit): https://www.exploit-db.com/exploits/16922

Samba 3.0.20 < 3.0.25rc3 - выполнение команд через 'Username' map script (Metasploit): https://www.exploit-db.com/exploits/16320

И ещё нашёл это: 

1524/tcp (bindshell) - Metasploitable root shell. Как я понял это демонстрация атаки, возможность повторного проникновения без аутентификации и с повышенными привилегиями (что-то вроде инъекции или предустановленного бэкдора). 

Надо поставить Wazuh на работе... :)

---

## Задание 2 Сканирование в разных режимах

Я запустил сканирование виртуалки Metasploitable в четырёх режимах и записывал трафик в Wireshark.

Режимы и их отличия:

SYN (-sS)
Отправляет TCP SYN. Открытый порт отвечает SYN,ACK, закрытый RST (или RST,ACK).

FIN (-sF)
Отправляет TCP FIN. Открытый порт игнорирует пакет (нет ответа). Закрытый порт отвечает RST.

Xmas (-sX)
Отправляет FIN, PSH, URG. похож на FIN: открытый порт молчит, закрытый шлёт RST.

UDP (-sU)
Отправляет пустой UDP-пакет. Закрытый порт возвращает ICMP (Port Unreachable). Открытый порт может ответить UDP-данными или промолчать.

Ответ сервера в моём случае:

TCP: открытый порт либо подтверждает соединение, либо сбрасывает (RST).

UDP: Закрытый порт сообщает об ошибке через ICMP, открытый либо отвечает по своему протоколу, либо молчит.
