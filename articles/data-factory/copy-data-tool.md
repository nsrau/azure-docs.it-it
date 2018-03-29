---
title: Strumento Copia dati di Azure Data Factory | Microsoft Docs
description: Contiene informazioni sullo strumento Copia dati nell'interfaccia utente di Azure Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.date: 01/10/2018
ms.author: jingwang
ms.openlocfilehash: b82ee060ff3f25e7a92c85114d457ecb349159b3
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-tool-in-azure-data-factory"></a>Strumento Copia dati di Azure Data Factory
Lo strumento Copia dati di Azure Data Factory semplifica e ottimizza l'inserimento di dati in un data lake, che è in genere il primo passaggio in uno scenario di integrazione dei dati end-to-end.  In questo modo è possibile risparmiare tempo, soprattutto quando si usa Azure Data Factory per inserire per la prima volta i dati da un'origine. Questi sono alcuni dei vantaggi offerti dallo strumento:

- Quando si usa lo strumento Copia dati di Azure Data Factory, non è necessario comprendere le definizioni di Data Factory per i servizi, i set di dati, le pipeline, le attività e i trigger collegati. 
- Il flusso dello strumento Copia dati per caricare i dati in un data lake è intuitivo. Lo strumento crea automaticamente tutte le risorse di Data Factory necessarie per copiare i dati dall'archivio dati di origine selezionato all'archivio dati di destinazione/sink selezionato. 
- Lo strumento Copia dati consente di convalidare i dati inseriti in fase di creazione, permettendo così di evitare già dall'inizio possibili errori.
- Se è necessario implementare una logica di business complessa per caricare i dati in un data lake, è comunque possibile modificare le risorse di Data Factory create tramite lo strumento Copia dati usando la funzione di creazione per singola attività nell'interfaccia utente di Data Factory. 

La tabella seguente indica i diversi casi in cui usare lo strumento Copia dati e la funzione di creazione per singola attività nell'interfaccia utente di Data Factory: 

| Strumento Copia dati | Creazione per singola attività (copia) |
| -------------- | -------------------------------------- |
| Si vuole creare con facilità un'attività di caricamento dei dati senza avere informazioni specifiche sulle entità di Azure Data Factory (set di dati, pipeline, servizi collegati e così via). | Si vuole implementare una logica complessa e flessibile per il caricamento di dati nel lake. |
| Si vuole caricare rapidamente un numero notevole di elementi dati in un data lake. | Si vuole concatenare l'attività di copia con le attività successive di pulizia o elaborazione dei dati. |

Per avviare lo strumento Copia dati, fare clic sul riquadro **Copia dati** nella home page della data factory.

![Pagina introduttiva con il collegamento allo strumento Copia dati](./media/copy-data-tool/get-started-page.png)


## <a name="intuitive-flow-for-loading-data-into-a-data-lake"></a>Flusso intuitivo per il caricamento di dati in un data lake
Questo strumento consente di spostare i dati da un'ampia gamma di origini alle destinazioni desiderate in modo semplice e rapido, seguendo un flusso intuitivo:  

1. Configurare le impostazioni per l'**origine**.
2. Configurare le impostazioni per la **destinazione**. 
3. Configurare **impostazioni avanzate** per l'operazione di copia, ad esempio il mapping delle colonne, le impostazioni per le prestazioni e quelle relative alla tolleranza di errore. 
4. Definire una **pianificazione** per l'attività di caricamento dei dati. 
5. Esaminare il **riepilogo** delle entità di Data Factory da creare. 
6. **Modificare** la pipeline per aggiornare le impostazioni per l'attività di copia in base alle esigenze. 

 Lo strumento è stato concepito fin da subito per l'uso con i Big Data e offre il supporto per svariati tipi di oggetti e dati. È possibile usarlo per spostare una quantità notevole di cartelle, file o tabelle. Lo strumento supporta inoltre l'anteprima automatica dei dati, l'acquisizione e il mapping automatico dello schema, nonché l'applicazione di filtri ai dati.

![Strumento Copia dati](./media/copy-data-tool/copy-data-tool.png)

## <a name="automatic-data-preview"></a>Anteprima automatica dei dati
È possibile visualizzare in anteprima una parte dei dati dall'archivio dati di origine selezionato, in modo da poter convalidare i dati che vengono copiati. Inoltre, se l'origine dati è in un file di testo, lo strumento Copia dati analizza il file per rilevare automaticamente lo schema e i delimitatori di riga e colonna.

![Impostazioni del file](./media/copy-data-tool/file-format-settings.png)

Dopo il rilevamento:

![Impostazioni del file rilevate e anteprima](./media/copy-data-tool/after-detection.png)

## <a name="schema-capture-and-automatic-mapping"></a>Acquisizione e mapping automatico dello schema
In molti casi lo schema dell'origine dati può non essere uguale a quello della destinazione. Se succede, è necessario eseguire il mapping delle colonne che appartengono allo schema di origine con le colonne dello schema di destinazione.

