---
title: Configurare SQL Edge di Azure (anteprima)
description: Informazioni sulla configurazione di Azure SQL Edge (anteprima).
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 07/28/2020
ms.openlocfilehash: 0cb2eed0895c10f649facaa184a5f9f9ea158aa5
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/04/2020
ms.locfileid: "87551983"
---
# <a name="configure-azure-sql-edge-preview"></a>Configurare SQL Edge di Azure (anteprima)

SQL Edge di Azure supporta la configurazione tramite una delle due opzioni seguenti:

- Variabili di ambiente
- Un file MSSQL. conf inserito nella cartella/var/opt/MSSQL

> [!NOTE]
> L'impostazione delle variabili di ambiente sostituisce le impostazioni specificate nel file MSSQL. conf.

## <a name="configure-by-using-environment-variables"></a>Configurare usando le variabili di ambiente

SQL Edge di Azure espone diverse variabili di ambiente con cui è possibile configurare il contenitore SQL Edge. Queste variabili di ambiente sono un subset di quelle disponibili per SQL Server in Linux. Per ulteriori informazioni sulle variabili di ambiente SQL Server in Linux, vedere [variabili di ambiente](/sql/linux/sql-server-linux-configure-environment-variables/).

La variabile di ambiente SQL Server in Linux seguente non è supportata per Azure SQL Edge. Se definito, questa variabile di ambiente verrà ignorata durante l'inizializzazione del contenitore.

| Variabile di ambiente | Descrizione |
|-----|-----|
| **MSSQL_ENABLE_HADR** | Abilitare il gruppo di disponibilità. Ad esempio, **1** è abilitato e **0** è disabilitato. |

> [!IMPORTANT]
> La variabile di ambiente **MSSQL_PID** per SQL Edge accetta solo **Premium** e **Developer** come valori validi. Azure SQL Edge non supporta l'inizializzazione con un codice Product Key.

> [!NOTE]
> Scaricare le [condizioni di licenza software Microsoft](https://go.microsoft.com/fwlink/?linkid=2128283) per Azure SQL Edge.

### <a name="specify-the-environment-variables"></a>Specificare le variabili di ambiente

Specificare le variabili di ambiente per SQL Edge quando si distribuisce il servizio tramite la [portale di Azure](deploy-portal.md). È possibile aggiungerli nella sezione **variabili di ambiente** della distribuzione del modulo o come parte delle opzioni di **creazione del contenitore**.

Aggiungere valori nelle **variabili di ambiente**.

![Imposta usando l'elenco delle variabili di ambiente](media/configure/set-environment-variables.png)

Aggiungere i valori nelle **Opzioni di creazione del contenitore**.

![Imposta usando le opzioni di creazione del contenitore](media/configure/set-environment-variables-using-create-options.png)

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

A partire da Azure SQL Edge CTP 2.2, i contenitori di SQL Edge possono essere eseguiti con un utente o un gruppo non radice. Quando viene distribuito tramite Azure Marketplace, a meno che non venga specificato un altro utente o gruppo, i contenitori di SQL Edge vengono avviati come utente MSSQL (non root). Per specificare un utente diverso dalla radice durante la distribuzione, aggiungere la `*"User": "<name|uid>[:<group|gid>]"*` coppia chiave-valore in opzioni di creazione del contenitore. Nell'esempio seguente SQL Edge è configurato per l'avvio come utente `*IoTAdmin*` .

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

Le versioni precedenti della versione CTP di Azure SQL Edge sono state configurate per l'esecuzione come utenti radice. Le opzioni seguenti sono disponibili quando si esegue l'aggiornamento da versioni precedenti della versione CTP

- Continua a usare l'utente root: per continuare a usare l'utente root, Aggiungi la `*"User": "0:0"*` coppia chiave-valore in opzioni di creazione del contenitore.
- Usare l'utente MSSQL predefinito: per usare l'utente MSSQL predefinito, attenersi alla procedura seguente
  - Aggiungere un utente denominato MSSQL nell'host docker. Nell'esempio seguente viene aggiunto un utente MSSQL con ID 10001. Questo utente viene aggiunto anche al gruppo radice.
    ```bash
    sudo useradd -M -s /bin/bash -u 10001 -g 0 mssql
    ```
  - Modificare l'autorizzazione per la directory o il volume di montaggio in cui si trova il file di database 
    ```bash
    sudo chgrp -R 0 /var/lib/docker/volumes/kafka_sqldata/
    sudo chmod -R g=u /var/lib/docker/volumes/kafka_sqldata/
    ```
- Usare un account utente diverso da root: per usare un account utente diverso da root
  - Aggiornare le opzioni di creazione del contenitore per specificare Aggiungi `*"User": "user_name | user_id*` coppia chiave-valore in opzioni di creazione del contenitore. Sostituire user_name o user_id con un user_name o un user_id effettivo dall'host docker. 
  - Modificare le autorizzazioni per la directory o il volume di montaggio.



## <a name="next-steps"></a>Passaggi successivi

- [Connettersi a SQL Edge di Azure](connect.md)
- [Creare una soluzione IoT end-to-end con SQL Edge](tutorial-deploy-azure-resources.md)
