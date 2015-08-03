<properties 
	pageTitle="Creare moduli R personalizzati in Azure Machine Learning | Microsoft Azure" 
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
	ms.date="05/07/2015" 
	ms.author="bradsev;ankarloff" />


# Creare moduli R personalizzati in Azure Machine Learning

In questo argomento viene descritto come creare e distribuire un modulo R personalizzato in Azure Machine Learning. Viene descritto in cosa consistono i moduli R personalizzati e i file usati per definirli. Viene illustrato come creare i file che definiscono un modulo e come registrare il modulo per la distribuzione in un'area di lavoro di Machine Learning. Vengono quindi descritti in modo più dettagliato elementi e attributi utilizzati nella definizione del modulo personalizzato. Viene inoltre illustrato come usare le funzionalità e i file ausiliari e gli output multipli.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## In cosa consiste un modulo R personalizzato?
Un **modulo personalizzato** è un modulo definito dall'utente che può essere caricato nell'area di lavoro ed eseguito come parte di un esperimento di Azure Machine Learning. Un **modulo R personalizzato** è un modulo personalizzato che esegue una funzione R definita dall'utente. R è un linguaggio di programmazione per il calcolo e statistico e la grafica ampiamente utilizzato in campo statistico e di analisi dei dati per l'implementazione degli algoritmi. Attualmente, R è l'unico linguaggio supportato in moduli personalizzati, ma il supporto per lingue aggiuntive verrà aggiunto in versioni future.

In Azure Machine Learning, i moduli personalizzati presentano uno **stato di prima classe** nel senso che possono essere usati esattamente come qualsiasi altro modulo. Possono essere eseguiti con altri moduli, inclusi nelle visualizzazioni o negli esperimenti pubblicati. Gli utenti possono controllare l'algoritmo implementato dal modulo, le porte di input e output da utilizzare, i parametri di modellazione e altri comportamenti di runtime. Un modulo personalizzato è disponibile solo nell'area di lavoro in cui è stato creato e non può essere pubblicato in esperimenti della community.

## File in un modulo R personalizzato
Un modulo R personalizzato viene definito da un file ZIP che contiene almeno due file:

* Un **file di origine** che implementa la funzione R esposta dal modulo
* Un **file di definizione XML** che descrive l'interfaccia del modulo personalizzato

Nel file ZIP è possibile includere anche altri file ausiliari che forniscono funzionalità a cui è possibile accedere dal modulo personalizzato. Questa opzione viene illustrata di seguito.

## Esempio di Guida introduttiva: definire, creare un pacchetto e registrare un modulo R personalizzato
In questo esempio viene illustrato come costruire i file richiesti da un modulo R personalizzato, inserirli in un file ZIP e quindi registrare il modulo nell'area di lavoro di Machine Learning. I file e il pacchetto ZIP di esempio possono essere scaricati da [qui](http://go.microsoft.com/fwlink/?LinkID=524916&clcid=0x409).

Considerare l'esempio di un modulo **Add Rows personalizzato** che modifica l'implementazione standard del modulo Add Rows usato per concatenare le righe (osservazioni) da due set di dati (frame di dati). Il modulo Add Rows standard aggiunge le righe del secondo set di dati di input alla fine del primo set di dati di input usando l'algoritmo rbind. Analogamente, la funzione `CustomAddRows` personalizzata accetta due set di dati, ma accetta anche un parametro di scambio booleano aggiuntivo come input. Se il parametro di scambio è **FALSE**, restituisce lo stesso set di dati come implementazione standard. Ma, se il parametro di scambio è **TRUE**, aggiunge invece righe del primo set di dati di input alla fine del secondo set di dati. Il file che implementa la funzione `CustomAddRows` R, esposta dal modulo **Add Rows personalizzato**, contiene il seguente codice R.

	CustomAddRows <- function(dataset1, dataset2, swap=FALSE) 
	{
		if (swap)
		{
			return (rbind(dataset2, dataset1));
		}
		else
		{
			return (rbind(dataset1, dataset2));
		} 
	} 

