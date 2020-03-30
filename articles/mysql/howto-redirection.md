---
title: Connettersi con il reindirizzamento - Database di Azure per MySQLConnect with redirection - Azure Database for MySQL
description: Questo articolo descrive come configurare l'applicazione per connettersi al database di Azure per MySQL con il reindirizzamento.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: f987d5d9640c3bfef61320df379a68eae2f4712b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246336"
---
# <a name="connect-to-azure-database-for-mysql-with-redirection"></a>Connettersi al database di Azure per MySQL con il reindirizzamentoConnect to Azure Database for MySQL with redirection

Questo argomento illustra come connettere un'applicazione al database di Azure per il server MySQL con la modalità di reindirizzamento. Il reindirizzamento ha lo scopo di ridurre la latenza di rete tra le applicazioni client e i server MySQL consentendo alle applicazioni di connettersi direttamente ai nodi del server back-end.

## <a name="before-you-begin"></a>Prima di iniziare
Accedere al [portale](https://portal.azure.com)di Azure . Creare un database di Azure per il server MySQL con la versione del motore 5.6, 5.7 o 8.0.Create an Azure Database for MySQL server with engine version 5.6, 5.7, or 8.0. Per informazioni dettagliate, vedere [Come creare il database di Azure per il server MySQL dal portale](quickstart-create-mysql-server-database-using-azure-portal.md) o [Come creare il database di Azure per il server MySQL con l'interfaccia della riga di comando](quickstart-create-mysql-server-database-using-azure-cli.md).

Il reindirizzamento è attualmente supportato solo quando SSL è abilitato nel database di Azure per il server MySQL.Redirection is currently only supported when **SSL is enabled** on your Azure Database for MySQL server. Per informazioni dettagliate su come configurare l'autenticazione SSL, vedere [Uso di SSL con il database di Azure per MySQL](howto-configure-ssl.md#step-3--enforcing-ssl-connections-in-azure).

## <a name="php"></a>PHP

Il supporto per il reindirizzamento nelle applicazioni PHP è disponibile tramite l'estensione [mysqlnd_azure,](https://github.com/microsoft/mysqlnd_azure) sviluppata da Microsoft. 

L'estensione mysqlnd_azure è disponibile per aggiungere alle applicazioni PHP tramite PECL ed è altamente raccomandato per installare e configurare l'estensione attraverso il [pacchetto PECL](https://pecl.php.net/package/mysqlnd_azure)pubblicato ufficialmente .

> [!IMPORTANT]
> Il supporto per il reindirizzamento nell'estensione [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) PHP è attualmente in anteprima.

### <a name="redirection-logic"></a>Logica di reindirizzamento

>[!IMPORTANT]
> La logica/comportamento di reindirizzamento che inizia la versione 1.1.0 è stata aggiornata ed è consigliabile usare la **versione 1.1.0.**

Il comportamento di reindirizzamento è `mysqlnd_azure.enableRedirect`determinato dal valore di . Nella tabella seguente viene descritto il comportamento del reindirizzamento in base al valore di questo parametro a partire dalla **versione 1.1.0.**

Se si utilizza una versione precedente dell'estensione mysqlnd_azure (versione 1.0.0-1.0.3), il `mysqlnd_azure.enabled`comportamento di reindirizzamento è determinato dal valore di . I valori `off` validi sono (atti simili al comportamento descritto `on` nella tabella `preferred` seguente) e (si comporta come nella tabella seguente).  

|**mysqlnd_azure.enableRedirect**| **Comportamento**|
|----------------------------------------|-------------|
|`off` o `0`|Il reindirizzamento non verrà utilizzato. |
|`on` o `1`|- Se SSL non è abilitato nel database di Azure per il server MySQL, non verrà stabilita alcuna connessione.- If SSL is not enabled on the Azure Database for MySQL server, no connection will be made. Verrà restituito il seguente errore: *"mysqlnd_azure.enableRedirect è attivato, ma l'opzione SSL non è impostata nella stringa di connessione. Il reindirizzamento è possibile solo con SSL."*<br>- Se SSL è abilitato sul server MySQL, ma il reindirizzamento non è supportato sul server, la prima connessione viene interrotta e viene restituito il seguente errore: *"Connessione interrotta perché il reindirizzamento non è abilitato sul server MySQL o il pacchetto* di rete non soddisfa il protocollo di reindirizzamento".<br>- Se il server MySQL supporta il reindirizzamento, ma la connessione reindirizzata non è riuscita per qualsiasi motivo, interrompere anche la prima connessione proxy. Restituire l'errore della connessione reindirizzata.|
|`preferred` o `2`<br>  (valore predefinito)|- mysqlnd_azure utilizzerà il reindirizzamento, se possibile.<br>- Se la connessione non utilizza SSL, il server non supporta il reindirizzamento o la connessione reindirizzata non riesce a connettersi per qualsiasi motivo non irreversibile mentre la connessione proxy è ancora valida, verrà eseguito il rollback alla prima connessione proxy.|

Nelle sezioni successive del documento verrà `mysqlnd_azure` descritto come installare l'estensione utilizzando PECL e impostare il valore di questo parametro.

### <a name="ubuntu-linux"></a>Ubuntu Linux

#### <a name="prerequisites"></a>Prerequisiti 
- PhP versioni 7.2.15 e 7.3.2
- PHP PEAR 
- php-mysql
- Database di Azure per il server MySQL con SSL abilitatoAzure Database for MySQL server with SSL enabled

1. Installare [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) con [PECL](https://pecl.php.net/package/mysqlnd_azure). Si consiglia di utilizzare la versione 1.1.0.

    ```bash
    sudo pecl install mysqlnd_azure
    ```

2. Individuare la directory`extension_dir`di estensione ( ) eseguendo quanto segue:

    ```bash
    php -i | grep "extension_dir"
    ```

3. Passare alla cartella restituita `mysqlnd_azure.so` e assicurarsi che si trovi in questa cartella. 

4. Individuare la cartella per i file ini eseguendo quanto segue: 

    ```bash
    php -i | grep "dir for additional .ini files"
    ```

5. Passare alla cartella restituita. 

6. Creare un nuovo file `mysqlnd_azure`ini per . Assicurarsi che l'ordine alfabetico del nome è dopo quello di mysqnld, dal momento che i moduli vengono caricati in base all'ordine dei nomi dei file ini. Se ad `mysqlnd` esempio .ini `10-mysqlnd.ini`è denominato , denominare mysqlnd ini come `20-mysqlnd-azure.ini`.

7. All'interno del nuovo file ini, aggiungere le righe seguenti per abilitare il reindirizzamento.

    ```bash
    extension=mysqlnd_azure
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="windows"></a>WINDOWS

#### <a name="prerequisites"></a>Prerequisiti 
- PhP versioni 7.2.15 e 7.3.2
- php-mysql
- Database di Azure per il server MySQL con SSL abilitatoAzure Database for MySQL server with SSL enabled

1. Determinare se si esegue una versione x64 o x86 di PHP eseguendo il comando seguente:

    ```cmd
    php -i | findstr "Thread"
    ```

2. Scaricare la versione x64 o x86 corrispondente della DLL [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) da [PECL](https://pecl.php.net/package/mysqlnd_azure) che corrisponde alla versione di PHP in uso. Si consiglia di utilizzare la versione 1.1.0.

3. Estrarre il file zip e `php_mysqlnd_azure.dll`trovare la DLL denominata .

4. Individuare la directory`extension_dir`di estensione ( ) eseguendo il comando seguente:

    ```cmd
    php -i | find "extension_dir"
    ```

5. Copiare `php_mysqlnd_azure.dll` il file nella directory restituita nel passaggio 4. 

6. Individuare la cartella PHP contenente il `php.ini` file utilizzando il seguente comando:

    ```cmd
    php -i | find "Loaded Configuration File"
    ```

7. Modificare `php.ini` il file e aggiungere le seguenti righe aggiuntive per abilitare il reindirizzamento. 

    Nella sezione Estensioni dinamiche: 
    ```cmd
    extension=mysqlnd_azure
    ```
    
    Nella sezione Impostazioni modulo:     
    ```cmd 
    [mysqlnd_azure]
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="confirm-redirection"></a>Confermare il reindirizzamento

È inoltre possibile verificare che il reindirizzamento sia configurato con il codice PHP di esempio riportato di seguito. Creare un file `mysqlConnect.php` PHP chiamato e incollare il codice seguente. Aggiornare il nome del server, il nome utente e la password con il proprio. 
 
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
Per ulteriori informazioni sulle stringhe di connessione, vedere [Stringhe di connessione](howto-connection-string.md).
