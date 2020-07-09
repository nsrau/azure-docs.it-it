---
title: 'Connettersi con reindirizzamento: database di Azure per MariaDB'
description: Questo articolo descrive come è possibile configurare l'applicazione per la connessione al database di Azure per MariaDB con reindirizzamento.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: how-to
ms.date: 6/8/2020
ms.openlocfilehash: 45d93797b72b3b35dd44cddc22124acb73eb3454
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86121096"
---
# <a name="connect-to-azure-database-for-mariadb-with-redirection"></a>Connettersi al database di Azure per MariaDB con Reindirizzamento

Questo argomento illustra come connettere un'applicazione al database di Azure per il server MariaDB con la modalità di reindirizzamento. Il reindirizzamento mira a ridurre la latenza di rete tra le applicazioni client e i server MariaDB consentendo alle applicazioni di connettersi direttamente ai nodi del server back-end.

## <a name="before-you-begin"></a>Prima di iniziare
Accedere al [portale di Azure](https://portal.azure.com). Creare un database di Azure per il server MariaDB con il motore versione 10,2 o 10,3. 

Per informazioni dettagliate, vedere come creare un database di Azure per il server MariaDB usando il [portale di Azure](quickstart-create-mariadb-server-database-using-azure-portal.md) o l'interfaccia della riga di comando di [Azure](quickstart-create-mariadb-server-database-using-azure-cli.md).

## <a name="enable-redirection"></a>Abilita reindirizzamento

Nel database di Azure per il server MariaDB configurare il `redirect_enabled` parametro su `ON` per consentire le connessioni con la modalità di reindirizzamento. Per aggiornare il parametro del server, usare l' [portale di Azure](howto-server-parameters.md) o l'interfaccia della riga di comando di [Azure](howto-configure-server-parameters-cli.md).

## <a name="php"></a>PHP

Il supporto per il reindirizzamento nelle applicazioni PHP è disponibile tramite l'estensione [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure), sviluppata da Microsoft. 

L'estensione mysqlnd_azure è disponibile per l'aggiunta alle applicazioni PHP tramite PECL ed è consigliabile installarla e configurarla tramite il [pacchetto PECL](https://pecl.php.net/package/mysqlnd_azure) pubblicato ufficialmente.

> [!IMPORTANT]
> Il supporto per il reindirizzamento nell'estensione [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) PHP è attualmente in anteprima.

### <a name="redirection-logic"></a>Logica di reindirizzamento

>[!IMPORTANT]
> A partire dalla versione 1.1.0 la logica e il comportamento di reindirizzamento sono stati aggiornati ed **è consigliabile usare la versione 1.1.0+** .

Il comportamento di reindirizzamento è determinato dal valore di `mysqlnd_azure.enableRedirect`. La tabella seguente illustra il comportamento del reindirizzamento in base al valore di questo parametro a partire dalla **versione 1.1.0 +** .

Se si usa una versione precedente dell'estensione mysqlnd_azure (versione 1.0.0-1.0.3), il comportamento di reindirizzamento è determinato dal valore di `mysqlnd_azure.enabled`. I valori validi sono `off` (con un comportamento analogo a quello descritto nella tabella riportata di seguito) e `on` (con un comportamento analogo a quello di `preferred` nella tabella riportata di seguito).  

|**Valore mysqlnd_azure.enableRedirect**| **Comportamento**|
|----------------------------------------|-------------|
|`off` o `0`|Il reindirizzamento non viene usato. |
|`on` o `1`|- Se la connessione non usa SSL sul lato driver, non verrà eseguita alcuna connessione. Verrà restituito l'errore seguente: *"mysqlnd_azure.enableRedirect is on, but SSL option is not set in connection string. Redirection is only possible with SSL."* (mysqlnd_azure.enableRedirect è attivo ma l'opzione SSL non è impostata nella stringa di connessione. Il reindirizzamento è possibile solo con SSL).<br>-Se si usa SSL sul lato del driver, ma il reindirizzamento non è supportato nel server, la prima connessione viene interrotta e viene restituito l'errore seguente: *"connessione interrotta perché il reindirizzamento non è abilitato nel server MariaDB o il pacchetto di rete non soddisfa il protocollo di reindirizzamento".*<br>-Se il server MariaDB supporta il reindirizzamento, ma la connessione reindirizzata non riesce per qualsiasi motivo, interrompere anche la prima connessione proxy. Restituisce l'errore della connessione reindirizzata.|
|`preferred` o `2`<br> (valore predefinito)|- mysqlnd_azure userà il reindirizzamento, se possibile.<br>- Se la connessione non usa SSL sul lato driver, il server non supporta il reindirizzamento o la connessione reindirizzata non riesce per un qualsiasi motivo non irreversibile mentre la connessione proxy è ancora valida, verrà eseguito il fallback alla prima connessione proxy.|

Le prossime sezioni del documento illustrano come installare l'estensione `mysqlnd_azure` usando PECL e come impostare il valore di questo parametro.

### <a name="ubuntu-linux"></a>Ubuntu Linux

#### <a name="prerequisites"></a>Prerequisiti 
- PHP versioni 7.2.15+ e 7.3.2+
- PHP PEAR 
- php-mysql
- Database di Azure per il server MariaDB

1. Installare [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) con [PECL](https://pecl.php.net/package/mysqlnd_azure). È consigliabile usare la versione 1.1.0+.

    ```bash
    sudo pecl install mysqlnd_azure
    ```

2. Individuare la directory dell'estensione (`extension_dir`) eseguendo il comando seguente:

    ```bash
    php -i | grep "extension_dir"
    ```

3. Passare alla cartella restituita e verificare che `mysqlnd_azure.so` si trovi in questa cartella. 

4. Individuare la cartella dei file con estensione ini eseguendo il comando seguente: 

    ```bash
    php -i | grep "dir for additional .ini files"
    ```

5. Passare alla cartella restituita. 

6. Creare un nuovo file con estensione ini per `mysqlnd_azure`. Verificare che il nome sia successivo in ordine alfabetico a quello di mysqnld, perché i moduli vengono caricati in base all'ordine del nome dei file ini. Se ad esempio il nome del file con estensione ini di `mysqlnd` è `10-mysqlnd.ini`, assegnare al file ini di mysqlnd il nome `20-mysqlnd-azure.ini`.

7. All'interno del nuovo file con estensione ini aggiungere le righe seguenti per abilitare il reindirizzamento.

    ```bash
    extension=mysqlnd_azure
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="windows"></a>Windows

#### <a name="prerequisites"></a>Prerequisiti 
- PHP versioni 7.2.15+ e 7.3.2+
- php-mysql
- Database di Azure per il server MariaDB

1. Determinare se è in esecuzione una versione x64 o x86 di PHP eseguendo il comando seguente:

    ```cmd
    php -i | findstr "Thread"
    ```

2. Scaricare la versione x64 o x86 corrispondente della DLL di [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) da [PECL](https://pecl.php.net/package/mysqlnd_azure) corrispondente alla versione di PHP in uso. È consigliabile usare la versione 1.1.0+.

3. Estrarre il file ZIP e individuare la DLL denominata `php_mysqlnd_azure.dll`.

4. Individuare la directory dell'estensione (`extension_dir`) eseguendo il comando seguente:

    ```cmd
    php -i | find "extension_dir"
    ```

5. Copiare il file `php_mysqlnd_azure.dll` nella directory restituita nel passaggio 4. 

6. Individuare la cartella PHP che contiene il file `php.ini` usando il comando seguente:

    ```cmd
    php -i | find "Loaded Configuration File"
    ```

7. Modificare il file `php.ini` e aggiungere le righe seguenti per abilitare il reindirizzamento. 

    Nella sezione Dynamic Extensions: 
    ```cmd
    extension=mysqlnd_azure
    ```
    
    Nella sezione Module Settings:     
    ```cmd 
    [mysqlnd_azure]
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="confirm-redirection"></a>Verificare il reindirizzamento

È anche possibile verificare che il reindirizzamento sia configurato con il codice PHP di esempio seguente. Creare un file PHP denominato `mysqlConnect.php` e incollarvi il codice riportato di seguito. Aggiornare il nome del server, il nome utente e la password con quelli da usare effettivamente. 
 
 ```php
<?php
$host = '<yourservername>.mariadb.database.azure.com';
$username = '<yourusername>@<yourservername>';
$password = '<yourpassword>';
$db_name = 'testdb';
  echo "mysqlnd_azure.enableRedirect: ", ini_get("mysqlnd_azure.enableRedirect"), "\n";
  $db = mysqli_init();
  //The connection must be configured with SSL for redirection test
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
Per altre informazioni sulle stringhe di connessione, vedere [Stringhe di connessione](howto-connection-string.md).
