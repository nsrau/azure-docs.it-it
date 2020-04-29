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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79481960"
---
# <a name="monitor-identity-and-access"></a>Monitorare identità e accesso

> [!TIP]
> Dal 2020 marzo, le raccomandazioni relative all'identità e all'accesso del Centro sicurezza di Azure sono incluse in tutte le sottoscrizioni del piano tariffario gratuito. Se si dispone di sottoscrizioni per il livello gratuito, il Punteggio sicuro sarà influenzato dal fatto che non sono stati valutati in precedenza per la sicurezza dell'accesso e delle identità. 

Quando il Centro sicurezza identifica potenziali vulnerabilità della sicurezza, crea raccomandazioni utili per definire il processo di configurazione dei controlli necessari per rafforzare e tutelare le risorse.

Il perimetro di sicurezza si è evoluto da perimetro di rete a perimetro di identità. La sicurezza consiste sempre meno nel difendere la rete e sempre più nel difendere i dati e nel gestire la sicurezza di app e utenti. Attualmente, con il trasferimento nel cloud di una maggiore quantità di dati e app, l'identità diventa il nuovo perimetro.

Monitorando le attività di identità, è possibile intraprendere azioni proattive prima che avvenga un evento imprevisto oppure azioni reattive per arrestare un tentativo di attacco. Ad esempio, il Centro sicurezza potrebbe contrassegnare gli account deprecati (gli account che non sono più necessari e che non sono più necessari per l'accesso Azure Active Directory) per la rimozione. 

Esempi di raccomandazioni che è possibile vedere nella sezione relativa alla sicurezza delle risorse di **identità e accesso** nel centro sicurezza di Azure includono:

- L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di proprietario per la sottoscrizione
- Per la sottoscrizione devono essere designati al massimo 3 proprietari
- Gli account esterni con autorizzazioni di lettura devono essere rimossi dalla sottoscrizione
- Gli account deprecati devono essere rimossi dalla sottoscrizione

Per ulteriori informazioni su questi consigli, oltre a un elenco completo delle raccomandazioni che potrebbero essere visualizzate in questo articolo, vedere le indicazioni relative a [identità e accesso](recommendations-reference.md#recs-identity).

> [!NOTE]
> Se la sottoscrizione include più di 600 account, il Centro sicurezza non è in grado di applicare i consigli relativi all'identità per la sottoscrizione. Le raccomandazioni che non vengono eseguite sono elencate di seguito in "valutazioni non disponibili".
Il Centro sicurezza non è in grado di applicare i consigli relativi all'identità per gli agenti di amministrazione di un partner Cloud Solution Provider (CSP).
>


Tutte le indicazioni relative a identità e accesso sono disponibili all'interno di due controlli di sicurezza nella pagina **raccomandazioni** :

- Gestire l'accesso e le autorizzazioni 
- Abilitare MFA

![I due controlli di sicurezza con le raccomandazioni relative a identità e accesso](media/security-center-identity-access/two-security-controls-for-identity-and-access.png)


## <a name="enable-multi-factor-authentication-mfa"></a>Abilita multi-factor authentication (autenticazione a più fattori)

Per abilitare l'autenticazione a più fattori, è necessario disporre [delle autorizzazioni tenant di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 

- Se si ha un'edizione Premium di AD, abilitare l'autenticazione a più fattori usando [l'accesso condizionale](https://docs.microsoft.com/azure/active-directory/conditional-access/overview).

- Gli utenti di AD Free Edition possono abilitare le **impostazioni predefinite di sicurezza** in Azure Active Directory come descritto nella [documentazione di Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) , ma la raccomandazione del Centro sicurezza per abilitare l'autenticazione a più fattori verrà comunque visualizzata.


## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sui suggerimenti applicabili ad altri tipi di risorse di Azure, vedere gli articoli seguenti:

- [Protezione delle macchine virtuali nel Centro sicurezza di Azure](security-center-virtual-machine-protection.md)
- [Protezione della rete nel Centro sicurezza di Azure](security-center-network-recommendations.md)
- [Protezione del servizio SQL di Azure e dei dati nel Centro sicurezza di Azure](security-center-sql-service-recommendations.md)