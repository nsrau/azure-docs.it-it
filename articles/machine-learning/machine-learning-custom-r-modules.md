<properties 
	pageTitle="Creazione di moduli R personalizzati in Azure Machine Learning | Azure" 
	description="Guida introduttiva alla creazione di moduli R personalizzati in Azure Machine Learning." 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev"  
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="tbd" 
	ms.date="02/04/2015" 
	ms.author="bradsev" />


# Creazione di moduli R personalizzati in Azure Machine Learning

In questo argomento viene descritto come creare un modulo R personalizzato in Azure Machine Learning. Viene descritto in cosa consiste un modulo R personalizzato e i file utilizzati per definirlo. Viene illustrato come creare tali file e registrare il modulo per la distribuzione in un'area di lavoro di Machine Learning. Vengono quindi descritti in modo più dettagliato elementi e attributi utilizzati nella definizione del modulo personalizzato. Viene inoltre illustrato come utilizzare le funzionalità e i file ausiliari, e gli output multipli. 

## In cosa consiste un modulo R personalizzato?
Un modulo personalizzato è un modulo definito dall'utente che può essere caricato nell'area di lavoro di un utente ed eseguito come parte di un test di Azure Machine Learning. Un modulo R personalizzato è un modulo personalizzato che esegue una funzione R definita dall'utente in Machine Learning. R è un linguaggio di programmazione per il calcolo e statistico e la grafica ampiamente utilizzato in campo statistico e di analisi dei dati per l'implementazione degli algoritmi. Il supporto del linguaggio di Machine Learning è impostato, per impostazione predefinita, in modo da utilizzare il linguaggio R nei moduli personalizzati.

In Azure Machine Learning, i moduli personalizzati presentano uno stato di prima classe nel senso che possono essere utilizzati esattamente come qualsiasi altro modulo. Possono essere eseguiti con altri moduli, inclusi nei test pubblicati o visualizzati. Gli utenti possono controllare l'algoritmo implementato dal modulo, le porte di input e output da utilizzare, i parametri di modellazione e altri comportamenti di runtime.


## File in un modulo R personalizzato
Un modulo R personalizzato viene definito da un file ZIP che contiene almeno due file:

* Un file che implementa la funzione R esposta dal modulo
* Un file di definizione XML che descrive il modulo personalizzato

Nel file ZIP è possibile includere anche altri file ausiliari che forniscono funzionalità a cui è possibile accedere dal modulo personalizzato. Questa opzione viene illustrata di seguito.

## Esempio di Guida introduttiva
In questo esempio viene illustrato come costruire i file richiesti da un modulo R personalizzato, inserirli in un file ZIP e quindi registrare il modulo nell'area di lavoro di Machine Learning.

Considerare l'esempio di un modulo personalizzato Aggiungi righe personale che modifica l'implementazione standard del modulo Aggiungere righe utilizzato per concatenare le righe (osservazioni) da due set di dati (frame di dati). Il modulo Aggiungi righe aggiunge le righe del secondo set di dati input alla fine del primo set di dati input utilizzando l'algoritmo rbind. Analogamente, la funzione `myAddRows` personalizzata accetta due set di dati ma accetta anche un parametro di scambio booleano aggiuntivo come input. Se il parametro di scambio è **FALSE**, restituisce lo stesso set di dati come implementazione standard. Ma se il parametro di scambio è **TRUE**, aggiunge invece righe del primo set di dati di input alla fine del secondo set di dati. Il file che implementa la funzione di myAddRows R, esposta dal modulo Aggiungi righe personale, contiene il seguente codice R.

	myAddRows <- function(Dataset1, Dataset2, swap=FALSE) {
	if (swap) { 
		dataset <- rbind(Dataset2, Dataset1))
	 } else { 
	  	dataset <- rbind(Dataset1, Dataset2)) 
	 } 
	return (dataset)
	}

Per esporre tale funzione `myAddRows` come modulo di Azure Machine Learning, è necessario creare un file di definizione XML per specificare l'aspetto e il comportamento del modulo Aggiungi righe personale. 

	<Module Version="v0.00.1" type="Public" insync="false" Owner="myName">
	  <GUID>{1CE529D1-B9D2-496F-AB42-8DBA60DE8279}</GUID>
	  <ID>myAddRows</ID>
	  <Name>My Add Rows</Name>	
	  <State>Custom</State>
	  <Description>This is my module description. </Description>
	  <Language Name="R" EntryPointFile="myAddRows.R" EntryPoint="myAddRows" />  
	    <Ports>
	      <output id="dataset" display="dataset" type="DataTable">
	        <Description>Combined Data</Description>
	      </output>
	      <input id="Dataset1" display="Dataset1" type="DataTable">
		    <Description>Input dataset 1</Description>
	      </input>
	      <input id="Dataset2" display="Dataset2" type="DataTable">
		    <Description>Input dataset 2</Description>
	      </input>
	    </Ports>
	    <Arguments>
	      <arg id="swap" display="swap" type="bool" >
	        <Description>Swaps inputs</Description>
	      </arg>
	    </Arguments>
	  <Category>My Category</Category>
	</Module>

 
