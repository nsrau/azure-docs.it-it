---
title: API di adempimento SaaS - Domande frequenti Azure Marketplace
description: Individuazione e acquisto di esperienze da parte dei clienti di un'offerta SaaS in Azure Marketplace.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 6d3a84341d5221950da20f39456461dafc5d2e75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275697"
---
# <a name="saas-fulfillment-apis---faq"></a>API di evasione ordini SaaS - domande frequenti

Sono elencati i requisiti di integrazione con Azure Marketplace per consentire ai clienti di Azure di sottoscrivere l'offerta SaaS.The integration requirements with Azure Marketplace to enable Azure customers to subscribe to the SaaS offer are listed.

## <a name="discovery-experience"></a>Esperienza di scoperta

Dopo la pubblicazione dell'offerta, gli utenti di Azure possono individuare l'offerta SaaS in Azure Marketplace.Once the offer is published, Azure users can discover the SaaS offer in Azure Marketplace. I tuoi clienti saranno in grado di filtrare le offerte in base al tipo di prodotto (SaaS) e scoprire i servizi SaaS a cui sono interessati.

## <a name="purchase-experience"></a>Esperienza di acquisto

Quando un utente è interessato a un servizio SaaS specifico, l'utente può sottoscriverlo da Azure Marketplace.Once a user is interested in a specific SaaS service, the user can subscribe it to it from Azure Marketplace.

### <a name="what-does-it-mean-for-an-azure-user-to-subscribe-to-a-saas-offer-in-azure-marketplace"></a>Che cosa significa per un utente di Azure sottoscrivere un'offerta SaaS in Azure Marketplace?

Ciò significa che un utente può visualizzare le condizioni per l'utilizzo e l'informativa sulla privacy associate al servizio SaaS e accettare di pagarle in base alle condizioni di fatturazione stabilite dall'utente, l'editore dell'offerta SaaS, sulla fattura di Microsoft. Gli utenti possono usare il profilo di pagamento esistente in Azure per pagare il consumo del servizio SaaS.Users can use their existing payment profile in Azure to pay for the SaaS service consumption.

Questo è utile per un sacco di motivi. I clienti possono ora individuare e sottoscrivere in un'unica posizione utilizzando Microsoft Cloud Platform come fonte attendibile, senza dover controllare ogni software ISV che intende utilizzare. Inoltre, i clienti possono utilizzare il loro profilo di pagamento esistente senza dover pagare esplicitamente ogni software ISV in modo indipendente.

### <a name="is-the-user-charged-automatically-when-the-offer-is-subscribed"></a>L'utente viene addebitato automaticamente quando l'offerta viene iscritta?

Durante l'iscrizione all'offerta SaaS, l'utente ha accettato di pagare per il consumo del servizio SaaS tramite la piattaforma Microsoft. Tuttavia, gli addebiti inizieranno solo quando l'offerta viene consumata. L'utente deve accedere all'offerta SaaS e confermare la creazione dell'account per iniziare a utilizzare l'offerta. Verrà quindi notificato a Microsoft di avviare la fatturazione per la sottoscrizione SaaS del cliente.

### <a name="how-are-you-notified-when-a-user-subscribes-to-your-saas-offer"></a>Come si riceve una notifica quando un utente si iscrive alla propria offerta SaaS?

Dopo aver sottoscritto un'offerta, l'utente di Azure può individuare e gestire tutte le offerte in Azure.After sottoscribing to an offer, the Azure user can discover and manage all their offers in Azure. Per impostazione predefinita, lo stato di un'offerta SaaS appena sottoscritta è **'Provisioning, adempimento in sospeso'.** In questo stato, all'utente di Azure verrà richiesta un'azione per **'Configura account',** per passare all'esperienza di gestione delle sottoscrizioni SaaS nel portale di Azure.In this state, the Azure user will be prompted with an action to 'Configure Account' , in order to browse to their SaaS subscription management experience in the Azure portal.

Quando l'utente fa clic su **'Configura account'**, verrà reindirizzato al sito Web del servizio SaaS. L'URL a cui si accede viene forniti dall'autore al momento della pubblicazione dell'offerta. Questa pagina viene definita pagina di destinazione dell'editore. Azure users should be able to login to the SaaS landing page based on their existing AAD credentials in Azure.

