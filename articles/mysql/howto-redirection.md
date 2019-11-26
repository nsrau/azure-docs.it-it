---
title: Connettersi a database di Azure per MySQL con Reindirizzamento
description: Questo articolo descrive come configurare l'applicazione per la connessione a database di Azure per MySQL con reindirizzamento.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 11/15/2019
ms.openlocfilehash: eed52c444bbcad60b4e629cf476c285f53c9f515
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233740"
---
# <a name="connect-to-azure-database-for-mysql-with-redirection"></a>Connettersi a database di Azure per MySQL con Reindirizzamento

Questo argomento illustra come connettere un'applicazione al server di database di Azure per MySQL con la modalità di reindirizzamento. Il reindirizzamento mira a ridurre la latenza di rete tra le applicazioni client e i server MySQL consentendo alle applicazioni di connettersi direttamente ai nodi del server back-end.

> [!IMPORTANT]
> Il supporto per il reindirizzamento nel [MYSQLND_AZURE](https://github.com/microsoft/mysqlnd_azure) php è attualmente in versione di anteprima.

## <a name="before-you-begin"></a>Prima di iniziare
Accedere al [portale di Azure](https://portal.azure.com). Creare un database di Azure per il server MySQL con il motore versione 5,6, 5,7 o 8,0. Per informazioni dettagliate, vedere [Come creare il database di Azure per il server MySQL dal portale](quickstart-create-mysql-server-database-using-azure-portal.md) o [Come creare il database di Azure per il server MySQL con l'interfaccia della riga di comando](quickstart-create-mysql-server-database-using-azure-cli.md).

Il reindirizzamento è attualmente supportato solo quando SSL è abilitato. Per informazioni dettagliate su come configurare l'autenticazione SSL, vedere [Uso di SSL con il database di Azure per MySQL](https://docs.microsoft.com/azure/mysql/howto-configure-ssl#step-3-enforcing-ssl-connections-in-azure). 

## <a name="php"></a>PHP

### <a name="ubuntu-linux"></a>Ubuntu Linux

#### <a name="prerequisites"></a>prerequisiti 
- Versioni PHP 7.2.15 + e 7.3.2 +
- PERA PHP 
- PHP-MySQL
- Database di Azure per il server MySQL con SSL abilitato

1. Installare [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) con [PECL](https://pecl.php.net/package/mysqlnd_azure).

    ```bash
    sudo pecl install mysqlnd_azure
    ```

2. Individuare la directory dell'estensione (`extension_dir`) eseguendo questa procedura:

    ```bash
    php -i | grep "extension_dir"
    ```

3. Passare alla cartella restituita e verificare che `mysqlnd_azure.so` si trovi in questa cartella. 

4. Individuare la cartella per i file ini eseguendo il seguente: 

    ```bash
    php -i | grep "dir for additional .ini files"
    ```

5. Modificare le directory in questa cartella restituita. 

6. Creare un nuovo file ini per `mysqlnd_azure`. Verificare che l'ordine alfabetico del nome sia successivo a quello di mysqnld, perché i moduli vengono caricati in base all'ordine dei nomi dei file ini. Ad esempio, se `mysqlnd`. ini è denominato `10-mysqlnd.ini`, denominare il file mysqlnd ini come `20-mysqlnd-azure.ini`.

7. All'interno del nuovo file ini aggiungere le righe seguenti per abilitare il reindirizzamento.

    ```bash
    extension=mysqlnd_azure
    mysqlnd_azure.enabled=on
    ```

### <a name="windows"></a>Windows

#### <a name="prerequisites"></a>prerequisiti 
- Versioni PHP 7.2.15 + e 7.3.2 +
- PHP-MySQL
- Database di Azure per il server MySQL con SSL abilitato

1. Determinare se è in esecuzione una versione x64 o x86 di PHP eseguendo il comando seguente:

    ```cmd
    php -i | findstr "Thread"
    ```

2. Scaricare la versione x64 o x86 corrispondente della dll [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) da [PECL](https://pecl.php.net/package/mysqlnd_azure) che corrisponde alla versione di php. 

3. Estrarre il file zip e trovare la DLL denominata `php_mysqlnd_azure.dll`.

4. Individuare la directory dell'estensione (`extension_dir`) eseguendo il comando seguente:

    ```cmd
    php -i | find "extension_dir"s
    ```

5. Copiare il file `php_mysqlnd_azure.dll` nella directory restituita nel passaggio 4. 

6. Individuare la cartella PHP che contiene il file di `php.ini` usando il comando seguente:

    ```cmd
    php -i | find "Loaded Configuration File"
    ```

7. Modificare il file di `php.ini` e aggiungere le righe aggiuntive seguenti per abilitare il reindirizzamento. 

    Nella sezione estensioni dinamiche: 
    ```cmd
    extension=mysqlnd_azure
    ```
    
    Nella sezione Impostazioni modulo:     
    ```cmd 
    [mysqlnd_azure]
    mysqlnd_azure.enabled=on
    ```

### <a name="confirm-redirection"></a>Conferma Reindirizzamento

È anche possibile confermare che il reindirizzamento sia configurato con il codice PHP di esempio seguente. Creare un file PHP denominato `mysqlConnect.php` e incollare il codice seguente. Aggiornare il nome del server, il nome utente e la password con quelli personalizzati. 
 
 ```php
<?php
$host = '<yourservername>.mysql.database.azure.com';
$username = '<yourusername>@<yourservername>';
$password = '<yourpassword>';
$db_name = 'testdb';
  echo "mysqlnd_azure.enabled: ", ini_get("mysqlnd_azure.enabled") == true?"On":"Off", "\n";
  $db = mysqli_init();
  $link = mysqli_real_connect ($db, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL);
  if (!$link) {
     die ('Connect error (' . mysqli_connect_errno() . '): ' . mysqli_connect_error() . "\n");
  }
  else {
    echo $db->host_info, "\n"; //if redirection succeeds, the host_info will differ from the hostname you used used to connect
    $res = $db->query('SHOW TABLES;'); //test query with the connection
    print_r ($res);
$db->close();
  }
 ?>
 ```

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle stringhe di connessione, vedere l'argomento relativo alle [stringhe di connessione](howto-connection-string.md).