Lo strumento Copia dati è in grado di monitorare e apprendere il comportamento dell'utente quando esegue il mapping delle colonne tra gli archivi di origine e destinazione. Dopo che l'utente ha selezionato una o più colonne dall'archivio dati di origine e ne ha eseguito il mapping con lo schema di destinazione, lo strumento Copia dei dati inizia ad analizzare il criterio adottato per la selezione delle coppie di colonne in entrambi gli archivi e quindi applica lo stesso criterio alle colonne rimanenti. Si noterà così che tutte le colonne vengono mappate alla destinazione nel modo desiderato solo dopo poche semplici operazioni.  Se non si è soddisfatti del mapping delle colonne eseguito dallo strumento Copia dati, è possibile ignorarlo e continuare l'operazione manualmente. Nel frattempo, lo strumento Copia dati acquisisce altre informazioni e aggiorna costantemente il modello in modo da identificare il criterio corretto che l'utente vuole adottare per il mapping delle colonne. 

> [!NOTE]
> Quando si copiano dati da SQL Server o dal database SQL di Azure in Azure SQL Data Warehouse, se nell'archivio di destinazione la tabella non esiste, lo strumento Copia dati supporta la creazione automatica della tabella in base allo schema di origine. 

## <a name="filter-data"></a>Filtrare i dati
È possibile filtrare i dati di origine per selezionare soltanto quelli da copiare nell'archivio dati sink. L'uso del filtro consente di ridurre il volume di dati da copiare nell'archivio dati sink, aumentando l'efficacia dell'operazione di copia. Lo strumento Copia dati offre un modo flessibile per filtrare i dati in un database relazionale con il linguaggio di query SQL o per filtrare i file in una cartella BLOB di Azure. 

### <a name="filter-data-in-a-database"></a>Filtrare i dati in un database
Lo screenshot seguente mostra una query SQL usata per filtrare i dati.

![Filtrare i dati in un database](./media/copy-data-tool/filter-data-in-database.png)

### <a name="filter-data-in-an-azure-blob-folder"></a>Filtrare i dati in una cartella BLOB di Azure
Per copiare i dati da una cartella, è possibile usare variabili nel percorso della cartella. Le variabili supportate sono **{year}**, **{month}**, **{day}**, **{hour}** e **{minute}**. Ad esempio: inputfolder/{year}/{month}/{day}. 

Si supponga di avere cartelle di input nel formato seguente: 

```
2016/03/01/01
2016/03/01/02
2016/03/01/03
...
```

Fare clic sul pulsante **Esplora** per **File o cartella**, passare a una di queste cartelle, ad esempio 2016->03->01->02, e fare clic su **Scegli**. Nella casella di testo dovrebbe essere visualizzato 2016/03/01/02. 

Sostituire quindi **2016** con **{year}**, **03** con **{month}**, **01** con **{day}** e **02** con **{hour}** e premere **TAB**. Dovrebbero essere visualizzati elenchi a discesa da cui selezionare il formato per queste quattro variabili:

![Filtrare un file o una cartella](./media/copy-data-tool/filter-file-or-folder.png)

Lo strumento Copia dati genera parametri con espressioni, funzioni e variabili di sistema che possono essere usate per rappresentare {year}, {month}, {day}, {hour} e {minute} durante la creazione della pipeline. Per altre informazioni, vedere l'articolo [Come leggere o scrivere dati partizionati](how-to-read-write-partitioned-data.md).

## <a name="scheduling-options"></a>Opzioni di pianificazione
È possibile eseguire l'operazione di copia una sola volta oppure in base a una pianificazione, con cadenza oraria, giornaliera e così via. Queste opzioni possono essere usate per i connettori tra diversi ambienti, inclusi l'ambiente locale, il cloud e il computer desktop. 

Un'operazione di copia eseguita una sola volta permette di spostare dati da un'origine a una destinazione una sola volta. Si applica ai dati di qualsiasi dimensione e in qualsiasi formato supportato. L'operazione di copia pianificata consente di copiare i dati in base a una ricorrenza specificata. Per configurare la copia pianificata sono disponibili impostazioni avanzate, come la ripetizione dei tentativi, il timeout, gli avvisi e così via.

![Opzioni di pianificazione](./media/copy-data-tool/scheduling-options.png)


## <a name="next-steps"></a>Passaggi successivi
Provare a eseguire queste esercitazioni in cui viene usato lo strumento Copia dati:

- [Avvio rapido: Creare una data factory tramite il portale di Azure](quickstart-create-data-factory-copy-data-tool.md)
- [Esercitazione: Copiare dati di Azure con lo strumento Copia dati](tutorial-copy-data-tool.md) 
- [Esercitazione: Copiare dati locali in Azure con lo strumento Copia dati](tutorial-hybrid-copy-data-tool.md)
