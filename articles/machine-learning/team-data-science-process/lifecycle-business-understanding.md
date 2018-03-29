---
title: Fase di comprensione del business del clic di vita del processo di data science per i team - Azure | Microsoft Docs
description: Obiettivi, attività e risultati finali per la fase di comprensione del business dei progetti di data science
services: machine-learning
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: bradsev
ms.openlocfilehash: daaaccaeac196b5f4651183173c8e83085d5184d
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
---
# <a name="business-understanding"></a>Informazioni commerciali

Questo argomento descrive gli obiettivi, le attività e i risultati finali associati alla fase di comprensione del business del processo di data science per i team. Questo processo offre un ciclo di vita consigliato da usare per strutturare i progetti di data science. Il ciclo di vita descrive le fasi principali eseguite in genere dai progetti, spesso in modo iterativo:

   1. **Comprensione del business**
   2. **Acquisizione e comprensione dei dati**
   3. **Modellazione**
   4. **Distribuzione**
   5. **Accettazione del cliente**

Ecco una rappresentazione visiva del ciclo di vita del processo TDSP: 

![Ciclo di vita del processo TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Obiettivi
* Specificare le variabili principali da usare come target di modelli e le cui metriche vengono usate per determinare la riuscita del progetto.
* Identificare le origini dati pertinenti a cui l'azienda ha accesso o per le quali deve ottenere l'accesso.

## <a name="how-to-do-it"></a>Procedura
Questa fase comprende due attività principali: 

   * **Definizione degli obiettivi**: interagire con il cliente e altre parti interessate per comprendere e identificare i problemi aziendali. Formulare domande che definiscano i target aziendali e a cui si possano applicare le tecniche di data science.
   * **Identificare le origini dati**: trovare i dati rilevanti che consentono di rispondere alle domande di definizione degli obiettivi del progetto.

### <a name="define-objectives"></a>Definire gli obiettivi
1. Un obiettivo centrale di questo passaggio consiste nell'identificare le variabili aziendali principali che l'analisi deve prevedere. Queste variabili vengono definite come *target di modello* e le metriche associate vengono usate per stabilire la riuscita del progetto. Le previsioni di vendita e la probabilità che un ordine sia una frode sono due esempi di questi tipi di target.

2. Definire gli obiettivi del progetto ponendo e creando domande "precise" e rilevanti, specifiche e non ambigue. Il data science è un processo che usa nomi e numeri per rispondere a tali domande. Per altre informazioni su come porre domande precise, vedere il blog [How to do Data Science](https://blogs.technet.microsoft.com/machinelearning/2016/03/28/how-to-do-data-science/) (Come applicare il data science). In genere si usa il data science o Machine Learning per rispondere a cinque tipi di domande:
 
   * In che quantità o in che numero? (regressione)
   * Quale categoria? (classificazione)
   * Quale gruppo? (clustering)
   * È strano? (rilevamento anomalie)
   * Quale opzione scegliere? (suggerimento)

   Determinare a quali di queste domande si intende rispondere e in che modo la risposta soddisfa gli obiettivi aziendali.

3. Definire il team del progetto specificando i ruoli e le responsabilità dei membri. Sviluppare un piano con attività cardine di alto livello da ripetere man mano che la quantità di informazioni acquisite aumenta. 

4. Definire le metriche di riuscita. Si supponga ad esempio di voler ottenere una stima del tasso di abbandono da parte dei clienti. Occorre un tasso di accuratezza di "x" % entro la fine di questo progetto che dura tre mesi. Con questi dati è possibile offrire ai clienti promozioni per ridurre il tasso di abbandono. Le metriche devono essere **SMART**: 

   * **S**pecific: specifiche 
   * **M**easurable: misurabili
   * **A**chievable: conseguibili 
   * **R**elevant: rilevanti 
   * **T**ime-bound: con associazione temporale 

### <a name="identify-data-sources"></a>Identificare le origini dati
Identificare le origini dati che contengono esempi noti di risposte a domande precise. Cercare i dati seguenti:

* Dati pertinenti alla domanda. Sono presenti misure del target e funzionalità correlate al target?
* Dati che rappresentano una misurazione accurata del target di modello e delle funzionalità di interesse.

Si può ad esempio scoprire che i sistemi esistenti devono raccogliere e registrare tipi di dati aggiuntivi per risolvere il problema e raggiungere gli obiettivi del progetto. In questo caso si possono cercare origini dati esterne o aggiornare i sistemi per raccogliere nuovi dati.

## <a name="artifacts"></a>Elementi
Ecco i risultati finali di questa fase:

   * [Documento di dichiarazione di intenti](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Charter.md): nella definizione della struttura del progetto TDSP è incluso un modello standard. Il documento di dichiarazione di intenti è sempre in evoluzione. Il modello viene aggiornato nel corso del progetto man mano che si ottengono nuove informazioni e cambiano i requisiti aziendali. È importante eseguire l'iterazione di questo documento, aggiungendo dettagli durante l'avanzamento del processo di scoperta. Coinvolgere il cliente e le altre parti interessate nelle modifiche e comunicare chiaramente l'importanza di tali modifiche.  
   * [Origini dati](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/Data%20Defintion.md#raw-data-sources): la sezione **Origini dati non elaborati** del report **Definizioni dei dati** disponibile nella cartella **Report dei dati** del progetto TDSP che contiene le origini dati. Questa sezione specifica le posizioni di origine e di destinazione dei dati non elaborati. Nelle fasi successive vengono inseriti dettagli aggiuntivi, ad esempio script per spostare i dati nell'ambiente di analisi.  
   * [Dizionari di dati](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Data_Dictionaries): questo documento contiene le descrizioni dei dati specificati dal client. Queste descrizioni includono informazioni sullo schema (tipi di dati e informazioni sulle regole di convalida, se presenti) e diagrammi sulle relazioni di entità, se disponibili.

## <a name="next-steps"></a>Passaggi successivi

Ecco i collegamenti a ogni passaggio del ciclo di vita del processo di data science per i team:

   1. [Comprensione del business](lifecycle-business-understanding.md)
   2. [Acquisizione e comprensione dei dati](lifecycle-data.md)
   3. [Modellazione](lifecycle-modeling.md)
   4. [Distribuzione](lifecycle-deployment.md)
   5. [Accettazione del cliente](lifecycle-acceptance.md)

Sono disponibili esercitazioni dettagliate complete che illustrano tutti i passaggi del processo per scenari specifici. L'articolo sulle [procedure dettagliate di esempio](walkthroughs.md) include un elenco degli scenari con i collegamenti e le descrizioni di anteprima. Le procedure dettagliate illustrano come combinare strumenti cloud, strumenti locali e servizi in un flusso di lavoro o in una pipeline per creare un'applicazione intelligente. 

Per esempi su come eseguire i passaggi nei processi di data science per i team (TDSP) che usano Azure Machine Learning Studio, vedere [Uso del processo di analisi scientifica dei dati per i team con Azure Machine Learning](http://aka.ms/datascienceprocess).
