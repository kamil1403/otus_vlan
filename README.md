<p align="center">
  <img src="https://upload.wikimedia.org/wikipedia/commons/a/af/Tux.png" alt="VLAN" width="120">
</p>

## ![Lesson](https://img.shields.io/badge/Lesson-VLAN_&_LACP-00758F?style=for-the-badge&logo=linux&logoColor=white&labelColor=111827)![Author](https://img.shields.io/badge/Author-Kamil%20Ibragimov-10B981?style=for-the-badge&logo=github&logoColor=white&labelColor=111827)![Date](https://img.shields.io/badge/Date-25.12.2025-F59E0B?style=for-the-badge&logo=calendar&logoColor=white&labelColor=111827)

### 📌 Задание
1. Развернуть стенд на 7 ВМ (CentOS 8 и Ubuntu 22.04).
2. Настроить **LACP/Bonding** (mode 1) между роутерами.
3. Настроить **VLAN** для разделения пар клиент-сервер.
4. Весь процесс автоматизирован через **Ansible**.

### ✅ Результат
- [x] Стенд поднимается через `vagrant up`.
- [x] Bonding и VLAN работают. Результат см. на скриншотах:
  - 🖼️ [Доступ запрещен](https://github.com/kamil1403/otus_pam/blob/main/screenshots/otus_pam_1.png)
  - 🖼️ [Доступ разрешен](https://github.com/kamil1403/otus_pam/blob/main/screenshots/otus_pam_2.png)

---

## 🧰 Шаг 1 — Инфраструктура
Схема сети и адресация:
| Хост | IP / VLAN | Роль | ОС |
|------|-----------|------|----|
| **inetRouter** | 192.168.255.1 (Bond0) | Border Router | CentOS 8 |
| **centralRouter**| 192.168.255.2 (Bond0) | Core Router | CentOS 8 |
| **testClient1** | 10.10.10.254 (VLAN 100) | Client 1 | CentOS 8 |
| **testServer1** | 10.10.10.1 (VLAN 100) | Server 1 | CentOS 8 |
| **testClient2** | 10.10.10.254 (VLAN 101) | Client 2 | Ubuntu 22.04 |
| **testServer2** | 10.10.10.1 (VLAN 101) | Server 2 | Ubuntu 22.04 |

---

## 🧰 Шаг 2 — Запуск
```bash
vagrant up
```

---

## 🧰 Шаг 3 — Проверка

### 1. Проверка Bonding (LACP)
На роутере проверяем статус агрегации:
```bash
vagrant ssh centralRouter
cat /proc/net/bonding/bond0   # Должен быть MII Status: up и 2 Slave интерфейса
```

### 2. Проверка VLAN 100 (CentOS)
```bash
vagrant ssh testClient1
ping -c 2 10.10.10.1         # Пинг до Server 1
ping -c 2 10.10.10.254       # Пинг Client 2
```

### 3. Проверка VLAN 101 (Ubuntu)
```bash
vagrant ssh testClient2
ip a show vlan101            
ping -c 2 10.10.10.1         # Пинг до Server 2
```