Per esporre la funzione `CustomAddRows` come modulo di Azure Machine Learning, è necessario creare un file di definizione XML per specificare l'aspetto e il comportamento del modulo **Add Rows personalizzato**.

	<!-- Defined a module using an R Script -->
	<Module name="Custom Add Rows">
	    <Owner>Microsoft Corporation</Owner>
	    <Description>Appends one dataset to another. Dataset 2 is concatenated to Dataset 1 when Swap is false, and vice versa when Swap is true.</Description>
	
	<!-- Specify the base language, script file and R function to use for this module. -->		
	    <Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />  
		
	<!-- Define module input and output ports -->
	<!-- Note: The values of the id attributes in the Input and Arg elements must match the parameter names in the R Function CustomAddRows defined in CustomAddRows.R. -->
	    <Ports>
			<Input id="dataset1" name="Dataset 1" type="DataTable">
				<Description>First input dataset</Description>
			</Input>
			<Input id="dataset2" name="Dataset 2" type="DataTable">
				<Description>Second input dataset</Description>
			</Input>
			<Output id="dataset" name="Dataset" type="DataTable">
				<Description>Combined dataset</Description>
			</Output>
	    </Ports>
		
	<!-- Define module parameters -->
	    <Arguments>
			<Arg id="swap" name="Swap" type="bool" >
				<Description>Swap input datasets.</Description>
			</Arg>
	    </Arguments>
	</Module>

 
Si noti che il valore degli attributi **id** degli elementi **Input** e **Arg** nel file XML deve corrispondere esattamente ai nomi dei parametri di funzione del codice R (*dataset1*, *dataset2* e *swap* nell'esempio). Analogamente, il valore dell'attributo **entryPoint** dell'elemento **Language** deve corrispondere esattamente al nome della funzione nello script R (*CustomAddRows* nell'esempio). Al contrario, l'attributo **id** per gli elementi di **Output** non corrisponde a nessuna variabile nello script R. Quando è necessario più di un output, restituire semplicemente un elenco dalla funzione R con i risultati posizionati nello stesso ordine in cui sono dichiarati gli output nel file XML.

Salvare questi due file come *CustomAddRows.R* e *CustomAddRows.xml*, quindi comprimerli insieme nel file *CustomAddRows.zip*.

Per registrarli nell'area di lavoro di Machine Learning, accedere all'area di lavoro in Machine Learning Studio, fare clic sul pulsante **+NUOVO** in fondo e scegliere **MODULO -> DA PACCHETTO ZIP** per caricare il nuovo modulo personalizzato Add Rows.

