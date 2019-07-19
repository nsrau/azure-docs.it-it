---
title: Risolvere gli errori durante il backup dei database SAP HANA usando backup di Azure | Microsoft Docs
description: Viene descritto come risolvere gli errori comuni che possono verificarsi quando si usa backup di Azure per eseguire il backup di SAP HANA database.
services: backup
author: pvrk
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: pullabhk
ms.openlocfilehash: 32e814ea83f30b48af5ce507ce250f37a34390da
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249498"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Risolvere i problemi di backup di database SAP HANA in Azure

Questo articolo fornisce informazioni sulla risoluzione dei problemi per il backup di database SAP HANA in macchine virtuali di Azure.

## <a name="prerequisites"></a>Prerequisiti

Come parte dei [prerequisiti](backup-azure-sap-hana-database.md#prerequisites), assicurarsi che lo script di preregistrazione sia stato eseguito nella macchina virtuale in cui è installato Hana.

### <a name="setting-up-permissions"></a>Impostazione delle autorizzazioni

Cosa fa lo script di preregistrazione:

1. Crea AZUREWLBACKUPHANAUSER nel sistema HANA e aggiunge questi ruoli e autorizzazioni necessari:
    - AMMINISTRATORE del DATABASE: per creare nuovi database durante il ripristino.
    - LETTURA catalogo: per leggere il catalogo di backup.
    - SAP_INTERNAL_HANA_SUPPORT: per accedere ad alcune tabelle private.
2. Aggiunge una chiave a Hdbuserstore per il plug-in HANA per gestire tutte le operazioni (query di database, operazioni di ripristino, configurazione ed esecuzione di backup).
   
   Per confermare la creazione della chiave, eseguire il comando HDBSQL nel computer HANA con le credenziali di SIDADM:

    ``` hdbsql
    hdbuserstore list
    ```
    
    L'output del comando dovrebbe visualizzare la chiave {SID} {DBNAME}, con l'utente visualizzato come AZUREWLBACKUPHANAUSER.

> [!NOTE]
> Assicurarsi di disporre di un set univoco di file SSFS in **/usr/sap/{SID}/Home/.HDB/** . In questo percorso deve essere presente una sola cartella.

### <a name="setting-up-backint-parameters"></a>Impostazione dei parametri BackInt

Quando si sceglie un database per il backup, il servizio backup di Azure configura i parametri backInt a livello di DATABASE:

- [catalog_backup_using_backint: true]
- [enable_accumulated_catalog_backup: false]
- [parallel_data_backup_backint_channels:1]
- [log_backup_timeout_s: 900)]
- [backint_response_timeout:7200]

> [!NOTE]
> Assicurarsi che questi parametri *non* siano presenti a livello di host. I parametri a livello di host eseguiranno l'override di questi parametri e potrebbero causare un comportamento imprevisto.

## <a name="common-user-errors"></a>Errori utente comuni

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

data| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Non è stato possibile connettersi al sistema HANA. Verificare che il sistema sia in esecuzione.| Il servizio backup di Azure non è in grado di connettersi a HANA perché il database HANA è inattivo. Oppure HANA è in esecuzione, ma non consente la connessione del servizio backup di Azure. | Controllare se il database o il servizio HANA è inattivo. Se il database o il servizio HANA è attivo e in esecuzione, verificare che [tutte le autorizzazioni siano impostate](#setting-up-permissions). Se la chiave non è presente, eseguire nuovamente lo script di preregistrazione per creare una nuova chiave. |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| È stata rilevata una configurazione Backint non valida. Arrestare la protezione e riconfigurare il database.| I parametri backInt non sono specificati correttamente per backup di Azure. | Controllare se [i parametri sono impostati](#setting-up-backint-parameters). Se i parametri basati su backInt sono presenti nell'HOST, rimuoverli. Se i parametri non sono presenti a livello di HOST ma sono stati modificati manualmente a livello di database, ripristinare i valori appropriati come descritto in precedenza. In alternativa, eseguire **Stop Protection e mantenere i dati di backup** dal portale di Azure, quindi selezionare Riprendi **backup**.|
