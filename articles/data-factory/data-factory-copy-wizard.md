<properties
	pageTitle="Copia guidata di data factory | Microsoft Azure"
	description="Informazioni su come usare Copia guidata di Data Factory per copiare i dati da origini dati supportate nei sink."
	services="data-factory"
	documentationCenter=""
	authors="spelluru"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="data-factory"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/27/2016"
	ms.author="spelluru"/>  

# Copia guidata di data factory
Copia guidata di Azure Data Factory consente di creare una pipeline per copiare dati dalle origini supportate nelle destinazioni senza scrivere definizioni JSON per i servizi collegati, i set di dati e le pipeline. Per avviare Copia guidata, fare clic sul riquadro **Copia dati** nella home page della data factory.

![Copia guidata](./media/data-factory-copy-wizard/copy-data-wizard.png)  

## Funzionalità

### Una procedura guidata intuitiva e perfettamente integrata per la copia dei dati
Questa procedura guidata consente di spostare facilmente i dati da un'origine a una destinazione in pochi minuti con i semplici passaggi seguenti:

1.	Selezione l'origine.
2.	Selezionare la destinazione.
3.	Configurare le impostazioni.

![Selezionare l'origine dati](./media/data-factory-copy-wizard/select-data-source-page.png)  

### Esplorazione avanzata dei dati e mapping dello schema
All'interno della procedura guidata è possibile sfogliare tabelle e cartelle, visualizzare dati in anteprima, eseguire il mapping di uno schema, convalidare espressioni ed eseguire semplici trasformazioni di dati.

**Esplorare tabelle o cartelle** ![Sfogliare tabelle e cartelle](./media/data-factory-copy-wizard/browse-tables-folders.png)

### Esperienza scalabile per tipi di dati e di oggetti diversi
L'esperienza è totalmente pensata per i Big Data. Creare pipeline di Data Factory che spostano centinaia di cartelle, file o tabelle è una procedura semplice ed efficiente.

**Visualizzare dati in anteprima** ![Impostazioni sul formato del file](./media/data-factory-copy-wizard/file-format-settings.png)

**Eseguire il mapping di uno schema** ![Mapping dello schema](./media/data-factory-copy-wizard/schema-mapping.png)

**Eseguire trasformazioni semplici** ![Espressione di convalida](./media/data-factory-copy-wizard/validate-expressions.png)

### Esperienza scalabile per tipi di dati e di oggetti diversi
Copia guidata permette di spostare centinaia di cartelle, file o tabelle in modo semplice ed efficiente.

![Selezionare le tabelle da cui copiare i dati](./media/data-factory-copy-wizard/select-tables-to-copy-data.png)  

### Opzioni di pianificazione più avanzate
È possibile eseguire l'operazione di copia una sola volta oppure in base a una pianificazione, con cadenza oraria, giornaliera e così via. Entrambe queste opzioni possono essere usate per tutti i vari connettori tra copie in locale, nel cloud e nei computer desktop locali.

Un'operazione di copia eseguita una sola volta permette di spostare dati da un'origine a una destinazione una sola volta. Si applica ai dati di qualsiasi dimensione e in qualsiasi formato supportato. La copia pianificata consente di copiare i dati secondo una ricorrenza predeterminata. Per configurare la copia pianificata sono disponibili impostazioni avanzate, come la ripetizione dei tentativi, il timeout, gli avvisi e così via.

![Proprietà di pianificazione](./media/data-factory-copy-wizard/scheduling-properties.png)  

## Variabili nel percorso della cartella del BLOB di Azure
È possibile usare le variabili nel percorso della cartella per copiare dati da una cartella determinata in fase di esecuzione in base alle [variabili di sistema WindowStart](data-factory-functions-variables.md#data-factory-system-variables). Le variabili supportate sono: **{year}**, **{month}**, **{day}**, **{hour}**, **{minute}** e **{custom}**. Esempio: inputfolder/{year}/{month}/{day}.

Si supponga di avere cartelle di input nel formato seguente:

	2016/03/01/01
	2016/03/01/02
	2016/03/01/03
	...

Fare clic sul pulsante **Esplora** per **File o cartella**, passare a una di queste cartelle, ad esempio 2016->03->01->02, e fare clic su **Scegli**. Nella casella di testo dovrebbe essere visualizzato **2016/03/01/02**. Sostituire **2016** con **{year}**, **03** con **{month}**, **01** with **{day}** e **02** con **{hour}** e premere TAB. Dovrebbero essere visualizzati elenchi a discesa da cui selezionare il formato per queste quattro variabili:

![Uso di variabili di sistema](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)  

Come mostra lo screenshot seguente, è anche possibile usare una variabile **personalizzata** e qualsiasi [stringa di formato supportato](https://msdn.microsoft.com/library/8kb3ddd4.aspx). Per selezionare una cartella con tale struttura, usare prima di tutto il pulsante **Esplora**. Sostituire un valore con **{custom}** e quindi premere TAB per visualizzare la casella di testo in cui è possibile digitare la stringa di formato.

![Uso di variabili personalizzate](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)  

## Passaggi successivi
Per una procedura dettagliata sull'uso di Copia guidata di Data Factory per creare una pipeline con l'attività di copia, vedere [Esercitazione: Creare una pipeline con l'attività di copia usando la Copia guidata di Data Factory](data-factory-copy-data-wizard-tutorial.md).

<!---HONumber=AcomDC_0824_2016-->