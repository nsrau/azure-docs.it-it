---
title: Prezzi e fatturazione - App per la logica di Azure | Microsoft Docs
description: Informazioni sul funzionamento dei prezzi e della fatturazione per App per la logica di Azure.
author: kevinlam1
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: f8f528f5-51c5-4006-b571-54ef74532f32
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: LADocs; klam
ms.translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 10a2c7771d9d2ec57f3e57e1d6bb4f82c55aa7f1
ms.contentlocale: it-it
ms.lasthandoff: 03/17/2017

---
# <a name="logic-apps-pricing-model"></a>Modello di determinazione prezzi delle app per la logica
Il servizio App per la logica di Azure supporta la scalabilità e l'esecuzione di un flusso di lavoro di integrazione nel cloud.  Di seguito sono riportati i dettagli relativi ai piani tariffari e di fatturazione di App per la logica.
## <a name="consumption-pricing"></a>Prezzi a consumo
Le nuove app per la logica create usano un piano a consumo. Con il modello di determinazione prezzi a consumo delle app per la logica si paga solo l'uso effettivo.  In caso di piano a consumo, le app per la logica non sono soggette a limitazione.
Viene misurato il consumo per tutte le azioni eseguite durante un'esecuzione di un'istanza dell'app per la logica.
### <a name="what-are-action-executions"></a>Informazioni sulle esecuzioni di azioni
Ogni passaggio della definizione di un'app per la logica è un'azione che include trigger, passaggi del flusso di controllo come condizioni, ambiti e cicli ForEach, cicli Do until, chiamate a connettori e chiamate ad azioni native.
I trigger sono azioni speciali progettate per creare una nuova istanza di un'app per la logica quando si verifica un determinato evento.  Diversi comportamenti dei trigger potrebbero influire sulla misurazione dell'app per la logica.
* **Trigger di poll**: questo trigger esegue continuamente il poll di un endpoint fino a quando non riceve un messaggio che soddisfa i criteri per la creazione di un'istanza di un'app per la logica.  L'intervallo di polling può essere configurato nel trigger nella finestra di progettazione delle app per la logica.  Ogni richiesta di poll, anche se non determina la creazione di un'istanza di un'app per la logica, viene conteggiata come esecuzione di azione.
* **Trigger di webhook** : questo trigger attende che un client gli invii una richiesta su un determinato endpoint.  Ogni richiesta inviata all'endpoint del webhook viene conteggiata come esecuzione di azione. I trigger Richiesta e Webhook HTTP sono entrambi trigger di webhook.
* **Trigger di ricorrenza**: questo trigger crea un'istanza dell'app per la logica in base all'intervallo di ricorrenza in esso configurato.  Un trigger di ricorrenza può ad esempio essere configurato per essere eseguito ogni tre giorni o anche ogni minuto.

Le esecuzioni dei trigger vengono visualizzate nella sezione Cronologia trigger del pannello delle risorse delle app per la logica.

Per tutte le azioni eseguite, riuscite o non riuscite, viene misurato il consumo come esecuzione di un'azione.  Le azioni ignorate perché non è stata soddisfatta una condizione o non eseguite perché l'app per la logica è stata terminata prima del completamento non vengono conteggiate come esecuzioni di azioni.

Le azioni eseguite all'interno di cicli vengono conteggiate per iterazione del ciclo.  Una singola azione in un ciclo ForEach che scorre un elenco di 10 elementi viene conteggiata come il numero di elementi dell'elenco (10) moltiplicato per il numero di azioni nel ciclo (1) più 1 per l'avvio del ciclo, ottenendo in questo esempio (10 * 1) + 1 = 11 esecuzioni di azioni.
Per le app per la logica disabilitate non possono essere create nuove istanze. Di conseguenza, mentre sono disabilitate, non viene effettuato alcun addebito.  Tenere presente che quando si disabilita un'app per la logica, la disattivazione delle istanze prima della disabilitazione completa potrebbe richiedere qualche minuto.
### <a name="integration-account-usage"></a>Utilizzo dell'account di integrazione
Nell'utilizzo a consumo è incluso un [account di integrazione](logic-apps-enterprise-integration-create-integration-account.md) a scopo di esplorazione, sviluppo e test, che consente di usare le funzionalità [B2B/EDI](logic-apps-enterprise-integration-b2b.md) e di [elaborazione XML](logic-apps-enterprise-integration-xml.md) di App per la logica senza costi aggiuntivi. È possibile creare un massimo di un account per area e archiviare fino a 10 contratti e 25 mappe. Non sono previsti limiti per schemi, certificati e partner ed è possibile caricare tutti quelli necessari.

Oltre a includere account di integrazione a consumo, è anche possibile creare account di integrazione standard senza questi limiti e con il contratto di servizio di App per la logica standard. Per altre informazioni, vedere la pagina relativa ai [prezzi di Azure](https://azure.microsoft.com/pricing/details/logic-apps).

## <a name="app-service-plans"></a>Piani di servizio app
Le app per la logica già create, che fanno riferimento a un piano di servizio app, continuano a comportarsi come prima. A seconda del piano scelto, la limitazione viene applicata dopo che viene superato il numero di esecuzioni giornaliere previste, ma la fatturazione si basa sul misuratore di esecuzioni delle azioni.
I clienti EA con un piano di servizio app nella sottoscrizione, che non deve essere associato in modo esplicito all'app per la logica, hanno il benefit delle quantità incluso.  Se, ad esempio, nella stessa sottoscrizione EA sono inclusi un piano di servizio app Standard e un'app per la logica , non viene effettuato l'addebito per 10.000 esecuzioni di azioni al giorno. Vedere la tabella seguente. 

Piani di servizio app ed esecuzioni di azioni giornaliere consentite:
|  | Gratuito/Condiviso/Basic | Standard | Premium |
| --- | --- | --- | --- |
| Esecuzioni di azioni al giorno |200 |10.000 |50.000 |
### <a name="convert-from-app-service-plan-pricing-to-consumption"></a>Passare dal piano tariffario del servizio app a prezzi al consumo
Per modificare un'app per la logica a cui è associato un piano di servizio in un modello a consumo, rimuovere il riferimento al piano di servizio app nella definizione dell'app per la logica.  Per apportare questa modifica, è sufficiente una chiamata a un cmdlet di PowerShell: `Set-AzureRmLogicApp -ResourceGroupName ‘rgname’ -Name ‘wfname’ –UseConsumptionModel -Force`
## <a name="pricing"></a>Prezzi
Per informazioni sui prezzi, vedere [Prezzi di App per la logica](https://azure.microsoft.com/pricing/details/logic-apps).

## <a name="next-steps"></a>Passaggi successivi
* [Informazioni su App per la logica][whatis]
* [Creare la prima app per la logica][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: logic-apps-what-are-logic-apps.md
[create]: logic-apps-create-a-logic-app.md


