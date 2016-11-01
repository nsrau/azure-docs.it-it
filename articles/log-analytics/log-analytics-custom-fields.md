<properties
   pageTitle="Campi personalizzati in Log Analytics | Microsoft Azure"
   description="La funzionalità Campi personalizzati di Log Analytics consente di creare campi ricercabili personalizzati dai dati di OMS che vengono aggiunti alle proprietà di un record raccolto.  Questo articolo descrive il processo di creazione di un campo personalizzato e illustra una procedura dettagliata con un evento di esempio."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="bwren" />


# <a name="custom-fields-in-log-analytics"></a>Campi personalizzati in Log Analytics

La funzionalità **Campi personalizzati** di Log Analytics consente di estendere i record esistenti nell'archivio di OMS con l'aggiunta di campi ricercabili personalizzati.  I campi personalizzati vengono popolati automaticamente con dati estratti da altre proprietà nello stesso record.

![Panoramica dei campi personalizzati](media/log-analytics-custom-fields/overview.png)

Il record di esempio riportato di seguito contiene dati utili nascosti nella descrizione dell'evento.  L'estrazione di tali dati in proprietà separate li rende disponibili per azioni come l'ordinamento e il filtro.

![Pulsante di ricerca log](media/log-analytics-custom-fields/sample-extract.png)

>[AZURE.NOTE] Nella versione di anteprima è previsto un limite di 100 campi personalizzati nell'area di lavoro.  Tale limite verrà esteso con il passaggio della funzionalità alla disponibilità generale.

## <a name="creating-a-custom-field"></a>Creazione di un campo personalizzato

Quando si crea un campo personalizzato, è necessario specificare a Log Analytics quali dati usare per popolare il relativo valore.  Per identificare rapidamente i dati viene usata la tecnologia FlashExtract di Microsoft Research.  Anziché chiedere all'utente di fornire istruzioni esplicite, Log Analytics ricava informazioni sui dati da estrarre dagli esempi forniti.

Le sezioni seguenti illustrano la procedura per la creazione di un campo personalizzato.  Alla fine di questo articolo è disponibile una procedura dettagliata per l'estrazione di un campione.

>[AZURE.NOTE] Il campo personalizzato viene popolato man mano che record corrispondenti ai criteri specificati vengono aggiunti all'archivio dati di OMS. Verrà quindi visualizzato solo nei record raccolti dopo la sua creazione.  Il campo personalizzato non viene aggiunto ai record già presenti nell'archivio al momento della creazione.

### <a name="step-1-identify-records-that-will-have-the-custom-field"></a>Passaggio 1: Identificare i record che conterranno il campo personalizzato
Il primo passaggio consiste nell'identificare i record che conterranno il campo personalizzato.  Iniziare con una [ricerca nei log standard](log-analytics-log-searches.md) e quindi selezionare un record che faccia da modello di apprendimento per Log Analytics.  Quando si specifica l'intenzione di estrarre i dati in un campo personalizzato, viene aperta l' **estrazione guidata campi** che permette di convalidare e affinare i criteri.

2. Passare alla **ricerca log** e usare una [query per recuperare i record](log-analytics-log-searches.md) che conterranno il campo personalizzato.
2. Selezionare un record che Log Analytics possa usare come modello per l'estrazione dei dati con cui popolare il campo personalizzato.  Identificare i dati da estrarre da questo record. Log Analytics userà queste informazioni per determinare la logica in base alla quale popolare il campo personalizzato per tutti i record simili.
3. Fare clic sul pulsante a sinistra di una qualsiasi proprietà testo del record e selezionare l'opzione **Extract fields from**.
4. Viene aperta l'**estrazione guidata campi** e il record selezionato viene visualizzato nella colonna **Esempio principale**.  Viene definito il campo personalizzato per i record con gli stessi valori delle proprietà selezionate.  
5. Se la selezione non corrisponde esattamente al previsto, selezionare campi aggiuntivi per limitare i criteri.  Per modificare i valori dei campi per i criteri, è necessario annullare e selezionare un altro record corrispondente ai criteri scelti.