Quando l'utente di Azure viene reindirizzato alla pagina di destinazione, viene aggiunto un token all'URL della query. Questo token è di breve durata e valido per un periodo di tempo di 24 ore. È quindi possibile rilevare la presenza di questo token e chiamare l'API di Microsoft per ottenere più contesto associato al token.

![Flusso di sottoscrizione del cliente](media/saas-metering-service-integration-flow-a.png)

Vedere il documento API di [evasione SaaS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) per ulteriori informazioni sul contratto API per la gestione degli scenari di transazione nel ciclo di vita di un'offerta SaaS.

### <a name="how-do-you-know-the-saas-offer-to-which-the-user-subscribes-in-azure"></a>Come si conosce l'offerta SaaS a cui l'utente sottoscrive in Azure?

La risposta `Resolve` all'API include le informazioni sull'offerta e sul piano associate alla sottoscrizione SaaS.

### <a name="how-can-the-azure-user-change-the-plan-associated-with-this-azure-subscription"></a>In che modo l'utente di Azure può modificare il piano associato a questa sottoscrizione di Azure?

* L'utente di Azure può modificare il piano associato alla sottoscrizione SaaS direttamente nell'esperienza SaaS o tramite la piattaforma Microsoft.The Azure user can change the plan associated with the SaaS subscription directly in the SaaS experience, or through the Microsoft platform.

* Le conversioni possono essere eseguite in qualsiasi momento nel ciclo di fatturazione. Devi riconoscere qualsiasi conversione, che diventerà effettiva una volta riconosciuta.

* Le tariffe del piano prepagato **(mensile** o **annuale)** sono ripartite proporzionalmente. Eventuali esaputi fino al momento della conversione verranno addebitati nella fattura successiva. Verranno emesse nuove eccedenze in base al nuovo piano.

>[!Note]
>È possibile bloccare i downgrade se non si desidera supportare percorsi di conversione specifici.

La sequenza seguente acquisisce il flusso quando un cliente di Azure cambia piano nell'esperienza SaaS:The sequence below captures the flow when a Azure customer changes plan in the SaaS experience:

![Flusso di modifiche del piano cliente](media/saas-metering-service-integration-flow-b.png)

La sequenza seguente acquisisce il flusso quando un cliente di Azure cambia piano nello storefront di MicrosoftThe sequence below captures the flow when an Azure customer changes plan in Microsoft storefront

![Flusso di modifiche del piano dello storefront del cliente](media/saas-metering-service-integration-flow-c.png)

### <a name="how-can-the-azure-user-unsubscribe-from-the-plan-associated-with-azure-subscription"></a>In che modo l'utente di Azure può annullare la sottoscrizione al piano associato alla sottoscrizione di Azure?

Un utente di Azure può annullare la sottoscrizione a un'offerta SaaS acquistata direttamente nell'esperienza SaaS o tramite la piattaforma Microsoft.An Azure user can unsubscribe from a purchased SaaS offer either directly in the SaaS experience, or through the Microsoft platform. Una volta che l'utente annulla l'iscrizione, non verrà più addebitato dal ciclo di fatturazione successivo.

La sequenza seguente acquisisce il flusso quando un cliente di Azure annulla la sottoscrizione all'offerta SaaS nell'esperienza SaaS:The sequence below captures the flow when an Azure customer unsubscribes to the SaaS offer in the SaaS experience:

![Il cliente annulla l'iscrizione all'esperienza SaaS](media/saas-metering-service-integration-flow-d.png)

La sequenza seguente acquisisce il flusso quando l'utente di Azure annulla la sottoscrizione nello storefront di Microsoft:The sequence below captures the flow when Azure user unsubscribes in Microsoft storefront:

![L'iscrizione del cliente nello storefront di Microsoft](media/saas-metering-service-integration-flow-e.png)

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni, vedere API del [servizio di controllo Marketplace.See Marketplace metering service APIs](./marketplace-metering-service-apis.md) for more information.
