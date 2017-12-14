---
title: 'Azure Active Directory Domain Services: aggiornare le impostazioni DNS per la rete virtuale di Azure | Microsoft Docs'
description: Introduzione a Servizi di dominio Azure Active Directory
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: d4f3e82c-6807-4690-b298-4eabad2b7927
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/26/2017
ms.author: maheshu
ms.openlocfilehash: c99d42eaf52a13afef6df76b6bb1a714e719fa64
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="enable-azure-active-directory-domain-services"></a>Abilitare Azure Active Directory Domain Services

## <a name="task-4-update-dns-settings-for-the-azure-virtual-network"></a>Attività 4: Aggiornare le impostazioni DNS per la rete virtuale di Azure
Nelle attività di configurazione precedenti è stata completata l'abilitazione di Azure Active Directory Domain Services per la directory. L'attività successiva consiste nell'assicurarsi che i computer della rete virtuale possano connettersi a questi servizi e utilizzarli. In questo articolo si aggiorneranno le impostazioni del server DNS per la rete virtuale in modo che puntino ai due indirizzi IP in cui è disponibile Azure Active Directory Domain Services nella rete virtuale.

Per aggiornare l'impostazione del server DNS per la rete virtuale in cui è stato abilitato Azure Active Directory Domain Services, seguire questa procedura:

1. La scheda **Panoramica** elenca una **Procedura di configurazione necessaria** da eseguire dopo il provisioning completo del dominio gestito. Il primo passaggio di configurazione è **Aggiorna le impostazioni del server DNS per la rete virtuale**.

    ![Domain Services - Scheda Panoramica dopo il provisioning completo](./media/getting-started/domain-services-provisioned-overview.png)

2. Quando il provisioning del dominio è stato completato, in questo riquadro vengono visualizzati due indirizzi IP. Ogni indirizzo IP rappresenta un controller di dominio per il dominio gestito.

3. Per copiare il primo indirizzo IP negli Appunti, fare clic sul pulsante Copia accanto all'indirizzo. Fare quindi clic sul pulsante **Configura i server DNS**.

4. Incollare il primo indirizzo IP nella casella di testo **Aggiungi server DNS** nel pannello **Server DNS**. Scorrere orizzontalmente verso sinistra per copiare il secondo indirizzo IP e incollarlo nella casella di testo **Aggiungi server DNS**.

    ![Domain Services - Aggiornamento di DNS](./media/getting-started/domain-services-update-dns.png)

5. Fare clic su **Salva** al termine dell'aggiornamento dei server DNS per la rete virtuale.

> [!NOTE]
> Le macchine virtuali nella rete ottengono le nuove impostazioni DNS solo dopo il riavvio. Se è necessario ottenere subito le impostazioni DNS aggiornate, eseguire il riavvio dal portale, da PowerShell o dall'interfaccia della riga di comando.
>
>

## <a name="next-step"></a>Passaggio successivo
[Attività 5: Abilitare la sincronizzazione password con Azure Active Directory Domain Services](active-directory-ds-getting-started-password-sync.md)
