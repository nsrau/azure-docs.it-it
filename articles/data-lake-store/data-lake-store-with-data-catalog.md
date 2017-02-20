---
title: Registrare i dati da Data Lake Store in Azure Data Catalog | Documentazione Microsoft
description: Registrare i dati da Archivio Data Lake in Azure Data Catalog
services: data-lake-store,data-catalog
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 3294d91e-a723-41b5-9eca-ace0ee408a4b
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/06/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 59f072c7a8272fc04e1d662c0ab17e7ee4500fa6
ms.openlocfilehash: b589742e23ba9cca919a845e4d262ccfea4c9aca


---
# <a name="register-data-from-data-lake-store-in-azure-data-catalog"></a>Registrare i dati da Archivio Data Lake in Azure Data Catalog
Questo articolo descrive come integrare Archivio Azure Data Lake con Azure Data Catalog per individuare i dati all'interno dell'organizzazione integrandoli con il catalogo dati. Per altre informazioni sulla catalogazione dei dati, vedere [Azure Data Catalog](../data-catalog/data-catalog-what-is-data-catalog.md). Per informazioni sugli scenari in cui è possibile usare il catalogo dati, vedere l'articolo relativo agli [scenari comuni di Azure Data Catalog](../data-catalog/data-catalog-common-scenarios.md).

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Abilitare la sottoscrizione di Azure** per l'anteprima pubblica di Data Lake Store. Vedere le [istruzioni](data-lake-store-get-started-portal.md).
* **Account di Archivio Data Lake di Azure**. Seguire le istruzioni fornite in [Introduzione ad Archivio Azure Data Lake tramite il portale di Azure](data-lake-store-get-started-portal.md). Per questa esercitazione, viene creato un account Archivio Data Lake denominato **datacatalogstore**.

    Dopo aver creato l'account, caricare un set di dati di esempio. Per questa esercitazione, caricare tutti i file con estensione csv nella cartella **AmbulanceData** del [repository Git di Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/). È possibile usare vari tipi di client, ad esempio [Azure Storage Explorer](http://storageexplorer.com/), per caricare i dati in un contenitore BLOB.
* **Azure Data Catalog**. È necessario che per l'organizzazione sia già stato creato un catalogo di Azure Data Catalog. Per ogni organizzazione è consentito un solo catalogo.

## <a name="register-data-lake-store-as-a-source-for-data-catalog"></a>Registrare Archivio Data Lake come origine per Data Catalog

> [!VIDEO https://channel9.msdn.com/Series/AzureDataLake/ADCwithADL/player]

1. Passare a `https://azure.microsoft.com/services/data-catalog`e fare clic su **Introduzione**.
2. Accedere al portale di Azure Data Catalog e fare clic su **Pubblica dati**.

    ![Registrare un'origine dati](./media/data-lake-store-with-data-catalog/register-data-source.png "Registrare un'origine dati")
3. Nella pagina successiva fare clic su **Avvia applicazione**. Verrà scaricato il file manifesto dell'applicazione nel computer in uso. Fare doppio clic sul file manifesto per avviare l'applicazione.
4. Nella pagina iniziale fare clic su **Accedi**e immettere le credenziali.

    ![Schermata iniziale](./media/data-lake-store-with-data-catalog/welcome.screen.png "Schermata iniziale")
5. Nella pagina Selezionare un'origine dati selezionare **Azure Data Lake** e quindi fare clic su **Avanti**.

    ![Selezionare l'origine dati](./media/data-lake-store-with-data-catalog/select-source.png "Selezionare l'origine dati")
6. Nella pagina successiva fornire il nome dell'account Archivio Data Lake che si vuole registrare in Data Catalog. Lasciare l'impostazione predefinita per le altre opzioni e quindi fare clic su **Connetti**.

    ![Connessione all'origine dati](./media/data-lake-store-with-data-catalog/connect-to-source.png "Connessione all'origine dati")
7. La pagina successiva può essere suddivisa nei segmenti seguenti.

    a. La casella **Gerarchia server** rappresenta la struttura della cartella dell'account Archivio Data Lake. **$Root** rappresenta la radice dell'account Data Lake Store, mentre **AmbulanceData** rappresenta la cartella creata nella radice dell'account Data Lake Store.

    b. La casella **Oggetti disponibili** elenca i file e le cartelle sotto la cartella **AmbulanceData**.

    c. **Oggetti da registrare** elenca i file e le cartelle che si vogliono registrare in Azure Data Catalog.

    ![Visualizzare la struttura dei dati](./media/data-lake-store-with-data-catalog/view-data-structure.png "Visualizzare la struttura dei dati")
8. Per questa esercitazione, è necessario registrare tutti i file nella directory. A tale scopo, fare clic sul pulsante (![spostamento di oggetti](./media/data-lake-store-with-data-catalog/move-objects.png "Spostamento di oggetti")) per spostare tutti i file nella casella **Oggetti da registrare**.

    Poiché i dati vengono registrati in un catalogo dati dell'organizzazione, è consigliabile aggiungere alcuni metadati che è possibile usare in un secondo momento per individuare rapidamente i dati. Ad esempio, è possibile aggiungere un indirizzo di posta elettronica per il proprietario dei dati (ad esempio, uno che si occupa del caricamento dei dati) o aggiungere un tag per identificare i dati. La schermata seguente mostra un tag aggiunto ai dati.

    ![Visualizzare la struttura dei dati](./media/data-lake-store-with-data-catalog/view-selected-data-structure.png "Visualizzare la struttura dei dati")

    Fare clic su **Register**.
9. La schermata seguente indica che i dati sono stati registrati correttamente nel catalogo dati.

    ![Registrazione completata](./media/data-lake-store-with-data-catalog/registration-complete.png "Visualizzare la struttura dei dati")
10. Fare clic su **Visualizza portale** per tornare al portale di Data Catalog e verificare che sia quindi possibile accedere ai dati registrati dal portale. Per cercare i dati, è possibile usare il tag che è stato usato durante la registrazione dei dati.

     ![Cercare dati nel catalogo](./media/data-lake-store-with-data-catalog/search-data-in-catalog.png "Cercare dati nel catalogo")
11. È ora possibile eseguire operazioni come l'aggiunta di annotazioni e documentazione ai dati. Per altre informazioni, vedere i collegamenti seguenti:

    * [Annotare le origini dati in Data Catalog](../data-catalog/data-catalog-how-to-annotate.md)
    * [Documentare le origini dati in Data Catalog](../data-catalog/data-catalog-how-to-documentation.md)

## <a name="see-also"></a>Vedere anche
* [Annotare le origini dati in Data Catalog](../data-catalog/data-catalog-how-to-annotate.md)
* [Documentare le origini dati in Data Catalog](../data-catalog/data-catalog-how-to-documentation.md)
* [Integrazione di Data Lake Store con altri servizi di Azure](data-lake-store-integrate-with-other-services.md)



<!--HONumber=Feb17_HO1-->


