---
title: Campi personalizzati in monitoraggio di Azure | Microsoft Docs
description: La funzionalità campi personalizzati di monitoraggio di Azure consente di creare campi ricercabili personalizzati dai record in un'area di lavoro Log Analytics che aggiungono le proprietà di un record raccolto.  Questo articolo descrive il processo di creazione di un campo personalizzato e illustra una procedura dettagliata con un evento di esempio.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/23/2019
ms.openlocfilehash: 1fa8fb8ee944103626966839def358e68a55d8ac
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932601"
---
# <a name="create-custom-fields-in-a-log-analytics-workspace-in-azure-monitor"></a>Creare campi personalizzati in un'area di lavoro Log Analytics in monitoraggio di Azure

> [!NOTE]
> Questo articolo descrive come analizzare i dati di testo in un'area di lavoro di Log Analytics mentre vengono raccolti. Si consiglia di analizzare i dati di testo in un filtro di query dopo che sono stati raccolti seguendo le linee guida descritte in [analizzare i dati di testo in monitoraggio di Azure](../log-query/parse-text.md). Offre diversi vantaggi rispetto all'utilizzo di campi personalizzati.

La funzionalità **campi personalizzati** di monitoraggio di Azure consente di estendere i record esistenti nell'area di lavoro log Analytics aggiungendo i propri campi ricercabili.  I campi personalizzati vengono popolati automaticamente con dati estratti da altre proprietà nello stesso record.

![Panoramica](media/custom-fields/overview.png)

Il record di esempio riportato di seguito contiene dati utili nascosti nella descrizione dell'evento. L'estrazione di questi dati in una proprietà distinta lo rende disponibile per azioni quali l'ordinamento e il filtro.

![Estrazione di esempio](media/custom-fields/sample-extract.png)

> [!NOTE]
> Nella versione di anteprima è previsto un limite di 100 campi personalizzati nell'area di lavoro.  Tale limite verrà esteso con il passaggio della funzionalità alla disponibilità generale.

## <a name="creating-a-custom-field"></a>Creazione di un campo personalizzato
Quando si crea un campo personalizzato, è necessario specificare a Log Analytics quali dati usare per popolare il relativo valore.  Per identificare rapidamente i dati viene usata la tecnologia FlashExtract di Microsoft Research.  Anziché richiedere di fornire istruzioni esplicite, monitoraggio di Azure apprende i dati che si vogliono estrarre dagli esempi forniti.

Le sezioni seguenti illustrano la procedura per la creazione di un campo personalizzato.  Alla fine di questo articolo è disponibile una procedura dettagliata per l'estrazione di un campione.

> [!NOTE]
> Il campo personalizzato viene popolato come record che corrispondono ai criteri specificati vengono aggiunti all'area di lavoro Log Analytics, quindi verrà visualizzato solo nei record raccolti dopo la creazione del campo personalizzato.  Il campo personalizzato non viene aggiunto ai record già presenti nell'archivio al momento della creazione.
> 

### <a name="step-1--identify-records-that-will-have-the-custom-field"></a>Passaggio 1: Identificare i record che conterranno il campo personalizzato
Il primo passaggio consiste nell'identificare i record che conterranno il campo personalizzato.  Si inizia con una [query di log standard](../log-query/log-query-overview.md) e quindi si seleziona un record che funga da modello da cui il monitoraggio di Azure apprenderà.  Quando si specifica l'intenzione di estrarre i dati in un campo personalizzato, viene aperta l' **estrazione guidata campi** che permette di convalidare e affinare i criteri.

1. Passare a **log** e usare una [query per recuperare i record](../log-query/log-query-overview.md) che avranno il campo personalizzato.
2. Selezionare un record che Log Analytics possa usare come modello per l'estrazione dei dati con cui popolare il campo personalizzato.  Identificare i dati da estrarre da questo record. Log Analytics userà queste informazioni per determinare la logica in base alla quale popolare il campo personalizzato per tutti i record simili.
3. Espandere le proprietà del record, fare clic sui puntini di sospensione a sinistra della proprietà Top del record e selezionare **Estrai campi da**.
4. Viene aperta l' **estrazione guidata campi** e il record selezionato viene visualizzato nella colonna **esempio principale** .  Viene definito il campo personalizzato per i record con gli stessi valori delle proprietà selezionate.  
5. Se la selezione non corrisponde esattamente al previsto, selezionare campi aggiuntivi per limitare i criteri.  Per modificare i valori dei campi per i criteri, è necessario annullare e selezionare un altro record corrispondente ai criteri scelti.

