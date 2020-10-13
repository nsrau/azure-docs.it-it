---
title: Monitorare identità e accesso nel Centro sicurezza di Azure | Microsoft Docs
description: Informazioni su come usare le funzionalità per identità e accesso nel Centro sicurezza di Azure per monitorare l'attività di accesso degli utenti e i problemi correlati all'identità.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/16/2020
ms.author: memildin
ms.openlocfilehash: 042780c313c444062fd512ab0d9f38aaeb6cf170
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90894560"
---
# <a name="monitor-identity-and-access"></a>Monitorare identità e accesso

> [!TIP]
> Dal 2020 marzo, le raccomandazioni relative a identità e a accesso del Centro sicurezza di Azure sono incluse in tutte le sottoscrizioni del piano tariffario gratuito. Se si hanno sottoscrizioni nel livello gratuito, il punteggio di sicurezza corrispondente sarà influenzato dal fatto che non sono state precedentemente valutate in termini di sicurezza dell'accesso e delle identità. 

Quando il Centro sicurezza identifica potenziali vulnerabilità della sicurezza, crea raccomandazioni utili per definire il processo di configurazione dei controlli necessari per rafforzare e tutelare le risorse.

Il perimetro di sicurezza si è evoluto da perimetro di rete a perimetro di identità. La sicurezza consiste sempre meno nel difendere la rete e sempre più nel difendere i dati e nel gestire la sicurezza di app e utenti. Attualmente, con il trasferimento nel cloud di una maggiore quantità di dati e app, l'identità diventa il nuovo perimetro.

Il monitoraggio delle attività associate alle identità consente di intraprendere azioni proattive prima che si verifichi un evento imprevisto o azioni reattive per bloccare un tentativo di attacco. Ad esempio, il Centro sicurezza potrebbe contrassegnare per la rimozione gli account deprecati, ovvero gli account che non sono più necessari e a cui Azure Active Directory ha bloccato l'accesso. 

Di seguito sono riportati alcuni esempi di raccomandazioni che è possibile vedere nella sezione relativa alla sicurezza delle risorse **Identità e accesso** del Centro sicurezza di Azure:

- L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di proprietario per la sottoscrizione
- Per la sottoscrizione devono essere designati al massimo 3 proprietari
- Gli account esterni con autorizzazioni di lettura devono essere rimossi dalla sottoscrizione
- Gli account deprecati devono essere rimossi dalla sottoscrizione

Per altre informazioni e per un elenco completo delle raccomandazioni che potrebbero essere visualizzate, vedere [Raccomandazioni relative a identità e accesso](recommendations-reference.md#recs-identity).

> [!NOTE]
> Se la sottoscrizione include più di 600 account, il Centro sicurezza non è in grado di applicare i consigli relativi all'identità per la sottoscrizione. Le raccomandazioni non applicate sono elencate in "valutazioni non disponibili".
Il Centro sicurezza non è in grado di applicare i consigli relativi all'identità per gli agenti di amministrazione di un partner Cloud Solution Provider (CSP).
>


Tutte le raccomandazioni relative a identità e accesso sono disponibili all'interno di due controlli di sicurezza nella pagina **Raccomandazioni**:

- Gestire l'accesso e le autorizzazioni 
- Abilitare MFA

![I due controlli di sicurezza con le raccomandazioni relative a identità e accesso](media/security-center-identity-access/two-security-controls-for-identity-and-access.png)


## <a name="enable-multi-factor-authentication-mfa"></a>Abilitare l'autenticazione a più fattori (MFA)

Per abilitare l'autenticazione a più fattori sono necessarie [autorizzazioni tenant di Azure Active Directory (AD)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles). 

- Se si ha un'edizione Premium di AD, abilitare l'autenticazione a più fattori usando l'[accesso condizionale](../active-directory/conditional-access/concept-conditional-access-policy-common.md).

- Se si usa AD Free Edition, abilitare le **impostazioni predefinite di sicurezza** in Azure Active Directory come descritto nella [documentazione di ad](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults).


## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle raccomandazioni applicabili ad altri tipi di risorse di Azure, vedere gli articoli seguenti:

- [Protezione della rete nel Centro sicurezza di Azure](security-center-network-recommendations.md)