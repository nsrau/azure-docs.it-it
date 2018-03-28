---
title: Note sulla versione sprint 3 di gennaio 2018 di Azure ML Workbench
description: Questo documento illustra in modo dettagliato gli aggiornamenti per la versione sprint 3 di Azure ML
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 01/22/2018
ms.openlocfilehash: fa209ba2259ae82796556fc1229cd6944c7a2ae1
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="sprint-3---january-2018"></a>Sprint 3 - Gennaio 2018 

#### <a name="version-number-01171218263"></a>Numero di versione: 0.1.1712.18263

>Ecco come [trovare il numero di versione](known-issues-and-troubleshooting-guide.md).

Benvenuti al quarto aggiornamento di Azure Machine Learning Workbench. Di seguito è riportato un elenco degli aggiornamenti e miglioramenti in questo sprint. Molti degli aggiornamenti sono una conseguenza diretta del feedback ricevuto. 

## <a name="notable-new-features-and-changes"></a>Nuove funzionalità e modifiche importanti
- Gli aggiornamenti allo stack di autenticazione forzano la selezione di login e account all'avvio

## <a name="detailed-updates"></a>Aggiornamenti dettagliati
Di seguito è riportato un elenco degli aggiornamenti dettagliati in ogni area dei componenti di Azure Machine Learning in questo sprint.

### <a name="workbench"></a>Workbench
- Possibilità di installare o disinstallare l'app da Installazione applicazioni
- Gli aggiornamenti allo stack di autenticazione forzano la selezione di login e account all'avvio
- Esperienza di Single Sign-On (SSO) migliorata in Windows
- Gli utenti che appartengono a più tenant con credenziali diverse ora saranno in grado di accedere a Workbench

#### <a name="ui"></a>UI
- Correzioni di bug e miglioramenti generali

### <a name="notebooks"></a>Notebook
- Correzioni di bug e miglioramenti generali

### <a name="data-preparation"></a>Preparazione dei dati 
- Miglioramento dei suggerimenti automatici migliorata durante l'esecuzione di trasformazioni in base a esempi
- Algoritmo migliorato per il controllo della frequenza
- Possibilità di inviare dati di esempio e feedback durante l'esecuzione di trasformazioni in base a esempi ![Immagine del collegamento per l'invio di feedback nella trasformazione di derivazione colonna](media/release-notes-sprint-3/SendFeedbackFromDeriveColumn.png)
- Miglioramenti del runtime Spark
- Scala ha sostituito Pyspark
- Risolta l'impossibilità di chiudere i dati non applicabili per la funzione di controllo delle serie temporali 
- Risolto il tempo di blocco per l'esecuzione di preparazione dei dati per HDI

### <a name="model-management-cli-updates"></a>Aggiornamenti all'interfaccia della riga di comando di Gestione modelli 
  - Per il provisioning di risorse non è più necessaria la proprietà della sottoscrizione. Per configurare l'ambiente di distribuzione sarà sufficiente l'accesso al gruppo di risorse di tipo Collaboratore.
  - Abilitata la configurazione dell'ambiente locale per le sottoscrizioni gratuite 
