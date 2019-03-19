---
title: Importare ed esportare dati in progetti di Azure Notebooks
description: Informazioni su come importare dati di origini esterne in un progetto di Azure Notebooks e su come esportare dati da un progetto.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 586b423b-6668-4bdd-9592-4c237d7458fb
ms.service: azure
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: c5f8b6db77cabdf5e7af66063cbd9a058bd01800
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58082773"
---
# <a name="work-with-data-files-in-azure-notebook-projects"></a>Usare file di dati in progetti di Azure Notebooks

I dati sono la linfa vitale di molti notebook di Jupyter, specialmente dei notebook usati per il data science. Con Azure Notebooks è possibile importare facilmente i dati di varie origini in un progetto e quindi usarli dai notebook. È anche possibile impostare i notebook in modo che generino dati che vengono archiviati nel progetto e che successivamente possono essere scaricati per essere usati altrove.

Il menu **Data** (Dati) di un notebook in esecuzione include i comandi **Upload** (Carica) e **Download** (Scarica), che sono applicabili ai file nel progetto nonché ai file temporanei della sessione corrente del notebook.

È anche possibile usare il codice in un notebook per accedere direttamente a varie origini dati, inclusi i file all'interno di un progetto, nonché accedere a dati arbitrari usando i comandi in una cella di codice. Poiché tali dati vengono archiviati in variabili all'interno della sessione del notebook, non verranno salvati nel progetto a meno che non si usi il codice per generare specificatamente i file di progetto.

