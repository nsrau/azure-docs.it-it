---
title: Connettersi ed eseguire query in SQL Edge di Azure (anteprima)
description: Informazioni sulla connessione e l'esecuzione di query in SQL Edge di Azure (anteprima)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 17bb5afebe0335bcdcf40298490f94999c04a621
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83593950"
---
# <a name="connect-and-query-azure-sql-edge-preview"></a>Connettersi ed eseguire query in SQL Edge di Azure (anteprima)

Dopo aver distribuito il contenitore SQL Edge di Azure, è possibile connettersi al motore di database SQL da una delle posizioni seguenti.

- Dall'interno del contenitore.
- Da un altro contenitore Docker in esecuzione nello stesso host.
- Dal computer host.
- Da qualsiasi altro computer client nella rete.

## <a name="tools-to-connect-to-azure-sql-edge"></a>Strumenti per la connessione a SQL Edge di Azure

Le connessioni a un'istanza di SQL Edge di Azure possono essere eseguite da uno degli strumenti comuni indicati di seguito.

* [sqlcmd](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools): gli strumenti client sqlcmd sono già inclusi nell'immagine del contenitore SQL Edge di Azure. Se ci si connette a un contenitore in esecuzione con una shell bash interattiva, è possibile eseguire gli strumenti in locale.
* [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms)
* [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/download-azure-data-studio)
* [Visual Studio Code](https://docs.microsoft.com/sql/visual-studio-code/sql-server-develop-use-vscode)

Per connettersi a un motore di database di SQL Edge di Azure da un computer della rete, è necessario quanto segue:

- *Indirizzo IP o nome di rete del computer host*: si tratta del computer host in cui è in esecuzione il contenitore SQL Edge di Azure.
- *Mapping della porta host del contenitore SQL Edge di Azure*: si tratta del mapping tra la porta del contenitore Docker e una porta nell'host. Il mapping all'interno del contenitore SQL Edge viene sempre eseguito alla porta 1433. La porta può essere modificata come parte della distribuzione di SQL Edge di Azure. Per modificare il numero di porta, aggiornare "Opzioni di creazione del contenitore" per il modulo SQL Edge in Azure IoT Edge. Nell'esempio riportato di seguito, la porta 1433 nel contenitore viene mappata alla porta 1600 nell'host.

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

- *Password SA per l'istanza di SQL Edge*: si tratta del valore specificato per la variabile di ambiente **SA_PASSWORD** durante la distribuzione di SQL Edge.

## <a name="connecting-to-the-database-engine-from-within-the-container"></a>Connessione al motore di database dal contenitore

Gli [strumenti da riga di comando di SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) sono inclusi nell'immagine del contenitore di SQL Edge di Azure. Se ci si connette al contenitore con un prompt dei comandi interattivo, è possibile eseguire gli strumenti localmente.

1. Usare il comando `docker exec -it` per avviare una shell Bash interattiva all'interno del contenitore in esecuzione. Nell'esempio seguente `e69e056c702d` è l'ID del contenitore.

    ```bash
    docker exec -it <Azure SQL Edge container id or name> /bin/bash
    ```

    > [!TIP]
    > Non è sempre necessario specificare l'intero ID del contenitore. È sufficiente specificare un numero sufficiente di caratteri per identificarlo in modo univoco. In questo esempio potrebbe essere quindi sufficiente usare `e6` o `e69` anziché l'ID completo.

2. Una volta all'interno del contenitore, eseguire la connessione in locale con sqlcmd. Sqlcmd non è incluso nel percorso per impostazione predefinita, quindi occorre specificare il percorso completo.

    ```bash
    /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P '<YourPassword>'
    ```

3. Dopo aver completato le operazioni con sqlcmd, digitare `exit`.

4. Al termine delle operazioni con il prompt dei comandi interattivo, digitare `exit`. Dopo la chiusura della shell Bash interattiva, il contenitore continua l'esecuzione.

## <a name="connect-to-sql-edge-from-another-container-on-the-same-host"></a>Connettersi a SQL Edge da un altro contenitore nello stesso host

Poiché due contenitori in esecuzione nello stesso host si trovano nella stessa rete Docker, è possibile accedervi facilmente usando il nome del contenitore e l'indirizzo della porta per il servizio. Ad esempio, se ci si connette all'istanza di SQL Edge da un altro modulo Python (contenitore) nello stesso host, è possibile usare una stringa di connessione simile alla seguente. Nell'esempio seguente si presuppone che SQL Edge sia configurato per l'ascolto sulla porta predefinita.

```python

import pyodbc
server = 'MySQLEdgeContainer' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

## <a name="connect-to-sql-edge-from-another-network-machine"></a>Connettersi a SQL Edge da un altro computer della rete

Per connettersi all'istanza di SQL Edge da un altro computer nella rete, è necessario usare l'indirizzo IP dell'host Docker e la porta host a cui è stato eseguito il mapping del contenitore di SQL Edge. Ad esempio, se l'indirizzo IP dell'host Docker è *xxx.xxx.xxx.xxx" e il contenitore SQL Edge viene mappato alla porta host *1600*, l'indirizzo del server per l'istanza di SQL Edge sarà **xxx.xxx.xxx.xxx,1600**. Lo script Python aggiornato sarà:

```python

import pyodbc
server = 'xxx.xxx.xxx.xxx,1600' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

Per connettersi a un'istanza di SQL Edge usando SQL Server Management Studio in esecuzione in un computer Windows, vedere [SQL Server Management Studio](https://docs.microsoft.com/sql/linux/sql-server-linux-manage-ssms).

Per connettersi a un'istanza di SQL Edge usando Visual Studio Code in un computer Windows, Mac o Linux, vedere [Visual Studio Code](https://docs.microsoft.com/sql/visual-studio-code/sql-server-develop-use-vscode).

Per connettersi a un'istanza di SQL Edge usando Azure Data Studio in Windows, il computer Mac o Linux si riferisce ad [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-server).

## <a name="see-also"></a>Vedere anche

[Connettersi ed eseguire query](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker#connect-and-query)

[Installare gli strumenti di SQL Server in Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools)
