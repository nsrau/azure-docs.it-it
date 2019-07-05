---
title: Offerta di sottoscrizione di Azure di modifica
description: Informazioni su come modificare la sottoscrizione di Azure e passare a un'offerta diversa tramite il centro Account di Azure.
author: bandersmsft
manager: amberb
tags: billing,top-support-issue
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 8e87bcb9f7d05fd5041623693c96f8c737758c32
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490974"
---
# <a name="change-your-azure-subscription-to-a-different-offer"></a>Modificare la sottoscrizione di Azure a un'offerta diversa

I clienti con un [singola sottoscrizione con tariffe a consumo](https://azure.microsoft.com/offers/ms-azr-0003p/), è possibile passare alla sottoscrizione di Azure a un'altra offerta nel [centro Account](https://account.windowsazure.com/Subscriptions). È ad esempio possibile usare questa funzionalità per sfruttare il [Credito Azure mensile per sottoscrittori di Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

**Per eseguire l'aggiornamento dalla versione di valutazione gratuita**, Visualizzare [aggiornare l'abbonamento](billing-upgrade-azure-subscription.md).

## <a name="whats-supported"></a>Attività supportate:

È possibile passare da una singola sottoscrizione con tariffe a consumo per:

- [Sviluppo/test con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/)

> [!NOTE]
> Per altre modifiche di offerte, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
>
>

## <a name="switch-subscription-offer"></a>Passare a un'offerta di sottoscrizione diversa

