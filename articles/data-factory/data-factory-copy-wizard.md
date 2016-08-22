<properties 
	pageTitle="Copia guidata di data factory | Microsoft Azure" 
	description="Informazioni su come usare Copia guidata di data factory per copiare i dati da origini dati supportate nei sink." 
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
**Copia guidata di Data Factory** consente di creare una pipeline per copiare dati dalle origini supportate nelle destinazioni senza scrivere definizioni JSON per i servizi collegati, i set di dati e le pipeline. Per avviare Copia guidata, fare clic sul riquadro **Copia dati** nella home page della data factory.

![Copia di dati guidata](./media/data-factory-copy-wizard/copy-data-wizard.png)  

## Funzionalità

### Una procedura guidata intuitiva e perfettamente integrata per la copia dei dati 
Questa procedura guidata consente di spostare facilmente i dati da un'origine a una destinazione in pochi minuti con i semplici passaggi seguenti:

1.	Selezionare **l'origine**
2.	Selezionare la **destinazione**
3.	Configurare le **impostazioni**

![Selezionare l'origine dati](./media/data-factory-copy-wizard/select-data-source-page.png)  

### Esplorazione avanzata dei dati e mapping dello schema
Mediante la procedura guidata è possibile sfogliare tabelle o cartelle, visualizzare dati in anteprima, mappare lo schema, convalidare espressioni ed eseguire semplici trasformazioni di dati.

**Esplorare tabelle o cartelle** ![Sfogliare tabelle e cartelle](./media/data-factory-copy-wizard/browse-tables-folders.png)

### Esperienza scalabile per tipi di dati e di oggetti diversi
L'esperienza è totalmente pensata per i Big Data. Creare pipeline di Data Factory che spostano centinaia di cartelle, file o tabelle è una procedura semplice ed efficiente.

**Visualizzare dati in anteprima ed eseguire il mapping dello schema e semplici trasformazioni** ![Impostazioni sul formato del file](./media/data-factory-copy-wizard/file-format-settings.png) ![Mapping dello schema](./media/data-factory-copy-wizard/schema-mapping.png) ![Espressione di convalida](./media/data-factory-copy-wizard/validate-expressions.png)

### Esperienza scalabile per tipi di dati e di oggetti diversi
L'esperienza è totalmente pensata per i Big Data. Spostare centinaia di cartelle, file o tabelle è una procedura altrettanto semplice ed efficiente mediante la copia guidata.

![Selezionare le tabelle per copiare i dati](./media/data-factory-copy-wizard/select-tables-to-copy-data.png)

### Opzioni di pianificazione più avanzate
È possibile eseguire l'operazione di copia una sola volta oppure ripeterla in base a una pianificazione (a intervalli di un'ora, un giorno, ecc.). Entrambe queste opzioni possono essere usate per tutti i vari connettori tra copie in locale, sul cloud e sui desktop locali. La copia una tantum consente lo spostamento dei dati da un'origine a una destinazione una sola volta e si applica a dati di qualsiasi dimensione e a tutti i formati supportati. La copia pianificata consente di copiare i dati secondo una ricorrenza predeterminata. Sono disponibili impostazioni avanzate (come ad esempio la ripetizione dei tentativi, timeout, avvisi, ecc.) per configurare la copia pianificata.

![Proprietà di pianificazione](./media/data-factory-copy-wizard/scheduling-properties.png)  


## Esercitazione
Per la procedura dettagliata dell'uso di **Copia guidata di Data Factory** per creare una pipeline con un'attività di copia, vedere [Esercitazione: Creare una pipeline con l'attività di copia usando la Copia guidata di Data Factory](data-factory-copy-data-wizard-tutorial.md).


## Variabili nel percorso della cartella di Azure Blob
È possibile usare le variabili nel percorso della cartella per copiare i dati da una cartella che viene determinata in fase di esecuzione in base alla [variabile di sistema WindowStart](data-factory-functions-variables.md#data-factory-system-variables). Le variabili supportate sono: **year**, **month**, **day**, **hour**, **minute** e **{custom}**. Esempio: inputfolder/{year}/{month}/{day}.

Si supponga di avere cartelle di input nel formato seguente:
	
	2016/03/01/01
	2016/03/01/02
	2016/03/01/03
	...

Fare clic sul pulsante **Sfoglia** per **File o cartella**, passare a una di queste cartelle, ad esempio 2016->03->01->02, e fare clic su **Scegli**. Nella casella di testo dovrebbe essere ora visualizzato **2016/03/01/02**. Sostituire **2016** con **{year}**, **03** con **{month}**, **01** con **{day}**, **02** con **{hour}** e premere **TAB**. Dovrebbero essere visualizzati elenchi a discesa da cui selezionare il **formato** per queste quattro variabili come indicato di seguito:

![Uso di variabili di sistema](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)

È anche possibile usare una variabile **personalizzata** come indicato di seguito e [stringhe di formati supportati](https://msdn.microsoft.com/library/8kb3ddd4.aspx). Verificare di aver selezionato una cartella con quella struttura usando il pulsante Sfoglia, sostituire un valore con **{custom}** e premere **TAB** per vedere la casella di testo in cui digitare la stringa del formato.

![Uso di variabili personalizzate](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)  

## Passaggi successivi
- [Esercitazione: Creare una pipeline con l'attività di copia usando la Copia guidata di Data Factory](data-factory-copy-data-wizard-tutorial.md)

<!---HONumber=AcomDC_0810_2016-->