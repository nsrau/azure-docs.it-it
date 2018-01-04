---
title: Cambiare l'offerta di sottoscrizione di Azure | Documentazione Microsoft
description: Informazioni su come modificare la sottoscrizione di Azure e passare a un'offerta diversa tramite il Centro account di Azure
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing,top-support-issue
ms.assetid: aae227b3-6d64-4550-a5b6-d359f53f0a59
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 08/30/2017
ms.author: genli
ms.openlocfilehash: 381994079b7bcaeff08802b06573b977bf631e9d
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2017
---
# <a name="change-your-azure-pay-as-you-go-subscription-to-a-different-offer"></a>Modificare la sottoscrizione con pagamento in base al consumo in un'offerta diversa

I clienti [con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/) possono passare a un'offerta diversa per la sottoscrizione di Azure nel [Centro account](https://account.windowsazure.com/Subscriptions). È ad esempio possibile usare questa funzionalità per sfruttare il [Credito Azure mensile per sottoscrittori di Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). 

**Per eseguire l'aggiornamento dalla versione di valutazione gratuita**, vedere [Aggiornare la versione di valutazione gratuita o la sottoscrizione di Azure per Microsoft Imagine alla sottoscrizione con pagamento in base al consumo](billing-upgrade-azure-subscription.md).

## <a name="whats-supported"></a>Attività supportate:

