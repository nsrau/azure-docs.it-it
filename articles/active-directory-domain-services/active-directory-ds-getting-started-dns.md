---
title: 'Azure Active Directory Domain Services: Aggiornare le impostazioni DNS per la rete virtuale di Azure | Microsoft Docs'
description: Introduzione ad Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
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
ms.openlocfilehash: 84484be80ec0932b45f520482938f74ecf54d1e8
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2019
ms.locfileid: "54856656"
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
