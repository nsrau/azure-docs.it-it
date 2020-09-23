---
title: Configurare SQL Edge di Azure
description: Informazioni sulla configurazione di Azure SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: b2c52457972d94b2e999c137d19d3a434ff17a7d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90888380"
---
# <a name="configure-azure-sql-edge"></a>Configurare SQL Edge di Azure

SQL Edge di Azure supporta la configurazione tramite una delle due opzioni seguenti:

- Variabili di ambiente
- Un file MSSQL. conf inserito nella cartella/var/opt/MSSQL

> [!NOTE]
> L'impostazione delle variabili di ambiente sostituisce le impostazioni specificate nel file MSSQL. conf.

## <a name="configure-by-using-environment-variables"></a>Configurare usando le variabili di ambiente

SQL Edge di Azure espone diverse variabili di ambiente con cui è possibile configurare il contenitore SQL Edge. Queste variabili di ambiente sono un subset di quelle disponibili per SQL Server in Linux. Per ulteriori informazioni sulle variabili di ambiente SQL Server in Linux, vedere [variabili di ambiente](/sql/linux/sql-server-linux-configure-environment-variables/).

Le nuove variabili di ambiente seguenti sono state aggiunte ad Azure SQL Edge. 

| Variabile di ambiente | Descrizione | Valori |     
|-----|-----| ---------- |   
| **MSSQL_TELEMETRY_ENABLED** | Abilitare o disabilitare la raccolta dati di utilizzo e diagnostica. | TRUE o FALSE |  
| **MSSQL_TELEMETRY_DIR** | Imposta la directory di destinazione per i file di controllo della raccolta dati di utilizzo e di diagnostica. | Percorso della cartella all'interno del contenitore SQL Edge. È possibile eseguire il mapping di questa cartella a un volume host usando punti di montaggio o volumi di dati. | 
| **MSSQL_PACKAGE** | Specifica il percorso del pacchetto dacpac o BACPAC da distribuire. | URL della cartella, del file o della firma di accesso condiviso contenente i pacchetti DACPAC o BACPAC. Per altre informazioni, vedere [distribuire i pacchetti DACPAC e BACPAC del database SQL in SQL Edge](deploy-dacpac.md). |


La variabile di ambiente SQL Server in Linux seguente non è supportata per Azure SQL Edge. Se definito, questa variabile di ambiente verrà ignorata durante l'inizializzazione del contenitore.

| Variabile di ambiente | Descrizione |
|-----|-----|
| **MSSQL_ENABLE_HADR** | Abilitare il gruppo di disponibilità. Ad esempio, **1** è abilitato e **0** è disabilitato. |

> [!IMPORTANT]
> La variabile di ambiente **MSSQL_PID** per SQL Edge accetta solo **Premium** e **Developer** come valori validi. Azure SQL Edge non supporta l'inizializzazione con un codice Product Key.

### <a name="specify-the-environment-variables"></a>Specificare le variabili di ambiente

Specificare le variabili di ambiente per SQL Edge quando si distribuisce il servizio tramite la [portale di Azure](deploy-portal.md). È possibile aggiungerli nella sezione **variabili di ambiente** della distribuzione del modulo o come parte delle opzioni di **creazione del contenitore**.

Aggiungere valori nelle **variabili di ambiente**.

