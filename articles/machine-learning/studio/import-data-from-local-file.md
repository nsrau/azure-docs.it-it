---
title: Importare dati da file titleSuffix: Descrizione di Azure Machine Learning Studio: Informazioni su come caricare un file di dati di training dal disco rigido in Azure Machine Learning Studio. Questa operazione crea un modulo set di dati nell'area di lavoro.
services: machine-learning ms.service: machine-learning ms.component: studio ms.topic: article

author: ericlicoding ms.author: amlstudiodocs ms.custom: seodec18 ms.date: 20/03/2017
---
# <a name="import-training-data-from-a-file-on-your-hard-drive-into-azure-machine-learning-studio"></a>Importare dati di training da un file sul disco rigido in Azure Machine Learning Studio

Informazioni su come caricare un file di dati da utilizzare come dati di training dal disco rigido in Azure Machine Learning Studio. Importando il file di dati, si dispone di un modulo set di dati pronto per l'utilizzo nell'area di lavoro.

## <a name="steps-to-import-data-from-a-local-file"></a>Passaggi per importare dati da un file locale
Per importare dati da un disco rigido locale effettuare le operazioni seguenti:

1. Fare clic su **+ NEW** nella parte inferiore della finestra di Machine Learning Studio.
2. Selezionare **DATASET** (SET DI DATI) e **FROM LOCAL FILE** (DA FILE LOCALE).
3. Nella finestra di dialogo **Caricare un nuovo set di dati** selezionare il file da caricare
4. Immettere un nome, identificare il tipo di dati e immettere facoltativamente una descrizione. Una descrizione è consigliabile perché consente di registrare tutte le caratteristiche relative ai dati da tenere presenti quando si useranno tali dati in futuro.
5. La casella di controllo **Questa è la nuova versione di un set di dati esistente** consente di aggiornare un set di dati esistente con nuovi dati. Fare clic su questa casella di controllo e quindi immettere il nome di un set di dati esistente.

![Caricare un nuovo set di dati](./media/import-data-from-local-file/upload-dataset.png)

Durante il caricamento, verrà visualizzato un messaggio che indica che il file è in fase di caricamento. Il tempo di caricamento dipende dalle dimensioni dei dati e dalla velocità della connessione al servizio. Se è già noto che il file richiederà molto tempo, è possibile eseguire altre operazioni in Machine Learning Studio durante l'attesa. In caso di chiusura del browser, tuttavia, il caricamento dei dati avrà esito negativo.

## <a name="dataset-module-is-ready-for-use"></a>Il modulo set di dati è pronto per l'utilizzo
Una volta caricati, i dati vengono archiviati in un modulo di set di dati e sono disponibili per eventuali esperimenti nell'area di lavoro.

Durante la modifica di un esperimento, è possibile individuare i set di dati creati nell'elenco **My Datasets** (Set di dati personali) presente nell'elenco **Saved Datasets** (Set di dati salvati) nella tavolozza dei moduli. È possibile trascinare il set di dati nell'area di disegno dell'esperimento per usarlo per l'ulteriore analisi e l'apprendimento automatico.
