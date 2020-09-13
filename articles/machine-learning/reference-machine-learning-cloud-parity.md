---
title: Parità tra aree pubbliche e sovrane
titleSuffix: Azure Machine Learning
description: Alcune funzionalità di Azure Machine Learning, ad esempio le funzionalità di anteprima pubblica, possono essere disponibili solo nelle aree del cloud pubblico. Questo articolo elenca le funzionalità disponibili anche nelle aree 21Vianet di Azure per enti pubblici, Azure Germania e Azure Cina.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: andzha
author: Anurzeuii
ms.date: 08/24/2020
ms.custom: references_regions
ms.openlocfilehash: 886e45e92fb3a882de167b5c59a9b5ee09a9c430
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89657583"
---
# <a name="azure-machine-learning-sovereign-cloud-parity"></a>Parità di Azure Machine Learning del cloud sovrano

Informazioni sulle funzionalità Azure Machine Learning sono disponibili nelle aree del cloud sovrano. 

Nell'elenco delle aree di Azure globali sono presenti diverse aree "sovrane" che svolgono mercati specifici. Ad esempio, le aree di Azure per enti pubblici e Azure Cina 21Vianet. Attualmente Azure Machine Learning viene distribuito nelle seguenti aree del cloud sovrano:

* Aree di Azure per enti pubblici **US-Arizona** e **US-Virginia**.
* Azure Cina 21Vianet Region **Cina-Est-2**.

> [!TIP]
> Per distinguere le aree sovrane e non sovrane, in questo articolo verrà usato il termine __cloud pubblico__ per fare riferimento a aree non sovrane.

Si intende fornire la parità massima tra il cloud pubblico e le aree sovrane. Tutte le funzionalità di Azure Machine Learning saranno disponibili in queste aree entro **30 giorni dalla** disponibilità generale nel cloud pubblico. Si abilita anche un numero selezionato di funzionalità in anteprima in queste aree. Di seguito vengono visualizzate le differenze di parità correnti tra i cloud sovrani e pubblici.

## <a name="azure-government"></a>Azure Government 

