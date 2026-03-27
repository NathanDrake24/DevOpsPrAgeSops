Age и SOPS – это два мощных и современных инструмента для управления секретами в DevOps, которые, будучи применены в синергии, создают надёжную и гибкую систему шифрования конфигурационных файлов. Хотя Age выступает простым и модульным инструментом файлового шифрования с использованием криптографии на эллиптических кривых, SOPS же дополняет его возможностью умного управления структурированными данными, позволяя шифровать не целые файлы, а лишь их ценные части.

## Теоретические основы Age и криптографические принципы

Age представляет собой утилиту шифрования файлов, которая опирается на проверенные криптографические примитивы и криптографию на эллиптических кривых. Инструмент использует **X25519** – алгоритм асимметричного шифрования, основанный на кривой Curve25519, который обеспечивает высокий уровень безопасности и, одновременно с этим, поддерживает достаточную производительность. Для шифрования самих данных Age применяет **ChaCha20-Poly1305** – современный алгоритм симметричного шифрования с аутентификацией (AEAD), который защищает не только конфиденциальность, но и целостность зашифрованной информации.​

Архитектура Age построена на принципе **envelope encryption**, который означает, что для каждого файла генерируется случайный ключ данных (DEK), этот ключ затем шифруется с использованием публичных ключей получателей, а сам файл шифруется этим ключом данных. Такой подход позволяет одному и тому же файлу быть зашифрованным для нескольких получателей одновременно, а каждый из них способен расшифровать его используя только свой приватный ключ. Ключевое отличие Age от других подобных инструментов (например, GPG) состоит в том, что Age не использует глобальный keyring и не требует сложной инфраструктуры управления ключами; вместо этого каждый ключ хранится отдельно в простом текстовом файле, что существенно упрощает управление.​

Age поддерживает несколько способов криптографического обмена данными, включая использование публичных ключей в формате Bech32 (удобном для копирования и использования в командной строке), парольную защиту файлов ключей через Scrypt, а также совместимость с существующими SSH ключами – что позволяет разработчикам использовать уже имеющиеся у них SSH пары ключей для шифрования.



# Практическое руководство по Age и SOPS для DevOps

## Установка
### Linux (Ubuntu/Debian)

```bash
# Способ 1: Через пакетный менеджер
sudo apt-get update
sudo apt-get install -y age

# Способ 2: Скачивание бинарного файла
cd /tmp
wget https://github.com/FiloSottile/age/releases/download/v1.1.1/age-v1.1.1-linux-amd64.tar.gz
tar xzf age-v1.1.1-linux-amd64.tar.gz
sudo mv age/age age/age-keygen /usr/local/bin/
chmod +x /usr/local/bin/age*

# Проверка установки
age --version
```

### Linux SOPS

```bash
# Скачивание и установка
curl -LO https://github.com/getsops/sops/releases/download/v3.8.1/sops-v3.8.1.linux.amd64
sudo mv sops-v3.8.1.linux.amd64 /usr/local/bin/sops
chmod +x /usr/local/bin/sops

# Проверка
sops --version
```

### Windows (Native)

```powershell
# Способ 1: Chocolatey
choco install age.portable
choco install sops

# Способ 2: Scoop
scoop bucket add extras
scoop install age sops

# Способ 3: Прямое скачивание
# 1. Перейти на https://github.com/FiloSottile/age/releases
# 2. Скачать age-v1.1.1-windows-amd64.zip
# 3. Распаковать в C:\Program Files\age
# 4. Добавить в PATH
# 5. Перезагрузить PowerShell

# Проверка
age --version
sops --version
```

### Windows с WSL2 (Рекомендуется)

WSL2 позволяет использовать полноценное Linux окружение на Windows, что упрощает работу с DevOps инструментами.

```powershell
# В PowerShell (as Administrator)
wsl --install

# Это установит WSL2 и Ubuntu по умолчанию
# После перезагрузки, откройте Ubuntu и выполните команды Linux установки
```

```bash
# Внутри WSL2 (Ubuntu)
sudo apt-get update
sudo apt-get install -y age

# Для SOPS
curl -LO https://github.com/getsops/sops/releases/download/v3.8.1/sops-v3.8.1.linux.amd64
sudo mv sops-v3.8.1.linux.amd64 /usr/local/bin/sops
chmod +x /usr/local/bin/sops

# Проверка
age --version
sops --version
```



### Этап 1: Проверка установки

<img width="955" height="126" alt="image" src="https://github.com/user-attachments/assets/48bcfed5-185b-4045-8f48-674545b31680" />


### Этап 2: Создание рабочей директории

<img width="613" height="69" alt="image" src="https://github.com/user-attachments/assets/35ccd2f8-f13f-4283-b19b-ba7cbe5d8fb8" />

### Этап 3: Генерация ключей Age

<img width="1438" height="288" alt="image" src="https://github.com/user-attachments/assets/5265309a-9fd9-4d6e-a59c-cdb4ff6132f1" />


### Этап 4: Создание конфигурации SOPS


<img width="613" height="352" alt="image" src="https://github.com/user-attachments/assets/80d8c112-1c31-4ca3-a266-c966cf46aaaa" />


### Этап 5: Создание .gitignore

<img width="703" height="509" alt="image" src="https://github.com/user-attachments/assets/d0779a22-8902-43ec-8bc2-5f345eb4d192" />

### Этап 6: Создание незашифрованного файла с секретами


<img width="765" height="559" alt="image" src="https://github.com/user-attachments/assets/0d36d0a2-2929-4f25-b00c-6214aff21922" />


### Этап 7: Шифрование файла с SOPS


<img width="1429" height="250" alt="image" src="https://github.com/user-attachments/assets/792c26a5-00b4-41f4-866f-b022b7f66de6" />


### Этап 8: Просмотр зашифрованного файла

<img width="1898" height="590" alt="image" src="https://github.com/user-attachments/assets/92144e9a-48d5-4df5-aca7-dd324fdee14d" />


### Этап 9: Подготовка к расшифровке

<img width="1462" height="348" alt="image" src="https://github.com/user-attachments/assets/eecc0c80-5b73-4810-a148-389e5279c7e5" />



### Этап 10: Расшифровка и просмотр содержимого

<img width="774" height="237" alt="image" src="https://github.com/user-attachments/assets/90ed31c4-386d-4640-83f2-2e55b9c261cd" />


### Этап 11: Расшифровка и сохранение


<img width="978" height="257" alt="image" src="https://github.com/user-attachments/assets/68cf629e-ae2f-42cd-973b-c5bcb88a2e15" />


### Этап 12: Редактирование зашифрованного файла

<img width="684" height="194" alt="image" src="https://github.com/user-attachments/assets/d48c5b27-69d1-4858-83d4-18212edf752c" />

---

<img width="614" height="289" alt="image" src="https://github.com/user-attachments/assets/3b7bb3e5-a1e8-4e74-85da-396e21cfc41f" />

---

<img width="600" height="295" alt="image" src="https://github.com/user-attachments/assets/f0e20427-ae7c-4eeb-8d2c-50cd0c9d0b65" />

---


<img width="957" height="447" alt="image" src="https://github.com/user-attachments/assets/ba204cb7-d78e-447f-a222-71f0cdcea014" />







