---
title: Portare i propri ML in Sentinel di Azure | Microsoft Docs
description: Questo articolo illustra come creare e usare gli algoritmi di apprendimento automatico per l'analisi dei dati in Sentinel di Azure.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: yelevin
ms.openlocfilehash: 17c0ba7306ab4cc51fe8bbe3709d5b6bc85fa487
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91344647"
---
# <a name="bring-your-own-machine-learning-ml-into-azure-sentinel"></a>Bring Your Own Machine Learning (ML) in Azure Sentinel

Machine Learning (ML) è uno dei principali sottostanti di Azure Sentinel e uno degli attributi principali che lo distinguono. Azure Sentinel offre ML in diverse esperienze: incorporata nel motore di correlazione di [Fusion](fusion.md) e nei notebook Jupyter e la nuova piattaforma di Machine Learning (BYO ml) disponibile per la compilazione. 

I modelli di rilevamento ML possono adattarsi a singoli ambienti e a modifiche al comportamento degli utenti, per ridurre i falsi positivi e identificare le minacce che non sono state rilevate con un approccio tradizionale. Molte organizzazioni di sicurezza sono consapevoli del valore di ML per la sicurezza, anche se non molte di esse hanno il lusso di professionisti esperti in materia di sicurezza e ML. Il Framework è stato progettato per consentire alle organizzazioni e ai professionisti della sicurezza di crescere con noi nel loro percorso di ML. Le organizzazioni che non hanno familiarità con ML, o senza la necessaria esperienza, possono ottenere un valore di protezione significativo dalle funzionalità di Machine Learning predefinite di Azure Sentinel.

:::image type="content" source="./media/bring-your-own-ml/machine-learning-framework.png" alt-text="Framework di Machine Learning":::

## <a name="what-is-the-bring-your-own-machine-learning-byo-ml-platform"></a>Qual è la piattaforma BYO-ML (Bring Your Own Machine Learning)?

Per le organizzazioni che hanno risorse ML e vogliono creare modelli di Machine Learning personalizzati per le proprie esigenze aziendali esclusive, offriamo la **piattaforma BYO-ml**. La piattaforma usa l'ambiente [Azure Databricks](https://docs.microsoft.com/azure/databricks/scenarios/what-is-azure-databricks) / [Apache Spark](http://spark.apache.org/) e i notebook Jupyter per produrre l'ambiente ml. Fornisce i componenti seguenti:

- un pacchetto BYO-ML, che include librerie che consentono di accedere ai dati ed eseguire il push dei risultati in Log Analytics (LA), in modo che sia possibile integrare i risultati con il rilevamento, l'analisi e la caccia. 

- Modelli di algoritmo ML che è possibile personalizzare per adattarsi a specifici problemi di sicurezza nell'organizzazione. 

- Notebook di esempio per il training del modello e la pianificazione del punteggio del modello. 

Oltre a tutto questo, è possibile usare modelli di ML personalizzati e/o un ambiente Spark personalizzato per l'integrazione con Sentinel di Azure.

Con la piattaforma BYO-ML è possibile iniziare subito a creare modelli di ML personalizzati: 

- Il notebook con dati di esempio consente di ottenere un'esperienza pratica end-to-end, senza doversi preoccupare di gestire i dati di produzione.

- Il pacchetto integrato con l'ambiente Spark riduce le difficoltà e le frizioni per la gestione dell'infrastruttura.

- Le librerie supportano lo spostamento dei dati. I notebook di formazione e assegnazione di punteggi dimostrano l'esperienza end-to-end e servono come modello per adattarsi all'ambiente.

### <a name="use-cases"></a>Casi d'uso
 
La piattaforma e il pacchetto BYO-ML riducono significativamente il tempo e lo sforzo necessari per la creazione di rilevamenti di ML personalizzati e consentono di risolvere problemi di sicurezza specifici in Sentinel di Azure. La piattaforma supporta i casi d'uso seguenti:

**Eseguire il training di un algoritmo ml per ottenere un modello personalizzato:** È possibile adottare un algoritmo di Machine Learning esistente (condiviso da Microsoft o dalla community degli utenti) ed eseguirne facilmente il training sui dati per ottenere un modello di ML personalizzato che meglio si adatta ai dati e all'ambiente.

**Modificare un modello di algoritmo ml per ottenere un modello personalizzato:** È possibile modificare un modello di algoritmo ML (condiviso da Microsoft o dalla community degli utenti) ed eseguire il training dell'algoritmo modificato sui propri dati, per derivare un modello personalizzato per adattarsi al problema specifico.

**Creare un modello personalizzato:** Crea il tuo modello da zero usando la piattaforma e le utilità BYO-ML di Sentinel di Azure.

