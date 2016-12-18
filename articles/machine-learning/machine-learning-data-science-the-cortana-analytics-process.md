---
title: Informazioni sul processo di analisi scientifica dei dati per i team  | Documentazione Microsoft
description: "Il Processo di analisi scientifica dei dati per i team è un metodo sistematico della scienza dei dati per lo sviluppo di applicazioni intelligenti che sfruttano le analisi avanzate."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: a098aa2e-fd79-4543-8e15-9aae9d8b3ee6
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: bradsev
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 49bb8201a93e622774e197801b566caa03ed28a6


---
# <a name="what-is-the-team-data-science-process-tdsp"></a>Informazioni sul Processo di analisi scientifica dei dati per i team (TDSP)
Il [Processo di analisi scientifica dei dati per i team (TDSP)](data-science-process-overview.md) offre un approccio sistematico alla creazione di applicazioni intelligenti che permettono ai team di data scientist di collaborare in modo efficace per l'intero ciclo di vita delle attività necessarie per trasformare queste applicazioni in prodotti. Il TDSP illustra una sequenza di passaggi che fornisce **istruzioni** su come definire il problema, predisporre gli strumenti e l'ambiente necessario, analizzare i dati pertinenti, compilare e valutare i modelli predittivi e quindi distribuire tali modelli nelle applicazioni aziendali. 

Di seguito sono riportati i passaggi del **Processo di analisi scientifica dei dati per i team**:  

![Flusso di lavoro CAP](./media/machine-learning-data-science-the-cortana-analytics-process/CAP-workflow.png)

Il processo è **iterativo**: la comprensione di elementi nuovi ed esistenti o perfezionati nel modello si evolve e richiede la rielaborazione di passaggi completati in precedenza nella sequenza. I processi esistenti per lo sviluppo organizzativo e la pianificazione del progetto possono essere **facilmente adattati** per l'uso con la sequenza di passaggi definita da TDSP. 

I passaggi del processo sono rappresentati graficamente e collegati nel [percorso di apprendimento TDSP](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) , nonché descritti di seguito.  

## <a name="preparation-steps"></a>Passaggi preliminari
## <a name="p1-plan-the-analytics-project"></a>P1. Pianificare il progetto di analisi
Avviare un progetto di analisi definendo gli obiettivi aziendali e i problemi, specificandoli in termini di **requisiti aziendali**. Un obiettivo essenziale di questo passaggio consiste nell'identificare le variabili aziendali principali, ad esempio le previsioni di vendita o la probabilità che un ordine sia illecito, che l'analisi deve prevedere per soddisfare i requisiti. Sono quindi in genere necessarie operazioni aggiuntive di pianificazione per sviluppare una comprensione delle **origini dati** necessarie per soddisfare gli obiettivi del progetto da un punto di vista analitico. Non è ad esempio insolito scoprire che i sistemi esistenti devono raccogliere e registrare tipi di dati aggiuntivi per risolvere il problema e raggiungere gli obiettivi del progetto. Per istruzioni, vedere [Pianificare l'ambiente per il processo di analisi scientifica dei dati per i team](machine-learning-data-science-plan-your-environment.md) e [Scenari per l'analisi avanzata in Azure Machine Learning](machine-learning-data-science-plan-sample-scenarios.md).  

## <a name="p2-setup-analytics-environment"></a>P2. Configurare l'ambiente di analisi
Un ambiente di analisi per il TDSP include numerosi componenti: 

* **Aree di lavoro dati** , in cui i dati sono gestiti in modalità temporanea per l'analisi e la modellazione. 
* **Infrastruttura di elaborazione** per la pre-elaborazione, l'esplorazione e la modellazione dei dati.
* **Infrastruttura di runtime** per rendere operativi i modelli analitici e quindi eseguire le applicazioni client intelligenti che usano i modelli.  

L'infrastruttura di analisi da configurare è spesso parte di un ambiente distinto dai sistemi operativi principali, ma in genere sfrutta i dati provenienti da più sistemi entro l'azienda, oltre da origini esterne all'azienda. L'infrastruttura di analisi può essere basata esclusivamente sul cloud oppure può avere una configurazione locale o una combinazione di entrambe. Per informazioni sulle opzioni, vedere [Impostare gli ambienti per la scienza dei dati per l'uso nel Processo di analisi scientifica dei dati per i team](machine-learning-data-science-environment-setup.md).

## <a name="analytics-steps"></a>Passaggi di analisi:
## <a name="1-ingest-data-into-the-analytical-environment"></a>1. Inserire dati nell'ambiente di analisi
Il primo passaggio consiste nell'inserire i dati rilevanti provenienti da diverse origini, interne o esterne all'organizzazione, in un ambiente di analisi in cui possano essere elaborati. Il **formato** dei dati all'origine può essere diverso dal formato richiesto dalla destinazione. È quindi possibile che lo strumento di inserimento debba eseguire alcune trasformazioni di dati. Per le opzioni, vedere [Caricare i dati in ambienti di archiviazione per l'analisi](machine-learning-data-science-ingest-data.md)

Oltre all'inserimento iniziale dei dati, molte applicazioni intelligenti devono aggiornare regolarmente i dati come parte di un processo di apprendimento continuativo. Per ottenere questo risultato, è possibile configurare una **pipeline di dati** o un flusso di lavoro. Ciò fa parte della fase iterativa del processo che include la ricompilazione e la rivalutazione dei modelli analitici usati dall'applicazione intelligente che distribuisce la soluzione. Vedere, ad esempio, [Spostare i dati da un server SQL locale a SQL Azure con il Data factory di Azure](machine-learning-data-science-move-sql-azure-adf.md).

