---
title: Installare il servizio Mobility per la replica del server fisico in Azure | Microsoft Docs
description: Questo articolo illustra come installare l'agente del servizio Mobility nei server fisici per la replica in Azure con il servizio Azure Site Recovery.
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 3189fbcd-6b5b-4ffb-b5a9-e2080c37f9d9
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: d73267d7a64221a3138af19e9a2d5dd15809b927
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="step-9-install-the-mobility-service"></a>Passaggio 9: Installare il servizio Mobility


Questo articolo illustra come installare il componente del servizio Mobility quando si replicano server fisici Windows/Linux locali in Azure usando il servizio [Azure Site Recovery](site-recovery-overview.md) nel portale di Azure.

Il servizio Mobility acquisisce le scritture di dati in un computer e le inoltra al server di elaborazione. Questo servizio deve essere installato in ogni server di cui si vuole eseguire la replica in Azure.

È possibile installare il servizio Mobility manualmente, usando un'installazione push dal server di elaborazione di Site Recovery quando la replica è abilitata o usando uno strumento come System Center Configuration Manager. Se si usa l'installazione push, il servizio viene installato nel server quando viene abilitata la replica.

Inserire commenti e domande nella parte inferiore di questo articolo oppure nel [forum sui servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="install-manually"></a>Eseguire l'installazione manuale

1. Controllare i [prerequisiti](site-recovery-vmware-to-azure-install-mob-svc.md#prerequisites) per l'installazione manuale.
2. Seguire [queste istruzioni](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-by-using-the-gui) per l'installazione manuale tramite il portale.
3. Se si preferisce eseguire l'installazione dalla riga di comando, seguire [queste istruzioni](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-at-a-command-prompt).

## <a name="install-from-the-process-server"></a>Eseguire l'installazione dal server di elaborazione

Se si vuole effettuare il push dell'installazione del servizio Mobility dal server di elaborazione quando si abilita la replica per una macchina, è necessario un account che possa essere usato dal server di elaborazione per accedere alla macchina. L'account viene usato solo per l'installazione push.

1. Se non è stato ancora creato un account, eseguire questa operazione usando le linee guida seguenti:

    - È possibile usare un account di dominio o locale.
    - Per Windows, se non si usa un account di dominio è necessario disabilitare il Controllo dell'accesso utente remoto nel computer locale. A questo scopo, aggiungere la voce DWORD **LocalAccountTokenFilterPolicy** con un valore di 1 nel Registro di sistema in **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**.
    - Per aggiungere la voce del Registro di sistema per Windows da un'interfaccia della riga di comando, digitare:

        ```
        REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.
        ```

    - Per Linux, l'account deve essere radice nel server Linux di origine.

2. Seguire [queste istruzioni](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-by-push-installation-from-azure-site-recovery) se si vuole eseguire il push del servizio Mobility nelle VM che eseguono Windows o Linux.

## <a name="other-installation-methods"></a>Altri metodi di installazione

- [Ulteriori informazioni](site-recovery-install-mobility-service-using-sccm.md) sull'installazione del servizio Mobility con Gestione configurazione
- [Ulteriori informazioni](site-recovery-automate-mobility-service-install.md) sull'installazione con la piattaforma DSC di Automazione di Azure.


## <a name="next-steps"></a>Passaggi successivi

Andare a [Passaggio 10: Abilitare la replica](physical-walkthrough-enable-replication.md)