**Integrare l'ambiente databricks/Spark:** Integra l'ambiente databricks/Spark esistente in Sentinel di Azure e usa le librerie e i modelli di BYO-ML per creare modelli di machine learning per le situazioni univoche.

**Importare un modello di ml personalizzato:** È possibile importare i propri modelli di ML e usare la piattaforma e le utilità BYO-ML per integrarle con Azure Sentinel.

**Condividere un algoritmo ml:** Condividere un algoritmo ML che la community deve adottare e adattare.

**USA ml per potenziare** i dati: usa il tuo modello di ml personalizzato e i risultati per la caccia, i rilevamenti, l'analisi e la risposta.

Questo articolo illustra i componenti della piattaforma BYO-ML e come sfruttare la piattaforma e l'algoritmo di accesso alle risorse anomalo per fornire un rilevamento di ML personalizzato con Azure Sentinel.

## <a name="azure-databricksspark-environment"></a>Ambiente Azure Databricks/Spark

[Apache Spark™](http://spark.apache.org/) ha fatto un passo avanti nel semplificare la Big Data fornendo un framework unificato per la creazione di pipeline di dati. Azure Databricks offre un ulteriore livello di disponibilità grazie a una piattaforma cloud di gestione zero basata su Spark. Si consiglia di usare databricks per la piattaforma BYO-ML, in modo da potersi concentrare sull'individuazione di risposte che hanno un effetto immediato sull'azienda, anziché affrontare le pipeline di dati e i problemi della piattaforma.
Se si dispone già di databricks o di qualsiasi altro ambiente Spark e si preferisce usare la configurazione esistente, anche il pacchetto BYO-ML funzionerà correttamente. 

## <a name="byo-ml-package"></a>Pacchetto BYO-ML

Il pacchetto BYO ML include le procedure consigliate e la ricerca di Microsoft nel front-end di ML per la sicurezza. In questo pacchetto viene fornito il seguente elenco di utilità, notebook e modelli di algoritmo per i problemi di sicurezza.

| Nome file | Descrizione |
| --------- | ----------- |
| azure_sentinel_utilities. WHL | Contiene utilità per la lettura di BLOB da Azure e la scrittura in Log Analytics. |
| AnomalousRASampleData | Notebook illustra l'uso di un modello di accesso alle risorse anomalo in Sentinel con dati di esempio di training e test generati. |
| AnomalousRATraining. ipynb | Notebook per eseguire il training dell'algoritmo, compilare e salvare i modelli. |
| AnomalousRAScoring. ipynb | Notebook per pianificare l'esecuzione del modello, visualizzare il risultato e scrivere il punteggio in Sentinel di Azure. |
|

Il primo modello di algoritmo ML offerto è per il [rilevamento anomalo dell'accesso alle risorse](https://github.com/Azure/Azure-Sentinel/tree/master/BYOML). Si basa su un algoritmo di filtro collaborativo e viene sottoposto a training con i log di accesso con condivisione file di Windows (eventi di sicurezza con ID evento 5140). Le informazioni chiave necessarie per questo modello nel log sono l'associazione di utenti e risorse a cui si accede. 

## <a name="example-walkthrough-anomalous-file-share-access-detection"></a>Procedura dettagliata di esempio: rilevamento anomalo dell'accesso alla condivisione file 

Ora che si conoscono i componenti chiave della piattaforma BYO-ML, di seguito è riportato un esempio per illustrare come usare la piattaforma e i componenti per fornire un rilevamento di ML personalizzato.

### <a name="setup-the-databricksspark-environment"></a>Configurare l'ambiente databricks/Spark

Se non ne è già presente uno, sarà necessario configurare un ambiente databricks. Per istruzioni, vedere il documento di [avvio rapido di databricks](https://docs.microsoft.com/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal?tabs=azure-portal) .

### <a name="auto-export-instruction"></a>Istruzione di esportazione automatica

Per creare modelli di Machine Learning personalizzati basati sui dati in Sentinel, è necessario esportare i dati da Log Analytics a una risorsa di archiviazione BLOB o hub eventi, in modo che il modello ML possa accedervi da databricks. Informazioni su come [inserire dati in Sentinel di Azure](connect-data-sources.md).

Per questo esempio, è necessario avere i dati di training per il log di accesso alla condivisione file nell'archivio BLOB di Azure. Il formato dei dati è documentato nel notebook e nelle librerie.

È possibile esportare automaticamente i dati da Log Analytics usando l' [interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/monitor/log-analytics). 

Per eseguire i comandi, è necessario disporre del ruolo **collaboratore** nell'area di lavoro log Analytics, nell'account di archiviazione e nella risorsa EventHub. 

Di seguito è riportato un set di comandi di esempio per la configurazione dell'esportazione automatica:

```azurecli

az –version

# Login with Azure CLI
 az login

# List all Log Analytics clusters
 az monitor log-analytics cluster list

# Set to specific subscription
 az account set --subscription "SUBSCRIPTION_NAME"
 
# Export to Storage - all tables
 az monitor log-analytics workspace data-export create --resource-group "RG_NAME" --workspace-name "WS_NAME" -n LAExportCLIStr --destination “DESTINATION_NAME" --enable "true" --tables SecurityEvent
 
# Export to EventHub - all tables
 az monitor log-analytics workspace data-export create --resource-group "RG_NAME" --workspace-name "WS_NAME" -n LAExportCLIEH --destination “DESTINATION_NAME" --enable "true" --tables SecurityEvent Heartbeat"]

# List export settings
az monitor log-analytics workspace data-export list --resource-group "RG_NAME" --workspace-name "WS_NAME"

# Delete export setting
 az monitor log-analytics workspace data-export delete --resource-group "RG_NAME" --workspace-name "WS_NAME" --name "NAME"
```

### <a name="export-custom-data"></a>Esportare dati personalizzati

Per i dati personalizzati non supportati da Log Analytics esportazione automatica, è possibile usare l'app per la logica o altre soluzioni per spostare i dati. È possibile fare riferimento al Blog e allo script di [esportazione dei dati di log Analytics nell'archivio BLOB](https://www.borninthecloud.com/exporting-log-analytics-data-to-blob-store/?preview=true) .

### <a name="correlate-with-data-outside-of-azure-sentinel"></a>Correlare con i dati all'esterno di Sentinel di Azure

È anche possibile importare dati dall'esterno di Azure Sentinel nell'archivio BLOB o nell'hub eventi e correlarli con i dati Sentinel per compilare i modelli ML. 
 
### <a name="copy-and-install-the-related-packages"></a>Copiare e installare i pacchetti correlati

Copiare il pacchetto BYO-ML dal repository GitHub di Azure Sentinel menzionato in precedenza all'ambiente databricks. Aprire quindi i notebook e seguire le istruzioni all'interno del notebook per installare le librerie necessarie nei cluster.

### <a name="model-training-and-scoring"></a>Training del modello e assegnazione dei punteggi

Seguire le istruzioni riportate nei due notebook per modificare le configurazioni in base all'ambiente e alle risorse, attenersi alla procedura per eseguire il training e la compilazione del modello, quindi pianificare il modello per assegnare un punteggio ai log di accesso alla condivisione file in ingresso.

### <a name="write-results-to-log-analytics"></a>Scrivi risultati in Log Analytics

Una volta ottenuto il Punteggio pianificato, è possibile usare il modulo nel notebook di assegnazione dei punteggi per scrivere i risultati del punteggio nell'area di lavoro Log Analytics associata all'istanza di Azure Sentinel.

### <a name="check-results-in-azure-sentinel"></a>Controllare i risultati in Sentinel di Azure

Per visualizzare i risultati con punteggio con i dettagli relativi ai log, tornare al portale di Azure Sentinel. Nei **log** > log personalizzati, i risultati vengono visualizzati nella tabella **AnomalousResourceAccessResult_CL** (o nel nome della tabella personalizzata). È possibile usare questi risultati per migliorare le esperienze di ricerca e caccia.

:::image type="content" source="./media/bring-your-own-ml/anomalous-resource-access-logs.png" alt-text="log di accesso alle risorse anomali":::

### <a name="build-custom-analytics-rule-with-ml-results"></a>Creare una regola di analisi personalizzata con risultati ML

Dopo aver verificato che i risultati di ML si trovano nella tabella dei log personalizzati e si è soddisfatti della fedeltà dei punteggi, è possibile creare un rilevamento in base ai risultati. Passare a **Analytics** dal portale di Azure Sentinel e [creare una nuova regola di rilevamento](tutorial-detect-threats-custom.md). Di seguito è riportato un esempio in cui viene illustrata la query utilizzata per creare il rilevamento.

:::image type="content" source="./media/bring-your-own-ml/create-byo-ml-analytics-rule.png" alt-text="creare una regola di analisi personalizzata per i rilevamenti B Y O M L":::

### <a name="view-and-respond-to-incidents"></a>Visualizzare e rispondere agli eventi imprevisti
Una volta configurata la regola di analisi in base ai risultati di ML, se sono presenti risultati superiori alla soglia impostata nella query, verrà generato un evento imprevisto che verrà esposto nella pagina **eventi imprevisti** in Sentinel di Azure. 

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come usare la piattaforma BYO-ML di Sentinel di Azure per creare o importare i propri algoritmi di machine learning per analizzare i dati e rilevare le minacce.

- Vedere i post relativi a Machine Learning e molti altri argomenti rilevanti nel [Blog di Azure Sentinel](https://aka.ms/azuresentinelblog).
