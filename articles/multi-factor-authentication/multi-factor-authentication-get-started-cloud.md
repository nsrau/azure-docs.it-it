---
title: Introduzione ad Azure MFA nel cloud | Documentazione Microsoft
description: "Questa è la pagina relativa a Microsoft Azure Multi-Factor Authentication che descrive come iniziare a usare Azure MFA nel cloud."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 6b2e6549-1a26-4666-9c4a-cbe5d64c4e66
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/24/2017
ms.author: kgremban
ms.translationtype: HT
ms.sourcegitcommit: a9cfd6052b58fe7a800f1b58113aec47a74095e3
ms.openlocfilehash: 1cce449a87571fdabd0dbf76f764f442b2990ffe
ms.contentlocale: it-it
ms.lasthandoff: 08/12/2017

---
# <a name="getting-started-with-azure-multi-factor-authentication-in-the-cloud"></a>Introduzione ad Azure Multi-Factor Authentication nel cloud
Questo articolo illustra come iniziare a usare Azure Multi-Factor Authentication nel cloud.

> [!NOTE]
> La documentazione seguente fornisce informazioni su come abilitare gli utenti tramite il **portale di Azure classico**. Per informazioni su come configurare Azure Multi-Factor Authentication per gli utenti di Office 365, vedere [Configurare Multi-Factor Authentication per Office 365.](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6?ui=en-US&rs=en-US&ad=US)

![MFA nel cloud](./media/multi-factor-authentication-get-started-cloud/mfa_in_cloud.png)

## <a name="prerequisite"></a>Prerequisito
[Iscriversi per ottenere una sottoscrizione di Azure](https://azure.microsoft.com/pricing/free-trial/) : se non si ha già una sottoscrizione ad Azure, è necessario iscriversi. Se si è appena iniziato a usare il servizio Azure MFA, è possibile usare una sottoscrizione di valutazione

## <a name="enable-azure-multi-factor-authentication"></a>Abilitare Azure Multi-Factor Authentication
Se gli utenti hanno licenze che comprendono Azure Multi-Factor Authentication, non è necessario eseguire operazioni per attivare Azure MFA. È possibile iniziare a richiedere la verifica in due passaggi per i singoli utenti. Le licenze che abilitano Azure MFA sono le seguenti:
- Azure Multi-Factor Authentication
- Azure Active Directory Premium
- Enterprise Mobility + Security

Se nessuna di queste tre licenze è disponibile oppure se le licenze disponibili non bastano per tutti gli utenti, è sufficiente eseguire un passaggio aggiuntivo e [creare un provider di Multi-Factor Authentication](multi-factor-authentication-get-started-auth-provider.md) nella directory.

## <a name="turn-on-two-step-verification-for-users"></a>Attivare la verifica in due passaggi per gli utenti

Usare una delle procedure elencate in [Come richiedere la verifica in due passaggi per un utente o un gruppo](multi-factor-authentication-get-started-user-states.md) per iniziare a usare Azure MFA. È possibile scegliere di applicare la verifica in due passaggi per tutti gli accessi o creare criteri di accesso condizionale per richiedere la verifica in due passaggi solo quando lo si ritiene opportuno.

## <a name="next-steps"></a>Passaggi successivi
Dopo avere configurato Azure Multi-Factor Authentication nel cloud, è possibile configurare e impostare la distribuzione. Per altri dettagli, vedere [Configurazione di Azure Multi-Factor Authentication](multi-factor-authentication-whats-next.md).


