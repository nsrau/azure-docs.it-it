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
ms.openlocfilehash: 152168bbadd5815659bc5f70c91bd2a28f5e049d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481960"
---
# <a name="monitor-identity-and-access"></a>Monitorare identità e accesso

> [!TIP]
> A partire da marzo 2020, i consigli per l'identità e l'accesso del Centro sicurezza di Azure sono inclusi in tutte le sottoscrizioni nel piano tariffario gratuito. Se si dispone di sottoscrizioni nel livello gratuito, il relativo punteggio sicuro sarà influenzato dal fatto che non sono state valutate in precedenza per la sicurezza dell'identità e dell'accesso. 

Quando il Centro sicurezza identifica potenziali vulnerabilità della sicurezza, crea raccomandazioni utili per definire il processo di configurazione dei controlli necessari per rafforzare e tutelare le risorse.

Il perimetro di sicurezza si è evoluto da perimetro di rete a perimetro di identità. La sicurezza consiste sempre meno nel difendere la rete e sempre più nel difendere i dati e nel gestire la sicurezza di app e utenti. Attualmente, con il trasferimento nel cloud di una maggiore quantità di dati e app, l'identità diventa il nuovo perimetro.

Monitorando le attività di identità, è possibile intraprendere azioni proattive prima che si verifica un evento imprevisto o azioni reattive per arrestare un tentativo di attacco. Ad esempio, il Centro sicurezza potrebbe contrassegnare gli account deprecati (account non più necessari e bloccati dall'accesso da parte di Azure Active Directory) per la rimozione. 

Esempi di consigli che potrebbero essere visualizzati nella sezione Sicurezza delle risorse di **identità e accesso** del Centro sicurezza di Azure includono:Examples of recommendations you might see on the Identity and access resource security section of Azure Security Center include:

- L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di proprietario per la sottoscrizione
- Per la sottoscrizione devono essere designati al massimo 3 proprietari
- Gli account esterni con autorizzazioni di lettura devono essere rimossi dalla sottoscrizione
- Gli account deprecati devono essere rimossi dalla sottoscrizione

Per ulteriori informazioni su questi consigli e per un elenco completo dei consigli che è possibile visualizzare qui, vedere Suggerimenti per [l'identità e l'accesso](recommendations-reference.md#recs-identity).

> [!NOTE]
> Se la sottoscrizione include più di 600 account, il Centro sicurezza non è in grado di applicare i consigli relativi all'identità per la sottoscrizione. I consigli che non vengono eseguiti sono elencati di seguito in "Valutazioni non disponibili".
Il Centro sicurezza non è in grado di applicare i consigli relativi all'identità per gli agenti di amministrazione di un partner Cloud Solution Provider (CSP).
>


Tutti i consigli per l'identità e l'accesso sono disponibili all'interno di due controlli di sicurezza nella pagina **Raccomandazioni:**

- Gestire l'accesso e le autorizzazioni 
- Abilitare MFA

![I due controlli di sicurezza con le raccomandazioni relative all'identità e all'accesso](media/security-center-identity-access/two-security-controls-for-identity-and-access.png)


## <a name="enable-multi-factor-authentication-mfa"></a>Abilitare l'autenticazione a più fattori (MFA)Enable multi-factor authentication (MFA)

L'abilitazione dell'autenticazione a più fattori richiede le autorizzazioni del tenant di [Azure Active Directory (AD).](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 

- Se si dispone di un'edizione Premium di Active Directory, abilitare l'autenticazione a più fattori tramite [l'accesso condizionale.](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- Gli utenti dell'edizione gratuita di Active Directory possono abilitare **le impostazioni predefinite** di sicurezza in Azure Active Directory come descritto nella documentazione di Active [Directory,](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) ma verrà comunque visualizzato il suggerimento del Centro sicurezza per abilitare l'autenticazione a più fattori.


## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle raccomandazioni che si applicano ad altri tipi di risorse di Azure, vedere gli articoli seguenti:To learn more about recommendations that apply to other Azure resource types, see the following articles:

- [Protezione delle macchine virtuali nel Centro sicurezza di Azure](security-center-virtual-machine-protection.md)
- [Protezione della rete nel Centro sicurezza di Azure](security-center-network-recommendations.md)
- [Protezione del servizio SQL di Azure e dei dati nel Centro sicurezza di Azure](security-center-sql-service-recommendations.md)