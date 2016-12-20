---
title: Trasferire la sottoscrizione di Azure a un&quot;altra offerta | Documentazione Microsoft
description: Informazioni su come modificare la sottoscrizione di Azure e passare a un&quot;offerta diversa tramite il portale di gestione della sottoscrizione
services: 
documentationcenter: 
author: genlin
manager: mbaldwin
editor: 
tags: billing,top-support-issue
ms.assetid: aae227b3-6d64-4550-a5b6-d359f53f0a59
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2016
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: c7fdfb6e915269d171f1a9fa1a397faefd8b8a99


---
# <a name="switch-your-azure-subscription-to-another-offer"></a>Trasferire la sottoscrizione di Azure a un'altra offerta
I clienti [con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/) possono passare a un'offerta diversa per la sottoscrizione di Azure nel [Centro account](https://account.windowsazure.com/Subscriptions). È ad esempio possibile usare questa funzionalità per sfruttare il [Credito Azure mensile per sottoscrittori di Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Se si usa la [versione di valutazione gratuita](https://azure.microsoft.com/free/), vedere le informazioni relative a come [eseguire l'aggiornamento alla versione con pagamento in base al consumo](billing-upgrade-azure-subscription.md).

#### <a name="whats-supported"></a>Attività supportate:
| Da | To |
| --- | --- |
| [Pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/) |[Sviluppo/test con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0023p/) |
| [Pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/) |[Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/) |
| [Pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/) |[Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) |
| [Pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/) |[MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p/) |
| [Pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/) |[Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) |
| [Pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/) |[Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |

> [!NOTE]
> Per altre modifiche di offerte, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
> 
> 

## <a name="switch-subscription-offer"></a>Passare a un'offerta di sottoscrizione diversa
> [!VIDEO https://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/Switch-to-a-different-Azure-offer/player]
> 
> 

1. Accedere al [Centro account di Azure](https://account.windowsazure.com/Subscriptions).
2. Selezionare la sottoscrizione con pagamento in base al consumo.
3. Fare clic su **Passa a un'altra offerta**. Il pulsante è disponibile se si usa una sottoscrizione con pagamento in base al consumo ed è stato completato il primo periodo di fatturazione.
   
   ![Si noti il pulsante Cambia offerta sul lato destro della pagina](./media/billing-how-to-switch-azure-offer/switchbutton.png)
4. **Selezionare l'offerta preferita** dall'elenco di offerte disponibili per l'aggiornamento della sottoscrizione. L'elenco varia in base alle appartenenze associate all'account. Se non sono disponibili opzioni, controllare l'[elenco di offerte disponibili a cui è possibile passare](#whats-supported) e assicurarsi che siano disponibili le appartenenze corrette. 
   
   ![Selezionare un'offerta a cui passare](./media/billing-how-to-switch-azure-offer/selectoffer.png)
5. A seconda dell'offerta a cui si passa, può essere visualizzata una nota relativa all'impatto del passaggio. Leggere attentamente l'elenco e seguire le istruzioni prima di procedere.
   
   ![Esaminare le note](./media/billing-how-to-switch-azure-offer/thingstonote.png)
6. È possibile rinominare la sottoscrizione. Per impostazione predefinita, viene impostata sul nuovo nome dell'offerta. Fare clic su **Cambia offerta** per completare il processo.
   
   ![Fare clic sul pulsante verde](./media/billing-how-to-switch-azure-offer/confirmpage.png)
7. Completamento della procedura La sottoscrizione viene ora trasferita alla nuova offerta.

## <a name="why-cant-i-switch-offers"></a>Perché non è possibile passare a un'altra offerta?
Il pulsante **Passa a un'altra offerta** potrebbe non essere visibile se:

* Non si usa una sottoscrizione [con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/). È attualmente possibile aggiornare a un'altra offerta solo le sottoscrizioni con pagamento in base al consumo.
  
  * Se si usa la [versione di valutazione gratuita](https://azure.microsoft.com/free/), vedere le informazioni relative a come [eseguire l'aggiornamento alla versione con pagamento in base al consumo](billing-upgrade-azure-subscription.md).
  * Per cambiare offerta da una sottoscrizione diversa, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
* Il primo periodo di fatturazione non è ancora terminato ed è necessario attenderne il completamento prima di poter cambiare le offerte.

È possibile che venga visualizzato il messaggio **Non ci sono al momento offerte disponibili nella propria area geografica o nel proprio paese** se:

* Non è consentito cambiare offerta. Vedere l'elenco delle [offerte disponibili a cui è possibile passare](#whats-supported).

## <a name="what-does-switching-azure-offers-do-to-my-service-and-billing"></a>Qual è l'effetto del passaggio a un'altra offerta di Azure sul servizio e sulla fatturazione?
Le informazioni dettagliate seguenti illustrano gli effetti del passaggio a un altro piano di Azure nel Centro account.

### <a name="access-to-services"></a>Accesso ai servizi
Non è previsto alcun tempo di inattività dei servizi per gli utenti associati alla sottoscrizione. Tuttavia, è possibile che l'offerta a cui si passa preveda restrizioni. Alcune offerte, ad esempio, non consentono l'uso in produzione. Sarà quindi necessario spostare le risorse di produzione a un'altra sottoscrizione.

### <a name="billing"></a>Fatturazione
Il giorno del passaggio viene generata una fattura per tutti gli addebiti in sospeso. Gli addebiti per la sottoscrizione vengono quindi applicati in base alle condizioni tariffarie della nuova offerta. La ricorrenza di fatturazione della sottoscrizione viene modificata alla data in cui è stato eseguito il passaggio di offerte. I dati relativi all'utilizzo e alla fatturazione generati prima del passaggio a un'altra offerta non vengono conservati, quindi è consigliabile scaricarne una copia prima del passaggio.

> [!NOTE]
> A causa delle limitazioni relative alla fatturazione, il passaggio a un'altra offerta non è possibile durante il primo ciclo di fatturazione dopo la creazione di una sottoscrizione.
> 
> 

## <a name="can-i-migrate-from-pay-as-you-go-to-cloud-solution-providerhttpspartnermicrosoftcomsolutionscloud-reseller-overview-csp-or-enterprise-agreementhttpsazuremicrosoftcompricingenterprise-agreement-ea"></a>È possibile eseguire la migrazione dalla sottoscrizione con pagamento in base al consumo a [Cloud Solution Provider](https://partner.microsoft.com/Solutions/cloud-reseller-overview) (CSP) o al [Contratto Enterprise ](https://azure.microsoft.com/pricing/enterprise-agreement/)?
Non è attualmente supportato il passaggio a un'offerta CSP o di tipo Contratto Enterprise nel Centro account. Per migrare la sottoscrizione esistente a un Contratto Enterprise, chiedere all'amministratore dell'iscrizione di aggiungere l'account al Contratto Enterprise. Si riceverà quindi un invito tramite posta elettronica. Quando si seguono le istruzioni per accettare l'invito, le sottoscrizioni vengono spostate automaticamente al Contratto Enterprise. Per eseguire la migrazione all'offerta CSP, vedere l'argomento relativo alla [migrazione della sottoscrizione di Azure all'offerta CSP](https://blogs.technet.microsoft.com/hybridcloudbp/2016/08/26/azure-subscription-migration-to-csp/).

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [gestire i ruoli di amministratore](billing-add-change-azure-subscription-administrator.md) per la sottoscrizione
* Tenere traccia dell'uso [scaricando i dati sull'uso e la fattura](billing-download-azure-invoice-daily-usage-date.md)

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico.
Per altre domande, è possibile [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.




<!--HONumber=Nov16_HO3-->


