---
title: Eseguire la migrazione di account di archiviazione e sottoscrizioni per il servizio Gestione dispositivi StorSimple | Microsoft Docs
description: Informazioni su come eseguire la migrazione di sottoscrizioni e account di archiviazione per il servizio Gestione dispositivi StorSimple 8000.
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2017
ms.author: alkohli
ms.openlocfilehash: edac2635086106f83b30316ab190bead5245f195
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-subscriptions-and-storage-accounts-associated-with-storsimple-device-manager-service"></a>Eseguire la migrazione di sottoscrizioni e account di archiviazione associati al servizio Gestione dispositivi StorSimple

Potrebbe essere necessario spostare il servizio StorSimple in una nuova registrazione o in una nuova sottoscrizione. Questi scenari di migrazione prevedono modifiche agli account o ai data center. La tabella seguente elenca gli scenari supportati e la procedura dettagliata per spostarli.

## <a name="account-changes"></a>Modifiche agli account

| È possibile spostare...| Supportato| Tempo di inattività| Processo di supporto di Azure| Approccio|
|-----|-----|-----|-----|-----|
| Un'intera sottoscrizione (che include il servizio StorSimple e gli account di archiviazione) in un'altra registrazione? | Sì       | No        | **Trasferimento della registrazione**<br>Uso:<li>Quando si acquista un nuovo impegno di Azure con un nuovo contratto.</li><li>Si vuole eseguire la migrazione di tutti gli account e di tutte le sottoscrizioni dalla vecchia registrazione a quella nuova. Sono inclusi tutti i servizi di Azure compresi nella sottoscrizione precedente.</li> | **Passaggio 1: Aprire un ticket di supporto per le operazioni di Azure Enterprise.**<li>Andare a [http://aka.ms/AzureEnt](http://aka.ms/AzureEnt).</li><li> Selezionare **Enrollment Administration** (Amministrazione registrazione) e quindi selezionare **Transfer from one enrollment to a new enrollment** (Trasferisci da una registrazione a una nuova registrazione).<br>**Passaggio 2: Fornire le informazioni richieste**<br>Includere:<li>Numero di registrazione di origine</li><li> Numero di registrazione di destinazione</li><li>Data di validità del trasferimento|
| Il servizio StorSimple da un account esistente a una nuova registrazione?    | Sì       | No        | **Trasferimento dell'account**<br>Usare:<li>Quando non si vuole trasferire completamente una registrazione.</li><li>Si vogliono spostare solo account specifici in una nuova registrazione.</li>| **Passaggio 1: Aprire un ticket di supporto per le operazioni di Azure Enterprise.**<li>Andare a [http://aka.ms/AzureEnt](http://aka.ms/AzureEnt).</li><li>Selezionare **Enrollment Administration** (Amministrazione registrazione) e quindi selezionare **Transfer from one enrollment to a new enrollment** (Trasferisci un account EA in una nuova registrazione).<br>**Passaggio 2: Fornire le informazioni richieste**<br>Includere:<li>Numero di registrazione di origine</li><li> Numero di registrazione di destinazione</li><li>Data di validità del trasferimento|
| Il servizio StorSimple da una sottoscrizione a un'altra sottoscrizione?      | No         |    Sì         | Nessuno, processo manuale|<li>Eseguire la migrazione dei dati dal dispositivo StorSimple.</li><li>Eseguire un ripristino delle impostazioni predefinite del dispositivo per eliminare i dati locali sul dispositivo.</li><li>Registrare il dispositivo con la nuova sottoscrizione in un servizio Gestione dispositivi StorSimple.</li><li>Eseguire la migrazione dei dati al dispositivo.|
| Il dispositivo StorSimple da un servizio Gestione dispositivi StorSimple a un altro servizio in un'area diversa?      | No         | Sì            | Nessuno, processo manuale |Come sopra.|
| L'account di archiviazione in una nuova sottoscrizione o in un nuovo gruppo di risorse?     | Sì        | No              |Spostare l'account di archiviazione in un'altra sottoscrizione o in un altro gruppo di risorse |Dopo lo spostamento, se le chiavi di accesso dell'account di archiviazione vengono aggiornate, l'utente dovrà configurare le chiavi di accesso manualmente per l'account di archiviazione migrato tramite il servizio Gestione dispositivi StorSimple.|
| L'account di archiviazione classico in un account di archiviazione di Azure Resource Manager?      | Sì        | No              |Eseguire la migrazione dal modello di distribuzione classica ad Azure Resource Manager |<li>Per istruzioni dettagliate su come eseguire la migrazione di un account di archiviazione dal modello di distribuzione classica ad Azure Resource Manager, vedere [Eseguire la migrazione di un account di archiviazione](../virtual-machines/windows/migration-classic-resource-manager-ps.md#step-62-migrate-a-storage-account).</li><li> Se le chiavi di accesso dell'account di archiviazione vengono aggiornate dopo la migrazione, l'utente dovrà sincronizzare le chiavi di accesso per l'account di archiviazione migrato tramite il servizio Gestione dispositivi StorSimple. In questo modo si garantisce che i dispositivi StorSimple continueranno a funzionare normalmente e saranno in grado di suddividere in livelli i dati primari/di backup in Azure. Per istruzioni dettagliate sulla sincronizzazione delle chiavi di accesso, vedere [Flusso di lavoro di rotazione](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts).</li><li> Nel caso di un'appliance cloud StorSimple, se viene eseguita la migrazione dell'account di archiviazione classico ma la macchina virtuale sottostante rimane nella modalità classica, l'appliance dovrebbe funzionare correttamente. Se viene eseguita la migrazione della macchina virtuale sottostante per l'appliance cloud, la funzionalità di disattivazione ed eliminazione non funzionerà.</li><li> È necessario creare una nuova appliance cloud StorSimple nel portale di Azure e quindi effettuare il failover dalle appliance cloud precedenti. Non è possibile creare un'appliance cloud StorSimple nel nuovo portale di Azure usando un account di archiviazione classico. È necessario disporre di un account di archiviazione di Azure Resource Manager. Per altre informazioni, vedere [Distribuire e gestire un'appliance cloud StorSimple](storsimple-8000-cloud-appliance-u2.md).</li>|Come sopra.|

## <a name="datacenter-changes"></a>Modifiche ai data center

| È possibile spostare...| Supportato|Tempo di inattività| Processo di supporto di Azure| Approccio|
|-----|-----|-----|-----|-----|
| Un servizio StorSimple da un data center di Azure a un altro? | No  | Sì |Nessuno, processo manuale  |<li>Eseguire la migrazione dei dati dal dispositivo StorSimple.</li><li>Eseguire un ripristino delle impostazioni predefinite del dispositivo per eliminare i dati locali sul dispositivo.</li><li>Registrare il dispositivo con la nuova sottoscrizione in un nuovo servizio Gestione dispositivi StorSimple.</li><li>Eseguire la migrazione dei dati al dispositivo.|
| Un account di archiviazione da un data center di Azure a un altro? | No  |Sì  |Nessuno, processo manuale  | Come sopra.|

## <a name="next-steps"></a>Passaggi successivi

* [Distribuire il servizio Gestione dispositivi StorSimple](storsimple-8000-manage-service.md)
* [Distribuire il dispositivo StorSimple serie 8000 nel portale di Azure](storsimple-8000-deployment-walkthrough-u2.md)
