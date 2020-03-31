---
title: Problemi noti/limitazioni di migrazione con l'utilizzo della modalità ibridaKnown issues/migration limitations with using Hybrid mode
description: Informazioni sui problemi noti/limitazioni di migrazione con l'uso del servizio Migrazione del database di Azure in modalità ibrida.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: aedc7ea3d778d52f6f348837430987568af188ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649603"
---
# <a name="known-issuesmigration-limitations-with-using-hybrid-mode"></a>Problemi noti/limitazioni di migrazione con l'utilizzo della modalità ibridaKnown issues/migration limitations with using hybrid mode

I problemi noti e le limitazioni associate all'uso del servizio Migrazione del database di Azure in modalità ibrida sono descritti nelle sezioni seguenti.

## <a name="installer-fails-to-authenticate"></a>Impossibile autenticare il programma di installazione

Dopo aver caricato il certificato in AdApp, si verifica un ritardo fino a un paio di minuti prima che possa eseguire l'autenticazione con Azure.After uploading the certificate to your AdApp, there is a delay of up to a couple of minutes before it can authenticate with Azure. Il programma di installazione tenterà di riprovare con un certo ritardo, ma è possibile che il ritardo di propagazione sia più lungo del nuovo tentativo e verrà visualizzato un messaggio **FailedToGetAccessTokenException.** Se il certificato è stato caricato nell'AdApp corretto e l'AppId corretto è stato fornito in dmsSettings.json, provare a eseguire nuovamente il comando install.

## <a name="service-offline-after-successful-installation"></a>Servizio "offline" dopo la corretta installazioneService "offline" after successful installation

Se il servizio viene visualizzato come non in linea al termine del processo di installazione, provare a eseguire la procedura seguente.

1. Nel portale di Azure, nell'istanza del servizio Migrazione del database di Azure, passare alla scheda Impostazioni **ibride** e quindi verificare che il ruolo di lavoro sia registrato controllando la griglia dei lavoratori registrati.

    Lo stato di questo worker dovrebbe essere **Online**, ma può essere visualizzato come **Non in linea** se si verifica un problema.

2. Nel computer di lavoro controllare lo stato del servizio eseguendo il comando PowerShell seguente:

    ```
    Get-Service Scenario*
    ```

    Questo comando fornisce lo stato del servizio Windows che esegue il worker. Ci dovrebbe essere un solo risultato. Se il worker viene arrestato, è possibile tentare di riavviarlo utilizzando il comando di PowerShell seguente:If the worker is stopped, you can attempt to restart it by using the following PowerShell command:

    ```
    Start-Service Scenario*
    ```

    È anche possibile controllare il servizio nell'interfaccia utente dei servizi di Windows.You can also check the service in the Windows Services UI.

3. Se il servizio Windows passa da In esecuzione a Arrestato, il worker ha riscontrato problemi durante l'avvio. Controllare i log di lavoro ibrido del servizio di migrazione del database di Azure per determinare il problema.

    - I log del processo di installazione vengono archiviati nella cartella "logs" all'interno della cartella da cui è stato eseguito l'eseguibile del programma di installazione.
    - I log di lavoro ibridi del servizio di migrazione del database di Azure vengono archiviati nella cartella **WorkerLogs,** nella cartella in cui è installato il worker. Il percorso predefinito per i file di registro di lavoro ibrido è **C:**.

## <a name="using-your-own-signed-certificate"></a>Utilizzo del proprio certificato firmato

Il certificato generato dall'azione GenerateCert è un certificato autofirmato, che potrebbe non essere accettabile in base ai criteri di sicurezza interni. Anziché utilizzare questo certificato, è possibile fornire il proprio certificato e l'identificazione personale in dmsSettings.json. Questo certificato dovrà essere caricato in AdApp e installato nel computer in cui si sta installando il ruolo di lavoro ibrido del servizio Migrazione del database di Azure.This certificate will need to be uploaded to your AdApp and installed on the computer on which you're installing the Azure Database Migration Service hybrid worker. Quindi, installare questo certificato con la chiave privata nell'archivio certificati del computer locale.

## <a name="running-the-worker-service-as-a-low-privilege-account"></a>Esecuzione del servizio di lavoro come account con privilegi limitati

Per impostazione predefinita, il servizio di lavoro ibrido del servizio di migrazione del database di Azure viene eseguito come account di sistema locale. È possibile modificare l'account utilizzato per questo servizio, purché l'account utilizzato disponga delle autorizzazioni di rete. Per modificare l'account del servizio 'esegui come', utilizzare il processo seguente.

1. Arrestare il servizio tramite i servizi di Windows o utilizzando il comando Stop-Service in PowerShell.Stop the service, either through Windows Services or by using the Stop-Service command in PowerShell.

2. Aggiornare il servizio per utilizzare un account di accesso diverso.

3. In certmgr per i certificati del computer locale assegnare le autorizzazioni di chiave privata al nuovo account per i certificati **DMS Hybrid App Key** e **DMS Scenario Engine Key Pair.**

    a. Aprire certmgr per visualizzare le seguenti chiavi:

    - Chiave dell'app ibrida DMS
    - Chiave di configurazione di DMS Hybrid Worker
    - Coppia di chiavi del motore dello scenario DMSDMS Scenario Engine Key Pair

    b. Fare clic con il pulsante destro del mouse sulla voce **DMS Hybrid App Key** , scegliere **Tutte le attività**, quindi scegliere Gestisci **chiavi private**.

    c. Nella scheda **Protezione** selezionare **Aggiungi**e quindi immettere il nome dell'account.

    d. Utilizzare la stessa procedura per concedere l'autorizzazione di chiave privata per il nuovo account al certificato di coppia di chiavi del motore dello **scenario DMS.**

## <a name="unregistering-the-worker-manually"></a>Annullamento manuale della registrazione del lavoratore

Se non si ha più accesso al computer di lavoro, è possibile annullare la registrazione del worker e riutilizzare l'istanza del servizio Migrazione del database di Azure eseguendo la procedura seguente:If you no longer have access to the worker computer, you can unregister the worker and reuse your Azure Database Migration Service instance by performing the following steps:

1. Nel portale di Azure passare all'istanza del servizio di migrazione del database di Azure e quindi passare alla pagina **Impostazioni ibride.**

   La voce del lavoratore viene visualizzata nell'elenco, con lo stato **Non in linea**.

2. All'estrema destra dell'elenco delle voci di lavoro, selezionare i puntini di sospensione e quindi **Annulla registrazione**.

## <a name="addressing-issues-for-specific-migration-scenarios"></a>Risoluzione dei problemi per scenari di migrazione specificiAddressing issues for specific migration scenarios

Nelle sezioni seguenti vengono descritti i problemi specifici dello scenario correlati all'uso della modalità ibrida del servizio Migrazione di database di Azure per eseguire una migrazione online.

### <a name="online-migrations-to-azure-sql-database-managed-instance"></a>Migrazioni online all'istanza gestita del database SQL di AzureOnline migrations to Azure SQL Database managed instance

**Uso elevato della CPU**

**Problema:** per le migrazioni online nell'istanza gestita del database SQL, il computer che esegue il worker ibrido incontrerà un utilizzo elevato della CPU se sono presenti troppi backup o se i backup sono troppo grandi.

**Mitigazione**: per ridurre questo problema, utilizzare i backup compressi, dividere la migrazione in modo che utilizzi più condivisioni o aumentare la scalabilità verticale del computer che esegue il worker ibrido.
