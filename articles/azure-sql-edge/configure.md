---
title: Configurare SQL Edge di Azure (anteprima)
description: Informazioni sulla configurazione di SQL Edge di Azure (anteprima)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 5dcdd1604674ca56fb7a646d4c571d63bd2c0e3e
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594010"
---
# <a name="configure-azure-sql-edge-preview"></a>Configurare SQL Edge di Azure (anteprima)

SQL Edge di Azure supporta la configurazione tramite una delle due opzioni seguenti:

- Utilizzo delle variabili di ambiente.
- Utilizzo del file mssql.conf presente nella cartella /var/opt/mssql.

> [!NOTE]
> Le impostazioni relative alle variabili di ambiente sostituiscono le impostazioni specificate nel file mssql.conf.

## <a name="configure-using-environment-variables"></a>Configurare l'uso delle variabili di ambiente

SQL Edge di Azure espone diverse variabili di ambiente con cui è possibile configurare il contenitore SQL Edge. Rappresentano, in realtà, un subset delle variabili di ambiente disponibili per SQL Server in Linux. Per altre informazioni sulle variabili di ambiente di SQL Server in Linux, vedere [Variabili di ambiente](/sql/linux/sql-server-linux-configure-environment-variables/).

Di seguito sono elencate le variabili di ambiente di SQL Server in Linux che NON sono supportate per SQL Edge di Azure. Se vengono definite, saranno ignorate durante l'inizializzazione del contenitore.

| Variabile di ambiente | Descrizione |
|-----|-----|
| **MSSQL_ENABLE_HADR** | Consente di abilitare un gruppo di disponibilità. Ad esempio, il valore '1' lo abilita e il valore '0' lo disabilita |

> [!IMPORTANT]
> La variabile di ambiente *MSSQL_PID* per SQL Edge accetta solo **Premium** e **Developer** come valori validi. SQL Edge di Azure non supporta l'inizializzazione tramite un codice Product Key.

> [!NOTE]
> Per scaricare il contratto di licenza con l'utente finale di SQL Edge di Azure, vedere [Contratto di licenza con l'utente finale](https://go.microsoft.com/fwlink/?linkid=2128283).

### <a name="specifying-the-environment-variables"></a>Definizione delle variabili di ambiente

È possibile definire le variabili di ambiente per SQL Edge quando si distribuisce SQL Edge di Azure tramite il [portale di Azure](deploy-portal.md). In alternativa, è possibile aggiungerle nella sezione "Variabili di ambiente" della procedura di distribuzione del modulo o nell'ambito delle opzioni di creazione del contenitore, come illustrato di seguito.

*Configurazione tramite le opzioni relative alle variabili di ambiente*

![configurazione tramite l'elenco delle variabili di ambiente](media/configure/set-environment-variables.png)

*Configurazione tramite le opzioni di creazione del contenitore*

![configurazione tramite le opzioni di creazione del contenitore](media/configure/set-environment-variables-using-create-options.png)

## <a name="configure-using-mssqlconf-file"></a>Configurazione tramite il file mssql.conf

SQL Edge di Azure non include l'[utilità di configurazione mssql-conf](/sql/linux/sql-server-linux-configure-mssql-conf/) come invece SQL Server in Linux e, pertanto, il file mssql.conf deve essere configurato manualmente e inserito nell'unità di archiviazione permanente mappata alla cartella /var/opt/mssql/ nel modulo SQL Edge. Quando si distribuisce SQL Edge da Azure Marketplace, questo mapping viene specificato come opzione **Mounts" nella sezione delle opzioni di creazione del contenitore

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

Le opzioni mssql.conf seguenti non sono applicabili a SQL Edge:
</br></br>
|Opzione|Descrizione|
|:---|:---|
|**Commenti e suggerimenti degli utenti** | Scegliere se in SQL Server è possibile o meno inviare commenti e suggerimenti a Microsoft. |
|**Profilo di Posta elettronica database** | Impostare il profilo predefinito di Posta elettronica database per SQL Server in Linux. |
|**Disponibilità elevata** | Abilitare i gruppi di disponibilità. |
|**Microsoft Distributed Transaction Coordinator** | Configurare e risolvere i problemi relativi a MSDTC in Linux. NON sono supportate per SQL Edge anche opzioni di configurazione aggiuntive relative alle transazioni distribuite. Per altre informazioni su queste opzioni di configurazione aggiuntive, vedere [Configurare MSDTC](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#msdtc) |
|**Contratti di licenza con l'utente finale di MLServices** | Accettare i Contratti di licenza con l'utente finale di R e Python per i pacchetti Machine Learning Services. Si applica solo a SQL Server 2019.|
|**outboundnetworkaccess** |Abilitare l'accesso alla rete in uscita per le estensioni R, Python e Java di [Machine Learning Services](/sql/linux/sql-server-linux-setup-machine-learning/).|

Di seguito è riportato un esempio di file mssql.conf compatibile con SQL Edge. Per altre informazioni sul formato del file mssql.conf, vedere [Formato mssql.conf](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#mssql-conf-format).

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

## <a name="next-step"></a>Passaggio successivo

- [Connettersi a SQL Edge di Azure](connect.md)
- [Creazione di una soluzione IoT end-to-end con SQL Edge](tutorial-deploy-azure-resources.md)
