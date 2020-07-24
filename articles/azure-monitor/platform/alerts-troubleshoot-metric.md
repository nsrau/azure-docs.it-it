---
title: Risoluzione dei problemi relativi agli avvisi delle metriche di Azure
description: Problemi comuni relativi agli avvisi delle metriche di monitoraggio di Azure e alle possibili soluzioni.
author: harelbr
ms.author: harelbr
ms.topic: reference
ms.date: 07/21/2020
ms.subservice: alerts
ms.openlocfilehash: 98cd7a4d31f4d7053426f44dd02a876759688cc7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87045231"
---
# <a name="troubleshooting-problems-in-azure-monitor-metric-alerts"></a>Risoluzione dei problemi negli avvisi relativi alle metriche di monitoraggio di Azure 

Questo articolo illustra i problemi comuni negli [avvisi delle metriche](alerts-metric-overview.md) di monitoraggio di Azure e come risolverli.

Gli avvisi di monitoraggio di Azure notificano in modo proattivo quando vengono rilevate condizioni importanti nei dati di monitoraggio. Consentono di identificare e risolvere i problemi prima che si palesino agli utenti. Per ulteriori informazioni sugli avvisi, vedere [Panoramica degli avvisi in Microsoft Azure](alerts-overview.md).

## <a name="metric-alert-should-have-fired-but-didnt"></a>L'avviso della metrica dovrebbe essere stato attivato ma non 

Se si ritiene che un avviso di metrica debba essere stato attivato ma non è stato attivato e non è stato trovato nella portale di Azure, provare a eseguire i passaggi seguenti:

1. **Configurazione** : esaminare la configurazione della regola di avviso della metrica per assicurarsi che sia configurata correttamente:
    - Verificare che il **tipo di aggregazione**, la **granularità dell'aggregazione (periodo)** e il **valore di soglia** o la **sensibilità** siano configurati come previsto.
    - Per una regola di avviso che utilizza soglie dinamiche, controllare se sono configurate impostazioni avanzate, in quanto il **numero di violazioni** può filtrare gli avvisi e **ignorare i dati prima** che possa influisca sulla modalità di calcolo delle soglie

       > [!NOTE] 
       > Le soglie dinamiche richiedono almeno 3 giorni e 30 campioni di metriche prima di diventare attive.

