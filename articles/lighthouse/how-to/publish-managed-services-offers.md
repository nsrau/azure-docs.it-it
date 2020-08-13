---
title: Pubblicare un'offerta di servizio gestito in Azure Marketplace
description: Informazioni su come pubblicare un'offerta di servizio gestito che carica i clienti nel Faro di Azure.
ms.date: 07/28/2020
ms.topic: how-to
ms.openlocfilehash: ab12238bac7e479766b3e328c02f73521d08ad96
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2020
ms.locfileid: "88167220"
---
# <a name="publish-a-managed-service-offer-to-azure-marketplace"></a>Pubblicare un'offerta di servizio gestito in Azure Marketplace

In questo articolo si apprenderà come pubblicare un'offerta di servizio gestito pubblica o privata in [Azure Marketplace](https://azuremarketplace.microsoft.com) usando il programma [Commercial Marketplace](../../marketplace/partner-center-portal/commercial-marketplace-overview.md) nel centro per i partner. I clienti che acquistano l'offerta delegheranno le sottoscrizioni o i gruppi di risorse, consentendo di gestirli tramite [Azure Lighthouse](../overview.md).

## <a name="publishing-requirements"></a>Requisiti per la pubblicazione

Per creare e pubblicare le offerte, è necessario disporre di un [account valido nel centro](../../marketplace/partner-center-portal/create-account.md) per i partner. Se non si dispone già di un account, il [processo di iscrizione](https://aka.ms/joinmarketplace) consentirà di creare un account nel centro per i partner e di iscriversi al programma Commercial Marketplace.

In base ai [requisiti di certificazione dell'offerta del servizio gestito](/legal/marketplace/certification-policies#7004-business-requirements), è necessario disporre di un livello di competenza della [piattaforma Cloud Silver o Gold](/partner-center/learn-about-competencies) oppure essere un [msp esperto di Azure](https://partner.microsoft.com/membership/azure-expert-msp) per pubblicare un'offerta di servizio gestito.

L'ID MPN (Microsoft Partner Network) verrà [associato automaticamente](../../cost-management-billing/manage/link-partner-id.md) alle offerte pubblicate per tenere traccia dell'impatto sugli engagement dei clienti.

> [!NOTE]
> Se non si vuole pubblicare un'offerta in Azure Marketplace, è possibile eseguire l'onboarding dei clienti manualmente usando i modelli di Azure Resource Manager. Per altre informazioni, vedere [onboarding an Customer to Azure Lighthouse](onboard-customer.md).

## <a name="create-your-offer"></a>Creare l'offerta

Per istruzioni dettagliate su come creare l'offerta, incluse tutte le informazioni e gli asset che è necessario fornire, vedere [creare un'offerta di servizio gestito](../../marketplace/partner-center-portal/create-new-managed-service-offer.md).

Per informazioni sul processo di pubblicazione generale, vedere la [Guida alla pubblicazione su Azure Marketplace e AppSource](../../marketplace/marketplace-publishers-guide.md). È anche necessario esaminare i [criteri di certificazione del Marketplace commerciale](/legal/marketplace/certification-policies), in particolare la sezione [Servizi gestiti](/legal/marketplace/certification-policies#700-managed-services).

Una volta che un cliente ha aggiunto l'offerta, sarà in grado di delegare una o più sottoscrizioni o gruppi di risorse, che verranno quindi caricati [in Azure Lighthouse](#the-customer-onboarding-process).

> [!IMPORTANT]
> Ogni piano in un'offerta di servizio gestito include una sezione dei **Dettagli del manifesto** , in cui si definiscono le entità Azure Active Directory (Azure ad) nel tenant che avranno accesso ai gruppi di risorse e/o alle sottoscrizioni delegate per i clienti che acquistano il piano. È importante tenere presente che qualsiasi gruppo, o utente o entità servizio, incluso avrà le stesse autorizzazioni per ogni cliente che acquista il piano. Per assegnare gruppi diversi per lavorare con ogni cliente, è possibile pubblicare un [piano privato](../../marketplace/private-offers.md) separato che è esclusivo per ogni cliente. Tenere presente che i piani privati non sono supportati con le sottoscrizioni stabilite tramite un rivenditore del programma Cloud Solution Provider (CSP).

## <a name="publish-your-offer"></a>Pubblicare l'offerta

Dopo aver completato tutte le sezioni, il passaggio successivo consiste nel pubblicare l'offerta in Azure Marketplace. Selezionare il pulsante **Pubblica** per attivare l'offerta. Altre informazioni su questo processo sono disponibili [qui](../../marketplace/partner-center-portal/create-new-managed-service-offer.md#publish). 

È possibile [pubblicare una versione aggiornata dell'offerta](../..//marketplace/partner-center-portal/update-existing-offer.md) in qualsiasi momento. Ad esempio, potrebbe essere necessario aggiungere una nuova definizione di ruolo a un'offerta pubblicata in precedenza. Quando si esegue questa operazione, i clienti che hanno già aggiunto l'offerta vedranno un'icona nella pagina [**Provider di servizi**](view-manage-service-providers.md) del portale di Azure che consente di sapere che è disponibile un aggiornamento. Ogni cliente sarà in grado di [esaminare le modifiche](view-manage-service-providers.md#update-service-provider-offers) e decidere se desiderano eseguire l'aggiornamento alla nuova versione. 

## <a name="the-customer-onboarding-process"></a>Il processo di onboarding del cliente

Dopo che un cliente ha aggiunto l'offerta, potrà [delegare una o più sottoscrizioni o gruppi di risorse specifici](view-manage-service-providers.md#delegate-resources), che verranno quindi caricati in Azure Lighthouse. Se un cliente ha accettato un'offerta, ma non ha ancora delegato alcuna risorsa, vedrà una nota nella parte superiore della sezione **Provider offers** (Offerte di provider) della pagina [**Provider di servizi**](view-manage-service-providers.md) del portale di Azure.

> [!IMPORTANT]
> La delega deve essere eseguita da un account non Guest nel tenant del cliente che ha il [ruolo predefinito proprietario](../../role-based-access-control/built-in-roles.md#owner) per la sottoscrizione da caricare (o che contiene i gruppi di risorse sottoposto a onboarding). Per visualizzare tutti gli utenti che possono delegare la sottoscrizione, un utente nel tenant del cliente può selezionare la sottoscrizione nel portale di Azure, aprire **Controllo di accesso (IAM)** e [visualizzare tutti gli utenti con il ruolo Proprietario](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription).

Quando il cliente delega una sottoscrizione (o uno o più gruppi di risorse all'interno di una sottoscrizione), il provider di risorse **Microsoft. ManagedServices** verrà registrato per tale sottoscrizione e gli utenti nel tenant potranno accedere alle risorse delegate in base alle autorizzazioni dell'offerta.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [Marketplace commerciale](../../marketplace/partner-center-portal/commercial-marketplace-overview.md).
- Informazioni sulle [esperienze di gestione tra tenant](../concepts/cross-tenant-management-experience.md).
- [Visualizzare e gestire i clienti](view-manage-customers.md) passando a **Clienti personali** nel portale di Azure.
