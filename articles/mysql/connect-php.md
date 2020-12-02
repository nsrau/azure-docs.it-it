---
title: 'Avvio rapido: Connettersi con PHP - Database di Azure per MySQL'
description: Questa guida introduttiva fornisce diversi esempi di codice PHP che è possibile usare per connettersi ai dati ed eseguire query da Database di Azure per MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/28/2020
ms.openlocfilehash: cb3b711c532ccf44bebf08d42b5284db458cf5b7
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492659"
---
# <a name="quickstart-use-php-to-connect-and-query-data-in-azure-database-for-mysql"></a>Avvio rapido: Usare PHP per connettersi ai dati ed eseguire query in Database di Azure per MySQL
Questa guida introduttiva illustra come connettersi a un database di Azure per MySQL usando un'applicazione [PHP](https://secure.php.net/manual/intro-whatis.php). Spiega come usare le istruzioni SQL per eseguire query, inserire, aggiornare ed eliminare dati nel database.

## <a name="prerequisites"></a>Prerequisiti
Per questa guida di avvio rapido, è necessario:

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free).
- Creare un server singolo del database di Azure per MySQL usando il [portale di Azure](./quickstart-create-mysql-server-database-using-azure-portal.md) <br/> o in alternativa l'[interfaccia della riga di comando di Azure](./quickstart-create-mysql-server-database-using-azure-cli.md).
- A seconda che si usi l'accesso pubblico o privato, completare **UNA** delle azioni seguenti per abilitare la connettività.

    |Action| Metodo di connettività|Guida pratica|
    |:--------- |:--------- |:--------- |
    | **Configurare le regole del firewall** | Pubblico | [Portale](./howto-manage-firewall-using-portal.md) <br/> [CLI](./howto-manage-firewall-using-cli.md)|
    | **Configurare l'endpoint di servizio** | Pubblico | [Portale](./howto-manage-vnet-using-portal.md) <br/> [CLI](./howto-manage-vnet-using-cli.md)|
    | **Configurare il collegamento privato** | Privato | [Portale](./howto-configure-privatelink-portal.md) <br/> [CLI](./howto-configure-privatelink-cli.md) |

