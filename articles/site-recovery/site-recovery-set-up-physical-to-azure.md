---
title: Configurare l&quot;ambiente di origine (server fisici in Azure) | Microsoft Docs
description: Questo articolo descrive come configurare l&quot;ambiente locale per avviare la replica in Azure di server fisici che eseguono Windows o Linux.
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 1/10/2017
ms.author: anoopkv
translationtype: Human Translation
ms.sourcegitcommit: 9dfcdeb4dc3e84089eb8031272b870f87d90b689
ms.openlocfilehash: 9416ec83089a6892099634b0572b2c794dc74dba

---

# <a name="set-up-the-source-environment-physical-server-to-azure"></a>Configurare l'ambiente di origine (server fisico in Azure)
> [!div class="op_single_selector"]
> * [Macchine virtuali VMware](./site-recovery-set-up-vmware-to-azure.md)
> * [Server fisici](./site-recovery-set-up-physical-to-azure.md)

Questo articolo descrive come configurare l'ambiente locale per avviare la replica in Azure di server fisici che eseguono Windows o Linux.

## <a name="prerequisites"></a>Prerequisiti

Nell'articolo si presuppone che l'utente disponga di quanto segue:
1. Insieme di credenziali dei servizi di ripristino nel [portale di Azure](http://portal.azure.com "Portale di Azure").
3. Computer fisico per installare il server di configurazione.

### <a name="configuration-server-minimum-requirements"></a>Requisiti minimi per il server di configurazione
La tabella seguente contiene i requisiti minimi hardware, software e di rete per un server di configurazione.
[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-server-requirements.md)]

> [!NOTE]
> I server proxy basati su HTTPS non sono supportati dal server di configurazione.

## <a name="choose-your-protection-goals"></a>Scegliere gli obiettivi della protezione

1. Nel portale di Azure accedere al pannello degli insiemi di credenziali dei **servizi di ripristino** e selezionare l'insieme di credenziali.
2. Nel menu delle risorse dell'insieme di credenziali fare clic su **Attività iniziali** > **Site Recovery** > **Passaggio 1: Preparare l'infrastruttura** > **Obiettivo di protezione**.

    ![Scegliere gli obiettivi](./media/site-recovery-set-up-physical-to-azure/choose-goals.png)
3. In **Obiettivo di protezione** selezionare **In Azure** e scegliere **Non virtualizzato/Altro**. Fare quindi clic su **OK**.

    ![Scegliere gli obiettivi](./media/site-recovery-set-up-physical-to-azure/physical-protection-goal.PNG)

## <a name="set-up-the-source-environment"></a>Configurare l'ambiente di origine

1. Se non è disponibile un server di configurazione, in **Prepara origine** fare clic su **+Server di configurazione** per aggiungerne uno.

  ![Impostare l'origine](./media/site-recovery-set-up-physical-to-azure/plus-config-srv.png)
2. Nel pannello **Aggiungi server** verificare che **Server di configurazione** sia visualizzato in **Tipo di server**.
4. Scaricare il file di installazione per l'Installazione unificata di Azure Site Recovery.
5. Scaricare la chiave di registrazione dell'insieme di credenziali, Per eseguire l'installazione unificata, è necessaria la chiave di registrazione. La chiave è valida per **cinque** giorni dopo la generazione.

    ![Impostare l'origine](./media/site-recovery-set-up-physical-to-azure/set-source2.png)
6. Nel computer usato come server di configurazione, eseguire l'**installazione unificata di Azure Site Recovery** per installare il server di configurazione, il server di elaborazione e il server master di destinazione.

#### <a name="running-the-azure-site-recovery-unified-setup"></a>Esecuzione dell'installazione unificata di Azure Site Recovery

> [!TIP]
> La registrazione del server di configurazione non riesce se l'ora nel computer è spostata in avanti o indietro di oltre cinque minuti rispetto all'ora locale.  Sincronizzare l'ora del sistema con un [server di riferimento ora](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service) prima di iniziare l'installazione.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> Il server di configurazione può essere installato mediante la riga di comando. Per altre informazioni, vedere l'argomento relativo all'[installazione del server di configurazione con gli strumenti della riga di comando](http://aka.ms/installconfigsrv).


## <a name="common-issues"></a>Problemi comuni

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Passaggi successivi
Nel prossimo passaggio viene eseguita la [configurazione dell'ambiente di destinazione](./site-recovery-vmware-to-azure.md#step-3-set-up-the-target-environment) in Azure.



<!--HONumber=Jan17_HO2-->