![](http://i.imgur.com/RFJhCls.png)

Ora è possibile accedere al modulo **Add Rows personalizzato** con gli esperimenti di Machine Learning.

## Elementi nel file di definizione .xml

### Elementi dei moduli
L'elemento **Module** viene usato per definire un modulo personalizzato nel file XML. Si possono definire più moduli in un file XML usando più elementi **Module**. Ogni modulo nell'area di lavoro deve avere un nome univoco. La registrazione di un modulo personalizzato con lo stesso nome di un modulo personalizzato esistente, permetterà la sostituzione del modulo esistente con quello nuovo. I moduli personalizzati, tuttavia, possono essere registrati con lo stesso nome di un modulo Azure Machine Learning esistente e verranno visualizzati nella categoria personalizzata della tavolozza dei moduli.

	<Module name="Custom Add Rows" isDeterministic="false"> 
		<Owner>Microsoft Corporation</Owner>
		<Description>Appends one dataset to another...</Description>/> 


All'interno dell'elemento **Module**, è possibile specificare un elemento **Owner** facoltativo incorporato nel modulo, nonché un elemento **Description** il cui testo viene visualizzato nella Guida rapida per il modulo e quando si passa il mouse sul modulo nell'interfaccia utente di Machine Learning.

**Regole per i limiti di caratteri negli elementi Module**:

* Il valore dell'attributo **Name** nell'elemento **Module** non deve superare i 64 caratteri. 
* Il contenuto dell'elemento **Description** non deve superare i 128 caratteri.
* Il contenuto dell'elemento **Owner** non deve superare i 32 caratteri.

** Indica se i risultati di un modulo sono deterministici o non deterministici

Per impostazione predefinita, tutti i moduli sono considerati deterministici. Ovvero, dato un set di parametri non modificabile, il modulo deve restituire gli stessi risultati ogni volta che viene eseguito. In base a tale comportamento, Azure Machine Learning Studio non esegue nuovamente i moduli contrassegnati come deterministici a meno che non sia stato modificato un parametro o i dati di input. Vengono restituiti i risultati memorizzati nella cache con un'esecuzione più rapida dell'esperimento.

Tuttavia, se il modulo usa una funzione che restituisce risultati diversi ogni volta che viene eseguito, ad esempio RAND o una funzione che restituisce la data o l'ora corrente, è possibile specificare il modulo come non deterministico impostando l'attributo **isDeterministic** facoltativo su **false**. Il modulo verrà eseguito nuovamente ogni volta che verrà eseguito l'esperimento, anche se il modulo di input e i parametri non sono stati modificati.

### Definizione lingua
L'elemento **Language** nel file di definizione XML viene usato per specificare il linguaggio del modulo personalizzato. R attualmente è l'unico linguaggio supportato. Il valore dell'attributo **sourceFile** deve corrispondere al nome del file R che contiene la funzione da chiamare quando viene eseguito il modulo. Questo file deve far parte del pacchetto zip. Il valore dell'attributo **entryPoint** è il nome della funzione chiamata e deve corrispondere a una funzione valida definita nel file di origine.

	<Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />


### Porte
Le porte di input e di output per un modulo personalizzato vengono specificate negli elementi figlio della sezione **Ports** del file di definizione XML. L'ordine di questi elementi determina il layout visualizzato (UX) dagli utenti. Il primo **input** o **output** figlio elencato nell'elemento **Ports** del file XML sarà la porta di input più a sinistra nell'esperienza utente di Machine Learning. Ogni porta di input e di output può disporre di un elemento figlio **Description** che specifica il testo visualizzato quando l'utente passa il cursore del mouse sulla porta nell'interfaccia utente di Machine Learning.

**Regole porte**:

* Il numero massimo di **porte di input e di output** è 8 per ciascuno.

### Elementi di input
Le porte di input consentono agli utenti di passare i dati all'area di lavoro e alla funzione R. I **tipi di dati** supportati dalle porte di input e output sono i seguenti:

**DataTable:** questo tipo viene passato alla funzione R come data.frame. Infatti tutti i tipi (ad esempio, i file CSV o i file ARFF) supportati da Machine Learning e compatibili con **DataTable** vengono convertiti automaticamente in data.frame.

		<Input id="dataset1" name="Input 1" type="DataTable" isOptional="false">
        	<Description>Input Dataset 1</Description>
       	</Input>

L'attributo **id** associato a ogni porta di input **DataTable** deve avere un valore univoco che deve corrispondere al relativo parametro denominato nella funzione R. Le porte **DataTable** facoltative che non vengono passate come input in un esperimento passeranno un valore **NULL** alla funzione R e i file ZIP facoltativi verranno ignorati se l'input non è connesso. L'attributo **isOptional** è facoltativo per i tipi **DataTable** e **Zip** ed è *false* per impostazione predefinita.
	   
**Zip:** i moduli personalizzati possono accettare un file ZIP come input. Tale input viene decompresso in una directory di esecuzione R della funzione

		<Input id="zippedData" name="Zip Input" type="Zip" IsOptional="false">
        	<Description>Zip files will be extracted to the R working directory.</Description>
       	</Input>

Per i moduli personalizzati R, non è necessario che l'ID di una porta Zip corrisponda a tutti i parametri della funzione R poiché il file ZIP viene automaticamente estratto nella directory di lavoro R.

**Regole di input:**

* Il valore dell'attributo **id** dell'elemento **Input** deve essere un nome di variabile R valido.
* Il valore dell'attributo **id** dell'elemento **Input** non deve superare i 64 caratteri.
* Il valore dell'attributo **name** dell'elemento **Input** non deve superare i 64 caratteri.
* Il contenuto dell'elemento **Description** non deve superare i 128 caratteri.
* Il valore dell'attributo **type** dell'elemento **Input** deve essere *Zip* o *DataTable*.
* Il valore dell'attributo **isOptional** dell'elemento **Input** non è obbligatorio (ed è *false* per impostazione predefinita quando non è specificato), ma, se è specificato, deve essere *true* o *false*.

### Elementi di output

**Porte di output standard:** le porte output corrispondono ai valori restituiti dalla funzione R, che può quindi essere usata dai moduli successivi. *DataTable* è l'unico tipo di porta di output standard attualmente supportato. Il supporto per *Learners* e *Transforms* è di prossima introduzione. Un output *DataTable* viene definito come:

	<Output id="dataset" name="Dataset" type="DataTable">
		<Description>Combined dataset</Description>
	</Output>

Per gli output in moduli R personalizzati, il valore dell'attributo **id** non deve corrispondere ad alcun elemento nello script R, ma deve essere univoco. Per l'output di un modulo singolo, il valore restituito dalla funzione R deve essere un *data.frame*. Per poter restituire più di un oggetto di un tipo di dati supportato, è necessario specificare le porte di output appropriate nel file di definizione XML e restituire gli oggetti come elenco. Gli oggetti di output verranno assegnati alle porte di output da sinistra a destra, in base all'ordine in cui gli oggetti vengono inseriti nell'elenco restituito.
 
Ad esempio, se si desidera restituire un set di dati, Dataset1 e Dataset2 in un set di dati di porte di output, dataset1 e dataset2 da sinistra a destra, rispettivamente, definire le porte di output nel file 'CustomAddRows.xmll' come segue:

Ad esempio, se si desidera modificare il modulo **Add rows personalizzato** per l'output dei due set di dati originali, *dataset1* e *dataset2*, oltre al nuovo set di dati *dataset* unito (in un ordine da sinistra a destra, del tipo *dataset*, *dataset1*, *dataset2*), definire le porte di output nel file CustomAddRows.xml come segue:

	<Ports> 
		<Output id="dataset" name="Dataset Out" type="DataTable"> 
			<Description>New Dataset</Description> 
		</Output> 
		<Output id="dataset1_out" name="Dataset 1 Out" type="DataTable"> 
			<Description>First Dataset</Description> 
		</Output> 
		<Output id="dataset2_out" name="Dataset 2 Out" type="DataTable"> 
			<Description>Second Dataset</Description> 
		</Output> 
		<Input id="dataset1" name="Dataset 1" type="DataTable"> 
			<Description>First Input Table</Description>
		</Input> 
		<Input id="dataset2" name="Dataset 2" type="DataTable"> 
			<Description>Second Input Table</Description> 
		</Input> 
	</Ports> 


Restituire quindi gli oggetti in un elenco con l'ordine corretto in 'CustomAddRows.R':

	CustomAddRows <- function(dataset1, dataset2, swap=FALSE) { 
		if (swap) { dataset <- rbind(dataset2, dataset1)) } 
		else { dataset <- rbind(dataset1, dataset2)) 
		} 
	return (list(dataset, dataset1, dataset2)) 
	} 
	
