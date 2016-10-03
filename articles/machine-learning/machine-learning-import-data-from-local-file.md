<properties
	pageTitle="Importare dati in Machine Learning Studio da un file locale | Microsoft Azure"
	description="Come importare dati di training in Azure Machine Learning Studio da un file locale."
	keywords="dati di importazione, formato dati, tipi di dati, origini dati, dati di training"
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="jhubbard"
	editor="cgronlun"/> 

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="garye;bradsev" />


# Importare dati di training in Azure Machine Learning Studio da un file locale

[AZURE.INCLUDE [import-data-into-aml-studio-selector](../../includes/machine-learning-import-data-into-aml-studio.md)]


Per usare i propri dati in Machine Learning Studio, è possibile caricare un file di dati in anticipo dal disco rigido locale per creare un modulo di set di dati nell'area di lavoro.


## Importare dati da un file locale

È possibile importare dati da un disco rigido locale effettuando le operazioni seguenti:

1. Fare clic su **+ NEW** nella parte inferiore della finestra di Machine Learning Studio.
2. Selezionare **SET DI DATI** e **DA FILE LOCALE**.
3. Nella finestra di dialogo **Caricare un nuovo set di dati** selezionare il file da caricare
4. Immettere un nome, identificare il tipo di dati e immettere facoltativamente una descrizione. Una descrizione è consigliata in quanto consente di registrare tutte le caratteristiche relative ai dati che è opportuno tenere presente quando si usano tali dati in futuro.
5. La casella di controllo **Questa è la nuova versione di un set di dati esistente** consente di aggiornare un set di dati esistente con nuovi dati. Fare clic su questa casella di controllo e quindi immettere il nome di un set di dati esistente.

Durante il caricamento, viene visualizzato un messaggio che indica che il file è in fase di caricamento. Il tempo di caricamento dipende dalla dimensione dei dati e dalla velocità della connessione al servizio. Se è già noto che il file richiederà molto tempo, è possibile eseguire altre operazioni in Machine Learning Studio durante l'attesa. Tuttavia, la chiusura del browser comporterà un errore di caricamento dei dati.

Una volta caricati, i dati vengono archiviati in un modulo di set di dati e sono disponibili per eventuali esperimenti nell'area di lavoro. Durante la modifica di un esperimento, è possibile individuare i set di dati creati nell’elenco **I miei set di dati** presente nell'elenco **Set di dati salvati** nella tavolozza dei moduli. È possibile trascinare e rilasciare il set di dati nell'area di disegno esperimento quando si desidera utilizzare il set di dati per l'ulteriore analisi e il machine learning.

<!---HONumber=AcomDC_0921_2016-->