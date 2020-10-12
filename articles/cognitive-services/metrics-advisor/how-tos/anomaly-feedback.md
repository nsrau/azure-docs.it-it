---
title: Fornire feedback sulle anomalie al servizio metrica Advisor
titleSuffix: Azure Cognitive Services
description: Informazioni su come inviare commenti e suggerimenti sulle anomalie rilevate dall'istanza di Advisor di metrica e ottimizzare i risultati.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 50d422edf1a4b45132d0b86eac9d4947cef5e5bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90938048"
---
# <a name="adjust-anomaly-detection-using-feedback"></a>Regolare il rilevamento anomalie usando il feedback

Se non si è soddisfatti dei risultati del rilevamento delle anomalie forniti dal monitoraggio delle metriche, è possibile aggiungere manualmente il feedback che influirà sul modello applicato ai dati. 

Usare i pulsanti nell'angolo superiore destro della pagina per attivare la modalità di annotazione dei commenti.

:::image type="content" source="../media/feedback/annotation-mode.png" alt-text="Modalità di annotazione commenti.":::

Dopo aver attivato la modalità di annotazione dei commenti, è possibile fornire commenti e suggerimenti per un punto o più punti continui.

## <a name="give-feedback-for-one-point"></a>Inviare commenti e suggerimenti per un punto 

Con la modalità di annotazione feedback attivata, fare clic su un punto per aprire un pannello **Aggiungi feedback** . È possibile impostare il tipo di feedback che si desidera applicare. Questo feedback verrà incorporato nel rilevamento per i punti futuri.  

* Selezionare **anomalia** se si ritiene che il punto sia stato erroneamente etichettato da monitoraggio metrica. È possibile specificare se un punto deve essere o meno un'anomalia. 
* Selezionare **ChangePoint** se si ritiene che il punto indichi l'inizio di una modifica di tendenza.
* Selezionare **periodo** per indicare la stagionalità. Il monitoraggio delle metriche può rilevare automaticamente intervalli di stagionalità oppure è possibile specificarlo manualmente. 

È possibile lasciare un commento nella casella di testo del **Commento** e fare clic su **Save (Salva** ) per salvare il feedback.

:::image type="content" source="../media/feedback/feedback-menu.png" alt-text="Modalità di annotazione commenti.":::

## <a name="give-feedback-for-multiple-continuous-points"></a>Inviare commenti e suggerimenti per più punti continui

È possibile inviare commenti e suggerimenti per più punti continui contemporaneamente facendo clic su di esso e trascinando il mouse sui punti che si desidera annotare. Viene visualizzato lo stesso menu di feedback illustrato in precedenza. Lo stesso feedback verrà applicato a tutti i punti scelti dopo aver fatto clic su **Salva**.

:::image type="content" source="../media/feedback/continuous-points.png" alt-text="Modalità di annotazione commenti.":::

## <a name="how-to-view-my-feedback"></a>Come visualizzare i commenti e suggerimenti

Per verificare se è stato modificato il rilevamento delle anomalie di un punto, passare il puntatore del mouse sul punto. La descrizione comando visualizzerà **interessato da feedback: true** se il rilevamento è stato modificato. Se viene visualizzato **false**, il calcolo del feedback è stato eseguito nel punto, ma il risultato del rilevamento anomalie non è stato modificato.

:::image type="content" source="../media/feedback/affected-point.png" alt-text="Modalità di annotazione commenti.":::

## <a name="when-should-i-annotate-an-anomaly-as-normal"></a>Quando è consigliabile annotare un'anomalia come "normale"

Esistono molti motivi per cui è possibile considerare che un'anomalia è un falso allarme. Si consiglia di utilizzare le seguenti funzionalità di Advisor di metrica se si applica uno degli scenari seguenti:


|Scenario  |Recommendation |
|---------|---------|
|L'anomalia è causata da una modifica dell'origine dati nota, ad esempio una modifica di sistema.     | Non annotare questa anomalia se non si prevede che questo scenario venga rieseguito regolarmente.        |
|L'anomalia è causata da festività.     | Usare [gli eventi predefiniti](configure-metrics.md#preset-events) per contrassegnare il rilevamento delle anomalie in orari specificati.       |
|Esiste un modello normale per rilevare le anomalie (ad esempio nei fine settimana) e non devono essere anomalie.      |Utilizzare la funzionalità feedback o gli eventi preimpostati.        |

## <a name="next-steps"></a>Passaggi successivi
- [Diagnosticare un evento imprevisto](diagnose-incident.md).
- [Configurare le metriche e ottimizzare la configurazione del rilevamento](configure-metrics.md)
- [Configurare avvisi e ottenere notifiche usando un hook](../how-tos/alerts.md)