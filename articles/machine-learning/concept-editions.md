---
title: Azure Machine Learning edizioni Enterprise e Basic
description: Informazioni sulle differenze tra le edizioni di Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: j-martens
ms.author: jmartens
ms.date: 06/11/2020
ms.openlocfilehash: 173ff8b638c40773ca2c4bdac5021f2ea4b84549
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85555536"
---
# <a name="enterprise-and-basic-editions-of-azure-machine-learning"></a>Edizioni Enterprise e Basic di Azure Machine Learning 

Azure Machine Learning offre due edizioni personalizzate per le esigenze di machine learning. Queste edizioni determinano quali strumenti di Machine Learning sono disponibili per sviluppatori e data scientist dalla relativa area di lavoro.

## <a name="choose-an-edition"></a>Scegliere un'edizione

L'edizione viene assegnata ogni volta che si crea un'area di lavoro. I clienti sono responsabili dei costi sostenuti per le risorse di calcolo e di altro tipo di Azure durante questo periodo di tempo. Informazioni su come [gestire i costi per Azure Machine Learning](concept-plan-manage-cost.md).

Vedere le informazioni su come [aggiornare un'area di lavoro dell'edizione Basic all'edizione Enterprise](how-to-manage-workspace.md#upgrade). 

## <a name="whats-in-each-edition"></a>Elementi di ogni edizione

### <a name="data-for-machine-learning-capabilities"></a>Dati per le funzionalità di Machine Learning  

| Funzionalità                     | Edizione                 |
|------------------------------------------------------------------------------------|:-----------:|
| Assegnazione di etichette: [creare e gestire progetti di assegnazione di etichette](tutorial-labeling.md) in studio (Web)                                                | Tutti                     |
| Assegnazione di etichette: Etichettatore in studio (Web)                                    | Tutti                     |
| Assegnazione di etichette: usare la forza lavoro privata                               | Tutti                     |
| Assegnazione di etichette: [classificazione delle immagini assistita da ml e rilevamento di oggetti](how-to-label-images.md)                  | Solo Enterprise Edition |
| Set di dati + archivi dati: creare e gestire in Python                       | Tutti                     |
| Set di dati + archivi dati: creare e gestire in studio (Web)                         | Tutti                     |
| Drift: visualizzare e gestire i monitoraggi del set di dati in Python                           | Tutti                     |
| Drift: visualizzare e gestire i monitoraggi del set di dati in studio (Web)                            | Solo Enterprise Edition |


<br/>
<br/>

### <a name="automated-training-capabilities-automl"></a>Funzionalità di training automatico (AutoML)

| Funzionalità    | Edizione                 |
|------------------------------------------------------------------------------------|:-----------:|
| Creazione ed esecuzione [di esperimenti AutoML nei notebook](how-to-configure-auto-train.md)               | Tutti                     |
| Creazione ed esecuzione [di esperimenti AutoML in studio (Web)](how-to-use-automated-ml-for-ml-models.md)   | Solo Enterprise Edition |
| Funzionalità di previsione AutoML leader di settore             | Solo Enterprise Edition |
| Supporto per Deep Learning e altri Learner avanzati | Solo Enterprise Edition |
| Attività di classificazione e regressione del supporto dati di grandi dimensioni (fino a 100 GB)                     | Solo Enterprise Edition |


<br/>
<br/>

### <a name="responsible-machine-learning"></a>Machine Learning responsabile

| Funzionalità    | Edizione                 |
|------------------------------------------------------------------------------------|:-----------:|
| [Spiegabilità del modello](how-to-machine-learning-interpretability-automl.md)                                              | Tutti                     |
| [Privacy differenziale](how-to-differential-privacy.md)                          | Tutti                     |
| Tag personalizzati per implementare i fogli dati    | Tutti                     |
| Integrazione con AzureML Equity                                      | Tutti                     |

<br/>
<br/>


### <a name="build-and-train-capabilities"></a>Funzionalità di compilazione e training

| Funzionalità    | Edizione                 |
|------------------------------------------------------------------------------------|:-----------:|
| Integrazione di Visual Studio Code                                                     | Tutti                     |
| Apprendimento per rinforzo                                                             | Tutti                     |
| Interfaccia utente di sperimentazione                                                                 | Tutti                     |
| Jupyter, integrazione JupyterLab                                                    | Tutti                     |
| Supporto per Python SDK                                                                 | Tutti                     |
| Supporto per R SDK                                                                      | Tutti                     |
| Pipeline ML: creare, eseguire e pubblicare in Python                           | Tutti                     |
| Pipeline ML: creare, modificare ed eliminare esecuzioni pianificate di pipeline in Python| Tutti                     |
| Pipeline di ML: creare endpoint della pipeline in Python SDK                                   | Tutti                     |
| Pipeline ML: visualizzare i dettagli dell'esecuzione in studio (Web)                                              | Tutti                     |
| Pipeline ML: creare, eseguire, visualizzare e pubblicare nella finestra di progettazione                  | Solo Enterprise Edition |
| Pipeline ML: creare endpoint della pipeline nella finestra di progettazione | Solo Enterprise Edition |
| Istanze di calcolo gestite per notebook integrati                                 | Tutti                     |


<br/>
<br/>

### <a name="deployment-and-model-management-capabilities"></a>Funzionalità di distribuzione e gestione dei modelli

| Funzionalità                            | Edizione                 |
|------------------------------------------------------------------------------------|:-----------:|
| L'estensione DevOps di Azure per Machine Learning e il interfaccia della riga di comando di Azure ML                 | Tutti                     |
| [Integrazione di Griglia di eventi](how-to-use-event-grid.md)                                                             | Tutti                     |
| Integrare Analisi di flusso di Azure con Azure Machine Learning                       | Tutti                     |
| Creare pipeline di ML in SDK                                                         | Tutti                     |
| Inferenza batch                                                                  | Tutti                     |
| Modelli con accelerazione hardware basati su FPGA                                             | Tutti                     |
| Profilatura del modello                                                                    | Tutti                     |

<br/>
<br/>

### <a name="security-governance-and-control-capabilities"></a>Funzionalità di sicurezza, governance e controllo

| Funzionalità     | Edizione                 |
|------------------------------------------------------------------------------------|:-----------:|
| Supporto del [controllo degli accessi in base al ruolo](how-to-assign-roles.md) (RBAC)                                           | Tutti                     |
| Supporto della [rete virtuale (VNet)](how-to-enable-virtual-network.md) per il calcolo                                         | Tutti                     |
| Assegnazione di punteggi all'autenticazione dell'endpoint                                                    | Tutti                     |
| [Collegamento privato area di lavoro](how-to-configure-private-link.md)                                                            | Tutti                     |
| [Gestione delle quote](how-to-manage-quotas.md) tra aree di lavoro                                                 | Solo Enterprise Edition |

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su ciò che è disponibile nella pagina relativa alla [panoramica e ai prezzi](https://azure.microsoft.com/pricing/details/machine-learning/)di Azure Machine Learning Edition. 

Vedere le informazioni su come [aggiornare un'area di lavoro dell'edizione Basic all'edizione Enterprise](how-to-manage-workspace.md#upgrade). 
