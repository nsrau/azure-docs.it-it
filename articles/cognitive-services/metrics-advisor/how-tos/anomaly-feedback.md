---
title: Fornire feedback sulle anomalie al servizio metrica Advisor
titleSuffix: Azure Cognitive Services
description: Informazioni su come inviare commenti e suggerimenti sulle anomalie rilevate dall'istanza di Advisor di metrica e ottimizzare i risultati.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: mbullwin
ms.openlocfilehash: 96a6dfebdd0047d5a6f4ff3e295ab96dd8c7226a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184970"
---
# <a name="provide-anomaly-feedback"></a>Fornire feedback sulle anomalie

Il feedback degli utenti è uno dei metodi più importanti per individuare i difetti all'interno del sistema di rilevamento delle anomalie. Qui viene fornito un modo per consentire agli utenti di contrassegnare i risultati di rilevamento non corretti direttamente in una serie temporale e di applicare immediatamente il feedback. In questo modo, un utente può insegnare al sistema di rilevamento delle anomalie come eseguire il rilevamento delle anomalie per una serie temporale specifica tramite interazioni attive. 

> [!NOTE]
> Attualmente il feedback influirà solo sui risultati del rilevamento delle anomalie in base al **rilevamento intelligente** ma non alla **soglia** fissa e alla **soglia di modifica**.

## <a name="how-to-give-time-series-feedback"></a>Come fornire commenti e suggerimenti sulle serie temporali

È possibile fornire commenti e suggerimenti dalla pagina dei dettagli della metrica su qualsiasi serie. È sufficiente selezionare un punto qualsiasi e viene visualizzata la finestra di dialogo Commenti e suggerimenti seguente. Mostra le dimensioni della serie scelta. È possibile riselezionare i valori della dimensione o persino rimuoverne alcuni per ottenere un batch di dati di serie temporali. Dopo aver scelto la serie temporale, selezionare il pulsante **Aggiungi** per aggiungere il feedback. ci sono quattro tipi di commenti e suggerimenti che è possibile assegnare. Per accodare più elementi di feedback, selezionare il pulsante **Salva** dopo aver completato le annotazioni.

:::image type="content" source="../media/feedback/click-on-any-point.png" alt-text="Grafico con dati di serie temporali con una linea blu e punti rossi in diversi punti. Casella rossa che circonda un punto con il testo: selezionare un punto qualsiasi":::

:::image type="content" source="../media/feedback/select-or-remove.png" alt-text="Finestra di dialogo Aggiungi feedback con due dimensioni e l'opzione per selezionare o rimuovere dimensioni e aggiungere commenti e suggerimenti.":::

### <a name="mark-the-anomaly-point-type"></a>Contrassegna il tipo di punto di anomalia

Come illustrato nell'immagine seguente, nella finestra di dialogo feedback verrà riempito automaticamente il timestamp del punto scelto, sebbene sia possibile modificare questo valore. Selezionare quindi se si desidera identificare questo elemento come `Anomaly` , `NotAnomaly` o `AutoDetect` .

:::image type="content" source="../media/feedback/anomaly-value.png" alt-text="Menu a discesa con scelte di anomalie, NotAnomaly e rilevamento automatico":::

La selezione applicherà i commenti alla futura elaborazione del rilevamento delle anomalie della stessa serie. I punti elaborati non verranno ricalcolati. Ciò significa che se è stata contrassegnata un'anomalia come NotAnomaly, si elimineranno anomalie simili in futuro e se si contrassegna un `NotAnomaly` punto come `Anomaly` , si tenderà a rilevare punti simili come `Anomaly` in futuro. Se `AutoDetect` si sceglie, eventuali commenti e suggerimenti precedenti sullo stesso punto verranno ignorati in futuro.

## <a name="provide-feedback-for-multiple-continuous-points"></a>Inviare commenti e suggerimenti per più punti continui 

Se si desidera fornire feedback sulle anomalie per più punti continui contemporaneamente, selezionare il gruppo di punti che si desidera annotare. Si noterà che l'intervallo di tempo scelto viene compilato automaticamente quando si fornisce il feedback delle anomalie.

:::image type="content" source="../media/feedback/continuous-anomaly-feedback.png" alt-text="Menu dei commenti sulle anomalie con anomalia selezionata e intervallo di tempo specifico":::

Per visualizzare se un singolo punto è interessato dai commenti sulle anomalie, quando si Esplora una serie temporale selezionare un singolo punto. Se il risultato del rilevamento delle anomalie è stato modificato da feedback, la descrizione comando visualizzerà **interessato da feedback: true**. Se viene visualizzato **interessato da feedback: false**, questo significa che è stato eseguito un calcolo di feedback anomalie per questo punto, ma il risultato del rilevamento anomalie non deve essere modificato.

:::image type="content" source="../media/feedback/affected-by-feedback.png" alt-text="Descrizione comando visualizzato con le parole: interessato da feedback: true, evidenziato in rosso":::

In alcune situazioni non è consigliabile fornire commenti e suggerimenti:

- L'anomalia è causata da una festività. È consigliabile usare un evento preimpostato per risolvere questo tipo di falso allarme, perché sarà più preciso.
- L'anomalia è causata da una modifica dell'origine dati nota. Ad esempio, si è verificata una modifica del sistema upstream in quel momento. In questa situazione, si prevede che venga generato un avviso di anomalia, perché il sistema non è stato in grado di individuare il risultato della modifica del valore e quando si verificano modifiche di valore simili. Pertanto non è consigliabile annotare questo tipo di problema come `NotAnomaly` .

## <a name="change-points"></a>Punti di modifica

