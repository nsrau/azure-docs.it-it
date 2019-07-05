---
title: Risoluzione degli errori durante il backup dei database di SAP HANA con Backup di Azure | Microsoft Docs
description: Questa guida illustra come risolvere gli errori comuni durante il tentativo di backup di database SAP HANA con Backup di Azure.
services: backup
author: pvrk
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: pullabhk
ms.openlocfilehash: 33f1b4674aad35d55ab014c45cd73753533931cb
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514178"
---
# <a name="troubleshoot-back-up-of-sap-hana-server-on-azure"></a>Risolvere i problemi di eseguire il backup del Server di SAP HANA in Azure

Questo articolo fornisce informazioni sulla risoluzione dei problemi per la protezione dei database SAP HANA in macchine virtuali di Azure. Prima di procedere alla risoluzione dei problemi, cerchiamo di comprendere alcuni punti chiave relativi alle autorizzazioni e impostazioni.

## <a name="understanding-pre-requisites"></a>Prerequisiti di comprensione

Come parte della [prerequisiti](backup-azure-sap-hana-database.md#prerequisites), lo script di pre-registrazione deve essere eseguito su macchina virtuale in cui è installato HANA per impostare le autorizzazioni appropriate.

### <a name="setting-up-permissions"></a>Impostazione delle autorizzazioni

Operazioni eseguite lo script di pre-registrazione:

1. Crea AZUREWLBACKUPHANAUSER nel sistema di HANA e aggiunge i necessari ruoli e autorizzazioni come indicato di seguito:
    - AMMINISTRATORE di DATABASE - creare nuovi database durante il ripristino
    - CATALOG READ-per leggere il catalogo di backup
    - SAP_INTERNAL_HANA_SUPPORT: per accedere alle tabelle private alcuni
2. Aggiunge una chiave a Hdbuserstore plug-in HANA per eseguire tutte le operazioni (richiesta di informazioni del database, la configurazione di backup, eseguire backup, eseguire il ripristino)
   
   - Per confermare la creazione delle chiavi, eseguire il comando HDBSQL all'interno della macchina HANA con credenziali SIDADM:

    ``` hdbsql
    hdbuserstore list
    ```
    
    L'output del comando deve visualizzare la chiave {SID} {DBNAME} con l'utente come 'AZUREWLBACKUPHANAUSER'.

> [!NOTE]
> Assicurarsi di avere un set univoco di SSFS file nel percorso "/ usr/sap/{SID}/home/.hdb/". Deve essere presente solo una cartella in questo percorso.

### <a name="setting-up-backint-parameters"></a>Impostazione dei parametri BackInt

Una volta che si sceglie un database per il backup, il servizio Backup di Azure configurerà backInt parametri a livello DATABASE.

- [catalog_backup_using_backint:true]
- [enable_accumulated_catalog_backup:false]
- [parallel_data_backup_backint_channels:1]
- [log_backup_timeout_s:900)]
- [backint_response_timeout:7200]

> [!NOTE]
> Assicurarsi che questi parametri non sono presenti a livello HOST. I parametri a livello di host eseguirà l'override di questi parametri e possono causare un comportamento diverso del previsto.

## <a name="understanding-common-user-errors"></a>Informazioni sugli errori utente comuni

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Non è stato possibile connettersi a HANA system.check che il sistema è in esecuzione.| Servizio Backup di Azure non è in grado di connettersi a HANA perché database HANA è inattivo. O HANA sia in esecuzione ma non consente il servizio Backup di Azure per la connessione | Controllare se il servizio/DB HANA è inattivo. Se/servizio di database HANA sia in esecuzione, controllare se tutte le autorizzazioni sono configurate come indicato [qui](#setting-up-permissions). Se la chiave è mancante, eseguire nuovamente lo script di pre-registrazione per creare una nuova chiave. |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Configurazione Backint non valido rilevato. Arrestare la protezione e riconfigurare il database.| I parametri backInt vengono specificati in modo non corretto per il Backup di Azure. | Controllare i parametri sono citate [qui](#setting-up-backint-parameters). Se sono presenti nell'HOST basato su backInt parametri, quindi di rimuoverli. Se i parametri non sono presenti nell'HOST, ma sono stati modificati manualmente a un livello di database, quindi ripristinando i valori appropriati come indicato in precedenza. O 'arrestare la protezione con Mantieni dati' dal portale di Azure e "Riprendi backup" ancora una volta.|
