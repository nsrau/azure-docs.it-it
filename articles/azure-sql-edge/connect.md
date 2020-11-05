---
title: Connettersi ed eseguire query in Azure SQL Edge
description: Informazioni su come connettersi ed eseguire query su Azure SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 07/25/2020
ms.openlocfilehash: b56b65261950e9cf534a3755d214229ef7d5bb1e
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93395207"
---
# <a name="connect-and-query-azure-sql-edge"></a>Connettersi ed eseguire query in Azure SQL Edge

In Azure SQL Edge, dopo la distribuzione di un contenitore, è possibile connettersi al motore di database da uno dei percorsi seguenti:

- Dall'interno del contenitore.
- Da un altro contenitore Docker in esecuzione nello stesso host
- Dal computer host
- Da qualsiasi altro computer client nella rete

## <a name="tools-to-connect-to-azure-sql-edge"></a>Strumenti per la connessione a SQL Edge di Azure

È possibile connettersi a un'istanza di Azure SQL Edge da uno degli strumenti comuni seguenti:

* [SQLCMD](/sql/linux/sql-server-linux-setup-tools): gli strumenti client sqlcmd sono già inclusi nell'immagine del contenitore di Azure SQL Edge. Se ci si connette a un contenitore in esecuzione con una shell bash interattiva, è possibile eseguire gli strumenti in locale. Gli strumenti client di SQL non sono disponibili nella piattaforma ARM64, di conseguenza non sono inclusi nella versione ARM64 dei contenitori SQL Edge. 
* [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms)
* [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)
* [Visual Studio Code](/sql/visual-studio-code/sql-server-develop-use-vscode)

Per connettersi a un motore di database di Azure SQL Edge da un computer di rete, è necessario quanto segue:

- **Indirizzo IP o nome di rete del computer host** : si tratta del computer host in cui è in esecuzione il contenitore Edge di Azure SQL.
- **Mapping della porta host del contenitore Edge di Azure SQL** : questo è il mapping della porta del contenitore Docker a una porta nell'host. All'interno del contenitore, Azure SQL Edge viene sempre mappato alla porta 1433. Se lo si desidera, è possibile modificarlo. Per modificare il numero di porta, aggiornare le **Opzioni di creazione del contenitore** per il modulo Azure SQL Edge in Azure IOT Edge. Nell'esempio seguente, la porta 1433 nel contenitore viene mappata alla porta 1600 nell'host.

    ```JSON
    {
        "PortBindings": {
          "1433/tcp": [
            {
              "HostPort": "1600"
            }
          ]
        }
    }
    ```

- **Password sa per l'istanza di Azure SQL Edge** : questo è il valore specificato per la `SA_PASSWORD` variabile di ambiente durante la distribuzione di Azure SQL Edge.

## <a name="connect-to-the-database-engine-from-within-the-container"></a>Connettersi al motore di database dall'interno del contenitore

Gli [strumenti da riga di comando SQL Server](/sql/linux/sql-server-linux-setup-tools) sono inclusi nell'immagine del contenitore di Azure SQL Edge. Se ci si connette al contenitore con un prompt dei comandi interattivo, è possibile eseguire gli strumenti localmente. Gli strumenti client di SQL non sono disponibili nella piattaforma ARM64, di conseguenza non sono inclusi nella versione ARM64 dei contenitori SQL Edge. 

1. Usare il comando `docker exec -it` per avviare una shell Bash interattiva all'interno del contenitore in esecuzione. Nell'esempio seguente `e69e056c702d` è l'ID del contenitore.

    ```bash
    docker exec -it <Azure SQL Edge container ID or name> /bin/bash
    ```

    > [!TIP]
    > Non è sempre necessario specificare l'intero ID del contenitore. È sufficiente specificare un numero sufficiente di caratteri per identificarlo in modo univoco. Quindi, in questo esempio, potrebbe essere sufficiente usare `e6` o `e69` , anziché l'ID completo.

2. Quando ci si trova all'interno del contenitore, connettersi localmente con sqlcmd. Per impostazione predefinita, sqlcmd non è presente nel percorso, quindi è necessario specificare il percorso completo.

    ```bash
    /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P '<YourPassword>'
    ```

3. Al termine di sqlcmd, digitare `exit` .

4. Al termine del prompt dei comandi interattivo, digitare `exit` . Dopo la chiusura della shell Bash interattiva, il contenitore continua l'esecuzione.

## <a name="connect-to-azure-sql-edge-from-another-container-on-the-same-host"></a>Connettersi ad Azure SQL Edge da un altro contenitore nello stesso host

Poiché due contenitori in esecuzione nello stesso host si trovano nella stessa rete Docker, è possibile accedervi facilmente usando il nome del contenitore e l'indirizzo della porta per il servizio. Se ad esempio si esegue la connessione all'istanza di Azure SQL Edge da un altro modulo Python (contenitore) nello stesso host, è possibile usare una stringa di connessione simile alla seguente. Questo esempio si basa sul presupposto che Azure SQL Edge sia configurato per l'ascolto sulla porta predefinita.

```python

import pyodbc
server = 'MySQLEdgeContainer' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

## <a name="connect-to-azure-sql-edge-from-another-network-machine"></a>Connettersi ad Azure SQL Edge da un altro computer di rete

Potrebbe essere necessario connettersi all'istanza di Azure SQL Edge da un altro computer in rete. A tale scopo, usare l'indirizzo IP dell'host Docker e la porta host a cui è stato eseguito il mapping del contenitore Edge di Azure SQL. Ad esempio, se l'indirizzo IP dell'host Docker è *xxx.xxx.xxx.xxx* e il contenitore Edge di Azure SQL viene mappato alla porta host *1600* , l'indirizzo del server per l'istanza di Azure SQL Edge sarà *xxx. xxx. xxx. xxx, 1600*. Lo script Python aggiornato è:

```python

import pyodbc
server = 'xxx.xxx.xxx.xxx,1600' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

Per connettersi a un'istanza di Azure SQL Edge usando SQL Server Management Studio in esecuzione in un computer Windows, vedere [SQL Server Management Studio](/sql/linux/sql-server-linux-manage-ssms).

Per connettersi a un'istanza di Azure SQL Edge usando Visual Studio Code in un computer Windows, Mac o Linux, vedere [Visual Studio Code](/sql/visual-studio-code/sql-server-develop-use-vscode).

Per connettersi a un'istanza di Azure SQL Edge usando Azure Data Studio in un computer Windows, Mac o Linux, vedere [Azure Data Studio](/sql/azure-data-studio/quickstart-sql-server).

## <a name="next-steps"></a>Passaggi successivi

[Connessione ed esecuzione di query](/sql/linux/sql-server-linux-configure-docker#connect-and-query)

[Installare gli strumenti di SQL Server in Linux](/sql/linux/sql-server-linux-setup-tools)