### <a name="step-2---perform-initial-extract"></a>Passaggio 2: Eseguire l'estrazione iniziale
Dopo aver identificato i record che conterranno il campo personalizzato, occorre identificare i dati da estrarre.  Log Analytics usa queste informazioni per identificare schemi analoghi in record simili.  Nel passaggio successivo sarà possibile convalidare i risultati e fornire altri dettagli che Log Analytics potrà usare nell'analisi.

1. Nel record di esempio evidenziare il testo con cui popolare il campo personalizzato.  Verrà quindi visualizzata una finestra di dialogo per fornire un nome e un tipo di dati per il campo ed eseguire l'estrazione iniziale.  I caratteri **\_CF** vengono aggiunti automaticamente.
2. Fare clic su **Extract** per eseguire un'analisi dei record raccolti.  
3. Le sezioni **Riepilogo** e **Risultati della ricerca** visualizzano i risultati dell'estrazione e consentono di verificarne l'accuratezza.  **Summary** visualizza i criteri usati per identificare i record e un conteggio per ciascuno dei valori di dati identificati.  **Search Results** contiene un elenco dettagliato dei record corrispondenti ai criteri.

### <a name="step-3--verify-accuracy-of-the-extract-and-create-custom-field"></a>Passaggio 3: Verificare l'accuratezza dell'estrazione e creare il campo personalizzato
Dopo aver eseguito l'estrazione iniziale, Log Analytics ne visualizza i risultati in base ai dati già raccolti.  Se i risultati sono accurati è possibile creare direttamente il campo personalizzato.  In caso contrario, è possibile limitare i risultati per permettere a Log Analytics di migliorare la logica.

1. Se i valori dell'estrazione iniziale non sono corretti, fare clic sull'icona di **modifica** accanto a un record non corretto e selezionare **Modifica questa evidenziazione** per modificare la selezione.
2. La voce viene copiata nella sezione **Esempi aggiuntivi** sotto **Esempio principale**.  È possibile regolare l'evidenziazione per permettere a Log Analytics di comprendere cosa avrebbe dovuto selezionare.
3. Fare clic su **Extract** per usare le nuove informazioni per valutare tutti i record esistenti.  È possibile modificare i risultati per i record diversi da quello appena modificato in base alle nuove informazioni.
4. Continuare ad aggiungere correzioni fino a quando tutti i record nell'estrazione non identificano correttamente i dati con cui popolare il nuovo campo personalizzato.
5. Quando il risultato è soddisfacente, fare clic su **Save extract** .  Il campo personalizzato è ora definito, ma non viene ancora aggiunto ai record.
6. Attendere che vengano raccolti nuovi record corrispondenti ai criteri specificati ed eseguire nuovamente la ricerca nei log. I nuovi record ora includono il campo personalizzato.
7. Il campo personalizzato può essere usato come le altre proprietà del record,  ad esempio per aggregare e raggruppare dati, ma anche per produrre nuove informazioni dettagliate.

## <a name="viewing-custom-fields"></a>Visualizzazione di campi personalizzati
È possibile visualizzare un elenco di tutti i campi personalizzati nel gruppo di gestione dal menu **Impostazioni avanzate** dell'area di lavoro Log Analytics nel portale di Azure.  Selezionare **Dati** e quindi **Campi personalizzati** per visualizzare un elenco di tutti i campi personalizzati nell'area di lavoro.  

![Campi personalizzati](media/custom-fields/list.png)

## <a name="removing-a-custom-field"></a>Rimozione di un campo personalizzato
Per rimuovere un campo personalizzato è possibile procedere in due modi.  Il primo consiste nel selezionare l'opzione **Remove** per ogni campo quando si visualizza l'elenco completo, come descritto in precedenza.  Il secondo metodo consiste nel recuperare un record e fare clic sul pulsante a sinistra del campo.  Sarà disponibile un'opzione di menu per rimuovere il campo personalizzato.

