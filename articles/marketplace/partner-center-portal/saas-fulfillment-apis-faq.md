---
title: Domande frequenti sull'API di evasione SaaS-Marketplace commerciale Microsoft
description: Informazioni sui requisiti di integrazione per Microsoft Commercial Marketplace per consentire ai clienti di Azure di sottoscrivere le offerte SaaS.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 97359083698e92f39ca854972c864ef769bda730
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86109247"
---
# <a name="common-questions-about-saas-fulfillment-apis"></a>Domande frequenti sulle API di evasione SaaS

Questo articolo descrive molti dei requisiti di integrazione per Microsoft Commercial Marketplace per consentire ai clienti di Azure di sottoscrivere le offerte SaaS.

## <a name="discovery-experience"></a>Esperienza di individuazione

Dopo la pubblicazione di un'offerta SaaS, gli utenti di Azure possono individuarla in Azure Marketplace. I clienti possono filtrare le offerte in base al tipo di prodotto (SaaS) e individuare i servizi SaaS a cui si è interessati.

## <a name="purchase-experience"></a>Esperienza di acquisto

Quando un utente è interessato a un servizio SaaS specifico, l'utente può sottoscriverlo da Azure Marketplace.

### <a name="what-does-it-mean-for-an-azure-user-to-subscribe-to-a-saas-offer-in-azure-marketplace"></a>Che cosa significa per un utente di Azure sottoscrivere un'offerta SaaS in Azure Marketplace?

Questo significa che un utente può visualizzare le condizioni per l'utilizzo e l'informativa sulla privacy associate al servizio SaaS e accettare di pagare in base ai termini di fatturazione impostati dall'utente, l'editore dell'offerta SaaS, sulla fattura di Microsoft. Gli utenti possono usare il profilo di pagamento esistente in Azure per pagare l'utilizzo del servizio SaaS.

Questa possibilità è utile in diversi modi. I clienti possono ora individuare e sottoscrivere in un'unica posizione usando Microsoft Cloud piattaforma come fonte attendibile, senza dover controllare ogni software ISV che intende usare. Inoltre, i clienti possono utilizzare il proprio profilo di pagamento esistente senza dover pagare in modo esplicito ogni software ISV in modo indipendente.

### <a name="is-the-user-charged-automatically-when-the-offer-is-subscribed"></a>L'utente viene addebitato automaticamente quando l'offerta è stata sottoscritta?

Quando si sottoscrive l'offerta SaaS, l'utente ha accettato di pagare il consumo del servizio SaaS attraverso la piattaforma Microsoft. Tuttavia, gli addebiti vengono avviati solo quando viene usata l'offerta. L'utente deve passare all'offerta SaaS e confermare la creazione dell'account per iniziare a usare l'offerta. Si invierà quindi una notifica a Microsoft per avviare la fatturazione per questa sottoscrizione SaaS del cliente.

### <a name="how-are-you-notified-when-a-user-subscribes-to-your-saas-offer"></a>Come si riceve una notifica quando un utente sottoscrive l'offerta SaaS?

Dopo la sottoscrizione a un'offerta, l'utente di Azure può individuare e gestire tutte le offerte in Azure. Per impostazione predefinita, lo stato di una nuova offerta SaaS sottoscritta viene visualizzato come **provisioning, evasione in sospeso**. In questo stato, all'utente di Azure verrà richiesta un'azione per **configurare l'account**, per passare all'esperienza di gestione delle sottoscrizioni SaaS nell'portale di Azure.

Quando l'utente seleziona **Configura account**, viene reindirizzato al sito Web del servizio SaaS. Il server di pubblicazione ha configurato l'URL al momento della pubblicazione dell'offerta. Questa pagina è indicata come pagina di destinazione dell'editore. Gli utenti di Azure possono accedere alla pagina di destinazione SaaS in base alle credenziali di AAD esistenti in Azure.

Quando l'utente di Azure viene reindirizzato alla pagina di destinazione, viene aggiunto un token all'URL della query. Questo token è di breve durata e è valido per un periodo di tempo di 24 ore. È quindi possibile rilevare la presenza di questo token e chiamare l'API Microsoft per ottenere un contesto associato al token.

![Flusso della sottoscrizione del cliente](media/saas-metering-service-integration-flow-a.png)

Per altre informazioni sul contratto API per la gestione di scenari Transact nel ciclo di vita di un'offerta SaaS, vedere [API di evasione Saas](pc-saas-fulfillment-api-v2.md).

### <a name="how-do-you-know-the-saas-offer-to-which-the-user-subscribes-in-azure"></a>Come si conosce l'offerta SaaS a cui l'utente sottoscrive Azure?

La risposta all' `Resolve` API include le informazioni sull'offerta e sul piano associate alla sottoscrizione Saas.

### <a name="how-can-the-azure-user-change-the-plan-associated-with-this-azure-subscription"></a>In che modo l'utente di Azure può modificare il piano associato a questa sottoscrizione di Azure?

* L'utente di Azure può modificare il piano associato alla sottoscrizione SaaS direttamente nell'esperienza SaaS o tramite la piattaforma Microsoft.

* Le conversioni possono essere eseguite in qualsiasi momento nel ciclo di fatturazione. Viene richiesto di confermare qualsiasi conversione, che diventerà effettiva una volta confermata.

* Le tariffe del piano prepagato (**mensile** o **annuale**) sono con classificazione Pro. Eventuali eccedenze emesse fino al momento della conversione verranno addebitate nella fattura successiva. Le nuove eccedenze verranno emesse in base al nuovo piano.

>[!Note]
>Se non si desidera supportare percorsi di conversione specifici, è possibile bloccare i downgrade.

La sequenza seguente acquisisce il flusso quando un piano di modifiche dei clienti di Azure nell'esperienza SaaS:

![Flusso di modifica piano cliente](media/saas-metering-service-integration-flow-b.png)

La sequenza seguente acquisisce il flusso quando un piano di modifiche dei clienti di Azure nella vetrina di Microsoft

![Flusso di modifica piano storefront cliente](media/saas-metering-service-integration-flow-c.png)

### <a name="how-can-the-azure-user-unsubscribe-from-the-plan-associated-with-azure-subscription"></a>In che modo l'utente di Azure Annulla la sottoscrizione al piano associato alla sottoscrizione di Azure?

Un utente di Azure può annullare la sottoscrizione a un'offerta SaaS acquistata direttamente nell'esperienza SaaS o tramite la piattaforma Microsoft. Una volta annullata la sottoscrizione, l'utente non verrà più addebitato al successivo ciclo di fatturazione.

La sequenza seguente acquisisce il flusso quando un cliente di Azure Annulla la sottoscrizione all'offerta SaaS nell'esperienza SaaS:

![Il cliente annulla la sottoscrizione nell'esperienza SaaS](media/saas-metering-service-integration-flow-d.png)

La sequenza seguente acquisisce il flusso quando l'utente di Azure Annulla la sottoscrizione nella vetrina di Microsoft:

![Il cliente annulla la sottoscrizione nella vetrina di Microsoft](media/saas-metering-service-integration-flow-e.png)

## <a name="next-steps"></a>Passaggi successivi

[API del servizio di misurazione di Marketplace](./marketplace-metering-service-apis.md)