| Da | A |
| --- | --- |
| Pagamento in base al consumo |[Sviluppo/test con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0023p/) |
| Pagamento in base al consumo |[Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/) |
| Pagamento in base al consumo |[Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) |
| Pagamento in base al consumo |[MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p/) |
| Pagamento in base al consumo |[Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) |
| Pagamento in base al consumo |[Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |

> [!NOTE]
> Per altre modifiche di offerte, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
>
>

## <a name="switch-subscription-offer"></a>Passare a un'offerta di sottoscrizione diversa

> [!VIDEO https://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/Switch-to-a-different-Azure-offer/player]
>
>

1. Accedere al [Centro account di Azure](https://account.windowsazure.com/Subscriptions).
1. Selezionare la sottoscrizione con pagamento in base al consumo.
1. Fare clic su **Passa a un'altra offerta**. Il pulsante è disponibile se si usa una sottoscrizione con pagamento in base al consumo ed è stato completato il primo periodo di fatturazione.

   ![Si noti il pulsante Cambia offerta sul lato destro della pagina](./media/billing-how-to-switch-azure-offer/switchbutton.png)
1. **Selezionare l'offerta preferita** dall'elenco di offerte disponibili per l'aggiornamento della sottoscrizione. L'elenco varia in base alle appartenenze associate all'account. Se non sono disponibili opzioni, controllare l'[elenco di offerte disponibili a cui è possibile passare](#whats-supported) e assicurarsi che siano disponibili le appartenenze corrette. 

   ![Selezionare un'offerta a cui passare](./media/billing-how-to-switch-azure-offer/selectoffer.png)
1. A seconda dell'offerta a cui si passa, può essere visualizzata una nota relativa all'impatto del passaggio. Leggere attentamente l'elenco e seguire le istruzioni prima di procedere.

   ![Esaminare le note](./media/billing-how-to-switch-azure-offer/thingstonote.png)
1. È possibile rinominare la sottoscrizione. Per impostazione predefinita, viene impostata sul nuovo nome dell'offerta. Fare clic su **Cambia offerta** per completare il processo.

   ![Fare clic sul pulsante verde](./media/billing-how-to-switch-azure-offer/confirmpage.png)
1. Completamento della procedura La sottoscrizione viene ora trasferita alla nuova offerta.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="what-is-an-azure-offer"></a>Che cos'è un'offerta di Azure?

Per offerta di Azure si intende il *tipo* di sottoscrizione di Azure di cui si dispone. Ad esempio, [pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/), [Azure in Open](https://azure.microsoft.com/offers/ms-azr-0111p/) e [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) sono tutte offerte di Azure. Tutte le offerte presentano diversi [termini](https://azure.microsoft.com/support/legal/offer-details/) e alcune offrono vantaggi speciali. L'offerta di sottoscrizione è disponibile nella pagina della sottoscrizione del Centro account. Fare clic sul nome dell'offerta per visualizzare maggiori dettagli.

   ![Fare clic sul collegamento dell'offerta nel Centro account per ottenere maggiori dettagli](./media/billing-how-to-switch-azure-offer/offerlink.png)

### <a name="why-dont-i-see-the-button"></a>Perché il pulsante non è visualizzato?

Il pulsante **Passa a un'altra offerta** potrebbe non essere visibile se:

* Non si usa una sottoscrizione [con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/). Attualmente solo le sottoscrizioni con pagamento in base al consumo possono essere convertite in un'altra offerta.
  * Se si usa la [versione di valutazione gratuita](https://azure.microsoft.com/free/), vedere le informazioni relative a come [eseguire l'aggiornamento alla versione con pagamento in base al consumo](billing-upgrade-azure-subscription.md).
  * Per cambiare offerta da una sottoscrizione diversa, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
* Il primo periodo di fatturazione non è ancora terminato ed è necessario attenderne il completamento prima di poter cambiare le offerte.

### <a name="why-do-i-see-there-are-no-offers-available-in-your-region-or-country-at-this-time"></a>Perché viene visualizzato il messaggio "Non ci sono al momento offerte disponibili nella propria area geografica o nel proprio paese"?

* Potrebbe non essere consentito passare a un'altra offerta. Controllare l'[elenco delle offerte disponibili a cui è possibile passare](#whats-supported) e assicurarsi di aver attivato i vantaggi corretti con Visual Studio o BizSpark.
* Alcune offerte potrebbero non essere disponibili in tutti i paesi.

### <a name="what-does-switching-azure-offers-do-to-my-service-and-billing"></a>Qual è l'effetto del passaggio a un'altra offerta di Azure sul servizio e sulla fatturazione?

Le informazioni dettagliate seguenti illustrano gli effetti del passaggio ad altre offerte di Azure nel Centro account.

#### <a name="no-service-downtime"></a>Non sono previsti tempi di inattività del servizio

Non è previsto alcun tempo di inattività dei servizi per gli utenti associati alla sottoscrizione. Tuttavia, è possibile che l'offerta a cui si passa preveda restrizioni. Alcune offerte, ad esempio, non consentono l'uso in produzione. Sarà quindi necessario spostare le risorse di produzione a un'altra sottoscrizione.

#### <a name="quota-increases-are-reset"></a>Gli aumenti di quota vengono reimpostati

Quando si cambia offerta, tutti gli [aumenti di limite o quota al di sopra del limite predefinito](../azure-supportability/resource-manager-core-quotas-request.md) vengono reimpostati. Non ci sono tempi di inattività del servizio, anche se si hanno risorse che superano il limite predefinito. Ad esempio, se si usassero 200 core nella sottoscrizione, con il cambio di offerta la quota di core potrebbe essere reimpostata sul valore predefinito di 20 core. Le VM che usano i 200 core non sono interessate e continuano a funzionare. Tuttavia, se non si effettua un'altra richiesta di aumento di quota, non sarà possibile eseguire il provisioning di altri core.

#### <a name="billing"></a>Fatturazione

Il giorno del passaggio viene generata una fattura per tutti gli addebiti in sospeso. Gli addebiti per la sottoscrizione vengono quindi applicati in base alle condizioni tariffarie della nuova offerta. La ricorrenza di fatturazione della sottoscrizione viene modificata alla data in cui è stato eseguito il passaggio di offerte. I dati relativi all'utilizzo e alla fatturazione generati prima del passaggio a un'altra offerta non vengono conservati, quindi è consigliabile scaricarne una copia prima del passaggio.

### <a name="can-i-migrate-from-pay-as-you-go-to-cloud-solution-providerhttpspartnermicrosoftcomsolutionscloud-reseller-overview-csp-or-enterprise-agreementhttpsazuremicrosoftcompricingenterprise-agreement-ea"></a>È possibile eseguire la migrazione dalla sottoscrizione con pagamento in base al consumo a [Cloud Solution Provider](https://partner.microsoft.com/Solutions/cloud-reseller-overview) (CSP) o al [Contratto Enterprise ](https://azure.microsoft.com/pricing/enterprise-agreement/)?

* Per eseguire la migrazione all'offerta CSP, vedere l'argomento relativo alla [migrazione della sottoscrizione di Azure con pagamento in base al consumo all'offerta CSP](https://docs.microsoft.com/azure/cloud-solution-provider/migration/migration-from-payg-to-csp).
* Per migrare la sottoscrizione esistente a un contratto Enterprise, chiedere all'amministratore dell'iscrizione di aggiungere l'account al contratto Enterprise. Seguire le istruzioni incluse nell'invito ricevuto tramite posta elettronica per lo spostamento delle sottoscrizioni nell'ambito dell'iscrizione al contratto Enterprise. Per altre informazioni, vedere [Associate an Existing Account](https://ea.azure.com/helpdocs/associateExistingAccount) (Associare un account esistente) nel portale EA.

### <a name="can-i-migrate-data-and-services-to-a-new-subscription"></a>È possibile eseguire la migrazione di dati e servizi a una nuova sottoscrizione?

* Sì, è possibile eseguire la migrazione delle risorse direttamente alla nuova sottoscrizione. Vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../azure-resource-manager/resource-group-move-resources.md).
* Per trasferire la proprietà di una sottoscrizione di Azure e tutti gli elementi in essa contenuti a un altro utente, vedere [Transferring ownership of an Azure subscription](billing-subscription-transfer.md) (Trasferimento della proprietà di una sottoscrizione di Azure)

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico.

Per altre domande, è possibile [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.