### <a name="step-2-perform-initial-extract"></a>Passaggio 2: Eseguire l'estrazione iniziale
Dopo aver identificato i record che conterranno il campo personalizzato, occorre identificare i dati da estrarre.  Log Analytics usa queste informazioni per identificare schemi analoghi in record simili.  Nel passaggio successivo sarà possibile convalidare i risultati e fornire altri dettagli che Log Analytics potrà usare nell'analisi.

1. Nel record di esempio evidenziare il testo con cui popolare il campo personalizzato.  Verrà visualizzata una finestra di dialogo in cui specificare un nome per il campo ed eseguire l'estrazione iniziale.  I caratteri **\_CF** vengono aggiunti automaticamente.
2. Fare clic su **Extract** per eseguire un'analisi dei record raccolti.  
3. Le sezioni **Riepilogo** e **Risultati della ricerca** visualizzano i risultati dell'estrazione e consentono di verificarne l'accuratezza.  **Summary** visualizza i criteri usati per identificare i record e un conteggio per ciascuno dei valori di dati identificati.  **Search Results** contiene un elenco dettagliato dei record corrispondenti ai criteri.


### <a name="step-3-verify-accuracy-of-the-extract-and-create-custom-field"></a>Passaggio 3: Verificare l'accuratezza dell'estrazione e creare il campo personalizzato

Dopo aver eseguito l'estrazione iniziale, Log Analytics ne visualizza i risultati in base ai dati già raccolti.  Se i risultati sono accurati è possibile creare direttamente il campo personalizzato.  In caso contrario, è possibile limitare i risultati per permettere a Log Analytics di migliorare la logica.

2.  Se i valori dell'estrazione iniziale non sono corretti, fare clic sull'icona di **modifica** accanto a un record non corretto e selezionare **Modifica questa evidenziazione** per modificare la selezione.
3.  La voce viene copiata nella sezione **Esempi aggiuntivi** sotto **Esempio principale**.  È possibile regolare l'evidenziazione per permettere a Log Analytics di comprendere cosa avrebbe dovuto selezionare.
4.  Fare clic su **Extract** per usare le nuove informazioni per valutare tutti i record esistenti.  È possibile modificare i risultati per i record diversi da quello appena modificato in base alle nuove informazioni.
5.  Continuare ad aggiungere correzioni fino a quando tutti i record nell'estrazione non identificano correttamente i dati con cui popolare il nuovo campo personalizzato.
6. Quando il risultato è soddisfacente, fare clic su **Save extract** .  Il campo personalizzato è ora definito, ma non viene ancora aggiunto ai record.
7.  Attendere che vengano raccolti nuovi record corrispondenti ai criteri specificati ed eseguire nuovamente la ricerca nei log. I nuovi record ora includono il campo personalizzato.
8.  Il campo personalizzato può essere usato come le altre proprietà del record,  ad esempio per aggregare e raggruppare dati, ma anche per produrre nuove informazioni dettagliate.


## <a name="viewing-custom-fields"></a>Visualizzazione di campi personalizzati
Per visualizzare un elenco di tutti i campi personalizzati nel gruppo di gestione è possibile usare il riquadro **Impostazioni** del dashboard di OMS.  Selezionare **Dati** e quindi **Campi personalizzati** per visualizzare un elenco di tutti i campi personalizzati nell'area di lavoro.  

![Campi personalizzati](media/log-analytics-custom-fields/list.png)

## <a name="removing-a-custom-field"></a>Rimozione di un campo personalizzato
Per rimuovere un campo personalizzato è possibile procedere in due modi.  Il primo consiste nel selezionare l'opzione **Remove** per ogni campo quando si visualizza l'elenco completo, come descritto in precedenza.  Il secondo metodo consiste nel recuperare un record e fare clic sul pulsante a sinistra del campo.  Sarà disponibile un'opzione di menu per rimuovere il campo personalizzato.

## <a name="sample-walkthrough"></a>Procedura dettagliata di esempio

La sezione seguente descrive un esempio completo di creazione di un campo personalizzato.  L'esempio estrae il nome del servizio dagli eventi Windows che indicano la modifica dello stato di un servizio.  Si basa sugli eventi creati da Gestione controllo servizi nel registro di sistema nei computer Windows.  Per seguire questo esempio, è necessario eseguire la [raccolta di eventi informativi per il registro di sistema](log-analytics-data-sources-windows-events.md).