**Output di visualizzazione:** è inoltre possibile specificare una porta di output di tipo *Visualization* che consente di visualizzare l'output del dispositivo e della console grafica R. Questa porta non fa parte dell'output della funzione R e non interferisce con l'ordine degli altri tipi di porta di output. Per aggiungere una porta di visualizzazione ai moduli personalizzati, aggiungere un elemento **Output** con un valore *Visualization* per il relativo attributo **type**:

	<Output id="deviceOutput" name="View Port" type="Visualization">
      <Description>View the R console graphics device output.</Description>
    </Output>
	
**Regole di output:**

* Il valore dell'attributo **id** dell'elemento **Output** deve essere un nome di variabile R valido.
* Il valore dell'attributo **id** dell'elemento **Output** non deve superare i 32 caratteri.
* Il valore dell'attributo **name** dell'elemento **Output** non deve superare i 64 caratteri.
* Il valore dell'attributo **type** dell'elemento **Output** deve essere *Visualization*.

### Argomenti
Dati aggiuntivi possono essere passati alla funzione R tramite i parametri del modulo definiti nell'elemento **Arguments**. Questi parametri vengono visualizzati nel riquadro delle proprietà più a destra dell'interfaccia utente di Machine Learning quando viene selezionato il modulo. Gli argomenti possono essere uno qualsiasi dei tipi supportati. In alternativa, è possibile creare un enumeratore personalizzato, se necessario. Analogamente agli elementi **Ports**, gli elementi **Arguments** possono presentare un elemento **Description** facoltativo che specifica il testo visualizzato quando si posiziona il mouse sul nome del parametro. Le proprietà facoltative per un modulo, quali defaultValue, minValue e maxValue possono essere aggiunte a qualsiasi argomento come attributi di un elemento **Properties**. Le proprietà valide per l'elemento **Properties** dipendono dal tipo di argomento e vengono descritte con i tipi di argomento supportati riportati di seguito. Così come con input e output, è fondamentale che ogni parametro presenti valori ID univoci associati. Inoltre, i valori ID devono corrispondere ai parametri denominati nella funzione R. Nell'esempio di avvio rapido il parametro/id associato era *swap*.