| Feature | Stato del cloud pubblico  | Stati Uniti-Virginia | Stati Uniti-Arizona| 
|----------------------------------------------------------------------------|:----------------------:|:--------------------:|:-------------:|
| **Machine Learning automatizzato** | | | |
| Creare ed eseguire esperimenti nei notebook                                    | GA                   | YES                | YES         |
| Creazione ed esecuzione di esperimenti in Studio Web Experience                        | Anteprima pubblica       | YES                | YES         |
| Funzionalità di previsione leader del settore                                  | GA                   | YES                | YES         |
| Supporto per Deep Learning e altri Learner avanzati                      | GA                   | YES                | YES         |
| Supporto dati di grandi dimensioni (fino a 100 GB)                                          | Anteprima pubblica       | YES                | YES         |
| Integrazione di Azure Databricks                                              | GA                   | NO                 | NO          |
| Integrazioni di SQL, CosmosDB e HDInsight                                   | GA                   | YES                | YES         |
| **Pipeline di Machine Learning** |   |  | | 
| Creare, eseguire e pubblicare pipeline con Azure ML SDK                   | GA                   | YES                | YES         |
| Creare endpoint della pipeline usando Azure ML SDK                           | GA                   | YES                | YES         |
| Creare, modificare ed eliminare esecuzioni pianificate di pipeline usando Azure ML SDK | GA                   | Sì               | Sì        |
| Visualizzare i dettagli dell'esecuzione della pipeline in studio                                        | GA                   | YES                | YES         |
| Creare, eseguire, visualizzare e pubblicare pipeline in Azure ML designer          | Anteprima pubblica       | YES                | YES         |
| Integrazione di Azure Databricks con la pipeline ML                             | GA                   | NO                 | NO          |
| Creare endpoint della pipeline in Azure ML designer                             | Anteprima pubblica       | YES                | YES         |
| **Notebook integrati** |   |  | | 
| Notebook e condivisione di file dell'area di lavoro                                        | GA                   | YES                | YES         |
| Supporto per R e Python                                                       | GA                   | YES                | YES         |
| Supporto della rete virtuale                                                    | Anteprima pubblica       | NO                 | NO          |
| **Istanza di calcolo** |   |  | | 
| Istanze di calcolo gestite per notebook integrati                         | GA                   | YES                | YES         |
| Jupyter, integrazione JupyterLab                                            | GA                   | YES                | YES         |
| Supporto della Rete virtuale di Microsoft Azure (VNet)                                             | Anteprima pubblica       | YES                | YES         |
| **SDK supportati** |  |  | | 
| Supporto per R SDK                                                              | Anteprima pubblica       | YES                | YES         |
| Supporto per Python SDK                                                         | GA                   | YES                | YES         |
| **Sicurezza** |   | | | 
| Supporto della rete virtuale (VNet) per il training                                | GA                   | YES                | YES         |
| Supporto della rete virtuale (VNet) per l'inferenza                               | GA                   | YES                | YES         |
| Assegnazione di punteggi all'autenticazione dell'endpoint                                            | Anteprima pubblica       | YES                | YES         |
| Collegamento privato area di lavoro                                                     | Anteprima pubblica       | NO                 | NO          |
| ACI dietro VNet                                                            | Anteprima pubblica       | NO                 | NO          |
| ACR dietro VNet                                                            | Anteprima pubblica       | NO                 | NO          |
| IP privato del cluster AKS                                                  | Anteprima pubblica       | NO                 | NO          |
| **Calcolo** |   | | |
| gestione delle quote tra aree di lavoro                                         | GA                   | YES                | YES         |
| **Dati per Machine Learning** |   | | |
| Creare, visualizzare o modificare set di dati e archivi dati dall'SDK                  | GA                   | YES                | YES         |
| Creare, visualizzare o modificare set di dati e archivi dati dall'interfaccia utente                   | GA                   | YES                | YES         |
| Visualizzare, modificare o eliminare i monitoraggi per la derivazione del set di dati dall'SDK                   | Anteprima pubblica       | YES                | YES         |
| Visualizzare, modificare o eliminare i monitoraggi per la derivazione del set di dati dall'interfaccia utente                    | Anteprima pubblica       | YES                | YES         |
| **Ciclo di vita di Machine Learning** |   | | |
| Profilatura del modello                                                            | GA                   | YES                | PARTIAL     |
| L'estensione DevOps di Azure per Machine Learning & interfaccia della riga di comando di Azure ML         | GA                   | YES                | YES         |
| Modelli con accelerazione hardware basati su FPGA                                     | GA                   | NO                 | NO          |
| Integrazione di Visual Studio Code                                             | Anteprima pubblica       | NO                 | NO          |
| Integrazione di Griglia di eventi                                                     | Anteprima pubblica       | NO                 | NO          |
| Integrare Analisi di flusso di Azure con Azure Machine Learning               | Anteprima pubblica       | NO                 | NO          |
| **Etichettatura** |   | | |
| Assegnazione di etichette al progetto portale di gestione                                        | GA                   | YES                | YES         |
| Portale etichettatore                                                            | GA                   | YES                | YES         |
| Assegnazione di etichette con il personale privato                                          | GA                   | YES                | YES         |
| Etichettatura assistita ML (classificazione immagini e rilevamento oggetti)           | Anteprima pubblica       | YES                | YES         |
| **ML responsabile** |   | | |
| Spiegazione dell'interfaccia utente                                                       | Anteprima pubblica       | NO                 | NO          |
| WhiteNoise Toolkit per la privacy differenziale                                    | OSS                  | NO                 | NO          |
| Tag personalizzati in Azure Machine Learning per implementare i fogli dati              | GA                   | NO                 | NO          |
| Integrazione con AzureML Equity                                               | Anteprima pubblica       | NO                 | NO          |
| SDK per l'interpretazione                                                      | GA                   | YES                | YES         |
| **Formazione** |   | | |
| Streaming del log di sperimentazione                                              | GA                   | YES                | YES         |
| Apprendimento per rinforzo                                                     | Anteprima pubblica       | NO                 | NO          |
| Interfaccia utente di sperimentazione                                                         | GA                   | YES                | YES         |
| Integrazione con .NET ML.NET 1,0                                                | GA                   | YES                | YES         |
| **Inferenza** |   | | |
| Inferenza batch                                                          | GA                   | YES                | YES         |
| Data Box Edge con FPGA                                                    | Anteprima pubblica       | NO                 | NO          |
| **Altri** |   | | |
| Set di dati aperti                                                              | Anteprima pubblica       | YES                | YES         |
| ricerca cognitiva personalizzati                                                    | Anteprima pubblica       | YES                | YES         |
| Molti modelli                                                                | Anteprima pubblica       | NO                 | NO          |


