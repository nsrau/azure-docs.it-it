---
title: Prezzi e fatturazione per i servizi di Azure Machine Learning
description: "Questo articolo riporta alcune domande frequenti e le corrispondenti risposte su prezzi e fatturazione per le funzionalità di anteprima di Azure Machine Learning"
services: machine-learning
author: j-martens
ms.author: jmartens
manager: cgronlund
ms.reviewer: mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 03/01/2018
ms.openlocfilehash: 756295e1b237e4b638effbac084ecd891f3f2d21
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2018
---
# <a name="pricing-and-billing-for-azure-machine-learning-services"></a>Prezzi e fatturazione per i servizi di Azure Machine Learning

Per informazioni dettagliate oppure per visualizzare una fattura di esempio, visitare la [pagina dei prezzi di Azure](https://azure.microsoft.com/pricing/details/machine-learning-services/).  

Ecco alcune domande frequenti su fatturazione e prezzi.

**Qual è il costo di Azure Machine Learning durante l'anteprima?** 

L'applicazione Azure Machine Learning Workbench è disponibile gratuitamente ai sottoscrittori di Azure.

Il Servizio Sperimentazione e Gestione modelli offrono livelli gratuiti e livelli a pagamento, disponibili con uno sconto durante l'anteprima pubblica.

**Gli addebiti saranno basati sul numero di esperimenti eseguiti?**

No. Il Servizio Sperimentazione permette di eseguire tutti gli esperimenti necessari e gli addebiti sono basati solo sul numero di utenti. Le risorse di calcolo di Sperimentazione vengono addebitate separatamente.  È consigliabile eseguire più esperimenti in modo da trovare il modello più adatto alla propria soluzione. 

**Gli addebiti sono basati sul numero di chiamate eseguite ai servizi Web?**

di serie I servizi Web possono essere chiamati con la frequenza necessaria, senza implicazioni a livello di fatturazione per Gestione modelli. L'utente ha il controllo completo sul ridimensionamento delle distribuzioni per soddisfare le esigenze delle proprie applicazioni.

**Come ridimensionare il numero di unità acquistate in Gestione modelli di Azure Machine Learning?**

È possibile cambiare il numero di unità, aumentandolo o riducendolo, usando il portale di gestione di Azure o l'interfaccia della riga di comando. 

**Che aspetto ha una fattura?**

Le fatture vengono calcolate giornalmente. Per le finalità di fatturazione, un giorno inizia a mezzanotte UTC. Le fatture vengono generate ogni mese. Per tutti i servizi Azure usati insieme ad Azure Machine Learning sono previsti addebiti separati. Tali addebiti possono includere, ad esempio: 
- Costi di calcolo
- HDInsight
- Servizio contenitore di Azure
- Registro di sistema del contenitore di Azure 
- Archiviazione BLOB di Azure
- Application Insights
- Azure Key Vault
- Visual Studio Team Services
- Hub eventi di Azure
- Analisi di flusso di Azure Per altri dettagli o per visualizzare un esempio di fattura, visitare la [pagina dei prezzi di Azure](https://azure.microsoft.com/pricing/details/machine-learning-services/). 
