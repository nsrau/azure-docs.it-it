---
title: Configurare il server di configurazione per il ripristino di emergenza da server fisici ad Azure con Azure Site Recovery | Microsoft Docs
description: Questo articolo descrive come configurare il server di configurazione locale per il ripristino di emergenza da server fisici locali ad Azure.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/14/2019
ms.author: ramamill
ms.openlocfilehash: 5f0578026e95378065fc68198434e347a87eb1fe
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123915"
---
# <a name="set-up-the-configuration-server-for-disaster-recovery-of-physical-servers-to-azure"></a>Configurare il server di configurazione per il ripristino di emergenza da server fisici ad Azure

Questo articolo descrive come configurare l'ambiente locale per avviare la replica in Azure di server fisici che eseguono Windows o Linux.

## <a name="prerequisites"></a>Prerequisiti

Nell'articolo si presuppone che l'utente disponga di quanto segue:
- Un insieme di credenziali di Servizi di ripristino nel [portale di Azure](https://portal.azure.com "portale di Azure").
- Un computer fisico in cui installare il server di configurazione.
- Se TLS 1.0 è stato disabilitato nel computer in cui si sta installando il server di configurazione, verificare che TLs 1.2 sia abilitato e che .NET Framework versione 4.6 o versione successiva sia installato nel computer (con crittografia avanzata disabilitata). [Altre informazioni](https://support.microsoft.com/help/4033999/how-to-resolve-azure-site-recovery-agent-issues-after-disabling-tls-1)

### <a name="configuration-server-minimum-requirements"></a>Requisiti minimi per il server di configurazione
La tabella seguente elenca i requisiti minimi hardware, software e di rete per un server di configurazione.
[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

> [!NOTE]
> I server proxy basati su HTTPS non sono supportati dal server di configurazione.

## <a name="choose-your-protection-goals"></a>Scegliere gli obiettivi della protezione

1. Nel portale di Azure accedere al pannello relativo agli insiemi di credenziali di **Servizi di ripristino** e selezionare l'insieme di credenziali specifico.
2. Nel menu delle **risorse** dell'insieme di credenziali fare clic su **Attività iniziali** > **Site Recovery** > **Passaggio 1: Preparare l'infrastruttura** > **Obiettivo di protezione**.

    ![Scegliere gli obiettivi](./media/physical-azure-set-up-source/choose-goals.png)
3. In **Obiettivo di protezione** selezionare **In Azure** e scegliere **Non virtualizzato/Altro**e quindi fare clic su **OK**.

    ![Scegliere gli obiettivi](./media/physical-azure-set-up-source/physical-protection-goal.png)

## <a name="set-up-the-source-environment"></a>Configurare l'ambiente di origine

1. Se non è disponibile un server di configurazione, in **Prepara origine** fare clic su **+Server di configurazione** per aggiungerne uno.

   ![Impostare l'origine](./media/physical-azure-set-up-source/plus-config-srv.png)
2. Nel pannello **Aggiungi server** verificare che **Server di configurazione** sia visualizzato in **Tipo di server**.
4. Scaricare il file di installazione per l'Installazione unificata di Azure Site Recovery.
5. Scaricare la chiave di registrazione dell'insieme di credenziali, Per eseguire l'installazione unificata, è necessaria la chiave di registrazione. La chiave è valida per cinque giorni dal momento in cui viene generata.

    ![Impostare l'origine](./media/physical-azure-set-up-source/set-source2.png)
6. Nel computer usato come server di configurazione, eseguire l'**installazione unificata di Azure Site Recovery** per installare il server di configurazione, il server di elaborazione e il server master di destinazione.

#### <a name="run-azure-site-recovery-unified-setup"></a>Eseguire l'installazione unificata di Azure Site Recovery

> [!TIP]
> La registrazione del server di configurazione non riesce se l'ora del sistema nel computer differisce di oltre cinque minuti dall'ora locale. Sincronizzare l'ora del sistema con un [server di riferimento ora](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service) prima di iniziare l'installazione.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> Il server di configurazione può essere installato tramite una riga di comando. [Altre informazioni](physical-manage-configuration-server.md#install-from-the-command-line)


## <a name="common-issues"></a>Problemi comuni

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Passaggi successivi

Nel prossimo passaggio viene eseguita la [configurazione dell'ambiente di destinazione](physical-azure-set-up-target.md) in Azure.