2. **Generato ma nessuna notifica** : esaminare l' [elenco degli avvisi](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2) attivati per verificare se è possibile individuare l'avviso generato. Se è possibile visualizzare l'avviso nell'elenco, ma si verifica un problema con alcune delle azioni o notifiche, vedere altre informazioni [qui](./alerts-troubleshoot.md#action-or-notification-on-my-alert-did-not-work-as-expected).

3. **Già attivo** : controllare se è già presente un avviso attivato per la serie temporale della metrica per cui si prevede di ricevere un avviso. Gli avvisi delle metriche sono avvisi con stato, di conseguenza, una volta generato un avviso in una serie temporale della metrica specifica, gli avvisi successivi in tale serie temporale non verranno attivati fino a quando il problema non viene più rilevato. Questa scelta di progettazione riduce il rumore. L'avviso viene risolto automaticamente quando la condizione di avviso non viene soddisfatta per tre valutazioni consecutive.

4. **Dimensioni usate** : se sono stati selezionati alcuni [valori di dimensione per una metrica](./alerts-metric-overview.md#using-dimensions), la regola di avviso monitora ogni singola serie temporale della metrica (definita dalla combinazione dei valori della dimensione) per una violazione di soglia. Per monitorare anche le serie temporali delle metriche aggregate (senza dimensioni selezionate), configurare un'altra regola di avviso sulla metrica senza selezionare le dimensioni.

5. **Granularità di aggregazione e ora** : se si visualizza la metrica usando i [grafici di metrica](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics), verificare che:
    * L' **aggregazione** selezionata nel grafico delle metriche è uguale a quella del **tipo di aggregazione** nella regola di avviso
    * La **granularità temporale** selezionata corrisponde alla **granularità di aggregazione (periodo)** nella regola di avviso (e non è impostata su' Automatic ')

## <a name="metric-alert-fired-when-it-shouldnt-have"></a>Avviso di metrica generato quando non dovrebbe avere

Se si ritiene che l'avviso della metrica non debba essere stato attivato ma è stato fatto, i passaggi seguenti potrebbero aiutare a risolvere il problema.

1. Esaminare l' [elenco degli avvisi](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2) attivati per individuare l'avviso attivato e fare clic per visualizzarne i dettagli. Esaminare le informazioni fornite in **perché questo avviso** è stato attivato? per visualizzare il grafico delle metriche, il **valore della metrica**e il **valore soglia** nel momento in cui è stato attivato l'avviso.

    > [!NOTE] 
    > Se si usa un tipo di condizione per le soglie dinamiche e si ritiene che le soglie usate non siano corrette, fornire commenti e suggerimenti tramite l'icona imbronciata. Questo feedback avrà un effetto sulla ricerca algoritmica di machine learning e contribuirà a migliorare i rilevamenti futuri.

2. Se sono stati selezionati più valori di dimensione per una metrica, l'avviso viene attivato quando **una** serie temporale metrica, definita dalla combinazione di valori di dimensione, viola la soglia. Per altre informazioni sull'uso delle dimensioni negli avvisi delle metriche, vedere [qui](./alerts-metric-overview.md#using-dimensions).

3. Esaminare la configurazione della regola di avviso per assicurarsi che sia configurata correttamente:
    - Verificare che il **tipo di aggregazione**, la **granularità dell'aggregazione (periodo)** e il **valore di soglia** o la **sensibilità** siano configurati come previsto.
    - Per una regola di avviso che utilizza soglie dinamiche, controllare se sono configurate impostazioni avanzate, in quanto il **numero di violazioni** può filtrare gli avvisi e **ignorare i dati prima** che possa influisca sulla modalità di calcolo delle soglie

   > [!NOTE]
   > Le soglie dinamiche richiedono almeno 3 giorni e 30 campioni di metriche prima di diventare attive.

4. Se si visualizza la metrica usando un [grafico delle metriche](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics), verificare che:
    - L' **aggregazione** selezionata nel grafico delle metriche è uguale a quella del **tipo di aggregazione** nella regola di avviso
    - La **granularità temporale** selezionata corrisponde alla **granularità di aggregazione (periodo)** nella regola di avviso (e non è impostata su' Automatic ')

5. Se l'avviso è stato generato quando sono già stati generati avvisi che monitorano gli stessi criteri (che non sono stati risolti), controllare se la regola di avviso è stata configurata con la proprietà *Automitigate* impostata su **false** (questa proprietà può essere configurata solo tramite REST/PowerShell/CLI, quindi controllare lo script usato per distribuire la regola di avviso). In tal caso, la regola di avviso non risolve automaticamente gli avvisi attivati e non richiede la risoluzione di un avviso attivato prima dell'attivazione.


## <a name="cant-find-the-metric-to-alert-on---virtual-machines-guest-metrics"></a>Non è possibile trovare la metrica per l'avviso sulle metriche Guest delle macchine virtuali

Per inviare un avviso sulle metriche del sistema operativo guest delle macchine virtuali (ad esempio: memoria, spazio su disco), assicurarsi di avere installato l'agente richiesto per raccogliere i dati nelle metriche di monitoraggio di Azure:
- [Per macchine virtuali Windows](./collect-custom-metrics-guestos-resource-manager-vm.md)
- [Per macchine virtuali Linux](./collect-custom-metrics-linux-telegraf.md)

Per ulteriori informazioni sulla raccolta di dati dal sistema operativo guest di una macchina virtuale, vedere [qui](../insights/monitor-vm-azure.md#guest-operating-system).
    
> [!NOTE] 
> Se sono state configurate le metriche Guest da inviare a un'area di lavoro Log Analytics, le metriche vengono visualizzate sotto la risorsa Log Analytics area di lavoro e inizieranno a visualizzare i dati **solo** dopo la creazione di una regola di avviso che li monitora. A tal proposito, seguire la procedura per [configurare un avviso delle metriche per i log](./alerts-metric-logs.md#configuring-metric-alert-for-logs).

## <a name="cant-find-the-metric-to-alert-on"></a>Impossibile trovare la metrica per l'avviso

Se si sta cercando di ricevere un avviso per una metrica specifica, ma non è possibile visualizzare alcuna metrica per la risorsa, [controllare se il tipo di risorsa è supportato per gli avvisi delle metriche](./alerts-metric-near-real-time.md).
Se è possibile visualizzare alcune metriche per la risorsa ma non si riesce a trovare una metrica specifica, [verificare se tale metrica è disponibile](./metrics-supported.md)e, in caso affermativo, vedere la descrizione della metrica per verificare se è disponibile solo in versioni o edizioni specifiche della risorsa.

## <a name="cant-find-the-metric-dimension-to-alert-on"></a>Impossibile trovare la dimensione metrica su cui inviare l'avviso

Se si sta cercando di avvisare su [valori di dimensione specifici di una metrica](./alerts-metric-overview.md#using-dimensions), ma questi valori non sono stati trovati, tenere presente quanto segue:

1. La visualizzazione dei valori delle dimensioni nell'elenco **Valori delle dimensioni** potrebbe richiedere alcuni minuti
1. I valori delle dimensioni visualizzati sono basati sui dati delle metriche raccolti negli ultimi tre giorni
1. Se il valore della dimensione non è ancora stato emesso, fare clic sul segno "+" per aggiungere un valore personalizzato
1. Per segnalare tutti i possibili valori di una dimensione (compresi i valori futuri), selezionare la casella di controllo ' Seleziona *'

## <a name="metric-alert-rules-still-defined-on-a-deleted-resource"></a>Regole di avviso delle metriche ancora definite in una risorsa eliminata 

Quando si elimina una risorsa di Azure, le regole di avviso delle metriche associate non vengono eliminate automaticamente. Per eliminare le regole di avviso associate a una risorsa che è stata eliminata:

1. Aprire il gruppo di risorse in cui è stata definita la risorsa eliminata
1. Nell'elenco che mostra le risorse selezionare la casella di controllo **Mostra tipi nascosti**
1. Filtrare l'elenco in base al tipo = = **microsoft.insights/metricalerts**
1. Selezionare le regole di avviso pertinenti e selezionare **Elimina** .

## <a name="make-metric-alerts-occur-every-time-my-condition-is-met"></a>Crea avvisi di metrica ogni volta che viene soddisfatta la condizione

Gli avvisi delle metriche sono con stato per impostazione predefinita e pertanto non vengono generati avvisi aggiuntivi se è già presente un avviso attivato in una determinata serie temporale. Se si vuole creare una regola di avviso metrica specifica senza stato e ricevere un avviso per ogni valutazione in cui viene soddisfatta la condizione di avviso, creare la regola di avviso a livello di codice (ad esempio, tramite [Gestione risorse](./alerts-metric-create-templates.md), [PowerShell](/powershell/module/az.monitor/?view=azps-3.6.1), [Rest](/rest/api/monitor/metricalerts/createorupdate), [CLI](/cli/azure/monitor/metrics/alert?view=azure-cli-latest)) e impostare la proprietà *automitigate* su "false".

> [!NOTE] 
> La creazione di una regola di avviso della metrica senza stato impedisce la risoluzione degli avvisi attivati, quindi anche dopo che la condizione non è più soddisfatta, gli avvisi attivati rimarranno in uno stato attivato fino al periodo di conservazione di 30 giorni.

## <a name="define-an-alert-rule-on-a-custom-metric-that-isnt-emitted-yet"></a>Definire una regola di avviso per una metrica personalizzata che non è ancora stata emessa

Quando si crea una regola di avviso per la metrica, il nome della metrica viene convalidato in base all' [API delle definizioni delle metriche](https://docs.microsoft.com/rest/api/monitor/metricdefinitions/list) per assicurarsi che esista. In alcuni casi, si vuole creare una regola di avviso su una metrica personalizzata anche prima che venga emessa. Ad esempio, quando si crea (usando un modello ARM) una risorsa Application Insights che emetterà una metrica personalizzata, insieme a una regola di avviso che monitora tale metrica.

Per evitare che la distribuzione abbia esito negativo quando si tenta di convalidare le definizioni della metrica personalizzata, è possibile usare il parametro *skipMetricValidation* nella sezione criteri della regola di avviso, che causerà l'omissione della convalida della metrica. Vedere l'esempio seguente per informazioni su come usare questo parametro in un modello ARM (per esempi completi di modelli ARM per la creazione di regole di avviso delle metriche, vedere [qui]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates)).

```json
"criteria": {
    "odata.type": "Microsoft.Azure.Monitor.SingleResourceMultipleMetricCriteria",
        "allOf": [
            {
                    "name" : "condition1",
                        "metricName": "myCustomMetric",
                "metricNamespace": "myCustomMetricNamespace",
                        "dimensions":[],
                        "operator": "GreaterThan",
                        "threshold" : 10,
                        "timeAggregation": "Average",
                    "skipMetricValidation": true
        }
              ]
        }
```

## <a name="metric-alert-rules-quota-too-small"></a>Quota regole di avviso metrica troppo piccola

Il numero di regole di avviso per la metrica consentito per ogni sottoscrizione è soggetto ai [limiti della quota](../service-limits.md).

Se è stato raggiunto il limite di quota, i passaggi seguenti possono contribuire a risolvere il problema:
1. Provare a eliminare o disabilitare le regole di avviso delle metriche che non vengono più usate.

2. Passare a usare le regole di avviso della metrica che monitorano più risorse. Con questa funzionalità, una singola regola di avviso può monitorare più risorse usando solo una regola di avviso calcolata in base alla quota. Per altre informazioni su questa funzionalità e sui tipi di risorse supportati, vedere [qui](./alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor).

3. Se è necessario aumentare il limite di quota, aprire una richiesta di supporto e fornire le informazioni seguenti:

    - ID sottoscrizione per i quali è necessario aumentare il limite di quota
    - Tipo di risorsa per l'aumento della quota: **avvisi metrica** o **avvisi metrica (versione classica)**
    - Limite di quota richiesto

## <a name="check-total-number-of-metric-alert-rules"></a>Controllare il numero totale di regole di avviso per la metrica

Per verificare l'utilizzo corrente delle regole di avviso delle metriche, attenersi alla procedura riportata di seguito.

### <a name="from-the-azure-portal"></a>Dal portale di Azure

1. Aprire la schermata **Avvisi** e fare clic su **Gestisci regole di avviso**
2. Filtrare per la sottoscrizione pertinente usando il controllo elenco a discesa **sottoscrizione**
3. Assicurarsi di non filtrare per un gruppo di risorse, un tipo di risorsa o una risorsa specifici
4. Nel controllo elenco a discesa **tipo di segnale** selezionare **metrica**
5. Verificare che il controllo elenco a discesa **stato** sia impostato su **abilitato**
6. Il numero totale di regole di avviso delle metriche viene visualizzato sopra l'elenco delle regole di avviso

### <a name="from-api"></a>Nell'API

- PowerShell - [Get-AzMetricAlertRuleV2](/powershell/module/az.monitor/get-azmetricalertrulev2?view=azps-3.7.0)
- REST API - [Elenco per sottoscrizione](/rest/api/monitor/metricalerts/listbysubscription)
- Interfaccia della riga di comando di Azure - [az monitor metrics alert list](/cli/azure/monitor/metrics/alert?view=azure-cli-latest#az-monitor-metrics-alert-list)

## <a name="managing-alert-rules-using-resource-manager-templates-rest-api-powershell-or-azure-cli"></a>Gestione delle regole di avviso tramite modelli di Gestione risorse, API REST, PowerShell o l'interfaccia della riga di comando di Azure

Se si verificano problemi durante la creazione, l'aggiornamento, il recupero o l'eliminazione degli avvisi delle metriche usando Gestione risorse modelli, l'API REST, PowerShell o l'interfaccia della riga di comando di Azure, i passaggi seguenti possono contribuire a risolvere il problema.

### <a name="resource-manager-templates"></a>Modelli di Gestione risorse

- Rivedere l'elenco di [Errori di distribuzione di Azure comuni](../../azure-resource-manager/templates/common-deployment-errors.md) e provare a risolvere il problema
- Per verificare che tutti i parametri siano stati passati correttamente, vedere gli [avvisi sulle metriche Azure Resource Manager esempi di modelli](./alerts-metric-create-templates.md) .

### <a name="rest-api"></a>API REST

Esaminare la [Guida dell'API REST](/rest/api/monitor/metricalerts/) per verificare che tutti i parametri siano stati passati correttamente

### <a name="powershell"></a>PowerShell

Assicurarsi di usare i cmdlet di PowerShell corretti per gli avvisi delle metriche:

- I cmdlet di PowerShell per gli avvisi delle metriche sono disponibili nel [modulo Az.Monitor](/powershell/module/az.monitor/?view=azps-3.6.1)
- Assicurarsi di usare i cmdlet che terminano con ' v2' per gli avvisi della metrica nuovi (non classici), ad esempio [Add-AzMetricAlertRuleV2](/powershell/module/az.monitor/add-azmetricalertrulev2?view=azps-3.6.1)

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Assicurarsi di usare i comandi dell'interfaccia della riga di comando corretti per gli avvisi delle metriche:

- I comandi dell'interfaccia della riga di comando per gli avvisi delle metriche iniziano con `az monitor metrics alert`. Rivedere le [informazioni di riferimento sull'interfaccia della riga di comando di Azure](/cli/azure/monitor/metrics/alert?view=azure-cli-latest) per apprenderne la sintassi.
- È possibile visualizzare un [esempio che illustra come usare l'interfaccia della riga di comando per gli avvisi delle metriche](./alerts-metric.md#with-azure-cli)
- Per generare un avviso su una metrica personalizzata, assicurarsi di anteporre al nome della metrica lo spazio dei nomi pertinente: NAMESPACE.METRIC

### <a name="general"></a>Generale

- Se si riceve un `Metric not found` errore:

   - Per una metrica della piattaforma: assicurarsi di usare il nome della **metrica** nella [pagina metrica supportata di monitoraggio di Azure](./metrics-supported.md)e non il **nome visualizzato della metrica** .

   - Per una metrica personalizzata: assicurarsi che la metrica sia già emessa (non è possibile creare una regola di avviso su una metrica personalizzata che non esiste ancora) e che si fornisca lo spazio dei nomi della metrica personalizzata (vedere [un esempio di](./alerts-metric-create-templates.md#template-for-a-static-threshold-metric-alert-that-monitors-a-custom-metric)modello ARM)

- Se si stanno creando [avvisi sulle metriche nei log](./alerts-metric-logs.md), verificare che siano incluse le dipendenze appropriate. Vedere il [modello di esempio](./alerts-metric-logs.md#resource-template-for-metric-alerts-for-logs).

- Se si sta creando una regola di avviso che contiene più criteri, tenere presente i vincoli seguenti:

   - È possibile selezionare un solo valore per dimensione all'interno di ogni criterio
   - Non è possibile usare "\*" come valore di dimensione
   - Quando le metriche configurate in criteri diversi supportano la stessa dimensione, un valore della dimensione configurato deve essere impostato in modo esplicito nello stesso modo per tutte le metriche (vedere [un esempio di](./alerts-metric-create-templates.md#template-for-a-static-threshold-metric-alert-that-monitors-multiple-criteria)modello Gestione risorse)


## <a name="no-permissions-to-create-metric-alert-rules"></a>Nessuna autorizzazione per la creazione di regole di avviso delle metriche

Per creare una regola di avviso per la metrica, è necessario disporre delle autorizzazioni seguenti:

- Autorizzazione Read per la risorsa di destinazione della regola di avviso
- Autorizzazione di scrittura per il gruppo di risorse in cui viene creata la regola di avviso (se si sta creando la regola di avviso dal portale di Azure, la regola di avviso viene creata nello stesso gruppo di risorse in cui risiede la risorsa di destinazione)
- Autorizzazione Read per qualsiasi gruppo di azione associato alla regola di avviso (se applicabile)


## <a name="naming-restrictions-for-metric-alert-rules"></a>Limitazioni di denominazione per le regole di avviso delle metriche

Prendere in considerazione le restrizioni seguenti per i nomi delle regole di avviso delle metriche:

- Non è possibile modificare i nomi delle regole di avviso delle metriche (rinominate) dopo la creazione
- I nomi delle regole di avviso delle metriche devono essere univoci in un gruppo di risorse
- I nomi delle regole di avviso per la metrica non possono contenere i caratteri seguenti: * # & +:  < > ? @ % { } \ / 
- I nomi delle regole di avviso della metrica non possono terminare con il carattere seguente:.


## <a name="restrictions-when-using-dimensions-in-a-metric-alert-rule-with-multiple-conditions"></a>Restrizioni relative all'uso di dimensioni in una regola di avviso metrica con più condizioni

Gli avvisi delle metriche supportano gli avvisi sulle metriche multidimensionali e supportano la definizione di più condizioni (fino a 5 condizioni per regola di avviso).

Quando si usano le dimensioni in una regola di avviso che contiene più condizioni, tenere presenti i vincoli seguenti:
- È possibile selezionare un solo valore per dimensione all'interno di ogni condizione.
- Non è possibile usare l'opzione per "selezionare tutti i valori correnti e futuri" (Select \* ).
- Quando le metriche configurate in condizioni diverse supportano la stessa dimensione, un valore della dimensione configurato deve essere impostato in modo esplicito nello stesso modo per tutte le metriche (nelle condizioni pertinenti).
Ad esempio:
    - Si consideri una regola di avviso metrica definita in un account di archiviazione e che monitora due condizioni:
        * Totale **transazioni** > 5
        * Media **SuccessE2ELatency** > 250 ms
    - Desidero aggiornare la prima condizione e monitorare solo le transazioni in cui la dimensione **ApiName** è uguale a *"GetBlob"*
    - Poiché le metriche **Transactions** e **SuccessE2ELatency** supportano una dimensione **ApiName** , sarà necessario aggiornare entrambe le condizioni e fare in modo che entrambe specifichino la dimensione **ApiName** con un valore *"GetBlob"* .


## <a name="next-steps"></a>Passaggi successivi

- Per informazioni generali sulla risoluzione dei problemi relativi a avvisi e notifiche, vedere [risoluzione dei problemi negli avvisi di monitoraggio di Azure](alerts-troubleshoot.md).
