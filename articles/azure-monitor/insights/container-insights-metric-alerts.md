---
title: Avvisi relativi alle metriche da monitoraggio di Azure per i contenitori | Microsoft Docs
description: Questo articolo esamina gli avvisi della metrica consigliati disponibili da monitoraggio di Azure per i contenitori in anteprima pubblica.
ms.topic: conceptual
ms.date: 07/21/2020
ms.openlocfilehash: c8b75b0d9b22658253c4637bd6507144575934de
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87097798"
---
# <a name="recommended-metric-alerts-preview-from-azure-monitor-for-containers"></a>Avvisi metrica consigliati (anteprima) da monitoraggio di Azure per i contenitori

Per generare un avviso sui problemi relativi alle risorse di sistema quando si verificano picchi di domanda e in esecuzione near Capacity, con monitoraggio di Azure per i contenitori è necessario creare un avviso di log in base ai dati sulle prestazioni archiviati nei log di monitoraggio di Azure Il monitoraggio di Azure per i contenitori include ora le regole di avviso delle metriche preconfigurate per il cluster AKS, disponibile in anteprima pubblica.

Questo articolo esamina l'esperienza e fornisce indicazioni sulla configurazione e la gestione di queste regole di avviso.

Se non si ha familiarità con gli avvisi di monitoraggio di Azure, vedere [Panoramica degli avvisi in Microsoft Azure prima di](../platform/alerts-overview.md) iniziare. Per altre informazioni sugli avvisi delle metriche, vedere la pagina relativa agli [avvisi delle metriche in monitoraggio di Azure](../platform/alerts-metric-overview.md).

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare quanto segue:

* Le metriche personalizzate sono disponibili solo in un subset di aree di Azure. Un elenco di aree supportate è documentato [qui](../platform/metrics-custom-overview.md#supported-regions).

* Per supportare gli avvisi sulle metriche e l'introduzione di altre metriche, la versione minima dell'agente richiesta è **Microsoft/OMS: ciprod05262020**.

    Per verificare che il cluster esegua la versione più recente dell'agente, è possibile effettuare una delle operazioni seguenti:

    * Eseguire il comando: `kubectl describe <omsagent-pod-name> --namespace=kube-system` . Nello stato restituito prendere nota del valore in **immagine** per omsagent nella sezione *contenitori* dell'output. 
    * Nella scheda **nodi** selezionare il nodo del cluster e nel riquadro **Proprietà** a destra, annotare il valore in **tag immagine agente**.

    Il valore visualizzato deve essere una versione successiva a **ciprod05262020**. Se il cluster ha una versione precedente, seguire l' [aggiornamento dell'agente nei passaggi del cluster AKS](container-insights-manage-agent.md#upgrade-agent-on-aks-cluster) per ottenere la versione più recente.
    
    Per ulteriori informazioni relative alla versione dell'agente, vedere [cronologia delle versioni degli agenti](https://github.com/microsoft/docker-provider/tree/ci_feature_prod). Per verificare la raccolta delle metriche, è possibile usare Esplora metriche di monitoraggio di Azure e verificare dallo **spazio dei nomi della metrica** che sono elencate le **informazioni dettagliate** . In tal caso, è possibile iniziare a configurare gli avvisi.

## <a name="alert-rules-overview"></a>Cenni preliminari sulle regole di avviso

Per segnalare gli aspetti importanti, monitoraggio di Azure per i contenitori include gli avvisi delle metriche seguenti per i cluster AKS:

|Nome| Description |Soglia predefinita |
|----|-------------|------------------|
|% CPU del contenitore media |Calcola la CPU media usata per ogni contenitore.|Quando l'utilizzo medio della CPU per ogni contenitore è maggiore del 95%.| 
|Percentuale media memoria working set contenitore |Calcola la memoria working set media utilizzata per ogni contenitore.|Quando l'utilizzo medio working set memoria per contenitore è maggiore del 95%. |
|Average CPU % (% media CPU) |Calcola l'utilizzo medio della CPU per nodo. |Quando l'utilizzo medio della CPU del nodo è superiore al 80% |
|Percentuale di utilizzo medio del disco |Calcola l'utilizzo medio del disco per un nodo.|Quando l'utilizzo del disco per un nodo è superiore al 80%. |
|Percentuale di memoria working set media |Calcola la memoria del working set media per un nodo. |Quando la memoria del working set media per un nodo è superiore al 80%. |
|Riavvio del numero di contenitori |Calcola il numero di contenitori di riavvio. | Quando i riavvii del contenitore sono maggiori di 0. |
|Conteggi Pod non riusciti |Calcola se un pod è in stato di errore.|Quando un numero di pod in stato di errore è maggiore di 0. |
|Stato nobattistrada nodo |Calcola se uno dei nodi è in uno stato non più battistrada.|Quando un numero di nodi nello stato nobattistrada è maggiore di 0. |
|Contenitori rimasti chiusi |Calcola il numero di contenitori rimasti chiusi. |Quando un numero di contenitori di memoria insufficiente è maggiore di 0. |
|% Pod pronti |Calcola lo stato medio pronto dei pod. |Quando lo stato pronto dei Pod è inferiore al 80%.|
|Conteggio processi completati |Calcola il numero di processi completati più di sei ore fa. |Quando il numero di processi non aggiornati precedenti a sei ore è maggiore di 0.|

Sono presenti proprietà comuni in tutte le regole di avviso:

* Tutte le regole di avviso sono basate sulle metriche.

* Tutte le regole di avviso sono disabilitate per impostazione predefinita.

* Tutte le regole di avviso vengono valutate una volta al minuto e riguardano gli ultimi 5 minuti di dati.

* Per impostazione predefinita, alle regole degli avvisi non è assegnato un gruppo di azione. È possibile aggiungere un [gruppo di azioni](../platform/action-groups.md) all'avviso selezionando un gruppo di azioni esistente o creando un nuovo gruppo di azione durante la modifica della regola di avviso.

* È possibile modificare la soglia per le regole di avviso modificandone direttamente i valori. Tuttavia, fare riferimento alle linee guida fornite in ogni regola di avviso prima di modificarne la soglia.

Le metriche basate sugli avvisi seguenti hanno caratteristiche di comportamento univoco rispetto alle altre metriche:

* la metrica *completedJobsCount* viene inviata solo quando sono presenti processi completati più di sei ore fa.

* la metrica *containerRestartCount* viene inviata solo quando vengono riavviati i contenitori.

* la metrica *oomKilledContainerCount* viene inviata solo quando sono presenti contenitori uccisi da memoria insufficiente.

* le metriche *cpuExceededPercentage*, *memoryRssExceededPercentage*e *memoryWorkingSetExceededPercentage* vengono inviate quando i valori della CPU, della memoria RSS e del working set della memoria superano la soglia configurata (la soglia predefinita è 95%). Queste soglie sono esclusive della soglia della condizione di avviso specificata per la regola di avviso corrispondente. Ciò significa che se si vuole raccogliere queste metriche e analizzarle da [Esplora metriche](../platform/metrics-getting-started.md), è consigliabile configurare la soglia su un valore inferiore a quello della soglia di avviso. È possibile eseguire l'override della configurazione relativa alle impostazioni di raccolta per le soglie di utilizzo delle risorse del contenitore nel file ConfigMaps nella sezione `[alertable_metrics_configuration_settings.container_resource_utilization_thresholds]` . Vedere la sezione [configurare le metriche per gli avvisi ConfigMaps](#configure-alertable-metrics-in-configmaps) per informazioni dettagliate sulla configurazione del file di configurazione di ConfigMap.

## <a name="metrics-collected"></a>Metriche raccolte

Le metriche seguenti sono abilitate e raccolte, se non diversamente specificato, come parte di questa funzionalità:

|Spazio dei nomi delle metriche |Metrica |Descrizione |
|---------|----|------------|
|Insights. contenitore/nodi |cpuUsageMillicores |Utilizzo della CPU in millicore da host.|
|Insights. contenitore/nodi |cpuUsagePercentage |Percentuale di utilizzo della CPU per nodo.|
|Insights. contenitore/nodi |memoryRssBytes |Utilizzo RSS di memoria in byte da host.|
|Insights. contenitore/nodi |memoryRssPercentage |Percentuale utilizzo RSS memoria per host.|
|Insights. contenitore/nodi |memoryWorkingSetBytes |Utilizzo working set della memoria in byte da host.|
|Insights. contenitore/nodi |memoryWorkingSetPercentage |Percentuale di utilizzo del working set della memoria per host.|
|Insights. contenitore/nodi |nodesCount |Conteggio dei nodi in base allo stato.|
|Insights. contenitore/nodi |diskUsedPercentage |Percentuale del disco utilizzato nel nodo dal dispositivo.|
|Insights. container/Pod |podCount |Numero di pod per controller, spazio dei nomi, nodo e fase.|
|Insights. container/Pod |completedJobsCount |I processi completati contano la soglia configurabile dall'utente precedente (il valore predefinito è sei ore) per controller, spazio dei nomi Kubernetes. |
|Insights. container/Pod |restartingContainerCount |Conteggio dei riavvii del contenitore per controller, spazio dei nomi Kubernetes.|
|Insights. container/Pod |oomKilledContainerCount |Numero di contenitori OOMkilled per controller, spazio dei nomi Kubernetes.|
|Insights. container/Pod |podReadyPercentage |Percentuale di Pod nello stato pronto per controller, spazio dei nomi Kubernetes.|
|Insights. contenitore/contenitori |cpuExceededPercentage |Percentuale di utilizzo della CPU per i contenitori che supera la soglia configurabile dall'utente (il valore predefinito è 95,0) per nome del contenitore, nome del controller, spazio dei nomi Kubernetes, nome del Pod.<br> Raccolti  |
|Insights. contenitore/contenitori |memoryRssExceededPercentage |Percentuale di memoria RSS per i contenitori che supera la soglia configurabile dall'utente (il valore predefinito è 95,0) per nome del contenitore, nome del controller, spazio dei nomi Kubernetes, nome del Pod.|
|Insights. contenitore/contenitori |memoryWorkingSetExceededPercentage |Percentuale working set della memoria per i contenitori che superano la soglia configurabile dall'utente (il valore predefinito è 95,0) per nome del contenitore, nome del controller, spazio dei nomi Kubernetes, nome del Pod.|

## <a name="enable-alert-rules"></a>Abilita regole di avviso

Seguire questa procedura per abilitare gli avvisi delle metriche in monitoraggio di Azure dalla portale di Azure. Per abilitare l'uso di un modello di Gestione risorse, vedere [abilitare con un modello di gestione risorse](#enable-with-a-resource-manager-template).

### <a name="from-the-azure-portal"></a>Dal portale di Azure

Questa sezione illustra l'abilitazione di monitoraggio di Azure per l'avviso della metrica dei contenitori (anteprima) dal portale di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. L'accesso alla funzionalità di avviso delle metriche di monitoraggio di Azure per i contenitori (anteprima) è disponibile direttamente da un cluster AKS selezionando **Insights** nel riquadro a sinistra nel portale di Azure.

3. Nella barra dei comandi selezionare **avvisi consigliati**.

    ![Opzione avvisi consigliati in monitoraggio di Azure per i contenitori](./media/container-insights-metric-alerts/command-bar-recommended-alerts.png)

4. Il riquadro delle proprietà **avvisi consigliati** viene visualizzato automaticamente sul lato destro della pagina. Per impostazione predefinita, tutte le regole di avviso nell'elenco sono disabilitate. Dopo aver selezionato **Abilita**, la regola di avviso viene creata e il nome della regola viene aggiornato per includere un collegamento alla risorsa avviso.

    ![Riquadro Proprietà avvisi consigliati](./media/container-insights-metric-alerts/recommended-alerts-pane.png)

    Dopo aver selezionato l'interruttore **Abilita/Disabilita** per abilitare l'avviso, viene creata una regola di avviso e il nome della regola viene aggiornato in modo da includere un collegamento alla risorsa di avviso effettiva.

    ![Abilitare una regola di avviso](./media/container-insights-metric-alerts/recommended-alerts-pane-enable.png)

5. Le regole di avviso non sono associate a un [gruppo di azioni](../platform/action-groups.md) per notificare agli utenti che è stato attivato un avviso. Selezionare **nessun gruppo di azione assegnato** e nella pagina **gruppi di azioni** specificare un gruppo esistente o creare un gruppo di azioni selezionando **Aggiungi** o **Crea**.

    ![Selezionare un gruppo di azioni](./media/container-insights-metric-alerts/select-action-group.png)

### <a name="enable-with-a-resource-manager-template"></a>Abilitare con un modello di Gestione risorse

È possibile usare un modello di Azure Resource Manager e un file di parametri per creare gli avvisi della metrica inclusi in monitoraggio di Azure.

I passaggi di base sono i seguenti:

1. Scaricare uno o tutti i modelli disponibili che descrivono come creare l'avviso da [GitHub](https://github.com/microsoft/Docker-Provider/tree/ci_dev/alerts/recommended_alerts_ARM).

2. Creare e usare un [file di parametri](../../azure-resource-manager/templates/parameter-files.md) come JSON per impostare i valori necessari per creare la regola di avviso.

3. Distribuire il modello dalla portale di Azure, da PowerShell o dall'interfaccia della riga di comando di Azure.

#### <a name="deploy-through-azure-portal"></a>Eseguire la distribuzione tramite portale di Azure

1. Scaricare e salvare in una cartella locale, il modello di Azure Resource Manager e il file dei parametri, per creare la regola di avviso usando i comandi seguenti:

2. Per distribuire un modello personalizzato tramite il portale, selezionare **Crea una risorsa** dal [portale di Azure](https://portal.azure.com).

3. Cercare il **modello**, quindi selezionare **modello**. distribuzione.

4. Selezionare **Crea**.

5. Sono disponibili diverse opzioni per la creazione di un modello, selezionare **Compila un modello personalizzato nell'editor**.

6. Nella **pagina Modifica modello**selezionare **Carica file** e quindi selezionare il file modello.

7. Nella pagina **modifica modello** selezionare **Salva**.

8. Nella pagina **distribuzione personalizzata** specificare gli elementi seguenti e quindi, al termine, selezionare **Acquista** per distribuire il modello e creare la regola di avviso.

    * Resource group
    * Location
    * Nome avviso
    * ID risorsa cluster

#### <a name="deploy-with-azure-powershell-or-cli"></a>Distribuire con Azure PowerShell o l'interfaccia della riga di comando

1. Scaricare e salvare in una cartella locale, il modello di Azure Resource Manager e il file dei parametri, per creare la regola di avviso usando i comandi seguenti:

2. È possibile creare l'avviso di metrica usando il modello e il file dei parametri tramite PowerShell o l'interfaccia della riga di comando di Azure.

    Uso di Azure PowerShell

    ```powershell
    Connect-AzAccount

    Select-AzSubscription -SubscriptionName <yourSubscriptionName>
    New-AzResourceGroupDeployment -Name CIMetricAlertDeployment -ResourceGroupName ResourceGroupofTargetResource `
    -TemplateFile templateFilename.json -TemplateParameterFile templateParameterFilename.parameters.json
    ```

    Utilizzare l'interfaccia della riga di comando di Azure

    ```azurecli
    az login

    az group deployment create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file templateFileName.json \
    --parameters @templateParameterFilename.parameters.json
    ```

    >[!NOTE]
    >Anche se è possibile che l'avviso di metrica sia stato creato in un gruppo di risorse diverso rispetto alla risorsa di destinazione, è consigliabile usare lo stesso gruppo di risorse della risorsa di destinazione.

## <a name="edit-alert-rules"></a>Modificare le regole di avviso

È possibile visualizzare e gestire le regole di avviso di monitoraggio di Azure per contenitori per modificare la soglia o configurare un [gruppo di azioni](../platform/action-groups.md) per il cluster AKS. Sebbene sia possibile eseguire queste azioni dal portale di Azure e dall'interfaccia della riga di comando di Azure, è possibile eseguire queste azioni anche direttamente dal cluster AKS in monitoraggio di Azure per i contenitori.

1. Nella barra dei comandi selezionare **avvisi consigliati**.

2. Per modificare la soglia, nel riquadro **avvisi consigliati** selezionare l'avviso attivato. Nella **regola di modifica**selezionare i **criteri di avviso** che si desidera modificare.

    * Per modificare la soglia della regola di avviso, selezionare la **condizione**.
    * Per specificare un gruppo di azioni esistente o crearne uno, selezionare **Aggiungi** o **Crea** in **gruppo di azioni**

Per visualizzare gli avvisi creati per le regole abilitate, nel riquadro **avvisi consigliati** selezionare **Visualizza negli avvisi**. Si viene reindirizzati al menu avviso per il cluster AKS, in cui è possibile visualizzare tutti gli avvisi attualmente creati per il cluster.

## <a name="configure-alertable-metrics-in-configmaps"></a>Configurare la metrica per gli avvisi in ConfigMaps

Eseguire la procedura seguente per configurare il file di configurazione ConfigMap per eseguire l'override delle soglie di utilizzo delle risorse predefinite del contenitore. Questi passaggi sono applicabili solo alle metriche di avviso seguenti.

* *cpuExceededPercentage*
* *memoryRssExceededPercentage*
* *memoryWorkingSetExceededPercentage*

1. Modificare il file YAML di ConfigMap nella sezione `[alertable_metrics_configuration_settings.container_resource_utilization_thresholds]` .

2. Per modificare la soglia *cpuExceededPercentage* al 90% e iniziare la raccolta di questa metrica quando tale soglia viene soddisfatta e superata, configurare il file ConfigMap usando l'esempio seguente.

    ```
    container_cpu_threshold_percentage = 90.0
    # Threshold for container memoryRss, metric will be sent only when memory rss exceeds or becomes equal to the following percentage
    container_memory_rss_threshold_percentage = 95.0
    # Threshold for container memoryWorkingSet, metric will be sent only when memory working set exceeds or becomes equal to the following percentage
    container_memory_working_set_threshold_percentage = 95.0
    ```

3. Eseguire il comando kubectl seguente: `kubectl apply -f <configmap_yaml_file.yaml>` .

    Esempio: `kubectl apply -f container-azm-ms-agentconfig.yaml`.

La modifica della configurazione può richiedere alcuni minuti prima di essere applicata e tutti i pod omsagent del cluster verranno riavviati. Il riavvio è un riavvio in sequenza per tutti i pod omsagent, non tutti i riavvii nello stesso momento. Al termine del riavvio, viene visualizzato un messaggio simile al seguente e include il risultato: `configmap "container-azm-ms-agentconfig" created` .

## <a name="next-steps"></a>Passaggi successivi

- Visualizzare gli [esempi di query di log](container-insights-log-search.md#search-logs-to-analyze-data) per visualizzare query e esempi predefiniti per valutare o personalizzare gli avvisi, la visualizzazione o l'analisi dei cluster.

- Per altre informazioni su monitoraggio di Azure e su come monitorare altri aspetti del cluster Kubernetes, vedere [visualizzare le prestazioni del cluster Kubernetes](container-insights-analyze.md).
