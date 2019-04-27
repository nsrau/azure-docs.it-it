---
title: Effettuare la registrazione per l'API Analisi del testo
titleSuffix: Azure Cognitive Services
description: Istruzioni per l'iscrizione e l'uso del servizio di Analisi del testo.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: 53532a19482a33f8727e71d44ae169225b5b1c98
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60829668"
---
# <a name="how-to-sign-up-for-the-text-analytics-api"></a>Come effettuare la registrazione per l'API Analisi del testo

Le risorse di Analisi del testo sono disponibili nel cloud 24 ore su 24, 7 giorni su 7. Per poter caricare i contenuti per l'analisi, è necessario eseguire la registrazione per ottenere una chiave di accesso. Per ogni chiamata all'API è necessaria una chiave di accesso nella richiesta.

+ Iniziare con una sottoscrizione di Azure. È possibile creare un [account gratuito](https://azure.microsoft.com/free/) per una prova senza addebiti.

+ Creare un [account API Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) scegliendo l'**API Analisi del testo**. La chiave viene generata al momento della registrazione.

Per Analisi del testo sono disponibili un livello gratuito per l'esplorazione e la valutazione e livelli fatturabili per i carichi di lavoro di produzione. È possibile avere più iscrizioni in ciascuna sottoscrizione: uno gratuito, uno a pagamento e così via. Se il volume delle richieste aumenta, è possibile passare a un livello che offre più transazioni.

Non è previsto alcun contratto di servizio per i servizi in anteprima o per il livello gratuito. Per altre informazioni, vedere [Contratto di Servizio per Servizi cognitivi](https://azure.microsoft.com/support/legal/sla/cognitive-services/v1_1/)

## <a name="how-to-change-tiers"></a>Come modificare il livello

Iniziare con un livello gratuito e quindi passare a un livello fatturabile per i carichi di lavoro di produzione. La fatturazione standard è disponibile in diversi livelli. È possibile passare da un livello a un altro mantenendo lo stesso endpoint e le stesse chiavi di accesso.

1. Accedere al [portale di Azure](https://portal.azure.com) e [individuare il servizio](text-analytics-how-to-access-key.md).

2. Fare clic su **Piano tariffario**.

   ![Comando Piano tariffario nel menu di spostamento a sinistra](../media/portal-pricing-tier.png)

3. Scegliere un livello e fare clic su **Seleziona**.  I nuovi limiti vengono applicati non appena viene elaborata la selezione. 

   ![Riquadri e pulsante Seleziona nella pagina di selezione del livello](../media/portal-choose-tier.png)

## <a name="how-billing-works"></a>Modalità di funzionamento della fatturazione

La fatturazione è basata sul numero di transazioni. È possibile acquistare un blocco di transazioni a un livello specifico in un ciclo di fatturazione mensile e quindi, se si supera il limite, verrà applicato un piccolo addebito per le eccedenze per ogni transazione. Se si supera il limite massimo regolarmente, valutare se passare a un livello superiore.

Per altre informazioni, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/).

### <a name="what-constitutes-a-transaction-in-the-text-analytics-api"></a>Da cosa è costituita una transazione nell'API Analisi del testo?
Qualsiasi annotazione a un documento viene conteggiata come transazione. Le chiamate del punteggio batch prenderanno in considerazione anche il numero di documenti per cui deve essere calcolato il punteggio nella transazione. Se, ad esempio, vengono inviati 1.000 documenti per l'analisi del sentiment in una singola chiamata API, verranno conteggiate 1.000 transazioni.

## <a name="see-also"></a>Vedere anche  

 [Panoramica di Analisi del testo](../overview.md)  
 [Domande frequenti](../text-analytics-resource-faq.md)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Ottenere una chiave di accesso](text-analytics-how-to-access-key.md)