![Imposta usando l'elenco delle variabili di ambiente](media/configure/set-environment-variables.png)

Aggiungere i valori nelle **Opzioni di creazione del contenitore**.

![Imposta usando le opzioni di creazione del contenitore](media/configure/set-environment-variables-using-create-options.png)

> [!NOTE]
> Nella modalità di distribuzione disconnessa è possibile specificare le variabili di ambiente utilizzando `-e` o `--env` o l' `--env-file` opzione del `docker run` comando.

## <a name="configure-by-using-an-mssqlconf-file"></a>Configurare usando un file MSSQL. conf

Azure SQL Edge non include l' [utilità di configurazione MSSQL-conf](/sql/linux/sql-server-linux-configure-mssql-conf/) come SQL Server in Linux. È necessario configurare manualmente il file MSSQL. conf e posizionarlo nell'unità di archiviazione persistente di cui è stato eseguito il mapping alla cartella/var/opt/MSSQL/nel modulo SQL Edge. Quando si distribuisce SQL Edge da Azure Marketplace, questo mapping viene specificato come opzione di **montaggio** nelle opzioni di **creazione del contenitore**.

```json
    {
        "Mounts": [
          {
            "Type": "volume",
            "Source": "sqlvolume",
            "Target": "/var/opt/mssql"
          }
        ]
      }
    }
```

Sono state aggiunte le nuove opzioni MSSQL. conf seguenti per Azure SQL Edge. 

|Opzione|Descrizione|
|:---|:---|
|**CustomerFeedback** | Scegliere se SQL Server invia commenti e suggerimenti a Microsoft. Per ulteriori informazioni, vedere la pagina relativa alla [disabilitazione della raccolta dati di utilizzo e diagnostica](usage-and-diagnostics-data-configuration.md#disable-usage-and-diagnostic-data-collection)|      
|**userrequestedlocalauditdirectory** | Imposta la directory di destinazione per i file di controllo della raccolta dati di utilizzo e di diagnostica. Per ulteriori informazioni, vedere [controllo locale della raccolta dei dati di utilizzo e di diagnostica](usage-and-diagnostics-data-configuration.md#local-audit-of-usage-and-diagnostic-data-collection) . |        

Le seguenti opzioni di MSSQL. conf non sono applicabili a SQL Edge:

|Opzione|Descrizione|
|:---|:---|
|**Commenti e suggerimenti degli utenti** | Scegliere se SQL Server invia commenti e suggerimenti a Microsoft. |
|**Profilo di posta elettronica database** | Impostare il profilo predefinito di Posta elettronica database per SQL Server in Linux. |
|**Disponibilità elevata** | Abilitare i gruppi di disponibilità. |
|**Microsoft Distributed Transaction Coordinator** | Configurare e risolvere i problemi relativi a MSDTC in Linux. Le opzioni di configurazione aggiuntive relative alle transazioni distribuite non sono supportate per SQL Edge. Per ulteriori informazioni su queste opzioni di configurazione aggiuntive, vedere [Configure MSDTC](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#msdtc). |
|**Contratti di licenza di ML Services** | Accettare i contratti di licenza R e Python per i pacchetti Azure Machine Learning. Si applica solo a SQL Server 2019.|
|**outboundnetworkaccess** |Abilitare l'accesso alla rete in uscita per le estensioni R, Python e Java di [Machine Learning Services](/sql/linux/sql-server-linux-setup-machine-learning/).|

Il file MSSQL. conf di esempio seguente funziona per SQL Edge. Per ulteriori informazioni sul formato di un file MSSQL. conf, vedere il [formato MSSQL. conf](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#mssql-conf-format).

```ini
[EULA]
accepteula = Y

[coredump]
captureminiandfull = true
coredumptype = full

[filelocation]
defaultbackupdir = /var/opt/mssql/backup/
defaultdatadir = /var/opt/mssql/data/
defaultdumpdir = /var/opt/mssql/data/
defaultlogdir = /var/opt/mssql/log/

[language]
lcid = 1033

[memory]
memorylimitmb = 6144

[sqlagent]
errorlogfile = /var/opt/mssql/log/sqlagentlog.log
errorlogginglevel = 7

[traceflag]
traceflag0 = 3604
traceflag1 = 3605
traceflag2 = 1204
```

## <a name="run-azure-sql-edge-as-non-root-user"></a>Eseguire Azure SQL Edge come utente non root

Per impostazione predefinita, i contenitori di Azure SQL Edge vengono eseguiti con un utente o un gruppo non radice. Quando viene distribuito tramite Azure Marketplace (o usando Docker Run), a meno che non venga specificato un altro utente o gruppo, i contenitori di SQL Edge vengono avviati come utente MSSQL (non root). Per specificare un utente diverso dalla radice durante la distribuzione, aggiungere la `*"User": "<name|uid>[:<group|gid>]"*` coppia chiave-valore in opzioni di creazione del contenitore. Nell'esempio seguente SQL Edge è configurato per l'avvio come utente `*IoTAdmin*` .

```json
{
    ..
    ..
    ..
    "User": "IoTAdmin",
    "Env": [
        "MSSQL_AGENT_ENABLED=TRUE",
        "ClientTransportType=AMQP_TCP_Only",
        "MSSQL_PID=Premium"
    ]
}
```

Per consentire all'utente non root di accedere ai file di database che si trovano su volumi montati, assicurarsi che l'utente o il gruppo in cui viene eseguito il contenitore disponga di autorizzazioni di lettura & scrittura per l'archiviazione di file permanente. Nell'esempio seguente viene impostato l'utente non root con user_id 10001 come proprietario dei file. 

```bash
chown -R 10001:0 <database file dir>
```

### <a name="upgrading-from-earlier-ctp-releases"></a>Aggiornamento da versioni CTP precedenti

CTP precedenti di Azure SQL Edge sono stati configurati per l'esecuzione come utenti radice. Quando si esegue l'aggiornamento da CTP precedenti, sono disponibili le opzioni seguenti.

- Continua a usare l'utente root: per continuare a usare l'utente root, Aggiungi la `*"User": "0:0"*` coppia chiave-valore in opzioni di creazione del contenitore.
- Usare l'utente MSSQL predefinito: per usare l'utente MSSQL predefinito, attenersi alla procedura seguente
  - Aggiungere un utente denominato MSSQL nell'host docker. Nell'esempio seguente viene aggiunto un utente MSSQL con ID 10001. Questo utente viene aggiunto anche al gruppo radice.
    ```bash
    sudo useradd -M -s /bin/bash -u 10001 -g 0 mssql
    ```
  - Modificare l'autorizzazione per la directory o il volume di montaggio in cui risiede il file di database 
    ```bash
    sudo chgrp -R 0 /var/lib/docker/volumes/kafka_sqldata/
    sudo chmod -R g=u /var/lib/docker/volumes/kafka_sqldata/
    ```
- Usare un account utente diverso da root: per usare un account utente diverso da root
  - Aggiornare le opzioni di creazione del contenitore per specificare Aggiungi `*"User": "user_name | user_id*` coppia chiave-valore in opzioni di creazione del contenitore. Sostituire user_name o user_id con un user_name o un user_id effettivo dall'host docker. 
  - Modificare le autorizzazioni per la directory o il volume di montaggio.

## <a name="persist-your-data"></a> Rendere persistenti i dati

Le modifiche alla configurazione di Azure SQL Edge e i file di database vengono salvati in modo permanente nel contenitore anche se il contenitore viene riavviato con `docker stop` e `docker start` . Tuttavia, se si rimuove il contenitore con `docker rm` , tutto il contenitore viene eliminato, inclusi Azure SQL Edge e i database. La sezione seguente illustra come usare i **volumi di dati** per rendere persistenti i file di database anche se vengono eliminati i contenitori associati.

> [!IMPORTANT]
> Per Azure SQL Edge, è fondamentale comprendere la persistenza dei dati in docker. Oltre alle informazioni in questa sezione, vedere la documentazione di Docker su [come gestire i dati nei contenitori Docker](https://docs.docker.com/engine/tutorials/dockervolumes/).

### <a name="mount-a-host-directory-as-data-volume"></a>Montare una directory host come volume di dati

La prima opzione consiste nel montare una directory nell'host come volume di dati nel contenitore. A tale scopo, usare il comando `docker run` con il flag `-v <host directory>:/var/opt/mssql`. In questo modo è possibile ripristinare i dati tra le esecuzioni dei contenitori.

```bash
docker run -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>' -p 1433:1433 -v <host directory>/data:/var/opt/mssql/data -v <host directory>/log:/var/opt/mssql/log -v <host directory>/secrets:/var/opt/mssql/secrets -d mcr.microsoft.com/azure-sql-edge
```

```PowerShell
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1433:1433 -v <host directory>/data:/var/opt/mssql/data -v <host directory>/log:/var/opt/mssql/log -v <host directory>/secrets:/var/opt/mssql/secrets -d mcr.microsoft.com/azure-sql-edge
```

Questa tecnica consente anche di condividere e visualizzare i file nell'host all'esterno di Docker.

> [!IMPORTANT]
> Il mapping del volume host per **Docker in Windows** non supporta attualmente il mapping della directory `/var/opt/mssql` completa. È tuttavia possibile eseguire il mapping di una sottodirectory, ad esempio `/var/opt/mssql/data` al computer host.

> [!IMPORTANT]
> Il mapping del volume host per **Docker su Mac** con l'immagine Azure SQL Edge non è supportato in questo momento. Usare invece i contenitori di volumi di dati. Questa restrizione è specifica per la directory `/var/opt/mssql`. La lettura da una directory montata funziona correttamente. Ad esempio, è possibile montare una directory host usando -v in Mac e ripristinare un backup da un file con estensione bak che risiede nell'host.

### <a name="use-data-volume-containers"></a>Usare i contenitori di volumi di dati

La seconda opzione consiste nell'usare un contenitore di volumi di dati. È possibile creare un contenitore di volumi di dati specificando un nome di volume anziché una directory host con il parametro `-v`. Nell'esempio seguente viene creato un volume di dati condiviso denominato **sqlvolume**.

```bash
docker run -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>' -p 1433:1433 -v sqlvolume:/var/opt/mssql -d mcr.microsoft.com/azure-sql-edge
```

```PowerShell
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1433:1433 -v sqlvolume:/var/opt/mssql -d mcr.microsoft.com/azure-sql-edge
```

> [!NOTE]
> Questa tecnica per la creazione implicita di un volume di dati nel comando run non funziona con le versioni precedenti di Docker. In tal caso, usare i passaggi espliciti descritti nella documentazione di Docker, in [Creating and mounting a data volume container](https://docs.docker.com/engine/tutorials/dockervolumes/#creating-and-mounting-a-data-volume-container) (Creazione e montaggio di un contenitore di volumi di dati).

Anche se si arresta e rimuove il contenitore, il volume di dati è persistente. È possibile visualizzarlo con il comando `docker volume ls`.

```bash
docker volume ls
```

Se successivamente si crea un altro contenitore con lo stesso nome del volume, il nuovo contenitore userà gli stessi dati di Azure SQL Edge contenuti nel volume.

Per rimuovere un contenitore di volumi di dati, usare il comando `docker volume rm`.

> [!WARNING]
> Se si elimina il contenitore del volume di dati, qualsiasi dato di Azure SQL Edge nel contenitore viene eliminato *definitivamente* .


## <a name="next-steps"></a>Passaggi successivi

- [Connettersi a SQL Edge di Azure](connect.md)
- [Creare una soluzione IoT end-to-end con SQL Edge](tutorial-deploy-azure-resources.md)
