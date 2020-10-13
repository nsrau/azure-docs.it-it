---
title: Diagnosticare e risolvere i problemi relativi a un ambiente Gen2-Azure Time Series Insights | Microsoft Docs
description: Informazioni su come diagnosticare e risolvere i problemi relativi a un ambiente Gen2 Azure Time Series Insights.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/01/2020
ms.custom: seodec18
ms.openlocfilehash: b994e8ce34319da4827d389b49e23ed6e5bcde95
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91653758"
---
# <a name="diagnose-and-troubleshoot-an-azure-time-series-insights-gen2-environment"></a>Diagnosticare e risolvere i problemi relativi a un ambiente Gen2 Azure Time Series Insights

Questo articolo riepiloga diversi problemi comuni che possono verificarsi quando si lavora con l'ambiente di Azure Time Series Insights Gen2. L'articolo descrive anche possibili cause e soluzioni per ogni problema.

## <a name="problem-i-cant-find-my-environment-in-the-gen2-explorer"></a>Problema: non è possibile trovare l'ambiente in Gen2 Explorer

Questo problema può verificarsi se non si hanno le autorizzazioni per accedere all'ambiente Time Series Insights. Gli utenti necessitano di un ruolo di lettore a livello di accesso per visualizzare l'ambiente Time Series Insights. Per verificare i livelli di accesso correnti e concedere accesso aggiuntivo, passare alla sezione **criteri di accesso ai dati** nella Time Series Insights risorsa nel [portale di Azure](https://portal.azure.com/).

  [![Verificare i criteri di accesso ai dati.](media/preview-troubleshoot/verify-data-access-policies.png)](media/preview-troubleshoot/verify-data-access-policies.png#lightbox)

## <a name="problem-no-data-is-seen-in-the-gen2-explorer"></a>Problema: non vengono visualizzati dati in Esplora Gen2

Esistono diversi motivi comuni per cui i dati potrebbero non essere visualizzati nel [Azure Time Series Insights Gen2 Explorer](https://insights.timeseries.azure.com/preview).

- L'origine evento potrebbe non ricevere correttamente i dati.

    Verificare che l'origine evento, ovvero un hub eventi o un hub IoT, riceva dati da tag o istanze. Per verificare ciò, passare alla pagina di panoramica della risorsa nel portale di Azure.

    [![Vedere Cenni preliminari sulle metriche del dashboard.](media/preview-troubleshoot/verify-dashboard-metrics.png)](media/preview-troubleshoot/verify-dashboard-metrics.png#lightbox)

- I dati dell'origine evento non sono in formato JSON.

    Time Series Insights supporta solo dati in formato JSON. Per gli esempi JSON, leggere le [forme JSON supportate](./how-to-shape-query-json.md).

- Alla chiave dell'origine evento manca un'autorizzazione obbligatoria.

  - Per un hub IoT è necessario fornire la chiave con l'autorizzazione di **connessione al servizio**.

    [![Verificare le autorizzazioni dell'hub Internet.](media/preview-troubleshoot/verify-correct-permissions.png)](media/preview-troubleshoot/verify-correct-permissions.png#lightbox)

    - I criteri **iothubowner** e **servizio** funzionano perché hanno l'autorizzazione di **connessione al servizio** .

  - Per un hub eventi è necessario fornire la chiave con l'autorizzazione di **ascolto**.
  
    [![Verificare le autorizzazioni dell'hub eventi.](media/preview-troubleshoot/verify-eh-permissions.png)](media/preview-troubleshoot/verify-eh-permissions.png#lightbox)

    - Sia i criteri di **lettura** che quelli di **gestione** funzionano perché hanno l'autorizzazione **Listen** .

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

## <a name="problem-data-was-showing-but-now-ingestion-has-stopped"></a>Problema: i dati sono stati visualizzati, ma ora l'inserimento è stato arrestato

- La chiave dell'origine evento potrebbe essere stata rigenerata e l'ambiente Gen2 richiede la nuova chiave di origine evento.

Questo problema si verifica quando la chiave specificata durante la creazione dell'origine evento non è più valida. I dati di telemetria vengono visualizzati nell'hub, ma nessun messaggio in ingresso ha ricevuto messaggi in Time Series Insights. Se non si è certi che la chiave sia stata rigenerata, è possibile cercare il log attività di hub eventi per "creare o aggiornare le regole di autorizzazione dello spazio dei nomi" oppure cercare "crea o aggiorna la risorsa IotHub" per l'hub Internet.

Per aggiornare l'ambiente di Time Series Insights Gen2 con la nuova chiave, aprire la risorsa Hub nel portale di Azure e copiare la nuova chiave. Passare alla risorsa TSI e fare clic su origini eventi.

   [![Screenshot mostra la voce di menu della risorsa T i con origini eventi denominata.](media/preview-troubleshoot/update-hub-key-step-1.png)](media/preview-troubleshoot/update-hub-key-step-1.png#lightbox)

Selezionare le origini eventi per cui è stato interrotto l'inserimento, incollare la nuova chiave e fare clic su Salva.

   [![Screenshot mostra la risorsa T S I con la chiave del criterio Hub I o T immessa.](media/preview-troubleshoot/update-hub-key-step-2.png)](media/preview-troubleshoot/update-hub-key-step-2.png#lightbox)

## <a name="problem-my-event-sources-timestamp-property-name-doesnt-work"></a>Problema: il nome della proprietà timestamp dell'origine evento non funziona

Verificare che il nome e il valore siano conformi alle regole seguenti:

- Il nome della proprietà Timestamp fa distinzione fra maiuscole e minuscole.
- Il valore della proprietà timestamp che deriva dall'origine evento come stringa JSON ha il formato `yyyy-MM-ddTHH:mm:ss.FFFFFFFK` . Un esempio di tale stringa è `"2008-04-12T12:53Z"`.

Il modo più semplice per garantire che il nome della proprietà timestamp venga acquisito e funzioni correttamente consiste nell'usare Time Series Insights Gen2 Explorer. All'interno del Time Series Insights Gen2 Explorer, utilizzare il grafico per selezionare un periodo di tempo dopo aver specificato il nome della proprietà timestamp. Fare clic con il pulsante destro del mouse sulla selezione e selezionare l'opzione **Esplora eventi**. L'intestazione della prima colonna contiene il nome della proprietà Timestamp. Deve avere `($ts)` accanto alla parola `Timestamp`, invece di:

- `(abc)`, che indica che Time Series Insights legge i valori dei dati come stringhe.
- Icona del **Calendario** , che indica che Time Series Insights legge il valore dei dati come DateTime.
- `#`, che indica che Time Series Insights legge i valori dei dati come integer.

Se la proprietà timestamp non è specificata in modo esplicito, come timestamp predefinito viene usato il tempo di accodamento dell'hub di eventi o dell'hub eventi.

## <a name="problem-i-cant-view-data-from-my-warm-store-in-the-explorer"></a>Problema: non è possibile visualizzare i dati dal negozio caldo in Esplora risorse

- È possibile che sia stato eseguito di recente il provisioning del negozio in caldo e che i dati continuino a propagarsi.
- È possibile che sia stato eliminato l'archivio a caldo, nel qual caso i dati verrebbero persi.

## <a name="problem-i-cant-view-or-edit-my-time-series-model"></a>Problema: non è possibile visualizzare o modificare il modello Time Series

- È possibile accedere a un ambiente Time Series Insights S1 o S2.

   I modelli Time Series sono supportati solo negli ambienti con pagamento in base al consumo. Per altre informazioni su come accedere all'ambiente S1 o S2 dal Time Series Insights Gen2 Explorer, vedere visualizzare i [dati nella finestra di esplorazione](./time-series-insights-update-explorer.md).

   [![Nessun evento nell'ambiente.](media/preview-troubleshoot/troubleshoot-no-events.png)](media/preview-troubleshoot/troubleshoot-no-events.png#lightbox)

- Non si dispone delle autorizzazioni per visualizzare e modificare il modello.

   Gli utenti devono accedere a livello di collaboratore per modificare e visualizzare il modello Time Series. Per verificare i livelli di accesso correnti e concedere l'accesso aggiuntivo, passare alla sezione **criteri di accesso ai dati** nella risorsa Time Series Insights nel portale di Azure.

## <a name="problem-all-my-instances-in-the-gen2-explorer-lack-a-parent"></a>Problema: tutte le istanze di Gen2 Explorer non dispongono di un elemento padre

Questo problema può verificarsi se nell'ambiente non è definita una gerarchia del modello Time Series. Per altre informazioni, vedere informazioni su come [usare i modelli Time Series](/azure/time-series-insights/time-series-insights-overview).

  [![Nelle istanze con non padre verrà visualizzato un avviso.](media/preview-troubleshoot/unparented-instances.png)](media/preview-troubleshoot/unparented-instances.png#lightbox)

## <a name="problem-power-bi-connector-shows-unable-to-connect"></a>Problema: Power BI connettore Mostra "Impossibile connettersi"

Questo problema può verificarsi se non si usa la versione più recente del connettore Power BI in Power BI Desktop.

[![Screenshot che mostra la finestra di dialogo non è possibile connettersi.](media/preview-troubleshoot/power-bi-unable-to-connect.png)](media/preview-troubleshoot/power-bi-unable-to-connect.png#lightbox)

- Controllare la versione del Power BI Desktop e verificare che sia in uso la versione 2020 di luglio. In caso contrario, aggiornare la Power BI Desktop ed eseguire di nuovo il connettore.

## <a name="next-steps"></a>Passaggi successivi

- Leggere le informazioni su come [usare i modelli Time Series](/azure/time-series-insights/time-series-insights-overview).

- Informazioni sulle [forme JSON supportate](./how-to-shape-query-json.md).

- Esaminare la [pianificazione e i limiti](./time-series-insights-update-plan.md) in Azure Time Series Insights Gen2.