## <a name="2-explore-and-pre-process-data"></a>2. Esplorare e pre-elaborare i dati
Il passaggio successivo consiste nell'ottenere una migliore comprensione dei dati esaminando le relative **statistiche riepilogative** e relazioni e usando tecniche quali la **visualizzazione**. In questa fase vengono anche gestiti i problemi relativi alla **qualità dei dati** e all'integrità, ad esempio valori mancanti, mancate corrispondenze tra tipi di dati e relazioni non coerenti tra i dati. Le trasformazioni di pre-elaborazione vengono usate per pulire i dati non elaborati prima di eseguire operazioni aggiuntive di analisi e modellazione. Per una descrizione, vedere [Attività per preparare i dati per operazioni avanzate con Machine Learning](machine-learning-data-science-prepare-data.md).

## <a name="3-develop-features"></a>3. Sviluppare funzionalità
Gli esperti di scienza dei dati, insieme agli esperti di dominio, devono identificare le funzionalità che acquisiscono le proprietà rilevanti del set di dati e che possono essere usate per prevedere in modo ottimale le variabili aziendali essenziali identificate durante la pianificazione. Queste nuove funzionalità possono essere derivate dai dati esistenti o possono richiedere la raccolta di dati aggiuntivi. Questo processo è noto come **progettazione di funzionalità** ed è uno dei passaggi principali per la creazione di un sistema di analisi predittiva efficace. Questo passaggio richiede una combinazione creativa di competenze a livello di dominio e di informazioni approfondite ottenute dal passaggio di esplorazione dei dati. Per istruzioni, vedere [Progettazione di funzionalità in Cortana Analytics Process](machine-learning-data-science-create-features.md).

## <a name="4-create-predictive-models"></a>4. Creare modelli produttivi
Gli esperti di scienza dei dati sviluppano modelli analitici per prevedere le variabili principali identificate dai requisiti aziendali definiti nella fase di pianificazione mediante i dati puliti e trasformati in funzionalità. I sistemi di Machine Learning supportano più **algoritmi di modellazione** applicabili a una vasta gamma di casi. Per istruzioni, vedere [Come scegliere gli algoritmi di Microsoft Azure Machine Learning](machine-learning-algorithm-choice.md).

Gli esperti di scienza dei dati devono scegliere il modello più appropriato per l'attività di previsione e non è raro che sia necessario combinare i risultati da più modelli per ottenere i risultati ottimali. I dati di input per la modellazione vengono in genere suddivisi casualmente in tre parti:

* Set di dati di training 
* Set di dati di convalida 
* Set di dati di test 

I modelli vengono sviluppati tramite il **set di dati di training**. La combinazione ottimale di modelli, con parametri perfezionati, viene selezionata eseguendo i modelli e misurando gli errori di previsione per il **set di dati di convalida**. Viene infine usato il **set di dati di test** per valutare le prestazioni del modello scelto su dati indipendenti non usati per il training o la convalida del modello.  Per le procedura, vedere [Come valutare le prestazioni del modello in Azure Machine Learning](machine-learning-evaluate-model-performance.md).

## <a name="5-deploy-and-consume-models"></a>5. Distribuire e utilizzare i modelli
Quando si ottiene un set di modelli con prestazioni ottimali, è possibile **renderli operativi** per l'utilizzo da parte di altre applicazioni. In base ai requisiti aziendali, le previsioni vengono eseguite in **tempo reale** o in **batch**. Per essere resi operativi, i modelli devono essere esposti con un' **interfaccia API aperta** che può essere facilmente usata da diverse applicazioni, ad esempio un sito Web online, fogli di calcolo, dashboard applicazioni line-of-business e back-end. Vedere, [Distribuire un servizio Web di Azure Machine Learning](machine-learning-publish-a-machine-learning-web-service.md).

## <a name="summary-and-next-steps"></a>Riepilogo e passaggi successivi
Il [Processo di analisi scientifica dei dati per i team](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) viene modellato come sequenza di passaggi iterati che **offrono indicazioni** sulle attività necessarie per usare l'analisi avanzata al fine di sviluppare un'applicazione intelligente. Ogni passaggio fornisce anche i dettagli su come usare diverse tecnologie Microsoft per completare l'attività descritta. 

Anche se TDSP non indica tipi specifici di elementi di **documentazione** , è consigliabile documentare i risultati dell'esplorazione dei dati, della modellazione e della convalida e quindi salvare il codice pertinente in modo che sia possibile iterare l'analisi quando necessario. Ciò consente anche il riutilizzo delle operazioni di analisi quando si lavora su altre applicazioni che includono dati e attività di previsioni simili.

Sono anche disponibili esercitazioni dettagliate complete che illustrano tutti i passaggi del processo per **scenari specifici** . Per esempi, vedere:

* [Processo di analisi scientifica dei dati per i team in azione: uso di SQL Sever](machine-learning-data-science-process-sql-walkthrough.md)
* [Processo di analisi scientifica dei dati per i team in azione: uso dei cluster Hadoop di HDInsight](machine-learning-data-science-process-hive-walkthrough.md).
* [Analisi scientifica dei dati con Spark in Azure HD.mdnsight](machine-learning-data-science-spark-overview.md)
* [Analisi scientifica dei dati scalabile in Azure Data Lake: procedura dettagliata end-to-end](machine-learning-data-science-process-data-lake-walkthrough.md)




<!--HONumber=Nov16_HO3-->