A volte il cambiamento di tendenza dei dati influirà sui risultati del rilevamento delle anomalie. Quando viene presa una decisione in merito alla presenza o meno di un punto anomalo, verrà preso in considerazione la finestra più recente dei dati cronologici. Quando la serie temporale presenta un cambiamento di tendenza, è possibile contrassegnare il punto di modifica esatto, in modo da consentire il rilevamento delle anomalie in un'analisi futura.

Come illustrato nella figura seguente, è possibile selezionare `ChangePoint` per il tipo di feedback e selezionare `ChangePoint` , `NotChangePoint` o `AutoDetect` dall'elenco a discesa.

:::image type="content" source="../media/feedback/changepoint.png" alt-text="Menu cambia punto con elenco a discesa contenente le opzioni per ChangePoint, NotChangePoint e rilevamento automatico":::

> [!NOTE]
> Se i dati continuano a cambiare, è necessario contrassegnare un solo punto come `ChangePoint` , quindi, se è stato contrassegnato un oggetto `timerange` , il timestamp e l'ora dell'ultimo punto verranno riempiti automaticamente. In questo caso, l'annotazione influirà solo sui risultati del rilevamento delle anomalie dopo 12 punti.

## <a name="seasonality"></a>Stagionalità

Per i dati stagionali, quando si esegue il rilevamento delle anomalie, un passaggio consiste nel stimare il periodo (stagionalità) della serie temporale e applicarlo alla fase di rilevamento delle anomalie. Talvolta è difficile identificare un periodo preciso e anche il periodo può cambiare. Un periodo definito in modo errato potrebbe avere effetti collaterali sui risultati del rilevamento delle anomalie. È possibile trovare il periodo corrente da una descrizione comando, il cui nome è `Min Period` .

:::image type="content" source="../media/feedback/min-period.png" alt-text="Descrizione comando sovrapposta con il periodo di parole e il numero 7 evidenziato in rosso.":::

È possibile fornire commenti e suggerimenti per il periodo di tempo per risolvere questo tipo di errore di rilevamento anomalie. Come illustrato nella figura, è possibile impostare un valore del punto. L'unità `interval` indica una granularità. Qui zero intervalli indica che i dati non sono stagionali. È anche possibile selezionare `AutoDetect` se si vuole annullare il feedback precedente e lasciare che la pipeline rilevi il periodo automaticamente. 
 
> [!NOTE]
> Quando si imposta period non è necessario assegnare un timestamp o un TimeRange, il periodo influirà sui rilevamenti delle anomalie future sull'intero timeseries dal momento in cui si invia un commento.


:::image type="content" source="../media/feedback/period-feedback.png" alt-text="Menu con periodo di rilevamento automatico impostato su 28 e intervallo impostato su zero che indica una non stagionalità.":::

## <a name="provide-comment-feedback"></a>Fornire commenti e suggerimenti sui commenti

È anche possibile aggiungere commenti per aggiungere annotazioni e fornire il contesto ai dati. Per aggiungere commenti, selezionare un intervallo di tempo e aggiungere il testo per il commento.

:::image type="content" source="../media/feedback/feedback-comment.png" alt-text="Menu con la possibilità di impostare un intervallo di tempo e una casella per aggiungere un commento basato su testo":::

## <a name="time-series-batch-feedback"></a>Feedback batch serie temporali

Come descritto in precedenza, la modalità feedback consente di riselezionare o rimuovere i valori delle dimensioni per ottenere un batch di serie temporali definito da un filtro della dimensione. È anche possibile aprire questo modale facendo clic sul pulsante "+" per inviare commenti e suggerimenti dal pannello sinistro e selezionare dimensioni e valori dimensione.

:::image type="content" source="../media/feedback/feedback-time-series.png" alt-text="Menu con segno più blu evidenziato in rosso accanto a commenti e suggerimenti di Word":::

:::image type="content" source="../media/feedback/feedback-dimension.png" alt-text="Menu Aggiungi feedback con due dimensioni indicate da Dim1 e Dim2":::

## <a name="how-to-view-feedback-history"></a>Come visualizzare la cronologia dei commenti

Esistono due modi per visualizzare la cronologia dei commenti. È possibile selezionare il pulsante cronologia commenti e suggerimenti nel pannello a sinistra per visualizzare un elenco di commenti modale. Elenca tutti i commenti e suggerimenti forniti prima per i filtri delle singole serie o delle dimensioni.

:::image type="content" source="../media/feedback/feedback-history-options.png" alt-text="Menu elenco Commenti":::

Un altro modo per visualizzare la cronologia dei commenti è quello di una serie. Si vedranno diversi pulsanti nell'angolo superiore destro di ogni serie. Selezionare il pulsante Mostra feedback. la riga passa dalla visualizzazione dei punti anomalie alla visualizzazione delle voci di feedback. Il flag verde rappresenta un punto di modifica e i punti blu sono altri punti di feedback. È anche possibile selezionarli e ottenere un elenco di commenti e suggerimenti che elenca i dettagli del feedback fornito per questo punto.

:::image type="content" source="../media/feedback/feedback-history-graph.png" alt-text="Grafico cronologia commenti":::

:::image type="content" source="../media/feedback/feedback-list.png" alt-text="Menu elenco commenti con due dimensioni":::

> [!NOTE]
> Chiunque abbia accesso alla metrica può inviare commenti e suggerimenti, in modo da poter visualizzare i commenti e i suggerimenti forniti da altri proprietari di feed di dati. Se si modifica lo stesso punto di un altro utente, i commenti e suggerimenti sovrascriveranno la voce precedente.       

## <a name="next-steps"></a>Passaggi successivi
- [Diagnosticare un evento imprevisto](diagnose-incident.md).
- [Configurare le metriche e ottimizzare la configurazione del rilevamento](configure-metrics.md)
- [Configurare avvisi e ottenere notifiche usando un hook](../how-tos/alerts.md)