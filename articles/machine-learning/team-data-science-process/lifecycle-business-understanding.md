---
title: Fase di comprensione del business del ciclo di vita del processo di data science per i team - Azure | Microsoft Docs
description: "Obiettivi, attività e risultati finali per la fase di comprensione del business dei progetti di data science."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/02/2017
ms.author: bradsev;
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 2adce61f8185bd86a6a870bb5752fe936701b0af
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---
# <a name="business-understanding"></a>Informazioni commerciali

Questo argomento descrive gli obiettivi, le attività e i risultati finali associati alla **fase di comprensione del business** del processo di data science per i team. Questo processo fornisce un ciclo di vita consigliato da usare per strutturare i progetti di data science. Il ciclo di vita descrive le fasi principali eseguite in genere dai progetti, spesso in modo iterativo:

* **Informazioni commerciali**
* **Acquisizione e comprensione dei dati**
* **Modellazione**
* **Distribuzione**
* **Accettazione del cliente**

Di seguito si riporta una rappresentazione visiva del **Ciclo di vita del processo di analisi scientifica dei dati di Team**. 

![Ciclo di vita del processo di data science per i team 2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Obiettivi
* Vengono specificate le **variabili principali** da usare come **target dei modelli** e le relative metriche vengono usate per determinare la riuscita del progetto.
* Vengono identificate le **origini dati** pertinenti a cui l'azienda ha accesso o per le quali deve ottenere l'accesso.

## <a name="how-to-do-it"></a>Procedura
Questa fase comprende due attività principali: 

* **Definizione degli obiettivi**: interagire con il cliente e altre parti interessate per comprendere e identificare i problemi aziendali. Formulare domande che definiscano gli obiettivi aziendali e a cui possono essere destinate le tecniche di analisi scientifica dei dati.
* **Identificare le origini dati**: trovare i dati rilevanti che consentono di rispondere alle domande di definizione degli obiettivi del progetto.

### <a name="11-define-objectives"></a>1.1 Definire gli obiettivi

1. Un obiettivo centrale di questo passaggio consiste nell'identificare le **variabili aziendali** principali che l'analisi deve prevedere. Queste variabili vengono definite come **target dei modelli** e le metriche associate vengono usate per stabilire la riuscita del progetto. Le previsioni di vendita o la probabilità di frode di un ordine sono due esempi di questi tipi di target.

2. Definire gli **obiettivi del progetto** ponendo e creando domande "precise" e rilevanti, specifiche e non ambigue. L'analisi scientifica dei dati è il processo di uso di nomi e numeri per rispondere a queste domande. Per altre informazioni su come porre domande precise, vedere il blog [How to do Data Science](https://blogs.technet.microsoft.com/machinelearning/2016/03/28/how-to-do-data-science/) (Come eseguire l'analisi scientifica dei dati). L'analisi scientifica dei dati o il Machine Learning vengono in genere usati per rispondere a cinque tipi di domande:
 
   * In che quantità o in che numero? (regressione)
   * Quale categoria? (classificazione)
   * Quale gruppo? (clustering)
   * È strano? (rilevamento anomalie)
   * Quale opzione scegliere? (suggerimento)

    Determinare quali di queste domande si sta ponendo e come rispondendo ad esse sia possibile conseguire gli obiettivi aziendali.

3. Definire il **team del progetto** specificando i ruoli e le responsabilità dei membri. Sviluppare un piano di alto livello da ripetere man mano che la quantità di informazioni acquisite aumenta.  

4. **Definire le metrica di riuscita**. Ad esempio: ottenere l'accuratezza della stima della varianza del cliente di X% entro la fine di questo progetto della durata di 3 mesi, in modo da poter offrire promozioni per ridurre la varianza. Le metriche devono essere **SMART**: 
   * **S**pecific: specifiche 
   * **M**easurable: misurabili
   * **A**chievable: conseguibili 
   * **R**elevant: rilevanti 
   * **T**ime-bound: con associazione temporale 

### <a name="12-identify-data-sources"></a>1.2 Identificare le origini dei dati
Identificare le origini dati che contengono esempi noti di risposte a domande precise. Cercare i dati seguenti:

* Dati **pertinenti** alla domanda. Sono presenti misure del target e caratteristiche correlate al target?
* Dati che non rappresentano una **misurazione accurata** del target del modello e delle funzionalità di interesse.

Non è ad esempio insolito scoprire che i sistemi esistenti devono raccogliere e registrare tipi di dati aggiuntivi per risolvere il problema e raggiungere gli obiettivi del progetto. In questo caso, si vuole cercare origini dati esterne o aggiornare i sistemi per raccogliere dati più recenti.

## <a name="artifacts"></a>Elementi
Ecco i risultati finali di questa fase:

* [**Documento di dichiarazione di intenti**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Charter.md): nella definizione della struttura del progetto TDSP viene fornito un modello standard. Si tratta di un documento aggiornato nel corso del progetto quando vengono effettuate nuove scoperte e quando i requisiti dell'azienda cambiano. È importante eseguire l'iterazione di questo documento, aggiungendo dettagli durante l'avanzamento del processo di scoperta. Coinvolgere il cliente e le altre parti interessate nelle modifiche e comunicare chiaramente l'importanza di tali modifiche.  
* [**Origini dati**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/Data%20Defintion.md#raw-data-sources): si tratta della sezione **Origini dati non elaborati** del report **Definizioni dei dati** disponibile nella cartella **Report dei dati** del progetto TDSP. Specifica i percorsi di origine e di destinazione dei dati non elaborati. Nelle fasi successive vengono inseriti dettagli aggiuntivi, ad esempio gli script per spostare i dati nell'ambiente di analisi.  
* [**Dizionari di dati**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/DataDictionaries): questo documento contiene le descrizioni dei dati forniti dal client. Queste descrizioni includono informazioni sullo schema (tipi di dati e informazioni sulle regole di convalida, se presenti) e diagrammi sulle relazioni di entità, se disponibili.

## <a name="next-steps"></a>Passaggi successivi

Ecco i collegamenti a ogni passaggio del ciclo di vita del processo di data science per i team:

* [1. Comprensione del business](lifecycle-business-understanding.md)
* [2. Acquisizione e comprensione dei dati](lifecycle-data.md)
* [3. Modellazione](lifecycle-modeling.md)
* [4. Distribuzione](lifecycle-deployment.md)
* [5. Accettazione del cliente](lifecycle-acceptance.md)

Sono anche disponibili esercitazioni dettagliate complete che illustrano tutti i passaggi del processo per **scenari specifici** . Sono elencate, con collegamenti e brevi descrizioni, nell'argomento [Procedure dettagliate di esempio](walkthroughs.md). Viene spiegato come combinare strumenti cloud, strumenti locali e servizi in un flusso di lavoro o in una pipeline per creare un'applicazione intelligente. 

Per esempi di esecuzione dei passaggi nel processo di data science per i team, che usano Azure Machine Learning Studio, vedere il percorso di apprendimento [Con Azure ML](http://aka.ms/datascienceprocess).
