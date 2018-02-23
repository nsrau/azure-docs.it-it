---
title: Note sulla versione sprint 2 di dicembre 2017 di Azure ML Workbench
description: Questo documento illustra in modo dettagliato gli aggiornamenti per la versione sprint 2 di Azure ML
services: machine-learning
author: raymondlaghaeian
ms.author: raymondl
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 12/04/2017
ms.openlocfilehash: 630e6e22bb41c777a043a7e6580239e254db9f1f
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2018
---
# <a name="sprint-2---december-2017"></a>Sprint 2 - Dicembre 2017 

#### <a name="version-number-01171115263"></a>Numero di versione: 0.1.1711.15263

>Ecco come [trovare il numero di versione](known-issues-and-troubleshooting-guide.md).

Benvenuti al terzo aggiornamento di Azure Machine Learning Workbench, che include miglioramenti all'app Workbench, all'interfaccia della riga di comando e ai servizi back-end. Si ringrazia per i commenti inviati. Molti degli aggiornamenti riportati di seguito sono una conseguenza diretta dei commenti ricevuti. 

## <a name="notable-new-features"></a>Nuove funzionalità importanti
- [Supporto per SQL Server e il database SQL di Azure come origine dati](data-prep-appendix2-supported-data-sources.md#types). 
- [Apprendimento avanzato su Spark con supporto GPU tramite MMLSpark](https://github.com/Azure/mmlspark/blob/master/docs/gpu-setup.md)
- [Compatibilità di tutti i contenitori AML con i dispositivi Azure IoT Edge quando vengono distribuiti (non sono necessarie operazioni aggiuntive)](http://aka.ms/aml-iot-edge-blog)
- Elenco modelli registrati e visualizzazioni Dettagli disponibili nel portale di Azure
- Accesso alle destinazioni di calcolo tramite l'autenticazione basata su chiave SSH oltre all'accesso basato su nome utente/password 
- Nuovo controllo della frequenza dei modelli nell'esperienza di preparazione dati 

## <a name="detailed-updates"></a>Aggiornamenti dettagliati
Di seguito è riportato un elenco degli aggiornamenti dettagliati in ogni area dei componenti di Azure Machine Learning in questo sprint.

### <a name="installer"></a>Programma di installazione
- Il programma di installazione può aggiornarsi automaticamente in modo da supportare le correzioni di bug e le nuove funzionalità senza che l'utente debba rieseguire l'installazione.

### <a name="workbench-authentication"></a>Autenticazione di Workbench
- Più correzioni al sistema di autenticazione. Inviare un commento nel caso in cui si verifichino ancora problemi di accesso.
- Modifiche all'interfaccia utente che rendono più semplice trovare le impostazioni di gestione proxy.

### <a name="workbench"></a>Workbench
- La visualizzazione dei file di sola lettura è ora su sfondo azzurro.
- Il pulsante Modifica è stato spostato a destra per renderlo più facilmente individuabile.
- È ora possibile eseguire il rendering dei formati di file "dsource", "dprep" e "ipynb" in un formato di testo non elaborato.
- È ora disponibile una nuova esperienza di modifica nel Workbench che guida gli utenti all'uso di un ambiente IDE esterno per modificare gli script e all'uso del Workbench solo per modificare i tipi di file con un'esperienza di modifica avanzata (ad esempio notebook, origini dati, pacchetti di preparazione dei dati).
- Il caricamento dell'elenco delle aree di lavoro e dei progetti a cui l'utente ha accesso è ora notevolmente più veloce.

### <a name="data-preparation"></a>Preparazione dei dati 
- È disponibile un controllo della frequenza dei modelli per visualizzare i modelli di stringa in una colonna. È anche possibile filtrare i dati tramite questi modelli. Comparirà una visualizzazione simile a quella del controllo dei conteggi di valore. La differenza è che la frequenza dei modelli mostra i conteggi dei modelli univoci dei dati, anziché i conteggi dei dati univoci. È anche possibile applicare filtri per visualizzare o escludere tutte le righe che soddisfano un determinato modello.

![Immagine del controllo della frequenza dei modelli sul numero del prodotto](media/release-notes-sprint-2/pattern-inspector-product-number.png)

- Miglioramenti delle prestazioni mentre si consigliano i casi limite da rivedere nella trasformazione "deriva colonna dall'esempio".

- [Supporto per SQL Server e il database SQL di Azure come origine dati](data-prep-appendix2-supported-data-sources.md#types). 

![Immagine della creazione di una nuova origine dati SQL server](media/release-notes-sprint-2/sql-server-data-source.png)

- È stata abilitata la visualizzazione "Riepilogo" dei conteggi di righe e colonne.

![Immagine del riepilogo dei conteggi di righe e colonne](media/release-notes-sprint-2/row-col-count.png)

- La preparazione dei dati è abilitata in tutti i contesti di calcolo.
- Le origini dati che usano un database SQL Server sono abilitate in tutti i contesti di calcolo.
- Le colonne della griglia di preparazione dei dati possono essere filtrate per tipo di dati.
- È stato risolto il problema relativo alla conversione di più colonne in data.
- È stato risolto il problema per cui un utente poteva selezionare una colonna di output come origine in Deriva colonna dall'esempio dopo avere modificato il nome della colonna di output nella modalità avanzata.

### <a name="job-execution"></a>Esecuzione dei processi
È ora possibile creare e accedere a una destinazione di calcolo di tipo cluster o remotedocker tramite l'autenticazione basata su chiave SSH eseguendo la procedura riportata di seguito:
- Collegare una destinazione di calcolo usando il comando seguente dell'interfaccia della riga di comando.

    ```azure-cli
    $ az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --use-azureml-ssh-key
    ```
[!NOTE] L'opzione -k (o --use-azureml-ssh-key) nel comando specifica di generare e usare la chiave SSH.

- Azure Machine Learning Workbench genererà una chiave pubblica e l'output nella console. Accedere alla destinazione di calcolo usando lo stesso nome utente e accodare questa chiave pubblica al file ~/.ssh/authorized_keys.

- È possibile preparare questa destinazione di calcolo e usarla per l'esecuzione. Azure Machine Learning Workbench userà questa chiave per l'autenticazione.  

Per altre informazioni sulla creazione di destinazioni di calcolo, vedere [Configurazione del servizio Sperimentazione di Azure Machine Learning](experimentation-service-configuration.md).

### <a name="visual-studio-tools-for-ai"></a>Visual Studio Tools for AI
- È stato aggiunto il supporto per [Visual Studio Tools for AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vstoolsai-vs2017). 

### <a name="command-line-interface-cli"></a>Interfaccia della riga di comando
- È stato aggiunto il comando `az ml datasource create` che consente di creare un file di origine dati dalla riga di comando.

### <a name="model-management-and-operationalization"></a>Gestione modelli e operazionalizzazione
- [Compatibilità di tutti i contenitori AML con i dispositivi Azure IoT Edge quando vengono resi operativi (non sono necessarie operazioni aggiuntive)](http://aka.ms/aml-iot-edge-blog). 
- Miglioramenti dei messaggi di errore nell'interfaccia della riga di comando o16n.
- Correzioni di bug nel portale di gestione modelli UX.  
- Combinazione coerente di maiuscole/minuscole per gli attributi di gestione modelli nella pagina di dettaglio.
- Timeout delle chiamate del punteggio in tempo reale impostato su 60 secondi.
- Elenco modelli registrati e visualizzazioni Dettagli disponibili nel portale di Azure.

![dettagli modello nel portale](media/release-notes-sprint-2/model-list.jpg)

![panoramica modello nel portale](media/release-notes-sprint-2/model-overview-portal.jpg)

### <a name="mmlspark"></a>MMLSpark
- Apprendimento avanzato su Spark con [supporto GPU](https://github.com/Azure/mmlspark/blob/master/docs/gpu-setup.md).
- Supporto per modelli di Resource Manager per la distribuzione semplificata delle risorse.
- Supporto per l'ecosistema SparklyR.
- [Integrazione di AZTK](https://github.com/Azure/aztk/wiki/Spark-on-Azure-for-Python-Users#optional-set-up-mmlspark).

### <a name="sample-projects"></a>Progetti di esempio
- Gli esempi [Iris](https://github.com/Azure/MachineLearningSamples-Iris) e [MMLSpark](https://github.com/Azure/mmlspark) sono stati aggiornati con la nuova versione SDK di Azure Machine Learning.

## <a name="breaking-changes"></a>MODIFICHE DI RILIEVO
- L'opzione `--type` in `az ml computetarget attach` è stata alzata di livello a sottocomando. 

    - `az ml computetarget attach --type remotedocker` è ora `az ml computetarget attach remotedocker`
    - `az ml computetarget attach --type cluster` è ora `az ml computetarget attach cluster`