È fondamentale che il contenuto dell'elemento ID nel file .xml corrisponda esattamente al nome della funzione. 

Salvare i due file come *myAddRows.R* e *myAddRows.xml*, quindi comprimerli insieme in un file *myAddRows.zip*.

Per registrarli nell'area di lavoro di Machine Learning, accedere all'area di lavoro in Machine Learning Studio, fare clic sul pulsante **+NUOVO** in fondo e scegliere **MODULO -> DA PACCHETTO ZIP** per caricare il nuovo modulo personalizzato Aggiungi righe personale.

![](http://i.imgur.com/RFJhCls.png)

Ora è possibile accedere al modulo Aggiungi righe personale con il test Machine Learning.

## Elementi nel file di definizione .xml

### Input e output
Gli input e gli output per un modulo personalizzato vengono specificati negli elementi figlio della sezione Porte del file di definizione XML. L'ordine di questi elementi di input e output determina il layout visualizzato (UX) dagli utenti. Il primo elemento figlio elencato nell'elemento porte  del file .xml sarà la porta di input più a sinistra nell'UX di Machine Learning. I tipi di dati supportati dalle porte di input e output sono i seguenti: 

**DataTable**: questo tipo viene passato alla funzione R come data.frame. Infatti, tutti i tipi supportati da Machine Learning (ad esempio, i file CSV o ARFF)  e compatibili con DataTable, vengono convertiti automaticamente in un data.frame. 

       <input id="dataset1" display="Input 1" type="DataTable" IsOptional="false">
           <Description>Input Dataset 1</Description>
       </input>

**Zip**: i moduli personalizzati possono accettare un file ZIP come input. Tale input viene decompresso in una directory di esecuzione della funzione

       <input id="zippedData" display="Zip Input" type="Zip" IsOptional="false">
           <Description>Zip Input for port</Description>
       </input>

È necessario che gli attributi ID associati a ciascuna delle porte di input e output abbiano un valore univoco e che tali valori corrispondano ai parametri denominati nella funzione R. Inoltre, è necessario specificare un valore predefinito per gli attributi con input facoltativi. L'attributo IsOptional è facoltativo per i tipi DataTable e Zip. Inoltre, per impostazione predefinita è False. Questo valore predefinito indica che il tipo di input non è facoltativo.


### Parametri
I parametri per un modulo personalizzato vengono specificati negli elementi figlio della sezione Argomenti del file di definizione .XML. Come con gli elementi figlio nella sezione Porte, l'ordine dei parametri nella sezione Argomenti definisce il layout riscontrato nell'UX. Il primo parametro elencato sarà il primo parametro della funzione. I tipi supportati da Machine Learning per i parametri sono elencati di seguito. Gli attributi facoltativi devono presentare valori predefiniti specificati nella definizione XML. Si tratta dei valori utilizzati se il valore del parametro non viene specificato quando si utilizza la funzione. Per ogni tipo vengono indicati gli attributi facoltativi.


**int** - Parametro di tipo Integer (32 bit).

       <arg id="intValue1" display="My int Param" type="int" IsOptional="false" MinValue="0" MaxValue="100">
           <Description>Integer Parameter 1</Description>
       </arg>

IsOptional, MinValue e MaxValue per int. sono facoltativi.

**double** - Parametro di tipo doppio.

       <arg id="doubleValue1" display="My double Param" type="double" IsOptional="false" min="0.000" max="0.999" default="0.3">
           <Description>Double Parameter 1</Description>
       </arg>
I valori predefiniti IsOptional, min, max, sono facoltativi per double.

**bool** - Parametro booleano rappresentato da una casella di controllo in UX.

       <arg id="boolValue1" display="My boolean Param" type="bool" default="true">
           <Description>Boolean Parameter 1 </Description>
       </arg>

Per bool, il valore predefinito è facoltativo.

**string**: stringa standard

        <arg id="stringValue1" display="My string Param" type="string" default="default value" IsOptional="true">
           <Description>String Parameter 1</Description>
        </arg>

predefinito e IsOptional sono facoltativi per string.

**ColumnPickerFor**: parametro di selezione della colonna. Questo tipo esegue il rendering in UX come selezione di colonne. L'ID di DataTable da cui si selezionano le colonne dovrebbe sostituire parte della tabella nel valore per l'attributo di tipo. La variabile verrà passata alla funzione come un elenco di stringhe. 

        <arg id="columnSelection1" display="My Column Param" type="ColumnPickerFor:table">
           <Description>My column selector Param 1</Description>		
        </arg>

Ad esempio, in presenza di un DataTable con un ID di dataset1, il tipo avrebbe 

		type="ColumnPickerFor:dataset1" 
                            
**enum:<DropDown Type ID>**: elenco (a discesa) enumerato. Il valore scelto viene passato come stringa alla funzione R. Questo tipo richiede che i valori enumerati validi siano i primi definiti nella sezione Argomenti.

       <DropDownType id="myDropDown1">
           <o id="red" display="Red"/>
           <o id="yellow" display="Yellow"/>
           <o id="blue" display="Blue"/>
       </DropDownType>
       <arg id="enum1" display="My Enum Param" default="red" type="enum:myDropDown1">
           <Description>My Enum Param 1</Description>
       </arg>

Così come con input e output, è fondamentale che ogni parametro presenti valori ID univoci associati. Inoltre, i valori ID devono corrispondere ai parametri denominati nella funzione R. Nell'esempio di avvio rapido il parametro/id associato era *swap*.

### Definizione lingua
L'elemento Lingua nel file di definizione XML definisce la funzionalità specifica della lingua. Per i moduli R, generalmente 

	<Language Name="R" EntryPointFile="myFunc.R" EntryPoint="myFunc" AddDisplayOutputPort="false"/>

specifica la lingua, il file in cui viene definita la funzione e il punto di ingresso in tale definizione. L'attributo AddDisplayOutputPort è facoltativo per l'elemento lingua. Come per il modulo Esegui script R (collegamento da definire), se si desidera aggiungere una porta di output che può essere utilizzata per visualizzare grafici/tracciati, scegliere *true* per il tag AddDisplayOutputPort e verrà visualizzata un'ulteriore porta di output. 

### Funzionalità ausiliarie

Sono disponibili diversi attributi non esposti in questo esempio, ma che possono essere utilizzati dagli autori del modulo personalizzato. Ad esempio, un modulo può avere un comportamento deterministico o non deterministico. Un modulo deterministico non verrà eseguito una seconda volta se vengono forniti gli stessi dati di input e la stessa configurazione dei parametri. I risultati memorizzati nella cache verranno invece utilizzati e propagati a tutti i moduli downstream. Un esempio di modulo deterministico in Azure Machine Learning è il modulo di Aggiungi righe. Un esempio di modulo non deterministico è Lettore. Per rendere il modulo personalizzato non deterministico, modificare l'impostazione predefinita aggiungendo l'attributo seguente alla definizione:

	<IsDeterministic>false</IsDeterministic>

### File ausiliari

Qualsiasi file inserito nel file ZIP del modulo personalizzato saranno disponibili all'uso durante la fase di esecuzione. Le strutture di directory eventualmente presenti verranno mantenute. Ciò significa che l'esecuzione del file funziona nello stesso modo, sia localmente, sia in Azure Machine Learning. 

Ad esempio, se si desidera rimuovere tutte le righe con NAs e tutte le righe duplicate nel set di dati prima di eseguire l'output in myAddRows, ed è già stata scritta una funzione R che esegue tale operazione in un file removeDupNARows.R:

	removeDupNARows <- function(dataFrame) {
		#Remove Duplicate Rows:
		dataFrame <- unique(dataFrame)
		#Remove Rows with NAs:
		finalDataFrame <- dataFrame[complete.cases(dataFrame),]
		return(finalDataFrame)
	}
È possibile utilizzare il file ausiliario removeDupNARows.R nella funzione myAddRows:

	myAddRows <- function(Dataset1, Dataset2, swap=FALSE) {
		source("removeDupNARows.R")
	if (swap) { 
		dataset <- rbind(Dataset2, Dataset1))
	 } else { 
	  	dataset <- rbind(Dataset1, Dataset2)) 
	 } 
	dataset <- removeDupNARows(dataset)
	return (dataset)
	}

