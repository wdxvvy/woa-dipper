<img align="right" src="https://github.com/n00b69/woa-dipper/blob/main/dipper.png" width="350" alt="Windows 11 running on dipper">

# Запуск Winows на Xiaomi Mi 8

## Установка Windows

### Требования
- [ARM образ Windows](https://worproject.com/esd)
  
- [Драйвера](https://github.com/n00b69/woa-dipper/releases/tag/Drivers)

- [Devcfg исправления touch](https://github.com/n00b69/woa-dipper/releases/download/Files/devcfg-polaris.img)
  
- [Образ UEFI](https://github.com/n00b69/woa-dipper/releases/tag/UEFI)

### Загрузка в UEFI
> Замените **<путь\к\dipper-uefi.img>** с актуальным путём к образу UEFI
```cmd
fastboot boot <путь\к\dipper-uefi.img>
```

#### Включение режима mass storage
> После загрузки в UEFI используйте кнопки регулировки громкости для навигации по меню и кнопку питания для подтверждения
- Выберите **UEFI Boot Menu**.
- Выберите **USB Attached SCSI (UAS) Storage**.
- Нажмите кнопку **питания** дважды чтобы подтвердить.

### Diskpart
> [!WARNING]
> НЕ УДАЛЯЙТЕ, НЕ СОЗДАВАЙТЕ И НЕ ИЗМЕНЯЙТЕ КАКИМ-ЛИБО ИНЫМ ОБРАЗОМ РАЗДЕЛЫ, НАХОДЯСЬ В DISKPART!!!! ЭТО МОЖЕТ ПРИВЕСТИ К УДАЛЕНИЮ UFS ИЛИ НЕВОЗМОЖНОСТИ ЗАГРУЗКИ В FASTBOOT!!!! ЭТО ОЗНАЧАЕТ, ЧТО ВАШЕ УСТРОЙСТВО БУДЕТ ОКИРПИЧЕНО БЕЗ КАКОГО-ЛИБО РЕШЕНИЯ! (за исключением доставки устройства в Xiaomi или его перепрошивки с помощью EDL, и то, и другое, скорее всего, будет стоить денег)
```cmd
diskpart
```

#### Найдите ваш телефон
> При этом отобразится список всех подключенных дисков
```cmd
lis dis
```

#### Выберите ваш телефон
> Замените `$` актуальным номером вашего телефона (должен быть последним)
```cmd
sel dis $
```

#### Отобразить список разделов вашего телефона
> Это отобразит список разделов вашего телефона 
```cmd
lis par
```

#### Выбрать раздел Windows 
> Замените `$` номером раздела Windows (должен быть 23)
```cmd
sel par $
```

#### Отформатировать раздел Windows
```cmd
format quick fs=ntfs label="WINDIPPER"
```

#### Добавить букву к разделу Windows
```cmd
assign letter X
```

#### Выбhfnm раздел ESP
> Замените `$` номером раздела ESP (должен быть 22)
```cmd
sel par $
```

#### Отформатировать раздел ESP
```cmd
format quick fs=fat32 label="ESPDIPPER"
```

#### Добавьте букву к ESP
```cmd
assign letter Y
```

#### Выйти из diskpart
```cmd
exit
```

### Установка Windows
> Замените `<путь\к\install.esd>` актуальным путём к install.esd (он также может называться install.wim)
```cmd
dism /apply-image /ImageFile:<путь\к\install.esd> /index:6 /ApplyDir:X:\
```

> Если вы получите `Error 87`, проверьте индекс вышего образа используя `dism /get-imageinfo /ImageFile:<путь\к\install.esd>`, затем замените `index:6` действтельным индексом Windows 11 Pro в вашем образе

### Установка драйверов
> Распакуйте пакет драйверов, затем откройте файл `OfflineUpdater.cmd` 

> Введите букву диска **WINDIPPER**, должна быть X, затем нажмите Enter

> Если в разделе **Installing App Packages** появятся какие-либо ошибки, проигнорируйте их и продолжайте
  
#### Создать файлы загрузчика Windows
```cmd
bcdboot X:\Windows /s Y: /f UEFI
```

#### Включение тестовой подписи
```cmd
bcdedit /store Y:\EFI\Microsoft\BOOT\BCD /set "{default}" testsigning on
```

#### Выключение восстановления 
```cmd
bcdedit /store Y:\EFI\Microsoft\BOOT\BCD /set "{default}" recoveryenabled no
```

#### Отключение проверки целостности
```cmd
bcdedit /store Y:\EFI\Microsoft\BOOT\BCD /set "{default}" nointegritychecks on
```

### Отвязать буквы дисков
> Чтобы они не остались после отключения устройства
```cmd
diskpart
```

#### Выбрать раздел Windows телефона
> Используйте `list volume` чтобы найти его, замените `$` номером **WINDIPPER**
```diskpart
select volume $
```

#### Отвязать букву X
```diskpart
remove letter x
```

#### Выбрать раздел ESP телефна
> Используйте `list volume` чтобы найти его, замените `$` номером **ESPDIPPER**
```diskpart
select volume $
```

#### Отвязать букву Y
```diskpart
remove letter y
```

#### Выйти из diskpart
```diskpart
exit
```

### Исправить touch
> Перезагрузитесь в fastboot, затем замените **path\to** путём к образу
```cmd
fastboot flash devcfg_ab path\to\devcgf-dipper.img
```

### Перезагрузка в Android
> Чтобы настроить двойную загрузку

## [Последний шаг: Настройка двойной загрузки](dualboot-ru.md)