### Elemento Arg
Un parametro del modulo viene definito mediante l'elemento figlio **Arg** della sezione **Arguments** del file di definizione XML. Come con gli elementi figlio nella sezione **Ports**, l'ordine dei parametri nella sezione **Arguments** definisce il layout riscontrato nell'esperienza utente. I parametri vengono visualizzati dall'alto verso il basso nell'interfaccia utente nello stesso ordine in cui sono definiti nel file XML. I tipi supportati da Machine Learning per i parametri sono elencati di seguito.

**int**: parametro di tipo Integer (32 bit).

		<Arg id="intValue1" name="Int Param" type="int">
			<Properties min="0" max="100" default="0" />
			<Description>Integer Parameter</Description>
       </Arg>



* *Proprietà facoltative*: **min**, **max** e **default**

**double**: parametro di tipo doppio.

       <Arg id="doubleValue1" name="Double Param" type="double">
           <Properties min="0.000" max="0.999" default="0.3" />
		   <Description>Double Parameter</Description>
       </Arg>


* *Proprietà facoltative*: **min**, **max** e **default**

**bool**: parametro booleano rappresentato da una casella di controllo nell'esperienza utente.

		<Arg id="boolValue1" name="Boolean Param" type="bool">
			<Properties default="true" />
			<Description>Boolean Parameter</Description>
		</Arg>



* *Proprietà facoltative*: **default** (false se non impostato)

**string**: stringa standard

        <Arg id="stringValue1" name="My string Param" type="string">
		   <Properties default="Default string value." isOptional="true" />
           <Description>String Parameter 1</Description>
        </Arg>


* *Proprietà facoltative*: **default** e **isOptional**. Una stringa facoltativa senza un valore predefinito verrà passata come null alla funzione R se non viene fornito in altro modo un valore da un utente.

**ColumnPicker**: parametro di selezione della colonna. Questo tipo esegue il rendering in UX come selezione di colonne. L'elemento **Property** viene usato per specificare l'ID della porta dal quale verranno selezionate le colonne, in cui il tipo di porta di destinazione deve essere *DataTable*. Il risultato della selezione delle colonne verrà passato alla funzione R come elenco di stringhe contenenti i nomi di colonna selezionati.

		<Arg id="colset" name="Column set" type="ColumnPicker">	  
		  <Properties portId="datasetIn1" allowedTypes="Numeric" default="NumericAll"/>
		  <Description>Column set</Description>
		</Arg>