## <a name="sample-walkthrough"></a>Procedura dettagliata di esempio
La sezione seguente descrive un esempio completo di creazione di un campo personalizzato.  L'esempio estrae il nome del servizio dagli eventi Windows che indicano la modifica dello stato di un servizio.  Questo comportamento si basa sugli eventi creati da Gestione controllo servizi durante l'avvio del sistema nei computer Windows.  Per seguire questo esempio, è necessario eseguire la [raccolta di eventi informativi per il registro di sistema](data-sources-windows-events.md).

Immettere la query seguente per restituire tutti gli eventi di Gestione controllo servizi con ID evento 7036, ovvero l'evento che indica l'avvio o l'arresto di un servizio.

![Query](media/custom-fields/query.png)

Selezionare ed espandere tutti i record con ID evento 7036.

![Record di origine](media/custom-fields/source-record.png)

Per definire campi personalizzati, fare clic sull'ellisse accanto alla proprietà Top.

![Estrarre i campi](media/custom-fields/extract-fields.png)

Si aprirà l'**estrazione guidata campi** e verranno selezionati i campi **EventLog** ed **EventID** nella colonna **Esempio principale**.  Questo indica che il campo personalizzato verrà definito per gli eventi del registro di sistema con ID evento 7036.  Ciò è sufficiente e non è necessario selezionare altri campi.

![Main example](media/custom-fields/main-example.png)

Evidenziare il nome del servizio nella proprietà **RenderedDescription** e usare **Service** per identificare il nome del servizio.  Il campo personalizzato sarà denominato **Service_CF**. Il tipo di campo in questo caso è una stringa, quindi è possibile lasciare invariato.

![Nome del campo](media/custom-fields/field-title.png)

Si noti che il nome del servizio viene identificato in modo corretto per alcuni record ma non per altri.   I **risultati della ricerca** mostrano che parte del nome di **Scheda delle prestazioni WMI** non è stato selezionato.  Il **Riepilogo** Mostra un **programma di installazione dei moduli** identificato al posto del programma di installazione dei moduli di **Windows**.  

![Risultati della ricerca](media/custom-fields/search-results-01.png)

Iniziare dal record della **scheda delle prestazioni WMI** .  Fare clic sulla relativa icona di modifica e quindi su **Modify this highlight**.  

![Modificare l'evidenziazione](media/custom-fields/modify-highlight.png)

Espandere l'evidenziazione per includere la parola **WMI** e quindi eseguire di nuovo l'estrazione.  

![Altro esempio](media/custom-fields/additional-example-01.png)

Ora le voci relative a **Scheda delle prestazioni WMI** risultano corrette e Log Analytics ha anche usato tali informazioni per correggere i record relativi a **Programma di installazione dei moduli di Windows**.

![Risultati della ricerca](media/custom-fields/search-results-02.png)

È ora possibile eseguire una query per verificare che **Service_CF** sia stato creato, ma non è ancora stato aggiunto a nessun record. Questo perché il campo personalizzato non funziona con i record esistenti, quindi è necessario attendere che vengano raccolti nuovi record.

![Conteggio iniziale](media/custom-fields/initial-count.png)

Dopo un periodo di tempo in cui vengono raccolti nuovi eventi, il campo **Service_CF** viene ora aggiunto ai record che corrispondono ai criteri specificati.

![Risultati finali](media/custom-fields/final-results.png)

Ora il campo personalizzato può essere usato come le altre proprietà del record.  Per illustrare questo concetto, viene creata una query che raggruppa in base al nuovo campo **Service_CF** per verificare quali sono i servizi più attivi.

![Raggruppa per query](media/custom-fields/query-group.png)

## <a name="next-steps"></a>Passaggi successivi
* Informazioni sulle [query di log](../log-query/log-query-overview.md) per compilare query usando campi personalizzati per i criteri.
* Monitorare i [file di log personalizzati](data-sources-custom-logs.md) analizzati usando campi personalizzati.

