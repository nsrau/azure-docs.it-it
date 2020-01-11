---
title: Diagnosticare e risolvere i problemi di un ambiente di anteprima-Azure Time Series Insights | Microsoft Docs
description: Informazioni su come diagnosticare e risolvere i problemi di un ambiente di Azure Time Series Insights Preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: seodec18
ms.openlocfilehash: 1fc3aa6caa6266d2cd42e4783e8e39d5cc92c220
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75861575"
---
# <a name="diagnose-and-troubleshoot-a-preview-environment"></a>Diagnosticare e risolvere i problemi di un ambiente di anteprima

Questo articolo riepiloga i diversi problemi comuni che possono verificarsi quando si lavora nell'ambiente di anteprima di Azure Time Series Insights. L'articolo descrive anche possibili cause e soluzioni per ogni problema.

## <a name="problem-i-cant-find-my-environment-in-the-preview-explorer"></a>Problema: non è possibile trovare l'ambiente in Esplora anteprime

Questo problema può verificarsi se non si dispone delle autorizzazioni per accedere all'ambiente di Time Series Insights. Gli utenti necessitano di un ruolo di lettore a livello di accesso per visualizzare l'ambiente Time Series Insights. Per verificare i livelli di accesso correnti e concedere accesso aggiuntivo, passare alla sezione **criteri di accesso ai dati** nella Time Series Insights risorsa nel [portale di Azure](https://portal.azure.com/).

  [![verificare i criteri di accesso ai dati.](media/preview-troubleshoot/verify-data-access-policies.png)](media/preview-troubleshoot/verify-data-access-policies.png#lightbox)

## <a name="problem-no-data-is-seen-in-the-preview-explorer"></a>Problema: non vengono visualizzati dati in Esplora anteprime

Esistono diversi motivi comuni per cui i dati potrebbero non essere visualizzati in [esplora Azure Time Series Insights Preview](https://insights.timeseries.azure.com/preview).

- L'origine evento potrebbe non ricevere correttamente i dati.

    Verificare che l'origine evento, ovvero un hub eventi o un hub IoT, riceva dati da tag o istanze. Per verificare ciò, passare alla pagina di panoramica della risorsa nel portale di Azure.

    [![rivedere i Cenni preliminari sulle metriche del dashboard.](media/preview-troubleshoot/verify-dashboard-metrics.png)](media/preview-troubleshoot/verify-dashboard-metrics.png#lightbox)

- I dati dell'origine evento non sono in formato JSON.

    Time Series Insights supporta solo dati in formato JSON. Per gli esempi JSON, leggere le [forme JSON supportate](./how-to-shape-query-json.md).

- Alla chiave dell'origine evento manca un'autorizzazione obbligatoria.

  * Per un hub IoT è necessario fornire la chiave con l'autorizzazione di **connessione al servizio**.

    [![verificare le autorizzazioni dell'hub Internet.](media/preview-troubleshoot/verify-correct-permissions.png)](media/preview-troubleshoot/verify-correct-permissions.png#lightbox)

    * I criteri **iothubowner** e **servizio** funzionano perché hanno l'autorizzazione di **connessione al servizio** .

  * Per un hub eventi è necessario fornire la chiave con l'autorizzazione di **ascolto**.
  
    [![verificare le autorizzazioni dell'hub eventi.](media/preview-troubleshoot/verify-eh-permissions.png)](media/preview-troubleshoot/verify-eh-permissions.png#lightbox)

    * Sia i criteri di **lettura** che quelli di **gestione** funzionano perché hanno l'autorizzazione **Listen** .

- Il gruppo di consumer specificato non è esclusivo di Time Series Insights.

    Durante la registrazione di un hub IoT o un hub eventi viene specificato il gruppo di consumer da usare per la lettura dei dati. Questo gruppo di consumer deve essere univoco per ogni ambiente. Se viene condiviso, l'hub eventi sottostante disconnette automaticamente uno dei lettori in modo casuale. Specificare un gruppo di consumer univoco per la lettura dei dati da parte di Time Series Insights.

- La proprietà ID di Time Series specificata al momento del provisioning è errata, mancante o null.

    Questo problema può verificarsi se la proprietà ID di Time Series non è configurata in modo corretto al momento del provisioning dell'ambiente. Per ulteriori informazioni, leggere le [procedure consigliate per la scelta di un ID di serie temporale](./time-series-insights-update-how-to-id.md). In questo momento, non è possibile aggiornare un ambiente Time Series Insights esistente per usare un ID Time Series differente.

## <a name="problem-some-data-shows-but-some-is-missing"></a>Problema: alcuni dati risultano mancanti.

È possibile che si stia inviando i dati senza ID Time Series.

- Questo problema può verificarsi quando si inviano gli eventi senza il campo ID Time Series nel payload. Per altre informazioni, vedere [forme JSON supportate](./how-to-shape-query-json.md).
- Questo problema può verificarsi perché l'ambiente è limitato nelle richieste.

    > [!NOTE]
    > Attualmente, Time Series Insights supporta una velocità di inserimento massima di 6 MB/s.

## <a name="problem-my-event-sources-timestamp-property-name-doesnt-work"></a>Problema: il nome della proprietà timestamp dell'origine evento non funziona

Verificare che il nome e il valore siano conformi alle regole seguenti:

* Il nome della proprietà Timestamp fa distinzione fra maiuscole e minuscole.
* Il formato del valore della proprietà timestamp che deriva dall'origine evento come stringa JSON è `yyyy-MM-ddTHH:mm:ss.FFFFFFFK`. Un esempio di tale stringa è `“2008-04-12T12:53Z”`.

Il modo più semplice per garantire l'acquisizione e il corretto funzionamento del nome della proprietà Timestamp consiste nell'usare lo strumento di esplorazione di anteprima di Time Series Insights. All'interno dello strumento di esplorazione di anteprima di Time Series Insights, usando il grafico, selezionare un periodo di tempo dopo aver specificato il nome della proprietà Timestamp. Fare clic con il pulsante destro del mouse sulla selezione e selezionare l'opzione **Esplora eventi**. L'intestazione della prima colonna contiene il nome della proprietà Timestamp. Deve avere `($ts)` accanto alla parola `Timestamp`, invece di:

* `(abc)`, che indica che Time Series Insights legge i valori dei dati come stringhe.
* Icona del **Calendario** , che indica che Time Series Insights legge il valore dei dati come DateTime.
* `#`, che indica che Time Series Insights legge i valori dei dati come integer.

Se la proprietà Timestamp non è specificata in modo esplicito, l'hub IoT o hub eventi di un evento viene ora usato come timestamp predefinito.

## <a name="problem-i-cant-view-data-from-my-warm-store-in-the-explorer"></a>Problema: non è possibile visualizzare i dati dal negozio caldo in Esplora risorse

- È possibile che sia stato eseguito di recente il provisioning del negozio in caldo e che i dati continuino a propagarsi.
- È possibile che sia stato eliminato l'archivio a caldo, nel qual caso i dati verrebbero persi.

## <a name="problem-i-cant-view-or-edit-my-time-series-model"></a>Problema: non è possibile visualizzare o modificare il modello Time Series

- È possibile accedere a un ambiente Time Series Insights S1 o S2.

   I modelli Time Series sono supportati solo negli ambienti con pagamento in base al consumo. Per altre informazioni su come accedere all'ambiente S1 o S2 da Esplora Time Series Insights Preview, vedere visualizzare i [dati nella finestra di esplorazione](./time-series-insights-update-explorer.md).

   [Non ![alcun evento nell'ambiente.](media/preview-troubleshoot/troubleshoot-no-events.png)](media/preview-troubleshoot/troubleshoot-no-events.png#lightbox)

- Non si dispone delle autorizzazioni per visualizzare e modificare il modello.

   Gli utenti devono accedere a livello di collaboratore per modificare e visualizzare il modello Time Series. Per verificare i livelli di accesso correnti e concedere l'accesso aggiuntivo, passare alla sezione **criteri di accesso ai dati** nella risorsa Time Series Insights nel portale di Azure.

## <a name="problem-all-my-instances-in-the-preview-explorer-lack-a-parent"></a>Problema: tutte le istanze personali in Esplora anteprime non dispongono di un elemento padre

Questo problema può verificarsi se l'ambiente non dispone di una gerarchia di modelli Time Series definita. Per altre informazioni, vedere [usare i modelli Time Series](./time-series-insights-update-how-to-tsm.md).

  [![istanze con stato non associato verrà visualizzato un avviso.](media/preview-troubleshoot/unparented-instances.png)](media/preview-troubleshoot/unparented-instances.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

- Leggere [Lavorare con modelli di Time Series](./time-series-insights-update-how-to-tsm.md).

- Informazioni sulle [forme JSON supportate](./how-to-shape-query-json.md).

- Esaminare la [pianificazione e i limiti](./time-series-insights-update-plan.md) in Azure Time Series Insights Preview.