> [!VIDEO https://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/Switch-to-a-different-Azure-offer/player]
>
>

1. Accedere al [Centro account di Azure](https://account.windowsazure.com/Subscriptions).
1. Selezionare la sottoscrizione singola con tariffe a consumo.
1. Fare clic su **Passa a un'altra offerta**. L'opzione è disponibile solo se si dispone di una singola sottoscrizione con tariffe a consumo e aver completato il primo periodo di fatturazione.

   ![Si noti il pulsante Cambia offerta sul lato destro della pagina](./media/billing-how-to-switch-azure-offer/switchbutton.png)
1. **Selezionare l'offerta preferita** dall'elenco di offerte disponibili per l'aggiornamento della sottoscrizione. L'elenco varia in base alle appartenenze associate all'account. Se non sono disponibili opzioni, controllare l'[elenco di offerte disponibili a cui è possibile passare](#whats-supported) e assicurarsi che siano disponibili le appartenenze corrette.

   ![Selezionare un'offerta a cui passare](./media/billing-how-to-switch-azure-offer/selectoffer.png)
1. A seconda dell'offerta a cui si passa, può essere visualizzata una nota relativa all'impatto del passaggio. Leggere attentamente l'elenco e seguire le istruzioni prima di procedere.

   ![Esaminare le note](./media/billing-how-to-switch-azure-offer/thingstonote.png)
1. È possibile rinominare la sottoscrizione. L'impostazione predefinita, è il nuovo nome dell'offerta. Fare clic su **Cambia offerta** per completare il processo.

   ![Fare clic sul pulsante verde](./media/billing-how-to-switch-azure-offer/confirmpage.png)
1. Completamento della procedura La sottoscrizione viene ora trasferita alla nuova offerta.

## <a name="frequently-asked-questions"></a>Domande frequenti
Le sezioni seguenti rispondono alle domande più frequenti.

### <a name="what-is-an-azure-offer"></a>Che cos'è un'offerta di Azure?

Per offerta di Azure si intende il *tipo* di sottoscrizione di Azure di cui si dispone. Ad esempio, [una sottoscrizione con tariffe a consumo](https://azure.microsoft.com/offers/ms-azr-0003p/), [Azure in Open](https://azure.microsoft.com/offers/ms-azr-0111p/), e [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) sono tutte offerte di Azure. Tutte le offerte presentano diversi [termini](https://azure.microsoft.com/support/legal/offer-details/) e alcune offrono vantaggi speciali. L'offerta di sottoscrizione è disponibile nella pagina della sottoscrizione del Centro account. Fare clic sul nome dell'offerta per visualizzare maggiori dettagli.

   ![Fare clic sul collegamento dell'offerta nel Centro account per ottenere maggiori dettagli](./media/billing-how-to-switch-azure-offer/offerlink.png)

### <a name="why-dont-i-see-the-button"></a>Perché il pulsante non è visualizzato?

Non è possibile visualizzare il **passare a un'altra offerta** se:

* Non hai un [sottoscrizione con tariffe a consumo](https://azure.microsoft.com/offers/ms-azr-0003p/). Attualmente solo le sottoscrizioni con tariffe a consumo possono essere convertite in un'altra offerta.
  * Se si dispone di un [versione di valutazione gratuita](https://azure.microsoft.com/free/), informazioni su come [aggiornamento in base al consumo](billing-upgrade-azure-subscription.md).
  * Per cambiare offerta da una sottoscrizione diversa, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
* Si è ancora nel periodo di fatturazione prima; è necessario attendere che il primo periodo di fatturazione terminare prima di poter cambiare le offerte.

### <a name="why-do-i-see-there-are-no-offers-available-in-your-region-or-country-at-this-time"></a>Perché viene visualizzato il messaggio "Non ci sono al momento offerte disponibili nella propria area geografica o nel proprio paese"?

* Potrebbe non essere consentito passare a un'altra offerta. Controllare l'[elenco delle offerte disponibili a cui è possibile passare](#whats-supported) e assicurarsi di aver attivato i vantaggi corretti con Visual Studio o BizSpark.
* Alcune offerte potrebbero non essere disponibili in tutti i paesi/aree geografiche.

### <a name="what-does-switching-azure-offers-do-to-my-service-and-billing"></a>Qual è l'effetto del passaggio a un'altra offerta di Azure sul servizio e sulla fatturazione?

Le informazioni dettagliate seguenti illustrano gli effetti del passaggio ad altre offerte di Azure nel Centro account.

#### <a name="no-service-downtime"></a>Non sono previsti tempi di inattività del servizio

Non è previsto alcun tempo di inattività dei servizi per gli utenti associati alla sottoscrizione. Tuttavia, è possibile che l'offerta a cui si passa preveda restrizioni. Alcune offerte, ad esempio, non consentono l'uso in produzione. Sarà quindi necessario spostare le risorse di produzione a un'altra sottoscrizione.

#### <a name="quota-increases-are-reset"></a>Gli aumenti di quota vengono reimpostati

Quando si cambia offerta, tutti gli [aumenti di limite o quota al di sopra del limite predefinito](../azure-supportability/resource-manager-core-quotas-request.md) vengono reimpostati. Non ci sono tempi di inattività del servizio, anche se si hanno risorse che superano il limite predefinito. Ad esempio, se si usassero 200 core nella sottoscrizione, con il cambio di offerta la quota di core potrebbe essere reimpostata sul valore predefinito di 20 core. Le VM che usano i 200 core non sono interessate e continuano a funzionare. Tuttavia, se non si effettua un'altra richiesta di aumento di quota, non sarà possibile eseguire il provisioning di altri core.

#### <a name="billing"></a>Fatturazione

Il giorno del passaggio viene generata una fattura per tutti gli addebiti in sospeso. Gli addebiti per la sottoscrizione vengono quindi applicati in base alle condizioni tariffarie della nuova offerta. La ricorrenza di fatturazione della sottoscrizione viene modificata alla data in cui è stato eseguito il passaggio di offerte. I dati relativi all'utilizzo e alla fatturazione generati prima del passaggio a un'altra offerta non vengono conservati, quindi è consigliabile scaricarne una copia prima del passaggio.

### <a name="can-i-migrate-from-a-subscription-with-pay-as-you-go-rates-to-cloud-solution-providerhttpspartnermicrosoftcomsolutionscloud-reseller-overview-csp-or-enterprise-agreementhttpsazuremicrosoftcompricingenterprise-agreement-ea"></a>È possibile eseguire la migrazione da una sottoscrizione con tariffe a consumo per [Cloud Solution Provider](https://partner.microsoft.com/Solutions/cloud-reseller-overview) (CSP) o [contratto Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/) (EA)?

* Per eseguire la migrazione all'offerta CSP, vedere l'argomento relativo alla [migrazione della sottoscrizione di Azure con pagamento in base al consumo all'offerta CSP](https://docs.microsoft.com/azure/cloud-solution-provider/migration/migration-from-payg-to-csp).
* Per migrare la sottoscrizione esistente a un contratto Enterprise, chiedere all'amministratore dell'iscrizione di aggiungere l'account al contratto Enterprise. Seguire le istruzioni incluse nell'invito ricevuto tramite posta elettronica per lo spostamento delle sottoscrizioni nell'ambito dell'iscrizione al contratto Enterprise. Per altre informazioni, vedere [Associate an Existing Account](https://ea.azure.com/helpdocs/associateExistingAccount) (Associare un account esistente) nel portale EA.

### <a name="can-i-migrate-data-and-services-to-a-new-subscription"></a>È possibile eseguire la migrazione di dati e servizi a una nuova sottoscrizione?

* Sì, è possibile eseguire la migrazione delle risorse direttamente alla nuova sottoscrizione. Vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../azure-resource-manager/resource-group-move-resources.md).
* Per trasferire la proprietà di una sottoscrizione di Azure e tutti gli elementi in essa contenuti a un altro utente, vedere [Transferring ownership of an Azure subscription](billing-subscription-transfer.md) (Trasferimento della proprietà di una sottoscrizione di Azure)

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi
- [Avviare l'analisi dei costi](../cost-management/quick-acm-cost-analysis.md)
