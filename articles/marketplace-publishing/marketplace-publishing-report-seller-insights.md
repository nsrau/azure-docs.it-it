---
title: Informazioni sui report basati sull'uso di Azure Marketplace e sui report sulle informazioni dettagliate del venditore | Documentazione Microsoft
description: "In qualità di venditore in Azure Marketplace, comprendere i report basati sull'utilizzo, detti anche report sulle informazioni dettagliate del venditore"
services: Azure Marketplace
documentationcenter: na
author: v-jeana
manager: lakoch
editor: 
ms.assetid: f1ffde66-98f0-4c3e-ad94-fee1f97cae03
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: v-jeana; hascipio; v-dabosl
ms.openlocfilehash: e098e27e32f7b7ae2009580a430f262aa7225206
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="understand-your-seller-insights-report"></a>Descrizione del report sulle informazioni dettagliate del venditore
**Quali sono le informazioni dettagliate del venditore?**

Tutti gli editori di servizi per gli sviluppatori di fatturazione basata sull'utilizzo o su VM ricevono report mensili da Microsoft, se le offerte generano utilizzo.

**Cosa si riceverà?**

* **Messaggio di benvenuto:** i nuovi editori ricevono un messaggio di benvenuto che comunica la cadenza mensile dei report sulle informazioni dettagliate del venditore.
* **Report mensili sulle vendite:** se l'offerta genera utilizzo, si riceve un secondo messaggio di posta elettronica contenente i report e istruzioni su come accedere alla password:

  * Se lo SKU di servizi per gli sviluppatori di soluzioni di fatturazione basata sull'uso o su macchina virtuale genera utilizzo, il report mensile conterrà informazioni dettagliate su ordini, utilizzo, mercati e clienti per le offerte.
  * I report sono protetti da una password nota solo all'utente e a Microsoft, per salvaguardare i dati dei clienti.
  * Se nessuna delle offerte ha generato utilizzo durante il mese, Microsoft non invia alcun report.

## <a name="understand-your-seller-insights-report"></a>Descrizione del report sulle informazioni dettagliate del venditore
**Ordini per SKU e tipo di licenza: scheda Ordini Marketplace**

![readingreportbyorders][2]

* I filtri dei dati permettono di filtrare il report in base a ogni elemento.
* Un grafico mostra gli ordini mensili per tipo di licenza di Azure. Ogni barra indica gli ordini totali per quel mese, suddivisi per tipo di licenza di Azure.
* Un grafico mostra gli ordini mensili per SKU. Ogni barra indica gli ordini mensili totali per tutti gli SKU, suddivisi per SKU.
* Un grafico mostra la tendenza mensile degli ordini per tipo di licenza di Azure e tipo di licenza di Azure Marketplace.
* I grafici a torta mostrano gli ordini per tipo di licenza di Azure e tipo di licenza Marketplace.
* Una tabella mostra gli ordini mensili totali per tipo di licenza Marketplace, i totali mensili e i totali cumulativi per tutti i mesi.

**Ordini per SKU e tipo di licenza: scheda Utilizzo Marketplace**

![readingreportbyusage][3]

* I filtri dei dati permettono di filtrare il report in base a ogni elemento.
* È necessario selezionare l’utilizzo della VM normalizzato o l'utilizzo non elaborato.
* Un grafico mostra l'utilizzo mensile per tipo di licenza di Azure. Ogni barra indica l'utilizzo totale per quel mese, suddiviso per tipo di licenza di Azure.
* Un grafico mostra l'utilizzo mensile per SKU. Ogni barra indica l'utilizzo mensile totale per tutti gli SKU, suddiviso per SKU.
* Un grafico mostra la tendenza mensile dell'utilizzo per tipo di licenza di Azure e tipo di licenza Marketplace.
* I grafici a torta mostrano l'utilizzo per tipo di licenza di Azure e tipo di licenza Marketplace.
* Una tabella mostra l'utilizzo mensile totale per tipo di licenza Marketplace, i totali mensili e i totali cumulativi per tutti i mesi.

**Schede Dati dell'ordine e Dati di utilizzo**

Queste schede forniscono i dati dettagliati utilizzati per generare il rapporto.

![orderdata][4]

![usagedata][5]

**Utilizzo per SKU e tipo di licenza: scheda Clienti**

![customerstab][6]

* Si noti la clausola di riservatezza.
* Questa scheda contiene un elenco dei clienti per SKU, informazioni del profilo, data della transazione e consenso al contatto a fini promozionali.
* Il report include il numero di ordini per SKU e totali.

**Dichiarazioni di non responsabilità legali**

![legal][1]

Leggere attentamente le dichiarazioni di non responsabilità legali. In caso di domande o commenti e suggerimenti, fare clic sul collegamento nella parte inferiore delle dichiarazioni di non responsabilità per essere instradati alla pagina del supporto Marketplace.

## <a name="request-a-password-reminder"></a>Richiedere un promemoria della password
Passare a https://publish.windowsazure.com/ e accedere con le credenziali dell'account Microsoft.
![passwordreminder][7]

Selezionare la scheda **Editori** .
![selectpublisherstab][8]

Trovare l'ID editore nell'URL:

* Usare questo ID come password per aprire il file di Excel delle informazioni dettagliate del venditore.
  Questa password sarà valida fino a nuova comunicazione.
* È consigliabile usare Microsoft Office 2013 con Windows per aprire la cartella di lavoro.  Sono stati segnalati problemi relativi all'uso di Microsoft Office per Mac.

![publisherid][9]

## <a name="next-steps"></a>Passaggi successivi
In caso di domande relative ai report e alle informazioni dettagliate, contattare il team di supporto.

1. Passare alle pagine di supporto all'indirizzo https://publish.windowsazure.com/.
2. Nella casella **Tipo di problema** selezionare **Report e informazioni dettagliate**.
3. Nella casella **Categoria** selezionare **Domande relative ai report**.
4. Fare clic su **Avvio richiesta**.
   ![sellerinsightsquestions][10]

[1]: ./media/marketplace-publishing-report-seller-insights/legal.png
[2]: ./media/marketplace-publishing-report-seller-insights/readingreportbyorders.png
[3]: ./media/marketplace-publishing-report-seller-insights/readingreportbyusage.png
[4]: ./media/marketplace-publishing-report-seller-insights/orderdata.png
[5]: ./media/marketplace-publishing-report-seller-insights/usagedata.png
[6]: ./media/marketplace-publishing-report-seller-insights/customerstab.png
[7]: ./media/marketplace-publishing-report-seller-insights/passwordreminder.png
[8]: ./media/marketplace-publishing-report-seller-insights/selectpublisherstab.png
[9]: ./media/marketplace-publishing-report-seller-insights/publisherid.png
[10]: ./media/marketplace-publishing-report-seller-insights/sellerinsightsquestions.png