- [Creare un database e un utente non amministratore](./howto-create-users.md?tabs=single-server)
- Installare la versione di PHP più recente per il sistema operativo in uso
    - [PHP in macOS](https://secure.php.net/manual/install.macosx.php)
    - [PHP in Linux](https://secure.php.net/manual/install.unix.php)
    - [PHP in Windows](https://secure.php.net/manual/install.windows.php)

> [!NOTE]
> In questo avvio rapido viene usata la libreria [MySQLi](https://www.php.net/manual/en/book.mysqli.php) per gestire la connessione ed eseguire query sul server.

## <a name="get-connection-information"></a>Ottenere informazioni di connessione
È possibile ottenere le informazioni di connessione del server di database dal portale di Azure seguendo questa procedura:

1. Accedere al [Portale di Azure](https://portal.azure.com/).
2. Passare alla pagina Database di Azure per MySQL. È possibile cercare e selezionare **Database di Azure per MySQL**.
:::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/find-azure-mysql-in-portal.png" alt-text="Trovare Database di Azure per MySQL":::

2. Selezionare il server MySQL (ad esempio **mydemoserver**).
3. Nella pagina **Panoramica** copiare il nome completo del server accanto a **Nome del server** e il nome utente amministratore accanto a **Nome di accesso dell'amministratore server**. Per copiare il nome del server o il nome host, passare il puntatore su di esso e selezionare l'icona **Copia**.

> [!IMPORTANT]
> - Se si è dimenticata la password, è possibile [reimpostarla](./howto-create-manage-server-portal.md#update-admin-password).
> - Sostituire i parametri **host, username, password** e **db_name** con i valori effettivi**

## <a name="step-1-connect-to-the-server"></a>Passaggio 1: Connettersi al server
SSL è abilitato per impostazione predefinita. Potrebbe essere necessario scaricare il [certificato SSL DigiCertGlobalRootG2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) per connettersi dall'ambiente locale. Questo codice chiama:
- [mysqli_init](https://secure.php.net/manual/mysqli.init.php) per inizializzare MySQLi.
- [mysqli_ssl_set](https://www.php.net/manual/en/mysqli.ssl-set.php) per puntare al percorso del certificato SSL. Questo è necessario per l'ambiente locale ma non per l'app Web del servizio app o per le macchine virtuali di Azure.
- [mysqli_real_connect](https://secure.php.net/manual/mysqli.real-connect.php) per stabilire la connessione a MySQL.
- [mysqli_close](https://secure.php.net/manual/mysqli.close.php) per chiudere la connessione.


```php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin@mydemoserver';
$password = 'your_password';
$db_name = 'your_database';

//Initializes MySQLi
$conn = mysqli_init();

// If using  Azure Virtual machines or Azure Web App, 'mysqli-ssl_set()' is not required as the certificate is already installed on the machines.
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/DigiCertGlobalRootG2.crt.pem", NULL, NULL);

// Establish the connection
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin@mydemoserver', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);

//If connection failed, show the error
if (mysqli_connect_errno($conn))
{
    die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
[Problemi? Segnalarli](https://aka.ms/mysql-doc-feedback)

## <a name="step-2-create-a-table"></a>Passaggio 2: Creare una tabella
Per stabilire la connessione, usare il codice seguente. Questo codice chiama:
- [mysqli_query](https://secure.php.net/manual/mysqli.query.php) per eseguire la query.
```php
// Run the create table query
if (mysqli_query($conn, '
CREATE TABLE Products (
`Id` INT NOT NULL AUTO_INCREMENT ,
`ProductName` VARCHAR(200) NOT NULL ,
`Color` VARCHAR(50) NOT NULL ,
`Price` DOUBLE NOT NULL ,
PRIMARY KEY (`Id`)
);
')) {
printf("Table created\n");
}
```

## <a name="step-3-insert-data"></a>Passaggio 3: Inserire i dati
Usare il codice seguente per inserire i dati usando un'istruzione SQL **INSERT**. Questo codice usa i metodi:
- [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php) per creare un'istruzione insert preparata
- [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php) per associare i parametri per ogni valore di colonna inserito
- [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php)
- [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php) per chiudere l'istruzione usando il metodo


```php
//Create an Insert prepared statement and run it
$product_name = 'BrandNewProduct';
$product_color = 'Blue';
$product_price = 15.5;
if ($stmt = mysqli_prepare($conn, "INSERT INTO Products (ProductName, Color, Price) VALUES (?, ?, ?)"))
{
    mysqli_stmt_bind_param($stmt, 'ssd', $product_name, $product_color, $product_price);
    mysqli_stmt_execute($stmt);
    printf("Insert: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
    mysqli_stmt_close($stmt);
}

```

## <a name="step-4-read-data"></a>Passaggio 4: Leggere i dati
Usare il codice seguente per leggere i dati mediante un'istruzione SQL **SELECT**.  Il codice usa i metodi:
- [mysqli_query](https://secure.php.net/manual/mysqli.query.php) per eseguire la query **SELECT**.
- [mysqli_fetch_assoc](https://secure.php.net/manual/mysqli-result.fetch-assoc.php) per recuperare le righe risultanti.

```php
//Run the Select query
printf("Reading data from table: \n");
$res = mysqli_query($conn, 'SELECT * FROM Products');
while ($row = mysqli_fetch_assoc($res))
 {
    var_dump($row);
 }

```


## <a name="step-5-delete-data"></a>Passaggio 5: Eliminare i dati
Usare il codice seguente per eliminare righe usando un'istruzione SQL **DELETE**. Il codice usa i metodi:
- [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php) per creare un'istruzione delete preparata
- [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php) per associare i parametri
- [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php) per eseguire l'istruzione delete preparata
- [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php) per chiudere l'istruzione

```php
//Run the Delete statement
$product_name = 'BrandNewProduct';
if ($stmt = mysqli_prepare($conn, "DELETE FROM Products WHERE ProductName = ?")) {
mysqli_stmt_bind_param($stmt, 's', $product_name);
mysqli_stmt_execute($stmt);
printf("Delete: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
mysqli_stmt_close($stmt);
}
```

## <a name="clean-up-resources"></a>Pulire le risorse

Per pulire tutte le risorse usate in questo argomento di avvio rapido, eliminare il gruppo di risorse con il comando seguente:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Gestire il server di Database di Azure per MySQL usando il portale](./howto-create-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [Gestire il server di Database di Azure per MySQL usando l'interfaccia della riga di comando](./how-to-manage-single-server-cli.md)

[Ci sono problemi a trovare le informazioni giuste? Segnalarli](https://aka.ms/mysql-doc-feedback).