L'uso di codice nei dati risulta ottimale all'interno di un notebook in esecuzione. A tal fine, fare riferimento al [notebook di esempio in Getting to your data in Azure Notebooks](https://notebooks.azure.com/Microsoft/projects/samples/html/Getting%20to%20your%20Data%20in%20Azure%20Notebooks.ipynb) (Accesso ai dati in Azure Notebooks).

Il resto di questo articolo descrive in dettaglio le operazioni su file a livello di progetto.

## <a name="import-data"></a>Importa dati

È possibile importare file in un progetto dal dashboard del progetto o in un notebook in esecuzione usando il menu **Data** (Dati) o un comando, ad esempio `curl`.

### <a name="import-files-from-the-project-dashboard"></a>Importare i file dal dashboard del progetto

1. Nel progetto passare alla cartella in cui si vuole importare i file.

1. Selezionare il comando **Upload** (Carica), quindi selezionare **From URL** (Da URL) o **From computer** (Da computer) e specificare i dettagli necessari per i dati da importare:

   - **From URL** (Da URL): Immettere l'indirizzo dell'origine nel campo **File URL** (URL del file) e il nome file da assegnare al notebook nel progetto nel campo **File Name** (Nome file). Selezionare quindi **+ Add File** (+Aggiunta file) per aggiungere l'URL all'elenco dei caricamenti. Ripetere la procedura per tutti gli URL da aggiungere, quindi selezionare **Done** (Chiudi).

     ![Caricamento dalla finestra popup dell'URL](media/quickstarts/upload-from-url-popup.png)

   - **From computer** (Da computer): Trascinare i file nella finestra popup o selezionare **Choose Files** (Scegli file), quindi passare ai file di dati da importare e selezionarli. È possibile eliminare o scegliere un numero qualsiasi di file, di qualsiasi tipo e formato in quanto è compito del codice nel notebook aprire il file e analizzarne i dati.

     ![Caricamento dalla finestra popup del computer](media/quickstarts/upload-from-computer-popup.png)

1. I file importati vengono visualizzati nel dashboard del progetto ed è possibile accedervi dal codice del notebook usando i percorsi relativi alla cartella che li contiene.

### <a name="import-files-from-the-file-menu-in-a-notebook"></a>Importare i file dal menu File di un notebook

1. In un notebook in esecuzione selezionare il comando **File** > **Upload** (Carica):

    ![Comando Upload (Carica) del menu File in un notebook](media/file-menu-upload.png)

1. Nella finestra di dialogo che viene visualizzata passare ai file da caricare e selezionarli. È possibile selezionare un numero qualsiasi di file di qualsiasi tipo. Al termine dell'operazione, selezionare **Open** (Apri).

1. Nella finestra popup **Upload status** (Stato caricamento) che viene visualizzata selezionare una **cartella di destinazione** nell'elenco a discesa:

    - Cartella di sessione (*~/*): consente di caricare i file nella sessione corrente del notebook, ma non crea i file nel progetto. La cartella di sessione è un peer della cartella di progetto, ma non viene conservata al termine della sessione. Per accedere ai file della sessione nel codice, anteporre ai nomi file il percorso relativo *.../*.

        La cartella di sessione è utile per fare delle prove ed evita di riempire il progetto di file che potrebbero non servire a lungo termine. Nella cartella di sessione è possibile caricare anche file con nomi identici ai file nel progetto senza che si generino conflitti e che li si debba rinominare. Si supponga ad esempio che nel progetto esista già una versione del file *data.csv*, ma che si voglia sperimentare *una versione diversa*. Se si carica il file nella cartella di sessione, è possibile eseguire il notebook usando i dati presenti nel file caricato (facendovi riferimento nel codice come *../data.csv*) invece di quelli presenti nel file del progetto.

    - Cartella di progetto (*/progetto*): consente di caricare file nel progetto. È possibile accedere a questi file usando percorsi relativi nel codice. La procedura di caricamento di un file in questa cartella è la stessa prevista per il caricamento di un file nel dashboard del progetto. Il file viene salvato con il progetto ed è disponibile nelle sessioni successive.

        Il caricamento ha esito negativo se si tenta di caricare un file che ha lo stesso nome di uno già esistente nel progetto. Per sovrascrivere un file, caricare invece il nuovo file dal dashboard del progetto. Nel dashboard è infatti disponibile l'opzione di sovrascrittura.

1. Selezionare **Start Upload** (Avvia caricamento) per completare il processo.

### <a name="create-or-import-files-using-commands"></a>Creare o importare file usando i comandi

È possibile usare i comandi all'interno di un terminale o in una cella di codice Python per creare file in entrambe le cartelle di sessione e di progetto. I comandi come `curl` e `wget`, ad esempio, scaricano i file direttamente dal Web.

Per scaricare i file nel terminale, selezionare il comando **Terminal** (Terminale) nel dashboard del progetto, quindi immettere i comandi appropriati:

```bash
curl https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv

wget https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv
```

Quando si usa una cella di codice Python in un notebook, anteporre il segno `!` ai comandi.

La cartella di progetto è la cartella predefinita, pertanto se si specifica un nome file di destinazione come *oil_price.csv*, tale file viene creato automaticamente nel progetto. Per creare un file di sessione, anteporre *../* al nome, ad esempio *../oil_price.csv*.

### <a name="create-files-in-code"></a>Creare file nel codice

Quando si usa codice per la creazione di un file, ad esempio la funzione pandas `write_csv`, i percorsi sono sempre relativi alla cartella di progetto. Se si usa *../*, viene creato un file della sessione che viene eliminato nel momento in cui il notebook viene arrestato e chiuso.

## <a name="export-files"></a>Esportare file

È possibile esportare dati dal dashboard del progetto o dall'interno di un notebook.

## <a name="export-files-from-the-project-dashboard"></a>Esportare i file dal dashboard del progetto

Nel dashboard del progetto fare clic con il pulsante destro del mouse su un file e selezionare **Download** (Scarica):

![Comando Download (Scarica) del menu di scelta rapida relativo all'elemento del progetto](media/download-command.png)

È anche possibile selezionare un file e usare il comando **Download** (Scarica) (tasto di scelta rapida: d) nel dashboard:

![Comando di scaricamento sulla barra degli strumenti del dashboard del progetto](media/download-command-toolbar.png)

## <a name="export-files-from-the-data-menu-in-a-notebook"></a>Esportare i file dal menu Data (Dati) di un notebook

1. Selezionare il comando di menu **File** > **Download** (Scarica):

    ![Comando Download (Scarica) del menu File in un notebook](media/file-menu-download.png)

1. Viene visualizzata una finestra popup che mostra le cartelle nella sessione. La cartella di *progetto* contiene i file di progetto:

    ![Finestra popup del comando Download (Scarica) del menu Data (Dati) in cui si selezionano i file e le cartelle](media/file-menu-download-popup.png)

1. Selezionare le caselle a sinistra dei file e delle cartelle che si desidera scaricare, quindi selezionare **Download Selected** (Scarica selezionati).

1. Il notebook prepara un unico file con estensione *zip* contenente i file selezionati, che può essere salvato come si fa normalmente da un browser. Il notebook crea un file con estensione *zip* anche quando si scarica un file singolo.

## <a name="next-steps"></a>Passaggi successivi

- [Access cloud data in a notebook](access-data-resources-jupyter-notebooks.md) (Accedere ai dati nel cloud in un notebook)
