---
title: Problemi noti/limitazioni della migrazione con l'uso della modalità ibrida
description: Informazioni sui problemi noti/limitazioni della migrazione con l'uso del servizio migrazione del database di Azure in modalità ibrida.
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
# <a name="known-issuesmigration-limitations-with-using-hybrid-mode"></a>Problemi noti/limitazioni della migrazione con l'uso della modalità ibrida

Le sezioni seguenti illustrano i problemi noti e le limitazioni associate all'uso del servizio migrazione del database di Azure in modalità ibrida.

## <a name="installer-fails-to-authenticate"></a>Non è possibile eseguire l'autenticazione del programma di installazione

Dopo aver caricato il certificato in decompressa, si verifica un ritardo di un paio di minuti prima di poter eseguire l'autenticazione con Azure. Il programma di installazione tenterà di riprovare con un certo ritardo, ma è possibile che il ritardo di propagazione sia più lungo del nuovo tentativo. verrà visualizzato un messaggio **FailedToGetAccessTokenException** . Se il certificato è stato caricato nel decompressa corretto e è stato fornito l'AppId corretto in dmsSettings. JSON, provare a eseguire di nuovo il comando di installazione.

## <a name="service-offline-after-successful-installation"></a>Servizio "offline" dopo l'installazione riuscita

Se il servizio viene visualizzato come offline al termine del processo di installazione, provare a eseguire i passaggi seguenti.

1. Nel portale di Azure, nell'istanza del servizio migrazione del database di Azure, passare alla scheda Impostazioni **ibride** e quindi verificare che il ruolo di lavoro sia registrato controllando la griglia dei processi di lavoro registrati.

    Lo stato di questo thread di lavoro deve essere **online**, ma potrebbe essere visualizzato come **offline** in caso di problemi.

2. Nel computer di lavoro verificare lo stato del servizio eseguendo il comando PowerShell seguente:

    ```
    Get-Service Scenario*
    ```

    Questo comando consente di fornire lo stato del servizio Windows che esegue il ruolo di lavoro. Deve essere presente solo un singolo risultato. Se il ruolo di lavoro viene interrotto, è possibile tentare di riavviarlo usando il comando PowerShell seguente:

    ```
    Start-Service Scenario*
    ```

    È anche possibile controllare il servizio nell'interfaccia utente dei servizi Windows.

3. Se il servizio Windows scorre tra l'esecuzione e l'arresto, il thread di lavoro ha riscontrato problemi di avvio. Per determinare il problema, controllare i log del ruolo di lavoro ibrido del servizio migrazione del database di Azure.

    - I log del processo di installazione vengono archiviati nella cartella "logs" all'interno della cartella da cui è stato eseguito il file eseguibile del programma di installazione.
    - I log del ruolo di lavoro ibrido del servizio migrazione del database di Azure vengono archiviati nella cartella **WorkerLogs** , nella cartella in cui è installato Worker. Il percorso predefinito per i file di log del ruolo di lavoro ibrido è **C:\Program Files\DatabaseMigrationServiceHybrid\WorkerLogs**.

## <a name="using-your-own-signed-certificate"></a>Uso del proprio certificato firmato

Il certificato generato dall'azione GenerateCert è un certificato autofirmato che potrebbe non essere accettabile in base ai criteri di sicurezza interni. Invece di usare questo certificato, è possibile fornire il proprio certificato e fornire l'identificazione personale in dmsSettings. JSON. Il certificato deve essere caricato nella decompressa e installato nel computer in cui si sta installando il ruolo di lavoro ibrido del servizio migrazione del database di Azure. Installare quindi questo certificato con la chiave privata nell'archivio certificati del computer locale.

## <a name="running-the-worker-service-as-a-low-privilege-account"></a>Esecuzione del servizio Worker come account con privilegi limitati

Per impostazione predefinita, il servizio ruolo di lavoro ibrido del servizio migrazione del database di Azure viene eseguito come account di sistema locale. È possibile modificare l'account utilizzato per il servizio purché l'account utilizzato disponga delle autorizzazioni di rete. Per modificare l'account ' Esegui come ' del servizio, usare il processo seguente.

1. Arrestare il servizio tramite i servizi Windows o tramite il comando Stop-Service in PowerShell.

2. Aggiornare il servizio per l'utilizzo di un account di accesso diverso.

3. In certmgr per i certificati del computer locale assegnare le autorizzazioni di chiave privata al nuovo account per i certificati della coppia di chiavi del **motore dello scenario** **DMS** e del DMS.

    a. Aprire certmgr per visualizzare le chiavi seguenti:

    - Chiave app ibrida DMS
    - Chiave di configurazione del ruolo di lavoro ibrido DMS
    - Coppia di chiavi del motore dello scenario DMS

    b. Fare clic con il pulsante destro del mouse sulla voce di **chiave dell'app ibrida DMS** , scegliere **tutte le attività**, quindi selezionare **Gestisci chiavi private**.

    c. Nella scheda **sicurezza** selezionare **Aggiungi**e quindi immettere il nome dell'account.

    d. Usare la stessa procedura per concedere l'autorizzazione per la chiave privata per il nuovo account al certificato della **coppia di chiavi del motore di scenario DMS** .

## <a name="unregistering-the-worker-manually"></a>Annullamento della registrazione manuale del ruolo di lavoro

Se non si ha più accesso al computer di lavoro, è possibile annullare la registrazione del ruolo di lavoro e riusare l'istanza del servizio migrazione del database di Azure attenendosi alla procedura seguente:

1. Nella portale di Azure accedere all'istanza del servizio migrazione del database di Azure e quindi passare alla pagina impostazioni **ibride** .

   La voce di lavoro viene visualizzata nell'elenco, con lo stato visualizzato come **offline**.

2. Selezionare i puntini di sospensione nella parte destra dell'elenco di voci di lavoro e quindi selezionare **Annulla registrazione**.

## <a name="addressing-issues-for-specific-migration-scenarios"></a>Risoluzione dei problemi per scenari di migrazione specifici

Le sezioni seguenti descrivono i problemi specifici dello scenario correlati all'uso della modalità ibrida del servizio migrazione del database di Azure per eseguire una migrazione in linea.

### <a name="online-migrations-to-azure-sql-database-managed-instance"></a>Migrazioni online per istanza gestita di database SQL di Azure

**Utilizzo elevato della CPU**

**Problema**: per le migrazioni online all'istanza gestita di database SQL, il computer che esegue il ruolo di lavoro ibrido rileverà un utilizzo elevato della CPU se sono presenti troppi backup o se i backup sono troppo grandi.

**Mitigazione**: per attenuare questo problema, usare i backup compressi, suddividere la migrazione in modo che usi più condivisioni o aumentare il numero di risorse del computer che esegue il ruolo di lavoro ibrido.
