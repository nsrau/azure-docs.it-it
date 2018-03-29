---
title: Note sulla versione sprint 0 di ottobre 2017 di Azure ML Workbench
description: Questo documento illustra in modo dettagliato gli aggiornamenti per la versione sprint 0 di Azure ML
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 10/12/2017
ms.openlocfilehash: 37e0a4461e8a0de631a6194a1eb8cc16b610954f
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2018
---
# <a name="sprint-0---october-2017"></a>Sprint 0 - Ottobre 2017 

**Numero di versione: 0.1.1710.04013**

È uscito il primo aggiornamento di Azure Machine Learning Workbench in seguito alla versione di anteprima pubblica iniziale in occasione della conferenza Microsoft Ignite 2017. Gli aggiornamenti principali di questa versione sono costituiti da correzioni relative all'affidabilità e alla stabilizzazione.  Ecco alcuni dei problemi critici risolti:

## <a name="new-features"></a>Nuove funzionalità
- macOS High Sierra è ora supportato

## <a name="bug-fixes"></a>Correzioni di bug
### <a name="workbench-experience"></a>Esperienza in Workbench
- Un'operazione di trascinamento e rilascio di un file in Workbench causa l'arresto anomalo di Workbench.
- La finestra del terminale in VS Code configurata come IDE per Workbench non riconosce i comandi _az ml_.

### <a name="workbench-authentication"></a>Autenticazione di Workbench
Sono stati apportati numerosi aggiornamenti per migliorare i vari problemi di accesso e autenticazione segnalati.
- La finestra di autenticazione viene continuamente visualizzata, in particolare quando la connessione Internet non è stabile.
- Sono stati apportati miglioramenti per i problemi di affidabilità relativi alla scadenza del token di autenticazione.
- In alcuni casi, la finestra di autenticazione viene visualizzata due volte.
- La finestra principale di Workbench continua a visualizzare un messaggio che indica che è in corso l'autenticazione anche quando il processo di autenticazione è in realtà terminato e la finestra di dialogo popup è già stata chiusa.
- Se non c'è la connessione Internet, la finestra di dialogo di autenticazione viene visualizzata con una schermata vuota.

### <a name="data-preparation"></a>Preparazione dei dati 
- Quando viene filtrato un valore specifico, vengono filtrati anche gli errori e i valori mancanti.
- La modifica di una strategia di campionamento comporta la rimozione delle successive operazioni di join esistenti.
- La trasformazione di sostituzione dei valori mancanti non prende in considerazione i valori NaN (Not a Number, non un numero).
- L'inferenza del tipo di dati genera un'eccezione quando viene rilevato un valore Null.

### <a name="job-execution"></a>Esecuzione dei processi
- Non c'è un chiaro messaggio di errore quando, durante l'esecuzione dei processi, non è possibile caricare la cartella di progetto perché è stato superato il limite di dimensioni.
- Se lo script Python dell'utente cambia la directory di lavoro, non viene tenuta traccia dei file scritti nelle cartelle di output. 
- Se la sottoscrizione di Azure attiva è diversa da quella a cui appartiene il progetto corrente, l'invio dei processi provoca un errore 403.
- Quando Docker non è presente, non viene restituito un chiaro messaggio di errore se l'utente cerca di usare Docker come destinazione di esecuzione.
- Il file con estensione runconfig non viene salvato automaticamente quando l'utente fa clic sul pulsante _Esegui_.

### <a name="jupyter-notebook"></a>Notebook Jupyter
- Non è possibile avviare il server Notebook se l'utente usa determinati tipi di account di ascesso.
- I messaggi di errore del server Notebook non sono presenti nei log visibili all'utente.

### <a name="azure-portal"></a>Portale di Azure
- Se si seleziona il tema scuro del portale di Azure, il pannello di Gestione modelli viene visualizzato come una casella nera.

### <a name="operationalization"></a>Operazionalizzazione
- Se si riutilizza un manifesto per aggiornare un servizio Web, viene creata una nuova immagine Docker con un nome casuale.
- Non è possibile recuperare i log del servizio Web dal cluster Kubernetes.
- Viene visualizzato un messaggio di errore fuorviante quando l'utente cerca di creare un account di Gestione modelli o un account di calcolo di ML e si verificano problemi relativi alle autorizzazioni.
