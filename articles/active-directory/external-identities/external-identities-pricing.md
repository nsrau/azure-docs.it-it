---
title: Modello di fatturazione MAU per Azure AD identità esterne
description: Informazioni sul modello di fatturazione di Azure AD identità esterne per utenti attivi mensili per la collaborazione tra utenti Guest (B2B) in Azure AD. Informazioni su come collegare il tenant di Azure AD a una sottoscrizione di Azure.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.workload: identity
ms.collection: M365-identity-device-management
ms.openlocfilehash: d281b0ecd38ebbb76e093fd4f85213da4fc7d713
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91265791"
---
# <a name="billing-model-for-azure-ad-external-identities"></a>Modello di fatturazione per Azure AD identità esterne

Il prezzo delle identità esterne Azure Active Directory (Azure AD) si basa su utenti attivi mensili (MAU), ovvero sul numero di utenti univoci con attività di autenticazione in un mese di calendario. Questo modello di fatturazione si applica sia ai tenant di collaborazione utente Guest (B2B) sia a [Azure ad B2C tenant](https://docs.microsoft.com/azure/active-directory-b2c/billing)di Azure ad. MAU Billing ti aiuta a ridurre i costi offrendo un livello gratuito e prezzi flessibili e prevedibili. In questo articolo vengono fornite informazioni sulla fatturazione di MAU e sul collegamento dei tenant di Azure AD a una sottoscrizione.

> [!IMPORTANT]
> Questo articolo non contiene i dettagli relativi ai prezzi. Per informazioni aggiornate sulla fatturazione e sui prezzi di utilizzo, vedere [Azure Active Directory prezzi](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="what-do-i-need-to-do"></a>Come devo procedere?

Per sfruttare i vantaggi della fatturazione di MAU, il tenant di Azure AD deve essere collegato a una sottoscrizione di Azure.

|Se il tenant è:  |È necessario:  |
|---------|---------|
| Un tenant di Azure AD già collegato a una sottoscrizione     | Non eseguire alcuna operazione. Quando si usano le funzionalità di identità esterne per collaborare con gli utenti guest, la fatturazione verrà eseguita automaticamente usando il modello MAU.        |
| Un tenant Azure AD non ancora collegato a una sottoscrizione     | [Collegare il tenant di Azure ad a una sottoscrizione](#link-your-azure-ad-tenant-to-a-subscription) per attivare la fatturazione di Mau.        |
|  |  |

## <a name="about-monthly-active-users-mau-billing"></a>Informazioni sulla fatturazione mensile di utenti attivi (MAU)

Nel tenant di Azure AD l'utilizzo della collaborazione utente guest viene fatturato in base al numero di utenti Guest univoci con attività di autenticazione in un mese di calendario. Questo modello sostituisce il modello di fatturazione con rapporto 1:5, che consentiva fino a cinque utenti guest per ogni licenza di Azure AD Premium nel tenant. Se il tenant è collegato a una sottoscrizione e si usano le funzionalità di identità esterne per collaborare con utenti guest, la fatturazione verrà eseguita automaticamente usando il modello di fatturazione basato sugli utenti attivi mensili.
  
Il piano tariffario applicabile agli utenti Guest si basa sul piano tariffario più alto assegnato al tenant Azure AD. Ad esempio, se il piano tariffario più alto nel tenant è Azure AD Premium P1, il piano tariffario Premium P1 si applica anche agli utenti guest. Se i prezzi più elevati sono Azure AD Free, verrà richiesto di eseguire l'aggiornamento a un piano tariffario Premium quando si tenta di usare le funzionalità Premium per gli utenti guest.

## <a name="link-your-azure-ad-tenant-to-a-subscription"></a>Collegare il tenant di Azure AD a una sottoscrizione

Un tenant di Azure AD deve essere collegato a una sottoscrizione di Azure per la fatturazione e l'accesso alle funzionalità appropriate. Se la directory non dispone già di una sottoscrizione a cui è possibile collegarsi, sarà possibile aggiungerne una durante questo processo.

1. Accedere al [portale di Azure](https://portal.azure.com/) con un account Azure a cui è stato assegnato almeno il ruolo [collaboratore](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) all'interno della sottoscrizione o un gruppo di risorse all'interno della sottoscrizione.

2. Selezionare la directory che si desidera collegare: nella barra degli strumenti portale di Azure selezionare l'icona **directory + sottoscrizione** e quindi selezionare la directory.

    ![Selezionare l'icona directory + sottoscrizione](media/external-identities-pricing/portal-mau-pick-directory.png)

3. In **servizi di Azure**selezionare **Azure Active Directory**.

4. Nel menu a sinistra selezionare **Identità esterne**.

5. In **sottoscrizioni**selezionare **sottoscrizioni collegate**.

6. Nell'elenco tenant selezionare la casella di controllo accanto al tenant, quindi selezionare **collega sottoscrizione**.

    ![Selezionare il tenant e collegare una sottoscrizione](media/external-identities-pricing/linked-subscriptions.png)

7. Nel riquadro collegamento a una sottoscrizione selezionare una **sottoscrizione** e un **gruppo di risorse**. Selezionare quindi **Applica**.

   > [!NOTE]
   > Se non sono elencate sottoscrizioni, è possibile [associare una sottoscrizione al tenant](../fundamentals/active-directory-how-subscriptions-associated-directory.md). In alternativa, è possibile aggiungere una nuova sottoscrizione selezionando il collegamento **se non si ha già una sottoscrizione di cui è possibile crearne una qui**.

    ![Selezionare una sottoscrizione e un gruppo di risorse](media/external-identities-pricing/link-subscription-resource.png)

Dopo aver completato questi passaggi, la sottoscrizione di Azure viene fatturata in base ai dettagli di Azure Direct o Enterprise Agreement, se applicabile.

## <a name="next-steps"></a>Passaggi successivi

Per le informazioni più aggiornate sui prezzi, vedere [Azure Active Directory prezzi](https://azure.microsoft.com/pricing/details/active-directory/).