Immettere la query seguente per restituire tutti gli eventi di Gestione controllo servizi con ID evento 7036, ovvero l'evento che indica l'avvio o l'arresto di un servizio.

![Query](media/log-analytics-custom-fields/query.png)

Selezionare quindi uno dei record con ID evento 7036.

![Record di origine](media/log-analytics-custom-fields/source-record.png)

Cercare il servizio il cui nome viene visualizzato nella proprietà **RenderedDescription** e selezionare il pulsante accanto alla proprietà.

![Estrarre i campi](media/log-analytics-custom-fields/extract-fields.png)

Si aprirà l'**estrazione guidata campi** e verranno selezionati i campi **EventLog** ed **EventID** nella colonna **Esempio principale**.  Questo indica che il campo personalizzato verrà definito per gli eventi del registro di sistema con ID evento 7036.  Ciò è sufficiente e non è necessario selezionare altri campi.

![Main example](media/log-analytics-custom-fields/main-example.png)

Evidenziare il nome del servizio nella proprietà **RenderedDescription** e usare **Service** per identificare il nome del servizio.  Il campo personalizzato sarà denominato **Service_CF**.

![Nome del campo](media/log-analytics-custom-fields/field-title.png)

Si noti che il nome del servizio viene identificato in modo corretto per alcuni record ma non per altri.   I **risultati della ricerca** mostrano che parte del nome di **Scheda delle prestazioni WMI** non è stato selezionato.  Il **riepilogo** mostra che quattro record con il servizio **DPRMA** includono per errore una parola in più e due record hanno identificato **Modules Installer** anziché **Programma di installazione dei moduli di Windows**.  

![Search Results](media/log-analytics-custom-fields/search-results-01.png)

Iniziare dal record della **scheda delle prestazioni WMI** .  Fare clic sulla relativa icona di modifica e quindi su **Modify this highlight**.  

![Modificare l'evidenziazione](media/log-analytics-custom-fields/modify-highlight.png)

Espandere l'evidenziazione per includere la parola **WMI** e quindi eseguire di nuovo l'estrazione.  

![Altro esempio](media/log-analytics-custom-fields/additional-example-01.png)

Ora le voci relative a **Scheda delle prestazioni WMI** risultano corrette e Log Analytics ha anche usato tali informazioni per correggere i record relativi a **Programma di installazione dei moduli di Windows**.  Nella sezione **Riepilogo** è tuttavia possibile osservare che **DPMRA** non viene ancora identificato in modo corretto.

![Search Results](media/log-analytics-custom-fields/search-results-02.png)

Scorrere fino a un record con il servizio DPMRA e usare la stessa procedura per correggere tale record.

![Altro esempio](media/log-analytics-custom-fields/additional-example-02.png)

 Eseguendo l'estrazione, tutti i risultati sono ora accurati.

![Search Results](media/log-analytics-custom-fields/search-results-03.png)

Si noti che **Service_CF** è stato creato, ma non è ancora stato aggiunto ai record.

![Conteggio iniziale](media/log-analytics-custom-fields/initial-count.png)

Dopo un periodo di tempo in cui vengono raccolti nuovi eventi, il campo **Service_CF** viene ora aggiunto ai record che corrispondono ai criteri specificati.

![Risultati finali](media/log-analytics-custom-fields/final-results.png)

Ora il campo personalizzato può essere usato come le altre proprietà del record.  Per illustrare questo concetto, viene creata una query che raggruppa in base al nuovo campo **Service_CF** per verificare quali sono i servizi più attivi.

![Raggruppa per query](media/log-analytics-custom-fields/query-group.png)

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come creare query usando i campi personalizzati come criteri, vedere l'articolo relativo alle [ricerche nei log](log-analytics-log-searches.md) .
- Monitorare i [file di log personalizzati](log-analytics-data-sources-custom-logs.md) analizzati usando campi personalizzati.



<!--HONumber=Oct16_HO2-->


