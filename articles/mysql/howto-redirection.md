---
title: Connettersi con reindirizzamento-database di Azure per MySQL
description: Questo articolo descrive come configurare l'applicazione per la connessione a database di Azure per MySQL con reindirizzamento.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: f987d5d9640c3bfef61320df379a68eae2f4712b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80246336"
---
# <a name="connect-to-azure-database-for-mysql-with-redirection"></a>Connettersi a database di Azure per MySQL con Reindirizzamento

Questo argomento illustra come connettere un'applicazione al server di database di Azure per MySQL con la modalità di reindirizzamento. Il reindirizzamento mira a ridurre la latenza di rete tra le applicazioni client e i server MySQL consentendo alle applicazioni di connettersi direttamente ai nodi del server back-end.

## <a name="before-you-begin"></a>Prima di iniziare
Accedere al [portale di Azure](https://portal.azure.com). Creare un database di Azure per il server MySQL con il motore versione 5,6, 5,7 o 8,0. Per informazioni dettagliate, vedere [Come creare il database di Azure per il server MySQL dal portale](quickstart-create-mysql-server-database-using-azure-portal.md) o [Come creare il database di Azure per il server MySQL con l'interfaccia della riga di comando](quickstart-create-mysql-server-database-using-azure-cli.md).

Il reindirizzamento è attualmente supportato solo quando **SSL è abilitato** nel database di Azure per il server MySQL. Per informazioni dettagliate su come configurare l'autenticazione SSL, vedere [Uso di SSL con il database di Azure per MySQL](howto-configure-ssl.md#step-3--enforcing-ssl-connections-in-azure).

## <a name="php"></a>PHP

Il supporto per il reindirizzamento nelle applicazioni PHP è disponibile tramite l'estensione [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) , sviluppato da Microsoft. 

L'estensione mysqlnd_azure è disponibile per l'aggiunta alle applicazioni PHP tramite PECL ed è consigliabile installare e configurare l'estensione tramite il [pacchetto PECL](https://pecl.php.net/package/mysqlnd_azure)pubblicato ufficialmente.

> [!IMPORTANT]
> Il supporto per il reindirizzamento nell'estensione PHP [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) è attualmente in versione di anteprima.

### <a name="redirection-logic"></a>Logica di Reindirizzamento

>[!IMPORTANT]
> La logica o il comportamento di reindirizzamento a partire dalla versione 1.1.0 è stato aggiornato ed **è consigliabile usare la versione 1.1.0 +**.

Il comportamento di reindirizzamento è determinato dal valore di `mysqlnd_azure.enableRedirect`. La tabella seguente illustra il comportamento del reindirizzamento in base al valore di questo parametro a partire dalla **versione 1.1.0 +**.

Se si usa una versione precedente dell'estensione mysqlnd_azure (versione 1.0.0-1.0.3), il comportamento di reindirizzamento è determinato dal valore di `mysqlnd_azure.enabled`. I valori validi sono `off` (agisce in modo analogo al comportamento descritto nella tabella riportata di seguito) `on` e (si `preferred` comporta come nella tabella riportata di seguito).  

|**valore mysqlnd_azure. enableRedirect**| **Comportamento**|
|----------------------------------------|-------------|
|`off` o `0`|Il reindirizzamento non verrà usato. |
|`on` o `1`|-Se SSL non è abilitato nel database di Azure per il server MySQL, non verrà eseguita alcuna connessione. Verrà restituito l'errore seguente: *"mysqlnd_azure. enableRedirect è on, ma l'opzione SSL non è impostata nella stringa di connessione. Il reindirizzamento è possibile solo con SSL. "*<br>-Se SSL è abilitato nel server MySQL, ma il reindirizzamento non è supportato nel server, la prima connessione viene interrotta e viene restituito l'errore seguente: *"connessione interrotta perché il reindirizzamento non è abilitato nel server MySQL oppure il pacchetto di rete non soddisfa il protocollo di reindirizzamento".*<br>-Se il server MySQL supporta il reindirizzamento, ma la connessione reindirizzata non riesce per qualsiasi motivo, interrompere anche la prima connessione proxy. Restituisce l'errore della connessione reindirizzata.|
|`preferred` o `2`<br>  (valore predefinito)|-mysqlnd_azure utilizzerà il reindirizzamento, se possibile.<br>-Se la connessione non usa SSL, il server non supporta il reindirizzamento o la connessione reindirizzata non riesce a connettersi per qualsiasi motivo non irreversibile, mentre la connessione del proxy è ancora valida, verrà eseguito il fallback alla prima connessione proxy.|

Nelle sezioni successive del documento verrà illustrato come installare l' `mysqlnd_azure` estensione utilizzando PECL e impostare il valore di questo parametro.

### <a name="ubuntu-linux"></a>Ubuntu Linux

#### <a name="prerequisites"></a>Prerequisiti 
- Versioni PHP 7.2.15 + e 7.3.2 +
- PERA PHP 
- PHP-MySQL
- Database di Azure per il server MySQL con SSL abilitato

1. Installare [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) con [PECL](https://pecl.php.net/package/mysqlnd_azure). Si consiglia di usare la versione 1.1.0 +.

    ```bash
    sudo pecl install mysqlnd_azure
    ```

2. Individuare la directory dell'estensione`extension_dir`() eseguendo la procedura seguente:

    ```bash
    php -i | grep "extension_dir"
    ```

3. Passare alla cartella restituita e assicurarsi `mysqlnd_azure.so` che si trovi in questa cartella. 

4. Individuare la cartella per i file ini eseguendo il seguente: 

    ```bash
    php -i | grep "dir for additional .ini files"
    ```

5. Modificare le directory in questa cartella restituita. 

6. Creare un nuovo file ini per `mysqlnd_azure`. Verificare che l'ordine alfabetico del nome sia successivo a quello di mysqnld, perché i moduli vengono caricati in base all'ordine dei nomi dei file ini. Ad esempio, se `mysqlnd` . ini è denominato `10-mysqlnd.ini`, denominare il file `20-mysqlnd-azure.ini`mysqlnd ini come.

7. All'interno del nuovo file ini aggiungere le righe seguenti per abilitare il reindirizzamento.

    ```bash
    extension=mysqlnd_azure
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="windows"></a>Windows

#### <a name="prerequisites"></a>Prerequisiti 
- Versioni PHP 7.2.15 + e 7.3.2 +
- PHP-MySQL
- Database di Azure per il server MySQL con SSL abilitato

1. Determinare se è in esecuzione una versione x64 o x86 di PHP eseguendo il comando seguente:

    ```cmd
    php -i | findstr "Thread"
    ```

2. Scaricare la versione x64 o x86 corrispondente della dll [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) da [PECL](https://pecl.php.net/package/mysqlnd_azure) che corrisponde alla versione di php. Si consiglia di usare la versione 1.1.0 +.

3. Estrarre il file zip e trovare la DLL denominata `php_mysqlnd_azure.dll`.

4. Individuare la directory dell'estensione`extension_dir`() eseguendo il comando seguente:

    ```cmd
    php -i | find "extension_dir"
    ```

5. Copiare il `php_mysqlnd_azure.dll` file nella directory restituita nel passaggio 4. 

6. Individuare la cartella PHP che contiene `php.ini` il file usando il comando seguente:

    ```cmd
    php -i | find "Loaded Configuration File"
    ```

7. Modificare il `php.ini` file e aggiungere le righe aggiuntive seguenti per abilitare il reindirizzamento. 

    Nella sezione estensioni dinamiche: 
    ```cmd
    extension=mysqlnd_azure
    ```
    
    Nella sezione Impostazioni modulo:     
    ```cmd 
    [mysqlnd_azure]
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="confirm-redirection"></a>Conferma Reindirizzamento

È anche possibile confermare che il reindirizzamento sia configurato con il codice PHP di esempio seguente. Creare un file PHP denominato `mysqlConnect.php` e incollare il codice seguente. Aggiornare il nome del server, il nome utente e la password con quelli personalizzati. 
 
 ```php
<?php
$host = '<yourservername>.mysql.database.azure.com';
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
Per ulteriori informazioni sulle stringhe di connessione, vedere [Connection Strings](howto-connection-string.md).
