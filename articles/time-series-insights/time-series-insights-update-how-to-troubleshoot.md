---
title: Diagnosticare e risolvere i problemi di un ambiente di anteprima - Azure Time Series Insights Documenti Microsoft
description: Informazioni su come diagnosticare e risolvere i problemi di un ambiente Azure Time Series Insights Preview.Learn how to diagnose and troubleshoot an Azure Time Series Insights Preview environment.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: 667dee6365f38ae058e91c61c24838d8912df26a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80152655"
---
# <a name="diagnose-and-troubleshoot-a-preview-environment"></a>Diagnosticare e risolvere i problemi di un ambiente di anteprimaDiagnose and troubleshoot a Preview environment

Questo articolo riepiloga i diversi problemi comuni che possono verificarsi quando si lavora nell'ambiente di anteprima di Azure Time Series Insights. L'articolo descrive anche possibili cause e soluzioni per ogni problema.

## <a name="problem-i-cant-find-my-environment-in-the-preview-explorer"></a>Problema: non riesco a trovare il mio ambiente in Esplora anteprima

Questo problema può verificarsi se non si dispone delle autorizzazioni per accedere all'ambiente di Time Series Insights. Gli utenti necessitano di un ruolo di lettore a livello di accesso per visualizzare l'ambiente Time Series Insights. Per verificare i livelli di accesso correnti e concedere l'accesso aggiuntivo, passare alla sezione **Criteri di accesso ai dati** nella risorsa Time Series Insights nel portale di [Azure.](https://portal.azure.com/)

  [![Verificare i criteri di accesso ai dati.](media/preview-troubleshoot/verify-data-access-policies.png)](media/preview-troubleshoot/verify-data-access-policies.png#lightbox)

## <a name="problem-no-data-is-seen-in-the-preview-explorer"></a>Problema: nessun dato viene visualizzato in Esplora anteprima

Esistono diversi motivi comuni per cui i dati potrebbero non essere visualizzati in [Azure Time Series Insights Preview explorer](https://insights.timeseries.azure.com/preview).

- L'origine evento potrebbe non ricevere correttamente i dati.

    Verificare che l'origine evento, ovvero un hub eventi o un hub IoT, riceva dati da tag o istanze. Per verificare ciò, passare alla pagina di panoramica della risorsa nel portale di Azure.

    [![Esaminare la panoramica delle metriche del dashboard.](media/preview-troubleshoot/verify-dashboard-metrics.png)](media/preview-troubleshoot/verify-dashboard-metrics.png#lightbox)

- I dati dell'origine evento non sono in formato JSON.

    Time Series Insights supporta solo dati in formato JSON. Per esempi JSON, leggere [Forme JSON supportate](./how-to-shape-query-json.md).

- Alla chiave dell'origine evento manca un'autorizzazione obbligatoria.

  * Per un hub IoT è necessario fornire la chiave con l'autorizzazione di **connessione al servizio**.

    [![Verificare le autorizzazioni dell'hub IoT.](media/preview-troubleshoot/verify-correct-permissions.png)](media/preview-troubleshoot/verify-correct-permissions.png#lightbox)

    * Entrambi i criteri **iothubowner** e **servizio** lavoro perché dispongono dell'autorizzazione di connessione del **servizio.**

  * Per un hub eventi è necessario fornire la chiave con l'autorizzazione di **ascolto**.
  
    [![Esaminare le autorizzazioni dell'hub eventi.](media/preview-troubleshoot/verify-eh-permissions.png)](media/preview-troubleshoot/verify-eh-permissions.png#lightbox)

    * Entrambi i criteri **di lettura** e **gestione** funzionano perché dispongono dell'autorizzazione **di ascolto.**

- Il gruppo di consumer specificato non è esclusivo di Time Series Insights.

    Durante la registrazione di un hub IoT o un hub eventi viene specificato il gruppo di consumer da usare per la lettura dei dati. Questo gruppo di consumer deve essere univoco per ogni ambiente. Se viene condiviso, l'hub eventi sottostante disconnette automaticamente uno dei lettori in modo casuale. Specificare un gruppo di consumer univoco per la lettura dei dati da parte di Time Series Insights.

- La proprietà ID di Time Series specificata al momento del provisioning è errata, mancante o null.

    Questo problema può verificarsi se la proprietà ID di Time Series non è configurata in modo corretto al momento del provisioning dell'ambiente. Per ulteriori informazioni, leggere Procedure consigliate per la scelta di [un ID serie temporali](./time-series-insights-update-how-to-id.md). In questo momento, non è possibile aggiornare un ambiente Time Series Insights esistente per usare un ID Time Series differente.

## <a name="problem-some-data-shows-but-some-is-missing"></a>Problema: alcuni dati mostrano, ma alcuni sono mancanti

È possibile che si stia inviando i dati senza ID Time Series.

- Questo problema può verificarsi quando si inviano gli eventi senza il campo ID Time Series nel payload. Per altre informazioni, vedere [Forme JSON supportate](./how-to-shape-query-json.md).
- Questo problema può verificarsi perché l'ambiente è limitato nelle richieste.

    > [!NOTE]
    > Attualmente, Time Series Insights supporta una velocità di inserimento massima di 6 MB/s.

## <a name="problem-data-was-showing-but-now-ingestion-has-stopped"></a>Problema: i dati venivano visualizzati, ma ora l'inserimento è stato interrotto

- La chiave di origine dell'evento potrebbe essere stata rigenerata e l'ambiente Preview richiede la nuova chiave di origine dell'evento.

Questo problema si verifica quando la chiave fornita durante la creazione dell'origine eventi non è più valida. I dati di telemetria vengono visualizzati nell'hub, ma non i messaggi ricevuti in ingresso in Time Series Insights.You would see telemetry in your hub but no Ingress Received Messages in Time Series Insights. Se non si è certi che la chiave sia stata rigenerata, è possibile cercare "Crea o aggiorna regole di autorizzazione dello spazio dei nomi" negli hub eventi o "Creare o aggiornare la risorsa IotHub" per l'hub IoT. 

Per aggiornare l'ambiente Time Series Insights Preview con la nuova chiave, aprire la risorsa hub nel portale di Azure e copiare la nuova chiave. Passare alla risorsa TSI e fare clic su Origini evento. 

   [![Chiave di aggiornamento.](media/preview-troubleshoot/update-hub-key-step-1.png)](media/preview-troubleshoot/update-hub-key-step-1.png#lightbox)

Selezionare le origini eventi da cui si è interrotta l'inserimento, incollare la nuova chiave e fare clic su Salva.

   [![Chiave di aggiornamento.](media/preview-troubleshoot/update-hub-key-step-2.png)](media/preview-troubleshoot/update-hub-key-step-2.png#lightbox)

## <a name="problem-my-event-sources-timestamp-property-name-doesnt-work"></a>Problema: il nome della proprietà Timestamp dell'origine evento non funziona

Verificare che il nome e il valore siano conformi alle regole seguenti:

* Il nome della proprietà Timestamp fa distinzione fra maiuscole e minuscole.
* Il valore della proprietà Timestamp proveniente dall'origine `yyyy-MM-ddTHH:mm:ss.FFFFFFFK`eventi come stringa JSON ha il formato . Un esempio di tale stringa è `“2008-04-12T12:53Z”`.

Il modo più semplice per garantire l'acquisizione e il corretto funzionamento del nome della proprietà Timestamp consiste nell'usare lo strumento di esplorazione di anteprima di Time Series Insights. All'interno dello strumento di esplorazione di anteprima di Time Series Insights, usando il grafico, selezionare un periodo di tempo dopo aver specificato il nome della proprietà Timestamp. Fare clic con il pulsante destro del mouse sulla selezione e selezionare l'opzione **Esplora eventi**. L'intestazione della prima colonna contiene il nome della proprietà Timestamp. Deve avere `($ts)` accanto alla parola `Timestamp`, invece di:

* `(abc)`, che indica che Time Series Insights legge i valori dei dati come stringhe.
* Icona del **calendario,** che indica che Time Series Insights legge il valore dei dati come datetime.
* `#`, che indica che Time Series Insights legge i valori dei dati come integer.

Se la proprietà Timestamp non è specificata in modo esplicito, l'hub IoT o hub eventi di un evento viene ora usato come timestamp predefinito.

## <a name="problem-i-cant-view-data-from-my-warm-store-in-the-explorer"></a>Problema: non riesco a visualizzare i dati dal mio negozio caldo nell'esploratore

- È possibile che il provisioning sia stato eseguito di recente nel provisioning dell'archivio a caldo e che i dati continuino a essere trasmessi.
- Potresti aver eliminato il tuo archivio caldo, nel qual caso avresti perso i dati.

## <a name="problem-i-cant-view-or-edit-my-time-series-model"></a>Problema: non riesco a visualizzare o modificare il mio modello Time Series

- È possibile accedere a un ambiente Time Series Insights S1 o S2.

   I modelli Time Series sono supportati solo in ambienti con pagamento in base al tempo. Per ulteriori informazioni su come accedere all'ambiente S1 o S2 da Time Series Insights Preview Explorer, leggere [Visualizzare i dati in Esplora risorse](./time-series-insights-update-explorer.md).

   [![Nessun evento nell'ambiente.](media/preview-troubleshoot/troubleshoot-no-events.png)](media/preview-troubleshoot/troubleshoot-no-events.png#lightbox)

- Non si dispone delle autorizzazioni per visualizzare e modificare il modello.

   Gli utenti devono accedere a livello di collaboratore per modificare e visualizzare il modello Time Series. Per verificare i livelli di accesso correnti e concedere l'accesso aggiuntivo, passare alla sezione Criteri di accesso ai dati nella risorsa Time Series Insights nel portale di Azure.To verify the current access levels and grant additional access, go to the **Data Access Policies** section on your Time Series Insights resource in the Azure portal.

## <a name="problem-all-my-instances-in-the-preview-explorer-lack-a-parent"></a>Problema: tutte le mie istanze in Esplora anteprima non dispongono di un elemento padre

Questo problema può verificarsi se l'ambiente non dispone di una gerarchia di modelli Time Series definita. Per ulteriori informazioni, vedere Utilizzare i [modelli Time Series](./time-series-insights-update-how-to-tsm.md).

  [![Le istanze senza padre visualizzeranno un avviso.](media/preview-troubleshoot/unparented-instances.png)](media/preview-troubleshoot/unparented-instances.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

- Leggere [Lavorare con modelli di Time Series](./time-series-insights-update-how-to-tsm.md).

- Informazioni sulle [forme JSON supportate.](./how-to-shape-query-json.md)

- Esaminare [la pianificazione e i limiti](./time-series-insights-update-plan.md) in Azure Time Series Insights Preview.Review planning and limits in Azure Time Series Insights Preview.
