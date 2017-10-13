---
title: Configurare l'origine e la destinazione per la replica Hyper-V in un sito secondario con Azure Site Recovery | Microsoft Docs
description: Descrive come configurare l'origine e la destinazione durante la replica di VM Hyper-V in un sito VMM secondario con Azure Site Recovery.
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: fa7809f1-7633-425f-b25d-d10d004e8d0b
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: 07135e9b5e619971a59cc22ec68a0a4e8bcaabe1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="step-6-set-up-the-replication-source-and-target"></a>Passaggio 6: Configurare l'origine e la destinazione della replica


Dopo la creazione di un insieme di credenziali di Servizi di ripristino per la replica Hyper-V in un sito VMM secondario con [Azure Site Recovery](site-recovery-overview.md), usare le istruzioni disponibili in questo articolo per configurare le posizioni di origine e di destinazione della replica. 

È possibile inviare commenti nella parte inferiore di questo articolo oppure nel [forum sui servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).




## <a name="set-up-the-source-environment"></a>Configurare l'ambiente di origine

Installare il provider di Azure Site Recovery nei server VMM, rilevare e registrare il server nell'insieme di credenziali.

1. Fare clic su **Passaggio 1: Preparare l'infrastruttura** > **Origine**.

    ![Impostare l'origine](./media/vmm-to-vmm-walkthrough-source-target/goals-source.png)
2. In **Prepara origine** fare clic su **+ VMM** per aggiungere un server VMM.

    ![Impostare l'origine](./media/vmm-to-vmm-walkthrough-source-target/set-source1.png)
3. In **Aggiungi server** verificare che in **Tipo di server** sia visualizzato **System Center VMM server** (Server System Center VMM) e che il server VMM sia conforme ai [prerequisiti](#prerequisites).
4. Scaricare il file di installazione del provider di Azure Site Recovery.
5. Scaricare la chiave di registrazione, che sarà necessaria durante l'installazione. La chiave è valida per cinque giorni dal momento in cui viene generata.

    ![Impostare l'origine](./media/vmm-to-vmm-walkthrough-source-target/set-source3.png)
6. Installare il provider di Azure Site Recovery nel server VMM. Nei server host Hyper-V non è necessario installare niente in modo esplicito.


## <a name="install-the-azure-site-recovery-provider"></a>Installare il provider di Azure Site Recovery

1. Eseguire il file di installazione del provider in ogni server VMM. Se VMM viene distribuito in un cluster, procedere come segue per la prima installazione:
    -  Installare il provider in un nodo attivo e completare l'installazione per registrare il server VMM nell'insieme di credenziali.
    - Quindi, installare il provider negli altri nodi. Tutti i nodi del cluster devono eseguire la stessa versione del provider.
2. Il programma di installazione esegue alcune verifiche dei prerequisiti e chiede l'autorizzazione per arrestare il servizio VMM. Il servizio VMM verrà riavviato automaticamente al termine dell'installazione. Se si esegue l'installazione in un cluster VMM, verrà richiesto di interrompere il ruolo Cluster.
3. In **Microsoft Update** è possibile acconsentire per specificare esplicitamente che gli aggiornamenti del provider vengano installati in base ai criteri di Microsoft Update.
4. In **Installazione** accettare o modificare il percorso predefinito di installazione e quindi fare clic su **Installa**.

    ![Percorso di installazione](./media/vmm-to-vmm-walkthrough-source-target/provider-location.png)
5. Al termine dell'installazione fare clic su **Registra** per registrare il server nell'insieme di credenziali.

    ![Percorso di installazione](./media/vmm-to-vmm-walkthrough-source-target/provider-register.png)
6. In **Vault name**verificare il nome dell'insieme di credenziali in cui verrà registrato il server. Fare clic su *Avanti*.

    ![Server registration](./media/vmm-to-vmm-walkthrough-source-target/vaultcred.png)
7. Nella pagina **Connessione Internet** specificare la modalità di connessione ad Azure del provider in esecuzione sul server VMM.

    ![Internet Settings](./media/vmm-to-vmm-walkthrough-source-target/proxydetails.png)

   - È possibile specificare che il provider debba connettersi direttamente a Internet o tramite un proxy.
   - Se necessario, specificare le impostazioni proxy.
   - Se si usa un proxy, viene creato automaticamente un account RunAs di VMM (DRAProxyAccount) con le credenziali del proxy specificate. Configurare il server proxy in modo che l'account possa eseguire correttamente l'autenticazione. È possibile modificare le impostazioni dell'account RunAs nella console di VMM > **Impostazioni** > **Sicurezza** > **Account RunAs**. Riavviare il servizio VMM per aggiornare le modifiche.
8. In **Chiave di registrazione**selezionare il codice di registrazione scaricato da Azure Site Recovery e copiato nel server VMM.
9. L'impostazione di crittografia viene usata solo quando si esegue la replica di VM Hyper-V in cloud VMM in Azure. Non viene usata se si esegue la replica in un sito secondario.
10. In **Nome server**specificare un nome descrittivo per identificare il server VMM nell'insieme di credenziali. In una configurazione cluster specificare il nome del ruolo relativo al cluster VMM.
11. In **Sincronizza i metadati cloud** scegliere se sincronizzare i metadati per tutti i cloud presenti nel server VMM con l'insieme di credenziali. È necessario eseguire questa azione solo una volta in ogni server. Se non si vogliono sincronizzare tutti i cloud, è possibile lasciare deselezionata questa opzione e sincronizzare ogni cloud singolarmente nelle proprietà del cloud nella console VMM.
12. Fare clic su **Avanti** per completare il processo. Dopo la registrazione, i metadati del server VMM vengono recuperati da Azure Site Recovery. Il server viene visualizzato nella scheda **Server VMM** della pagina **Server** nell'insieme di credenziali.

    ![Server](./media/vmm-to-vmm-walkthrough-source-target/provider13.png)
13. Quando il server sarà disponibile nella console di Site Recovery, in **Origine** > **Prepara origine** selezionare il server VMM e quindi il cloud in cui si trova l'host Hyper-V. Fare quindi clic su **OK**.

È anche possibile installare il provider dalla riga di comando:

[!INCLUDE [site-recovery-rw-provider-command-line](../../includes/site-recovery-rw-provider-command-line.md)]


## <a name="set-up-the-target-environment"></a>Configurare l'ambiente di destinazione

Selezionare il server VMM di destinazione e il cloud:

1. Fare clic su **Preparare l'infrastruttura** > **Destinazione** e selezionare il server VMM di destinazione da usare.
2. Verranno visualizzati i cloud nel server sincronizzati con Site Recovery. Selezionare il cloud di destinazione.

   ![Destinazione](./media/vmm-to-vmm-walkthrough-source-target/target-vmm.png)



## <a name="next-steps"></a>Passaggi successivi

Andare al [Passaggio 7: Configurare il mapping di rete](vmm-to-vmm-walkthrough-network-mapping.md).
