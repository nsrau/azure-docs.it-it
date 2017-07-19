---
title: Impostare l'origine e la destinazione per la replica del server fisico in Azure con Azure Site Recovery | Microsoft Docs
description: Vengono riepilogati i passaggi per configurare le impostazioni di origine e di destinazione per la replica dei server fisici in Archiviazione di Azure con il servizio Azure Site Recovery
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 2e3d03bc-4e53-4590-8a01-f702d3cc9500
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: e89bbf5a2c1d71852e49da43d3106a05ebfc28a8
ms.contentlocale: it-it
ms.lasthandoff: 06/29/2017


---
# <a name="step-7-set-up-the-source-and-target-for-physical-server-replication-to-azure"></a>Passaggio 7: Configurare l'origine e la destinazione per la replica del server fisico in Azure

Questo articolo illustra come configurare le impostazioni di origine e di destinazione per la replica di server fisici locali in Azure, usando il servizio [Azure Site Recovery](site-recovery-overview.md) nel portale di Azure.

Inserire commenti e domande nella parte inferiore di questo articolo oppure nel [forum sui servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="set-up-the-source-environment"></a>Configurare l'ambiente di origine

Configurare il server di configurazione, registrarlo nell'insieme di credenziali e individuare le macchine.

1. Fare clic su **Site Recovery** > **Passaggio 1: Preparare l'infrastruttura** > **Origine**.
2. Se non è disponibile un server di configurazione, fare clic su **+Server di configurazione**.
3. In **Aggiungi server** verificare che **Tipo di server** contenga **Server di configurazione**.
4. Scaricare il file di installazione per l'Installazione unificata di Azure Site Recovery.
5. Scaricare la chiave di registrazione dell'insieme di credenziali, che sarà necessaria quando si esegue l'Installazione unificata. La chiave è valida per cinque giorni dal momento in cui viene generata.

   ![Impostare l'origine](./media/vmware-walkthrough-source-target/set-source2.png)


## <a name="register-the-configuration-server-in-the-vault"></a>Registrare il server di configurazione nell'insieme di credenziali

Seguire questa procedura prima di iniziare, quindi eseguire l'Installazione unificata per installare il server di configurazione, il server di elaborazione e il server di destinazione master. Il video illustra l'impostazione dei componenti per la replica della macchina virtuale di VMware in Azure. Lo stesso processo, tuttavia, è valido anche per la replica del server fisico in Azure.

- Nella VM del server di configurazione verificare che il clock di sistema sia sincronizzato con un [server di riferimento ora](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service). Deve corrispondere. Se è avanti o indietro di 15 minuti, l'installazione potrebbe avere esito negativo.
- Eseguire l'installazione come amministratore locale nel server di configurazione.
- Verificare che nella VM sia abilitato TLS 1.0.


[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> Il server di configurazione può essere installato anche [dalla riga di comando](http://aka.ms/installconfigsrv).




## <a name="set-up-the-target-environment"></a>Configurare l'ambiente di destinazione

Prima di configurare l'ambiente di destinazione, verificare di aver configurato una rete virtuale e un account di archiviazione di Azure.

1. Fare clic su **Preparare l'infrastruttura** > **Destinazione** e selezionare la sottoscrizione di Azure da usare.
2. Specificare se per la destinazione deve essere usato il modello di distribuzione classica o Resource Manager.
3. Site Recovery verifica la disponibilità di uno o più account di archiviazione di Azure e reti compatibili.

   ![Destinazione](./media/physical-walkthrough-source-target/gs-target.png)

4. Se non si è creato un account di archiviazione o una rete, fare clic su **+Account di archiviazione** o **+Rete** per creare una rete o un account Resource Manager inline.

## <a name="next-steps"></a>Passaggi successivi

Andare al [Passaggio 8: Configurare i criteri di replica](physical-walkthrough-replication.md)