* *Proprietà obbligatorie*: **portId**. Corrisponde all'ID di un elemento Input di tipo *DataTable*.
* *Proprietà facoltative*:
	* **allowedTypes**: filtra i tipi di colonna tra cui l'utente può scegliere. I valori validi includono: 
		* 	Numerico
		* 	Boolean
		* 	Categorical
		* 	Stringa
		* 	Etichetta
		* 	Funzionalità
		* 	Score
		* 	Tutti

	* **default**: le selezioni predefinite valide per il selettore di colonna includono:
		* Nessuno
		* NumericFeature
		* NumericLabel
		* NumericScore
		* NumericAll
		* BooleanFeature
		* BooleanLabel
		* BooleanScore
		* BooleanAll
		* CategoricalFeature
		* CategoricalLabel
		* CategoricalScore
		* CategoricalAll
		* StringFeature
		* StringLabel
		* StringScore
		* StringAll
		* AllLabel
		* AllFeature
		* AllScore
		* Tutti

                            							
**DropDown**: elenco enumerato specificato dall'utente (elenco a discesa). Gli elementi dell'elenco a discesa vengono specificati all'interno dell'elemento **Properties** usando un elemento **Item**. L'**ID** per ogni **elemento** deve essere univoco e una variabile R valida e il nome dell'elemento è sia il testo visualizzato dagli utenti che il valore passato alla funzione R.

	<Arg id="color" name="Color" type="DropDown">
      <Properties default="red">
        <Item id="red" name="Red Value"/>
        <Item id="green" name="Green Value"/>
        <Item id="blue" name="Blue Value"/>
      </Properties>
      <Description>Select a color.</Description>
    </Arg>	

* *Proprietà facoltative*:
	* **default**: il valore della proprietà predefinita deve corrispondere a un valore ID di uno degli elementi **Item**.


### File ausiliari

Qualsiasi file inserito nel file ZIP del modulo personalizzato saranno disponibili all'uso durante la fase di esecuzione. Le strutture di directory eventualmente presenti verranno mantenute. Ciò significa che l'esecuzione del file funziona nello stesso modo, sia localmente, sia in Azure Machine Learning.

Ad esempio, si supponga di voler rimuovere tutte le righe con NAs e tutte le righe duplicate nel set di dati prima di eseguire l'output in CustomAddRows e di avere già scritto una funzione R che esegue tale operazione in un file RemoveDupNARows.R:

	RemoveDupNARows <- function(dataFrame) {
		#Remove Duplicate Rows:
		dataFrame <- unique(dataFrame)
		#Remove Rows with NAs:
		finalDataFrame <- dataFrame[complete.cases(dataFrame),]
		return(finalDataFrame)
	}
È possibile usare il file ausiliario RemoveDupNARows.R nella funzione CustomAddRows:

	CustomAddRows <- function(dataset1, dataset2, swap=FALSE) {
		source(“RemoveDupNARows.R”)
			if (swap) { 
				dataset <- rbind(dataset2, dataset1))
	 		} else { 
	  			dataset <- rbind(dataset1, dataset2)) 
	 		} 
		dataset <- removeDupNARows(dataset)
		return (dataset)
	}

Quindi, caricare il file ZIP contenente 'CustomAddRows.R', 'CustomAddRows.xml' e 'RemoveDupNARows.R' come modulo R personalizzato.

## Ambiente di esecuzione ##
L'ambiente di esecuzione dello script R usa la stessa versione di R come modulo **Execute R Script** e può usare gli stessi pacchetti predefiniti. È possibile aggiungere altri pacchetti R al modulo personalizzato includendoli nel pacchetto ZIP del modulo personalizzato e il caricandoli nello script R come si farebbe nel proprio ambiente R.

Le **limitazioni dell'ambiente di esecuzione** includono:

* File system non permanente: i file scritti quando viene eseguito il modulo personalizzato non verranno mantenuti in più esecuzioni dello stesso modulo.
* Nessun accesso alla rete



 

<!---HONumber=July15_HO4-->