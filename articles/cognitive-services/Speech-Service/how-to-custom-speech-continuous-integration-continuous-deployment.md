---
title: Integrazione continua/distribuzione continua per il servizio di Riconoscimento vocale personalizzato vocale
titleSuffix: Azure Cognitive Services
description: Applicare DevOps con i flussi di lavoro Riconoscimento vocale personalizzato e CI/CD. Implementare una soluzione DevOps esistente per il proprio progetto.
services: cognitive-services
author: KatieProchilo
manager: cmayomsft
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/09/2020
ms.author: kaprochi
ms.openlocfilehash: de0065abaf5669859e864186fc9a3fb88219414b
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94555822"
---
# <a name="cicd-for-custom-speech"></a>CI/CD per Riconoscimento vocale personalizzato

Implementare il training, il test e la gestione delle versioni automatizzati per consentire un miglioramento continuo dei modelli di Riconoscimento vocale personalizzato quando si applicano gli aggiornamenti ai dati di training e di testing. Grazie all'implementazione efficace dei flussi di lavoro CI/CD, è possibile verificare che l'endpoint per il modello di Riconoscimento vocale personalizzato con le prestazioni migliori sia sempre disponibile.

L' [integrazione continua](https://docs.microsoft.com/azure/devops/learn/what-is-continuous-integration) (ci) è la pratica di progettazione di un commit frequente degli aggiornamenti in un repository condiviso e l'esecuzione di una compilazione automatizzata. I flussi di lavoro CI per Riconoscimento vocale personalizzato eseguono il training di un nuovo modello dalle origini dati ed eseguono test automatizzati sul nuovo modello per garantire prestazioni migliori rispetto al modello precedente.

