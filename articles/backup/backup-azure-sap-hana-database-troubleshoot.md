---
title: Risolvere gli errori di backup SAP HANA database-backup di Azure
description: Viene descritto come risolvere gli errori comuni che possono verificarsi quando si usa backup di Azure per eseguire il backup di SAP HANA database.
ms.reviewer: pullabhk
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 08/03/2019
ms.author: dacurwin
ms.openlocfilehash: 004d10b794c6eca2e078e437880f44d91ca30acb
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2019
ms.locfileid: "72968443"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Risolvere i problemi di backup di database SAP HANA in Azure

Questo articolo fornisce informazioni sulla risoluzione dei problemi per il backup di database SAP HANA in macchine virtuali di Azure. Nella sezione seguente vengono illustrati i dati concettuali importanti necessari per diagnosticare gli errori comuni nel backup SAP HANA.

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
- [parallel_data_backup_backint_channels: 1]
- [log_backup_timeout_s: 900)]
- [backint_response_timeout: 7200]

> [!NOTE]
> Assicurarsi che questi parametri *non* siano presenti a livello di host. I parametri a livello di host eseguiranno l'override di questi parametri e potrebbero causare un comportamento imprevisto.

## <a name="restore-checks"></a>Controlli di ripristino

### <a name="single-container-database-sdc-restore"></a>Ripristino di un database contenitore singolo (DSC)

Prestare attenzione agli input durante il ripristino di un singolo database contenitore (DSC) per HANA in un altro computer DSC. Il nome del database deve essere specificato con lettere minuscole e con "DSC" accodato tra parentesi quadre. L'istanza HANA verrà visualizzata in lettere maiuscole.

Si supponga che venga eseguito il backup di un'istanza DSC HANA "H21". Nella pagina elementi di backup viene visualizzato il nome dell'elemento di backup come **"H21 (DSC)"** . Se si tenta di ripristinare il database in un'altra DSC di destinazione, ad esempio H11, è necessario fornire gli input seguenti.

![Input di ripristino DSC](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

Tenere presente quanto segue:

- Per impostazione predefinita, il nome del database ripristinato verrà popolato con il nome dell'elemento di backup, ad esempio H21 (DSC)
- Se si seleziona la destinazione come H11, il nome del database ripristinato non verrà modificato automaticamente. **Deve essere modificato in H11 (DSC)** . Nel caso di DSC, il nome del database ripristinato sarà l'ID dell'istanza di destinazione con lettere minuscole e "DSC" accodato tra parentesi quadre.
- Poiché la DSC può avere un solo database singolo, è anche necessario fare clic sulla casella di controllo per consentire l'override dei dati del database esistenti con i dati del punto di ripristino.
- Linux distingue tra maiuscole e minuscole e quindi assicurarsi di conservare il caso.

### <a name="multiple-container-database-mdc-restore"></a>Ripristino di più database contenitore (MDC)

In più database contenitore per HANA la configurazione standard è SYSTEMDB + 1 o più DB tenant. Il ripristino di un'intera istanza di SAP HANA significa ripristinare sia i database SYSTEMDB che i database tenant. Uno Ripristina prima SYSTEMDB, quindi procede per il database tenant. Il database di sistema indica essenzialmente di sostituire le informazioni di sistema nella destinazione selezionata. Questa operazione sostituisce anche le informazioni correlate a BackInt nell'istanza di di destinazione. Quindi, dopo che il database di sistema è stato ripristinato in un'istanza di destinazione, è necessario eseguire di nuovo lo script di pre-registrazione. Solo i ripristini di database tenant successivi riusciranno.

## <a name="common-user-errors"></a>Errori utente comuni

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

data| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Non è stato possibile connettersi al sistema HANA. Verificare che il sistema sia in esecuzione.| Il servizio backup di Azure non è in grado di connettersi a HANA perché il database HANA è inattivo. Oppure HANA è in esecuzione, ma non consente la connessione del servizio backup di Azure. | Controllare se il database o il servizio HANA è inattivo. Se il database o il servizio HANA è attivo e in esecuzione, verificare che [tutte le autorizzazioni siano impostate](#setting-up-permissions). Se la chiave non è presente, eseguire nuovamente lo script di preregistrazione per creare una nuova chiave. |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| È stata rilevata una configurazione Backint non valida. Arrestare la protezione e riconfigurare il database.| I parametri backInt non sono specificati correttamente per backup di Azure. | Controllare se [i parametri sono impostati](#setting-up-backint-parameters). Se i parametri basati su backInt sono presenti nell'HOST, rimuoverli. Se i parametri non sono presenti a livello di HOST ma sono stati modificati manualmente a livello di database, ripristinare i valori appropriati come descritto in precedenza. In alternativa, eseguire **Stop Protection e mantenere i dati di backup** dal portale di Azure, quindi selezionare **Riprendi backup**.|