### <a name="azure-government-scenarios"></a>Scenari di Azure per enti pubblici

| Scenario                                                    | Stati Uniti-Virginia | Stati Uniti-Arizona| Limitazioni  |
|----------------------------------------------------------------------------|:----------------------:|:--------------------:|-------------|
| **Configurazione della sicurezza generale** |   | | |
| Comunicazione di rete privata tra servizi                                     | NO | NO | Nessun collegamento privato attualmente | 
| Disabilitare/controllare l'accesso a Internet (in ingresso e in uscita) e VNet specifici | PARTIAL| PARTIAL   | Il record di controllo di VNet non è disponibile in Azure per enti pubblici-doppio controllo su ACI | 
| Selezione host per tutte le risorse/servizi associati  | YES | YES |  |
| Crittografia di inattivi e in transito.                                                 | YES | YES |  |
| Accesso radice e SSH alle risorse di calcolo.                                          | YES | YES |  |
| Mantenere la sicurezza dei sistemi distribuiti (istanze, endpoint e così via), inclusi Endpoint Protection, applicazione di patch e registrazione |  PARTIAL|  PARTIAL |ACI dietro VNet e endpoint privato attualmente non disponibile |                                  
| Controllo (Disabilita/limita/limita) uso dell'integrazione di ACI/AKS                    | PARTIAL| PARTIAL |ACI dietro VNet e endpoint privato attualmente non disponibile|
| Controllo degli accessi in base al ruolo (RBAC): creazioni di ruoli personalizzati                           | YES | YES |  |
| Controllare l'accesso alle immagini del registro contenitori di Azure usate dal servizio ML (fornito/gestito da Azure e personalizzato)  |PARTIAL|  PARTIAL | ACR dietro endpoint privato e VNet non supportato in Azure per enti pubblici |
| **Utilizzo generale del servizio Machine Learning** |  | | |
| Possibilità di disporre di un ambiente di sviluppo per compilare un modello, eseguire il training del modello, ospitarlo come endpoint e utilizzarlo tramite un WebApp     | YES | YES |  |
| Possibilità di eseguire il pull dei dati da ADLS (Data Lake Storage)                                 |YES | YES |  |
| Possibilità di eseguire il pull dei dati dall'archivio BLOB di Azure                                       |YES | YES |  |



### <a name="additional-azure-government-limitations"></a>Limitazioni aggiuntive di Azure per enti pubblici