Il [recapito continuo](https://docs.microsoft.com/azure/devops/learn/what-is-continuous-delivery) (CD) accetta modelli dal processo ci e crea un endpoint per ogni modello di riconoscimento vocale personalizzato migliorato. CD rende gli endpoint facilmente disponibili per l'integrazione nelle soluzioni.

Sono possibili soluzioni di integrazione continua/recapito continuo personalizzate, ma per una soluzione solida e precompilata, usare l' [Archivio di modelli di DevOps vocale](https://github.com/Azure-Samples/Speech-Service-DevOps-Template), che esegue i flussi di lavoro ci/CD usando le azioni di GitHub.

## <a name="cicd-workflows-for-custom-speech"></a>Flussi di lavoro CI/CD per Riconoscimento vocale personalizzato

Lo scopo di questi flussi di lavoro è garantire che ogni modello di Riconoscimento vocale personalizzato abbia un'accuratezza del riconoscimento migliore rispetto alla compilazione precedente. Se gli aggiornamenti ai dati di testing e/o di training migliorano l'accuratezza, questi flussi di lavoro creano un nuovo endpoint Riconoscimento vocale personalizzato.

I server Git come GitHub e Azure DevOps possono eseguire flussi di lavoro automatizzati quando si verificano eventi git specifici, ad esempio unioni o richieste pull. Ad esempio, un flusso di lavoro CI può essere attivato quando viene eseguito il push degli aggiornamenti ai dati di test nel ramo *Master* . Server Git diversi avranno strumenti diversi, ma consentiranno di eseguire lo scripting di comandi dell'interfaccia della riga di comando (CLI) in modo che possano essere eseguiti in un server di compilazione.

Lungo il percorso, i flussi di lavoro devono denominare e archiviare dati, test, file di test, modelli ed endpoint in modo che possano essere ritracciati nel commit o nella versione da cui provengono. È anche utile assegnare un nome a questi asset in modo che sia facile vedere quali sono stati creati dopo l'aggiornamento dei dati di test rispetto ai dati di training.

### <a name="ci-workflow-for-testing-data-updates"></a>Flusso di lavoro CI per il test degli aggiornamenti dei dati

Lo scopo principale dei flussi di lavoro di integrazione continua/recapito continuo è quello di compilare un nuovo modello usando i dati di training e di testare tale modello usando i dati di test per stabilire se la [frequenza degli errori di Word](how-to-custom-speech-evaluate-data.md#evaluate-custom-speech-accuracy) (WER) è stata migliorata rispetto al modello con le migliori prestazioni precedente (il "modello di benchmark"). Se il nuovo modello ha prestazioni migliori, diventa il nuovo modello di benchmark rispetto al quale vengono confrontati i modelli futuri.

Il flusso di lavoro CI per il test degli aggiornamenti dei dati deve testare nuovamente il modello di benchmark corrente con i dati di test aggiornati per calcolare il WER modificato. In questo modo, quando il valore di un nuovo modello viene confrontato con il tipo di dati del benchmark, entrambi i modelli sono stati testati con gli stessi dati di test e si sta confrontando come con like.

Questo flusso di lavoro deve attivare gli aggiornamenti dei dati di test e:

- Testare il modello di benchmark rispetto ai dati di test aggiornati.
- Archiviare l'output del test, che contiene l'WER del modello di benchmark, usando i dati aggiornati.
- Il WER di questi test diventerà il nuovo punto di riferimento che i modelli futuri devono superare.
- Il flusso di lavoro CD non viene eseguito per gli aggiornamenti dei dati di testing.

### <a name="ci-workflow-for-training-data-updates"></a>Flusso di lavoro CI per gli aggiornamenti dei dati di training

Gli aggiornamenti ai dati di training indicano gli aggiornamenti al modello personalizzato.

Questo flusso di lavoro deve attivare aggiornamenti per i dati di training e:

- Eseguire il training di un nuovo modello con i dati di training aggiornati.
- Testare il nuovo modello in base ai dati di test.
- Archiviare l'output del test che contiene l'oggetto WER.
- Confrontare il WER dal nuovo modello con il WER dal modello di benchmark.
- Se il WER non migliora, arrestare il flusso di lavoro.
- Se il valore di WER è migliore, eseguire il flusso di lavoro CD per creare un endpoint Riconoscimento vocale personalizzato.

### <a name="cd-workflow"></a>Flusso di lavoro CD

Dopo un aggiornamento ai dati di training, il flusso di lavoro CD deve essere eseguito automaticamente per creare un nuovo endpoint per il modello e renderlo disponibile in modo tale da poter essere utilizzato in una soluzione.

#### <a name="release-management"></a>Gestione versioni

La maggior parte dei team richiede un processo di revisione e approvazione manuale per la distribuzione in un ambiente di produzione. Per una distribuzione di produzione, è consigliabile assicurarsi che si verifichi quando le persone chiave del team di sviluppo sono disponibili per il supporto o durante i periodi di traffico ridotto.

### <a name="tools-for-custom-speech-workflows"></a>Strumenti per flussi di lavoro Riconoscimento vocale personalizzato

Usare gli strumenti seguenti per i flussi di lavoro di automazione CI/CD per Riconoscimento vocale personalizzato:

- [Interfaccia](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) della riga di comando di Azure per creare un'autenticazione dell'entità servizio di Azure, eseguire query sulle sottoscrizioni di Azure e archiviare i risultati dei test nel BLOB
- [Interfaccia](spx-overview.md) della riga di comando di Azure per interagire con il servizio riconoscimento vocale dalla riga di comando o da un flusso di lavoro automatizzato.

## <a name="devops-solution-for-custom-speech-using-github-actions"></a>Soluzione DevOps per Riconoscimento vocale personalizzato usando le azioni di GitHub

Per una soluzione DevOps già implementata per Riconoscimento vocale personalizzato, passare al [repository del modello DevOps vocale](https://github.com/Azure-Samples/Speech-Service-DevOps-Template). Creare una copia del modello e iniziare lo sviluppo di modelli personalizzati con un sistema DevOps affidabile che includa i test, il training e il controllo delle versioni usando le azioni di GitHub. Il repository fornisce dati di testing e di training di esempio per facilitare l'installazione e la descrizione del flusso di lavoro. Dopo l'installazione iniziale, sostituire i dati di esempio con i dati del progetto.

Il [repository del modello DevOps vocale](https://github.com/Azure-Samples/Speech-Service-DevOps-Template) fornisce l'infrastruttura e le linee guida dettagliate per:

- Copiare il repository di modelli nell'account GitHub, quindi creare risorse di Azure e un' [entità servizio](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) per i flussi di lavoro di integrazione continua/distribuzione continua di GitHub.
- Esaminare il "[ciclo interno di sviluppo](https://mitchdenny.com/the-inner-loop/)". Aggiornare i dati di training e di test da un branch di funzionalità, testare le modifiche con un modello di sviluppo temporaneo e generare una richiesta pull per proporre ed esaminare le modifiche.
- Quando i dati di training vengono aggiornati in una richiesta pull al *database master* , eseguire il training dei modelli con il flusso di lavoro ci azioni github.
- Eseguire test di accuratezza automatici per stabilire la [frequenza degli errori di Word](how-to-custom-speech-evaluate-data.md#evaluate-custom-speech-accuracy) (WER) del modello. Archiviare i risultati dei test nel BLOB di Azure.
- Eseguire il flusso di lavoro CD per creare un endpoint quando il WER migliora.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su DevOps con sintesi vocale:

- Usare il [repository del modello di DevOps vocale](https://github.com/Azure-Samples/Speech-Service-DevOps-Template) per implementare DevOps per riconoscimento vocale personalizzato con le azioni di GitHub.
