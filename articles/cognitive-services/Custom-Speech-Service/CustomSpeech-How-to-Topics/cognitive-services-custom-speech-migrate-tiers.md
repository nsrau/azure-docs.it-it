---
title: Eseguire la migrazione dei piani tariffari degli endpoint dal Servizio di riconoscimento vocale personalizzato su Azure | Microsoft Docs
description: Informazioni su come eseguire la migrazione delle distribuzioni dai piani S0 e S1 a S2 degli endpoint del Servizio di riconoscimento vocale personalizzato in Servizi cognitivi.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 07/05/2017
ms.author: panosper
ms.openlocfilehash: 6d92459deb3464cd97c215cbf9a8320628b6da80
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374132"
---
# <a name="migrate-deployments-to-the-new-pricing-model"></a>Eseguire la migrazione delle distribuzioni al nuovo modello tariffario
Da luglio 2017, il Servizio di riconoscimento vocale personalizzato offre un [nuovo modello tariffario](https://azure.microsoft.com/pricing/details/cognitive-services/custom-speech-service/). Il nuovo modello è *più facile da comprendere*, consente di *calcolare i costi in modo più semplice* ed è *più flessibile* in termini di scalabilità. Per la scalabilità, Microsoft ha introdotto il concetto di un'unità di scala. Ogni unità di scala può gestire cinque richieste simultanee. La scalabilità per le richieste simultanee nel modello precedente era impostata a 5 richieste simultanee per il piano S0 e a 12 richieste simultanee per il piano S1. Questi limiti sono stati aperti per offrire maggiore flessibilità con i requisiti dei casi d'uso.

Se si esegue un livello S0 o S1 precedente, si consiglia di eseguire la migrazione delle distribuzioni esistenti al nuovo livello S2. Il nuovo livello S2 comprende i livelli S0 e S1. È possibile visualizzare le opzioni disponibili nella figura seguente:

![La pagina per la scelta del piano tariffario](../../../media/cognitive-services/custom-speech-service/custom-speech-pricing-tier.png)

Microsoft gestisce la migrazione in modo semi-automatico. Per prima cosa l'utente attiva la migrazione selezionando il nuovo piano tariffario. La distribuzione viene quindi migrata automaticamente.

Il mapping dei vecchi piani con le unità di scala è illustrato nella tabella seguente:

| Livello | Richieste simultanee (vecchio modello) | Migrazione | Richieste simultanee |
|----- | ----- | ---- | ---- |
| S0 |  5   |   => **S2** con 1 unità di scala |   5 |
| S1 |  12  |   => **S2** con 3 unità di scala |  15 |

Per la migrazione al nuovo piano, eseguire queste operazioni:

## <a name="step-1-check-your-existing-deployment"></a>Passaggio 1: Controllare la distribuzione esistente
Andare al [portale di Servizio di riconoscimento vocale personalizzato](http://cris.ai) e controllare le distribuzioni esistenti. Nell'esempio ci sono due distribuzioni. Una distribuzione è in esecuzione su un piano S0 e l'altra è in esecuzione su un piano S1. Le distribuzioni sono mostrate nella colonna **Opzioni di distribuzione** della tabella seguente:

![La pagina delle distribuzioni](../../../media/cognitive-services/custom-speech-service/custom-speech-deployments.png)

## <a name="step-2-select-your-new-pricing-tier-in-the-azure-portal"></a>Passaggio 2: Scegliere il nuovo piano tariffario nel portale di Azure
1. Aprire una nuova finestra del browser e accedere al [portale di Azure](http://ms.portal.azure.com/). 

2. Nel riquadro **Servizi cognitivi Microsoft**, nell'elenco **Sottoscrizioni** selezionare la sottoscrizione del Servizio di riconoscimento vocale personalizzato. 

3. Nel riquadro della sottoscrizione selezionare **Piano tariffario**.

    ![Collegamento "Piano tariffario"](../../../media/cognitive-services/custom-speech-service/custom-speech-update-tier.png)

4. Nella pagina **per la scelta del piano tariffario** selezionare **S2 Standard**. Questo piano tariffario è nuovo, semplificato e maggiormente flessibile.

5. Fare clic sul pulsante **Seleziona**.

    ![La pagina per la scelta del piano tariffario](../../../media/cognitive-services/custom-speech-service/custom-speech-update-pricing.png)

## <a name="step-3-check-the-migration-status-in-the-custom-speech-service-portal"></a>Passaggio 3: Controllare lo stato della migrazione nel portale del Servizio di riconoscimento vocale personalizzato
Tornare al portale del Servizio di riconoscimento vocale personalizzato e controllare le distribuzioni. Se la finestra del browser è ancora aperta, aggiornarla. 

Lo stato della distribuzione correlata dovrebbe essere passato a *Elaborazione*. È anche possibile convalidare la migrazione controllando la colonna **Opzioni di distribuzione**. Qui è possibile trovare le informazioni relative alle unità di scala e alla registrazione. Le unità di scala dovrebbero riflettere il piano tariffario precedente. Anche la registrazione dovrebbe essere attivata, come mostrato nella tabella:

![La colonna Opzioni di distribuzione](../../../media/cognitive-services/custom-speech-service/custom-speech-deployments-new.png)


> [!NOTE]
> Se si verificano problemi durante l'esecuzione della migrazione, contattaci.
>

## <a name="next-steps"></a>Passaggi successivi
Per altre esercitazioni, vedere:
* [Creare un modello acustico personalizzato](cognitive-services-custom-speech-create-acoustic-model.md)
* [Creare un modello linguistico personalizzato](cognitive-services-custom-speech-create-language-model.md)
* [Creare un endpoint personalizzato per il riconoscimento vocale](cognitive-services-custom-speech-create-endpoint.md)
