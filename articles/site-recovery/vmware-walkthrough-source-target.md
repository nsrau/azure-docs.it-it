---
title: Impostare l'origine e destinazione per la replica VMware in Azure con Azure Site Recovery | Microsoft Docs
description: Riepilogo dei passaggi per configurare le impostazioni di origine e destinazione per la replica delle macchine virtuali VMware nell'archiviazione di Azure con Azure Site Recovery
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d99e422e-daf7-4fa8-af3c-af2340340136
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 94b629a62c3a54eee69ee397b2f27e3f20b753d5
ms.contentlocale: it-it
ms.lasthandoff: 06/29/2017


---
# <a name="step-8-set-up-the-source-and-target-for-vmware-replication-to-azure"></a>Passaggio 8: Configurare l'origine e la destinazione per la replica VMware in Azure

Questo articolo illustra come configurare impostazioni di origine e destinazione per la replica di macchine virtuali VMware locali in Azure usando il servizio [Azure Site Recovery](site-recovery-overview.md) nel portale di Azure.

Inserire commenti e domande nella parte inferiore di questo articolo oppure nel [forum sui servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="set-up-the-source-environment"></a>Configurare l'ambiente di origine

Configurare il server di configurazione, registrarlo nell'insieme di credenziali e individuare le VM.

1. Fare clic su **Site Recovery** > **Passaggio 1: Preparare l'infrastruttura** > **Origine**.
2. Se non è disponibile un server di configurazione, fare clic su **+Server di configurazione**.
3. In **Aggiungi server** verificare che **Tipo di server** contenga **Server di configurazione**.
4. Scaricare il file di installazione per l'Installazione unificata di Azure Site Recovery.
5. Scaricare la chiave di registrazione dell'insieme di credenziali, che sarà necessaria quando si esegue l'Installazione unificata. La chiave è valida per cinque giorni dal momento in cui viene generata.

   ![Impostare l'origine](./media/vmware-walkthrough-source-target/set-source2.png)


## <a name="register-the-configuration-server-in-the-vault"></a>Registrare il server di configurazione nell'insieme di credenziali

Seguire questa procedura prima di iniziare, quindi eseguire l'Installazione unificata per installare il server di configurazione, il server di elaborazione e il server di destinazione master.
    - Ottenere una breve panoramica video

        > [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video1-Source-Infrastructure-Setup/player]

    - Nella VM del server di configurazione verificare che il clock di sistema sia sincronizzato con un [server di riferimento ora](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service). Deve corrispondere. Se è avanti o indietro di 15 minuti, l'installazione potrebbe avere esito negativo.
    - Eseguire l'installazione come amministratore locale nella VM del server di configurazione.
    - Verificare che nella VM sia abilitato TLS 1.0.


[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> Il server di configurazione può essere installato anche [dalla riga di comando](http://aka.ms/installconfigsrv).



## <a name="connect-to-vmware-servers"></a>Connettersi ai server VMware

Per consentire ad Azure Site Recovery di individuare macchine virtuali in esecuzione nell'ambiente locale, è necessario connettere il server VMware vCenter o gli host vSphere ESXi a Site Recovery. Prima di iniziare tenere presente quanto segue:

- Se si aggiungono gli host vSphere o il server vCenter a Site Recovery con un account senza privilegi Administrator per il server, per l'account devono essere abilitati i privilegi seguenti:
    - Datacenter (Data center), Datastore (Archivio dati), Folder (Cartella), Host, Network (Rete), Resource (Risorsa), Virtual machine (Macchina virtuale) e vSphere Distributed Switch (Switch distribuito vSphere).
    - Per il server vCenter sono necessarie le autorizzazioni Storage views (Viste archiviazione).
- Quando si aggiungono server VMware a Site Recovery possono trascorrere 15 o più minuti prima che i server vengano visualizzati nel portale.

### <a name="add-the-account-for-automatic-discovery"></a>Aggiungere l'account per l'individuazione automatica

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

### <a name="set-up-a-connection"></a>Configurare una connessione

Stabilire la connessione ai server come indicato di seguito:

1. Selezionare **+vCenter** per avviare la connessione di un server VMware vCenter o di un host VMware vSphere ESXi.
2. In **Aggiungi vCenter** specificare un nome descrittivo per il server vCenter o l'host vSphere e quindi specificare l'indirizzo IP o il nome di dominio completo del server.
3. Lasciare la porta 443, a meno che i server VMware siano configurati per l'ascolto delle richieste su una porta diversa. Selezionare l'account che si deve connettere al server VMware vCenter o vSphere ESXi. Fare clic su **OK**.
4. Site Recovery si connette ai server VMware usando le impostazioni specificate e individua le VM.

> [!NOTE]
> Se si aggiunge un server o un host con un account che non ha privilegi Administrator per il server vCenter o il server host, assicurarsi che questi privilegi siano abilitati per l'account: Datacenter (Data center), Datastore (Archivio dati), Folder (Cartella), Host, Network (Rete), Resource (Risorsa), Virtual machine (Macchina virtuale) e vSphere Distributed Switch (Switch distribuito vSphere). Per il server VMware vCenter deve essere abilitato anche il privilegio Storage Views (Viste archiviazione).


## <a name="set-up-the-target-environment"></a>Configurare l'ambiente di destinazione

Prima di configurare l'ambiente di destinazione verificare di aver configurato una rete virtuale e un account di archiviazione di Azure.

1. Fare clic su **Preparare l'infrastruttura** > **Destinazione** e selezionare la sottoscrizione di Azure da usare.
2. Specificare se per la destinazione deve essere usato il modello di distribuzione classica o Resource Manager.
3. Site Recovery verifica la disponibilità di uno o più account di archiviazione di Azure e reti compatibili.

   ![Destinazione](./media/vmware-walkthrough-source-target/gs-target.png)
4. Se non si è creato un account di archiviazione o una rete, fare clic su **+Account di archiviazione** o **+Rete** per creare una rete o un account Resource Manager inline.

## <a name="next-steps"></a>Passaggi successivi

Andare al [Passaggio 9: Configurare i criteri di replica](vmware-walkthrough-replication.md)