* Per Azure Machine Learning istanze di calcolo, la possibilità di aggiornare un token che dura più di 24 ore non è disponibile in Azure per enti pubblici.
* La profilatura del modello non supporta 4 CPU nell'area Stati Uniti orientali.   
* I notebook di esempio potrebbero non funzionare in Azure per enti pubblici se è necessario l'accesso ai dati pubblici.
* Indirizzi IP: il comando CLI usato nelle istruzioni [VNet e tunneling forzato](how-to-secure-training-vnet.md#forced-tunneling) non restituisce gli intervalli IP. Usare invece gli [intervalli IP di Azure e i tag di servizio per Azure per enti pubblici](https://www.microsoft.com/download/details.aspx?id=57063) .
* Per le pipeline pianificate, viene fornito anche un meccanismo di trigger basato su BLOB. Questo meccanismo non è supportato per le aree di lavoro CMK. Per abilitare un trigger basato su BLOB per le aree di lavoro di CMK, è necessario eseguire un'installazione aggiuntiva. Per altre informazioni, vedere [attivare un'esecuzione di una pipeline di machine learning da un'app per la logica](how-to-trigger-published-pipeline.md).
* Firewall: quando si usa un'area di Azure per enti pubblici, aggiungere gli host aggiuntivi seguenti all'impostazione del firewall:

    * Per l'Arizona usare: `usgovarizona.api.ml.azure.us`
    * Per Virginia usare: `usgovvirginia.api.ml.azure.us`
    * Per entrambi: `graph.windows.net` 


## <a name="azure-china-21vianet"></a>Azure Cina 21Vianet 

| Feature                                       | Stato del cloud pubblico | CH-Est-2 | CH-Nord-3 |
|----------------------------------------------------------------------------|:------------------:|:--------------------:|:-------------:|
| **Machine Learning automatizzato** |    | | |
| Creare ed eseguire esperimenti nei notebook                                    | GA               | YES       | N/D        |
| Creazione ed esecuzione di esperimenti in Studio Web Experience                        | Anteprima pubblica   | YES       | N/D        |
| Funzionalità di previsione leader del settore                                  | GA               | YES       | N/D        |
| Supporto per Deep Learning e altri Learner avanzati                      | GA               | YES       | N/D        |
| Supporto dati di grandi dimensioni (fino a 100 GB)                                          | Anteprima pubblica   | YES       | N/D        |
| Integrazione di Azure Databricks                                              | GA               | NO        | N/D        |
| Integrazioni di SQL, CosmosDB e HDInsight                                   | GA               | YES       | N/D        |
| **Pipeline di Machine Learning** |    | | |
| Creare, eseguire e pubblicare pipeline con Azure ML SDK                   | GA               | YES       | N/D        |
| Creare endpoint della pipeline usando Azure ML SDK                           | GA               | YES       | N/D        |
| Creare, modificare ed eliminare esecuzioni pianificate di pipeline usando Azure ML SDK | GA               | YES       | N/D        |
| Visualizzare i dettagli dell'esecuzione della pipeline in studio                                        | GA               | YES       | N/D        |
| Creare, eseguire, visualizzare e pubblicare pipeline in Azure ML designer          | Anteprima pubblica   | YES       | N/D        |
| Integrazione di Azure Databricks con la pipeline ML                             | GA               | NO        | N/D        |
| Creare endpoint della pipeline in Azure ML designer                             | Anteprima pubblica   | YES       | N/D        |
| **Notebook integrati** |   | | |
| Notebook e condivisione di file dell'area di lavoro                                        | GA               | YES       | N/D        |
| Supporto per R e Python                                                       | GA               | YES       | N/D        |
| Supporto della rete virtuale                                                    | Anteprima pubblica   | NO        | N/D        |
| **Istanza di calcolo** |    | | |
| Istanze di calcolo gestite per notebook integrati                         | GA               | NO        | N/D        |
| Jupyter, integrazione JupyterLab                                            | GA               | YES       | N/D        |
| Supporto della Rete virtuale di Microsoft Azure (VNet)                                             | Anteprima pubblica   | YES       | N/D        |
| **SDK supportati** |    | | |
| Supporto per R SDK                                                              | Anteprima pubblica   | YES       | N/D        |
| Supporto per Python SDK                                                         | GA               | YES       | N/D        |
| **Sicurezza** |   | | |
| Supporto della rete virtuale (VNet) per il training                                | GA               | YES       | N/D        |
| Supporto della rete virtuale (VNet) per l'inferenza                               | GA               | YES       | N/D        |
| Assegnazione di punteggi all'autenticazione dell'endpoint                                            | Anteprima pubblica   | YES       | N/D        |
| Collegamento privato area di lavoro                                                     | Anteprima pubblica   | NO        | N/D        |
| ACI dietro VNet                                                            | Anteprima pubblica   | NO        | N/D        |
| ACR dietro VNet                                                            | Anteprima pubblica   | NO        | N/D        |
| IP privato del cluster AKS                                                  | Anteprima pubblica   | NO        | N/D        |
| **Calcolo** |   | | |
| gestione delle quote tra aree di lavoro                                         | GA               | YES       | N/D        |
| **Dati per Machine Learning** | | | |
| Creare, visualizzare o modificare set di dati e archivi dati dall'SDK                  | GA               | YES       | N/D        |
| Creare, visualizzare o modificare set di dati e archivi dati dall'interfaccia utente                   | GA               | YES       | N/D        |
| Visualizzare, modificare o eliminare i monitoraggi per la derivazione del set di dati dall'SDK                   | Anteprima pubblica   | YES       | N/D        |
| Visualizzare, modificare o eliminare i monitoraggi per la derivazione del set di dati dall'interfaccia utente                    | Anteprima pubblica   | YES       | N/D        |
| **Ciclo di vita di Machine Learning** |    | | |
| Profilatura del modello                                                            | GA               | PARTIAL   | N/D        |
| L'estensione DevOps di Azure per Machine Learning & interfaccia della riga di comando di Azure ML         | GA               | YES       | N/D        |
| Modelli con accelerazione hardware basati su FPGA                                     | GA               | NO        | N/D        |
| Integrazione di Visual Studio Code                                             | Anteprima pubblica   | NO        | N/D        |
| Integrazione di Griglia di eventi                                                     | Anteprima pubblica   | YES       | N/D        |
| Integrare Analisi di flusso di Azure con Azure Machine Learning               | Anteprima pubblica   | NO        | N/D        |
| **Etichettatura** |    | | |
| Assegnazione di etichette al progetto portale di gestione                                        | GA               | YES       | N/D        |
| Portale etichettatore                                                            | GA               | YES       | N/D        |
| Assegnazione di etichette con il personale privato                                          | GA               | YES       | N/D        |
| Etichettatura assistita ML (classificazione immagini e rilevamento oggetti)           | Anteprima pubblica   | YES       | N/D        |
| **ML responsabile** |    | | |
| Spiegazione dell'interfaccia utente                                                       | Anteprima pubblica   | NO        | N/D        |
| WhiteNoise Toolkit per la privacy differenziale                                    | OSS              | NO        | N/D        |
| Tag personalizzati in Azure Machine Learning per implementare i fogli dati              | GA               | NO        | N/D        |
| Integrazione con AzureML Equity                                               | Anteprima pubblica   | NO        | N/D        |
| SDK per l'interpretazione                                                      | GA               | YES       | N/D        |
| **Formazione** |    | | |
| Streaming del log di sperimentazione                                              | GA               | YES       | N/D        |
| Apprendimento per rinforzo                                                     | Anteprima pubblica   | NO        | N/D        |
| Interfaccia utente di sperimentazione                                                         | GA               | YES       | N/D        |
| Integrazione con .NET ML.NET 1,0                                                | GA               | YES       | N/D        |
| **Inferenza** |   | | |
| Inferenza batch                                                          | GA               | YES       | N/D        |
| Data Box Edge con FPGA                                                    | Anteprima pubblica   | NO        | N/D        |
| **Altri** |    | | |
| Set di dati aperti                                                              | Anteprima pubblica   | YES       | N/D        |
| ricerca cognitiva personalizzati                                                    | Anteprima pubblica   | YES       | N/D        |
| Molti modelli                                                                | Anteprima pubblica   | NO        | N/D        |



### <a name="additional-azure-china-limitations"></a>Limitazioni aggiuntive per Azure Cina

* Azure Cina ha SKU di VM limitati, specialmente per lo SKU GPU. Ha solo la famiglia NCv3 (V100).
* Gli endpoint dell'API REST sono diversi da Azure globale. Usare la tabella seguente per trovare l'endpoint dell'API REST per le aree di Azure Cina:

    | Endpoint REST                 | Azure globale                                 | Cina-governo                           |
    |------------------|--------------------------------------------|--------------------------------------------|
    | Piano di gestione | `https://management.azure.com/`              | `https://management.chinacloudapi.cn/`       |
    | Piano dati       | `https://{location}.experiments.azureml.net` | `https://{location}.experiments.ml.azure.cn` |
    | Azure Active Directory              | `https://login.microsoftonline.com`          | `https://login.chinacloudapi.cn`             |

* È possibile che il notebook di esempio non funzioni, se è necessario accedere ai dati pubblici.
* Intervalli di indirizzi IP: il comando CLI usato nelle istruzioni per il [tunneling forzato VNet](how-to-secure-training-vnet.md#forced-tunneling) non restituisce gli intervalli IP. Usare invece gli [intervalli IP di Azure e i tag di servizio per Azure Cina](https://www.microsoft.com//download/details.aspx?id=57062) .
* Azure Machine Learning l'anteprima delle istanze di calcolo non è supportata in un'area di lavoro in cui il collegamento privato è abilitato per il momento, ma nella successiva distribuzione per l'espansione del servizio in tutte le aree di AML.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle aree in cui è disponibile Azure Machine Learning, vedere [prodotti in base all'area](https://azure.microsoft.com/global-infrastructure/services/).
