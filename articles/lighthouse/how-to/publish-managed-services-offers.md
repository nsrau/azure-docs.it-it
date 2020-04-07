---
title: Pubblicare un'offerta di servizio gestito in Azure MarketplacePublish a managed service offer to Azure Marketplace
description: Informazioni su come pubblicare un'offerta di servizio gestito che esegue l'onboarding dei clienti nella gestione risorse delegate di Azure.
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: 922e78a0715ce5c758ca7d068a38af43e214a524
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673945"
---
# <a name="publish-a-managed-service-offer-to-azure-marketplace"></a>Pubblicare un'offerta di servizio gestito in Azure MarketplacePublish a managed service offer to Azure Marketplace

In questo articolo verrà illustrato come pubblicare un'offerta di servizio gestito pubblica o privata in [Azure Marketplace](https://azuremarketplace.microsoft.com) usando il programma [Commercial Marketplace](../../marketplace/partner-center-portal/commercial-marketplace-overview.md) nel Centro per i [partner.](https://partner.microsoft.com/) I clienti che acquistano l'offerta sono quindi in grado di onboarding di sottoscrizioni e gruppi di risorse per la gestione delle risorse delegate di [Azure.](../concepts/azure-delegated-resource-management.md)

## <a name="publishing-requirements"></a>Requisiti per la pubblicazione

Devi avere un [account](../../marketplace/partner-center-portal/create-account.md) valido nel Centro per i partner per creare e pubblicare offerte. Se non si dispone già di un account, il processo di [iscrizione](https://aka.ms/joinmarketplace) guiderà l'utente attraverso i passaggi per creare un account nel Centro per i partner e iscriversi al programma Commercial Marketplace.

In base ai requisiti di [certificazione dell'offerta di servizi gestiti,](https://docs.microsoft.com/legal/marketplace/certification-policies#7004-business-requirements)è necessario disporre di un livello di [competenza Silver o Gold Cloud Platform](https://docs.microsoft.com/partner-center/learn-about-competencies) o essere un MSP di Azure [Expert](https://partner.microsoft.com/membership/azure-expert-msp) per pubblicare un'offerta del servizio gestito.

L'ID MPN (Microsoft Partner Network) verrà [associato automaticamente](../../billing/billing-partner-admin-link-started.md) alle offerte pubblicate per tenere traccia dell'impatto sugli engagement dei clienti.

> [!NOTE]
> Se non si vuole pubblicare un'offerta in Azure Marketplace, è possibile eseguire l'onboarding dei clienti manualmente usando i modelli di Azure Resource Manager. Per altre informazioni, vedere [Eseguire l'onboarding di un cliente nella gestione risorse delegate di Azure](onboard-customer.md).

## <a name="create-your-offer"></a>Creare l'offerta

Per istruzioni dettagliate su come creare l'offerta, incluse tutte le informazioni e le risorse che è necessario fornire, vedere Creare una nuova offerta di [Servizi gestiti](../../marketplace/partner-center-portal/create-new-managed-service-offer.md).

Per altre informazioni sul processo di pubblicazione generale, vedere Guida alla pubblicazione di [Azure Marketplace e AppSource](../../marketplace/marketplace-publishers-guide.md). È anche necessario esaminare i [criteri di certificazione del Marketplace commerciale](https://docs.microsoft.com/legal/marketplace/certification-policies), in particolare la sezione [Servizi gestiti](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services).

Una volta che un cliente aggiunge l'offerta, sarà in grado di delegare una o più sottoscrizioni o gruppi di risorse, che verranno quindi [inpensione per](#the-customer-onboarding-process)la gestione delle risorse delegate di Azure.

> [!IMPORTANT]
> Ogni piano in un'offerta di servizio gestito include una sezione **Dettagli manifesto,** in cui si definiscono le entità di Azure Active Directory (Azure AD) nel tenant che avranno accesso ai gruppi di risorse delegate e/o alle sottoscrizioni per i clienti che acquistano il piano. È importante tenere presente che qualsiasi gruppo (o utente o entità servizio) incluso disporrà delle stesse autorizzazioni per ogni cliente che acquista il piano. Per assegnare gruppi diversi per lavorare con ogni cliente, è necessario pubblicare un [piano privato](../../marketplace/private-offers.md) separato esclusivo per ogni cliente.

## <a name="publish-your-offer"></a>Pubblicare l'offerta

Dopo aver completato tutte le sezioni, il passaggio successivo consiste nel pubblicare l'offerta in Azure Marketplace. Selezionare il pulsante **Pubblica** per attivare l'offerta. Ulteriori informazioni su questo processo sono disponibili [qui](../../marketplace/partner-center-portal/create-new-managed-service-offer.md#publish).

È possibile [pubblicare una versione aggiornata dell'offerta](../../marketplace/partner-center-portal/update-existing-offer.md) in qualsiasi momento. Ad esempio, potrebbe essere necessario aggiungere una nuova definizione di ruolo a un'offerta pubblicata in precedenza. Quando si esegue questa operazione, i clienti che hanno già aggiunto l'offerta vedranno un'icona nella pagina [**Provider di servizi**](view-manage-service-providers.md) del portale di Azure che consente di sapere che è disponibile un aggiornamento. Ogni cliente sarà in grado di [rivedere le modifiche](view-manage-service-providers.md#update-service-provider-offers) e decidere se eseguire l'aggiornamento alla nuova versione. 

## <a name="the-customer-onboarding-process"></a>Il processo di onboarding del cliente

Dopo aver aggiunto l'offerta, un cliente potrà [delegare una o più sottoscrizioni o gruppi di risorse specifici](view-manage-service-providers.md#delegate-resources), di cui verrà quindi eseguito l'onboarding per la gestione risorse delegate di Azure. Se un cliente ha accettato un'offerta, ma non ha ancora delegato alcuna risorsa, vedrà una nota nella parte superiore della sezione **Provider offers** (Offerte di provider) della pagina [**Provider di servizi**](view-manage-service-providers.md) del portale di Azure.

> [!IMPORTANT]
> La delega deve essere eseguita da un account non guest nel tenant del cliente con il [ruolo predefinito Proprietario](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) per l'onboarding della sottoscrizione (o che contiene i gruppi di risorse che vengono onboardati). Per visualizzare tutti gli utenti che possono delegare la sottoscrizione, un utente nel tenant del cliente può selezionare la sottoscrizione nel portale di Azure, aprire **Controllo di accesso (IAM)** e [visualizzare tutti gli utenti con il ruolo Proprietario](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription).

Una volta che il cliente delega una sottoscrizione (o uno o più gruppi di risorse all'interno di una sottoscrizione), il provider di risorse **Microsoft.ManagedServices** verrà registrato per tale sottoscrizione e gli utenti nel tenant saranno in grado di accedere alle risorse delegate in base alle autorizzazioni nell'offerta.

## <a name="next-steps"></a>Passaggi successivi

- Scopri di più sul [mercato commerciale](../../marketplace/partner-center-portal/commercial-marketplace-overview.md).
- Informazioni sulle [esperienze di gestione tra tenant](../concepts/cross-tenant-management-experience.md).
- [Visualizzare e gestire i clienti](view-manage-customers.md) passando a **Clienti personali** nel portale di Azure.