Quindi, caricare il file .ZIP contenente 'myAddRows.R', 'myAddRows.xml' e 'removeDupNARows.R' come modulo R personalizzato.

### Output multipli

Per poter restituire più di un oggetto di un tipo di dati supportato, è necessario specificare le porte di output appropriate nel file di definizione XML e restituire gli oggetti come elenco. Gli oggetti di output verranno assegnati alle porte di output da sinistra a destra, in base all'ordine in cui gli oggetti vengono inseriti nell'elenco restituito.
 
Ad esempio, se si desidera restituire un set di dati, Dataset1 e Dataset2 in un set di dati di porte di output, dataset1 e dataset2 da sinistra a destra, rispettivamente, definire le porte di output nel file 'myAddRows.xml' come segue:

	<Ports>
	    <output id="dataset" display="dataset" type="DataTable">
	        <Description>Combined Data</Description>
	    </output>
		<output id="Dataset1" display="dataset1" type="DataTable">
	        <Description>Combined Data</Description>
	    </output>
		<output id="Dataset2" display="dataset2" type="DataTable">
	        <Description>Combined Data</Description>
	    </output>
	    <input id="Dataset1" display="Dataset1" type="DataTable">
		    <Description>Input dataset 1</Description>
	    </input>
	    <input id="Dataset2" display="Dataset2" type="DataTable">
		    <Description>Input dataset 2</Description>
	    </input>
	</Ports>

Quindi restituire l'elenco degli oggetti in un elenco nell'ordine corretto in 'myAddRows.R':

	myAddRows <- function(Dataset1, Dataset2, swap=FALSE) {
	if (swap) { 
		dataset <- rbind(Dataset2, Dataset1))
	 } else { 
	  	dataset <- rbind(Dataset1, Dataset2)) 
	 } 
	return (list(dataset, Dataset1, Dataset2))
	}

## Ambiente di esecuzione

Da definire

## Passaggi successivi

Da definire

<!--HONumber=49--> 