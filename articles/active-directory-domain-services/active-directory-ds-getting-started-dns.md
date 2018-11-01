---
title: 'Azure Active Directory Domain Services: aggiornare le impostazioni DNS per la rete virtuale di Azure | Microsoft Docs'
description: Introduzione ad Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: d4f3e82c-6807-4690-b298-4eabad2b7927
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: ergreenl
ms.openlocfilehash: 7d2902c997259fc115a1f204f123983038821887
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2018
ms.locfileid: "50157344"
---
# <a name="enable-azure-active-directory-domain-services"></a>Abilitare Azure Active Directory Domain Services

## <a name="task-4-update-dns-settings-for-the-azure-virtual-network"></a>Attività 4: Aggiornare le impostazioni DNS per la rete virtuale di Azure
Nelle attività di configurazione precedenti è stata completata l'abilitazione di Azure Active Directory Domain Services per la directory. Abilitare quindi i computer della rete virtuale a connettersi a questi servizi e utilizzarli. In questo articolo si aggiorneranno le impostazioni del server DNS per la rete virtuale in modo che puntino ai due indirizzi IP in cui è disponibile Azure Active Directory Domain Services nella rete virtuale.

Per aggiornare le impostazioni del server DNS per la rete virtuale in cui è stato abilitato Azure Active Directory Domain Services, seguire questa procedura:


1. La scheda **Panoramica** elenca una **Procedura di configurazione necessaria** da eseguire dopo il provisioning completo del dominio gestito. Il primo passaggio di configurazione è **Aggiorna le impostazioni del server DNS per la rete virtuale**.

    ![Domain Services - Scheda Panoramica](./media/getting-started/domain-services-provisioned-overview.png)

    > [!TIP]
    > Il passaggio di configurazione non viene visualizzato? Se le impostazioni del server DNS per la rete virtuale sono aggiornate, il riquadro "Aggiorna le impostazioni del server DNS per la rete virtuale" nella scheda Panoramica non viene visualizzato.
    >
    >

2. Fare clic sul pulsante **Configura** per aggiornare le impostazioni del server DNS per la rete virtuale.

> [!NOTE]
> Le macchine virtuali nella rete ottengono le nuove impostazioni DNS solo dopo il riavvio. Se è necessario ottenere subito le impostazioni DNS aggiornate, eseguire il riavvio dal portale, da PowerShell o dall'interfaccia della riga di comando.
>
>

## <a name="next-step"></a>Passaggio successivo
[Attività 5: Abilitare la sincronizzazione dell'hash delle password con Azure Active Directory Domain Services](active-directory-ds-getting-started-password-sync.md)
