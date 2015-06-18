<properties 
	pageTitle="Guida al linguaggio di specifica Net# delle reti neurali per Azure ML" 
	description="Sintassi relativa al linguaggio per la specifica delle reti neurali Net# ed esempi sulla creazione di un modello di rete neurale personalizzato in Microsoft Azure ML mediante Net#" 
	services="machine-learning" 
	documentationCenter="" 
	authors="jeannt" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/08/2014" 
	ms.author="jeannt"/>



# Guida al linguaggio di specifica della rete neurale Net #

##Panoramica
Net# è un linguaggio sviluppato da Microsoft e usato per definire le architetture di reti neurali per moduli di rete neurale in Microsoft Azure Machine Learning. Contenuto dell'articolo:

-	Concetti di base correlati alle reti neurali
-	Requisiti relativi alla rete neurale e come definire i componenti principali
-	Sintassi e parole chiave del linguaggio di specifica Net#
-	Esempi di reti neurali personalizzate create usando Net# 
	
[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)] 

##Nozioni di base sulla rete neurale
Una struttura di rete neurale è composta da ***nodi*** organizzati in ***livelli*** e ***connessioni*** ponderate (o ***bordi***) tra i nodi. Le connessioni sono direzionali e ognuna dispone di un nodo di ***origine*** e un nodo di ***destinazione***.

Ogni ***livello in training*** (un livello nascosto o di output) dispone di una o più ***aggregazioni di connessioni***. Un'aggregazione di connessioni è costituita da un livello di origine e da una specifica delle connessioni provenienti da quel livello di origine. Tutte le connessioni in una determinata aggregazione condividono lo stesso ***livello di origine*** e lo stesso ***livello di destinazione***. In Net# un'aggregazione di connessioni è considerata come appartenente al livello di destinazione dell'aggregazione.
 
Net# supporta diversi tipi di aggregazioni di connessioni, permettendo quindi di personalizzare il modo in cui gli input sono mappati per i livelli nascosti e agli output.

L'aggregazione predefinita o standard è un'**aggregazione completa**, in cui ogni nodo nel livello di origine è connesso a ogni nodo del livello di destinazione.

Net# supporta anche i quattro tipi seguenti di aggregazioni di connessioni avanzate:

-	**Aggregazioni filtrate**. L'utente può definire un predicato usando le posizioni del nodo di livelli di origine e del nodo di livelli di destinazione. I nodi vengono connessi se il predicato è True.
-	**Aggregazioni convoluzionali**. L'utente può definire piccoli intorni di nodi nel livello di origine. Ogni nodo nel livello di destinazione è connesso a un intorno di nodi nel livello di origine.
-	**Aggregazioni di pooling** e **aggregazioni di normalizzazione delle risposte**. Sono simili alle aggregazioni convoluzionali, in quanto l'utente definisce piccoli intorni di nodi nel livello di origine. La differenza è che i pesi dei bordi in questi pacchetti non sono addestrabili. Infatti, una funzione predefinita viene applicata ai valori del nodo di origine per determinare il valore del nodo di destinazione.  

L'uso di Net# per definire la struttura di una rete neurale rende possibile la definizione di strutture complesse quali reti neurali profonde o convoluzioni di dimensioni arbitrarie, che notoriamente migliorano l'apprendimento relativo a dati quali immagini, audio e video.

##Personalizzazioni supportate
L'architettura dei modelli di rete neurale creati in Azure Machine Learning può essere ampiamente personalizzata tramite Net#. È possibile:

-	Creare livelli nascosti e controllare il numero di nodi in ogni livello.
-	Specificare la modalità di connessione reciproca dei livelli.
-	Definire strutture di connettività speciali, ad esempio aggregazioni di convoluzioni e per la condivisione dei pesi.
-	Specificare diverse funzioni di attivazione.  

Per dettagli relativi alla sintassi del linguaggio di specifica, vedere Specifica della struttura.
 
Per esempi relativi alla definizione di reti neurali per alcune attività comuni di Machine Learning, dalle più semplici alle più complesse, vedere Esempi.

##Requisiti generali
-	Devono essere esattamente disponibili un livello di output, almeno un livello di input e zero o più livelli nascosti. 
-	Ogni livello ha un numero fisso di nodi, disposti concettualmente in una matrice rettangolare di dimensioni arbitrarie. 
-	I livelli di input non sono associati a parametri sottoposti a training e rappresentano il punto in cui i dati delle istanze entrano nella rete. 
-	I livelli sottoponibili a training, ovvero i livelli nascosti e di output hanno parametri sottoposti a training associati, noti come pesi e distorsioni. 
-	I nodi di origine e di destinazione devono trovarsi in livelli separati. 
-	La connessione deve essere aciclica, ovvero non può essere presente una catena di connessioni che riporta al nodo di origine iniziale.
-	Il livello di output non può essere un livello di origine di un'aggregazione di connessioni.  

##Specifiche di struttura
La specifica della struttura di una rete neurale è composta da tre sezioni: la **dichiarazione della costante**, la **dichiarazione dei livelli** e la **dichiarazione della connessione**. È inoltre disponibile una sezione facoltativa la **dichiarazione delle condivisioni**. Le sezioni possono essere specificate in qualsiasi ordine.

##Dichiarazione della costante 
Una dichiarazione della costante è facoltativa. Fornisce un mezzo per definire i valori utilizzati in una qualunque posizione nella definizione della rete neurale. L'istruzione di dichiarazione è costituita da un identificatore seguito da un segno di uguale e un'espressione valore.

Ad esempio, l'istruzione seguente definisce una costante **x**:


    Const X = 28;  

Per definire contemporaneamente due o più costanti, racchiudere i nomi e i valori degli identificatori tra parentesi graffe e separarle da punti e virgola, ad esempio:

    Const { X = 28; Y = 4; }  

Il lato destro di ogni espressione di assegnazione può essere un numero intero, un numero reale, un valore booleano (True o False) o un'espressione matematica, ad esempio:

	Const { X = 17 * 2; Y = true; }  

##Dichiarazione dei livelli
La dichiarazione di livello è obbligatoria. Definisce le dimensioni e l'origine del livello, inclusi il raggruppamento di connessione e gli attributi. L'istruzione della dichiarazione inizia con il nome del livello (di input, nascosto o di output), seguito dalle dimensioni del livello (una tupla di valori Integer positivi), ad esempio.

	input Data[784];
	hidden Hidden[5,20] from Data all;
	output Result[2] from Hidden all;  

-	Il prodotto delle dimensioni è il numero di nodi nel livello. In questo esempio sono presenti due dimensioni [5,20]. Ciò significa che nel livello sono disponibili 100 nodi.
-	I livelli possono essere dichiarati in qualsiasi ordine, con una eccezione: se sono stati definiti più livelli di input, l'ordine in cui vengono dichiarati deve corrispondere all'ordine delle funzionalità nei dati di input.  

<!-- REMOVED THIS CONTENT UNTIL THIS FEATURE IS SUPPORTED IN THE PRODUCT
To specify that the number of nodes in a layer be determined automatically, use the **auto** keyword. The **auto** keyword has different effects, depending on the layer:  

-	In an input layer declaration, the number of nodes is the number of features in the input data.
-	In a hidden layer declaration, the number of nodes is the number that is specified by the parameter value for **Number of hidden nodes**. 
-	In an output layer declaration, the number of nodes is 2 for two-class classification, 1 for regression, and equal to the number of output nodes for multiclass classification.   

For example, the following network definition allows the size of all layers to be automatically determined:  

	input Data auto;
	hidden Hidden auto from Data all;
	output Result auto from Hidden all;  
-->

La dichiarazione dei livelli per livelli che possono essere sottoposti a training, ovvero i livelli nascosti o di output, può includere facoltativamente la funzione di output, definita anche funzione di attivazione, che assume il valore predefinito **sigmoid**. Sono supportate le funzioni di output seguenti:

-	sigmoid
-	linear
-	softmax
-	rlinear
-	square
-	sqrt
-	srlinear
-	abs
-	tanh 
-	brlinear  

Ad esempio, la dichiarazione seguente usa la funzione **softmax**:

	output Result [100] softmax from Hidden all;  

##Dichiarazione della connessione
Immediatamente dopo aver definito il livello sottoponibile a training, è necessario dichiarare connessioni tra i livelli definiti. La dichiarazione dell'aggregazione di connessioni inizia con la parola chiave **from**, seguita dal nome del livello di origine dell'aggregazione e dal tipo di aggregazione di connessioni da creare.

Sono attualmente supportati cinque tipi di aggregazioni di connessioni:

-	Aggregazioni **complete**, indicate dalla parola chiave **all**
-	Aggregazioni **filtrate**, indicate dalla parola chiave **where**, seguita da un'espressione del predicato
-	Aggregazioni **convoluzionali**, indicate dalla parola chiave **convolve**, seguite dagli attributi di convoluzione
-	Aggregazioni di **pooling**, indicate dalle parole chiave **max pool** o **mean pool**
-	Aggregazioni di **normalizzazione delle risposte**, indicate dalla parola chiave **response norm**  	

##Aggregazioni complete  

Un'aggregazione completa di connessioni include una connessione da ogni nodo del livello di origine verso ogni nodo del livello di destinazione. Si tratta del tipo di connessione di rete predefinito.

##Aggregazioni filtrate
Una specifica di aggregazione di connessioni filtrata include un predicato, espresso sintatticamente in modo analogo a un'espressione lambda in C#. L'esempio seguente definisce due aggregazioni filtrate:

	input Pixels [10, 20];
	hidden ByRow[10, 12] from Pixels where (s,d) => s[0] == d[0];
	hidden ByCol[5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;  

-	Nel predicato per ByRow, **s** è un parametro che rappresenta un indice nella matrice rettangolare di nodi del livello di input Pixels e **d** è un parametro che rappresenta un indice nella matrice di nodi del livello nascosto ByRow. Il tipo di **s** e **d** è una tupla di valori Integer con lunghezza pari a due. Concettualmente, **s** include tutte le coppie di valori Integer con 0 <= s[0] < 10 e 0 <= s[1] < 20, mentre **d** include tutte le coppie di valori Integer con 0 <= d[0] < 10 e 0 <= d[1] < 12. 
-	Una condizione è presente nella parte destra dell'espressione del predicato. In questo esempio per ogni valore di **s** e **d** tale da rendere true la condizione è disponibile un bordo dal nodo del livello di origine verso il nodo del livello di destinazione. Questa espressione di filtro indica quindi che l'aggregazione include una connessione dal nodo definito da **s** verso il nodo definito da **d** in tutti i casi in cui s[0] è uguale a d[0].  

È facoltativamente possibile specificare un insieme di pesi per un'aggregazione filtrata. Il valore dell'attributo **Weights** deve essere una tupla di valori a virgola mobile la cui lunghezza corrisponde al numero di connessioni definite dall'aggregazione. Per impostazione predefinita, i pesi sono generati in modo casuale.

I valori dei pesi sono raggruppati in base all'indice dei nodi di destinazione, ovvero il primo nodo di destinazione è connesso ai nodi di origine K, quindi i primi elementi K della tupla **Weights** rappresentano i pesi per il primo nodo di destinazione, in base all'ordine dell'indice di origine. La stessa procedura è applicabile ai nodi di destinazione rimanenti.

##Aggregazioni convoluzionali
Quando i dati di training hanno una struttura omogenea, le connessioni convoluzionali vengono solitamente usate per ottenere funzionalità di livello elevato dei dati. Ad esempio, nei dati di tipo immagine, audio o video è possibile che la dimensionalità spaziale o temporale sia abbastanza uniforme.

Le aggregazioni convoluzionali usano **kernel** rettangolari inseriti nelle dimensioni. Essenzialmente, ogni kernel definisce un insieme di pesi applicati negli intorni locali, indicati come **applicazioni di kernel**. Ogni applicazione di kernel corrisponde a un nodo nel livello di origine indicato come **nodo centrale**. I pesi di un kernel sono condivisi tra molte connessioni. In un'aggregazione convoluzionale ogni kernel è rettangolare e tutte le applicazioni di kernel hanno le stesse dimensioni.

Le aggregazioni convoluzionali supportano i seguenti attributi:

**InputShape** definisce la dimensionalità del livello di origine ai fini di questa aggregazione convoluzionale. Il valore deve essere una tupla di valori Integer positivi. Il prodotto dei valori Integer deve essere uguale al numero di nodi del livello di origine, ma non deve corrispondere in altro modo alla dimensionalità dichiarata per il livello di origine. La lunghezza di questa tupla diventa il valore di **arietà** per l'aggregazione convoluzionale. In genere, il grado fa riferimento al numero di argomenti oppure operandi che una funziona può accettare.

Per definire la forma e le posizioni dei kernel, usare gli attributi **KernelShape**, **Stride**, **Padding**, **LowerPad** e **UpperPad**:

-	**KernelShape**: (obbligatorio) definisce la dimensionalità di ogni kernel per l'aggregazione convoluzionale. Il valore deve essere una tupla di numeri interi positivi con una lunghezza uguale al valore del grado dell'aggregazione. Ogni componente di questa tupla non deve essere maggiore del componente corrispondente di **InputShape**. 
-	**Stride**: (facoltativo) definisce le dimensioni dei piani di inserimento della convoluzione (una dimensione di piano per ogni dimensione), cioè la distanza tra i nodi centrali. Il valore deve essere una tupla di numeri interi positivi con una lunghezza corrispondente al valore del grado dell'aggregazione. Ogni componente di questa tupla non deve essere maggiore del componente corrispondente di **KernelShape**. Il valore predefinito è una tupla con tutti i componenti uguali a uno. 
-	**Padding**: (facoltativo) determina se l'input deve essere riempito usando uno schema di riempimento predefinito. Il valore può essere un singolo valore booleano o una tupla di valori booleani, con lunghezza corrispondente al valore del grado dell'aggregazione. Un singolo valore booleano viene esteso in modo da diventare una tupla di lunghezza corretta con tutti i componenti uguali al valore specificato. Se il valore per una dimensione è True, l'origine sarà riempita in modo logico in quella dimensione con celle a valore zero per supportare altre applicazioni di kernel, in modo che i nodi centrali del primo e dell'ultimo kernel in quella dimensione siano i primi e gli ultimi nodi di quella dimensione nel livello di origine. Il numero di nodi "fittizi" in ogni dimensione viene quindi determinato automaticamente, in modo da inserire esattamente (InputShape[d] - 1)/Stride[d] + 1 kernel nel livello di origine riempito. Se il valore per una dimensione è False, i kernel verranno definiti in modo che il numero di nodi esclusi in ogni lato sia uguale (con una differenza massima di 1). Il valore predefinito di questo attributo è una tupla con tutti i componenti uguali a False.
-	**UpperPad** e **LowerPad**: (facoltativi) consentono di controllare la quantità di riempimento da usare. Questi attributi possono essere definiti se e solo se **Padding** ***non*** è stato definito. I valori devono essere tuple con numeri interi con lunghezza corrispondente al grado dell'aggregazione. Quando questi attributi sono specificati, i nodi "fittizi" vengono aggiunti alle estremità superiori e inferiori di ogni dimensione del livello di input. Il numero di nodi aggiunti alle estremità inferiori e superiori in ogni dimensione è determinato rispettivamente da **LowerPad**[i] e **UpperPad**[i]. Per assicurare che i kernel corrispondano solo a nodi "effettivi" e non a nodi "fittizi", è necessario che siano soddisfatte le condizioni seguenti:
	-	Ogni componente di **LowerPad** deve essere rigorosamente inferiore a KernelShape[d]/2. 
	-	Ciascun componente di **UpperPad** non deve essere maggiore di KernelShape[d]/2. 
	-	Il valore predefinito di questi attributi è una tupla con tutti i componenti uguali a 0. 
-	**Sharing**: (facoltativo) definisce la condivisione dei pesi per ogni dimensione della convoluzione. Il valore può essere un singolo valore booleano o una tupla di valori booleani, con lunghezza corrispondente al valore del grado dell'aggregazione. Un singolo valore booleano viene esteso in modo da diventare una tupla di lunghezza corretta con tutti i componenti uguali al valore specificato. Il valore predefinito è una tupla costituita interamente da valori True. 
-	**MapCount**: (facoltativo) definisce il numero di mapping di funzionalità per l'aggregazione convoluzionale. Il valore può essere un singolo numero intero positivo o una tupla di numeri interi positivi, con lunghezza corrispondente al valore del grado dell'aggregazione. Un singolo valore Integer viene esteso in modo da diventare una tupla di lunghezza corretta quando i primi componenti sono uguali al valore specificato e tutti i componenti rimanenti sono uguali a uno. Il valore predefinito è uno. Il numero totale di mapping di funzionalità è il prodotto dei componenti della tupla. La fattorizzazione di questo numero totale nei componenti determina il modo in cui i valori di mapping di funzionalità vengono raggruppati nei nodi di destinazione. 
-	**Weights**: (facoltativo) definisce i pesi iniziali per l'aggregazione. Il valore deve essere una tupla di valori a virgola mobile con una lunghezza corrispondente al numero di pesi per kernel, come definito in seguito in questo articolo. I pesi predefiniti sono generati in modo casuale.  

Per altre informazioni sulle reti convoluzionali e le relative applicazioni, vedere gli articoli seguenti:

-	[http://deeplearning.net/tutorial/lenet.html ](http://deeplearning.net/tutorial/lenet.html)
-	[http://research.microsoft.com/pubs/68920/icdar03.pdf](http://research.microsoft.com/pubs/68920/icdar03.pdf) 
-	[http://people.csail.mit.edu/jvb/papers/cnn_tutorial.pdf](http://people.csail.mit.edu/jvb/papers/cnn_tutorial.pdf)  

##Aggregazioni di pooling
Un'**aggregazione di pooling** applica una geometria analoga alla connettività convoluzionale, ma usa funzioni predefinite nei valori del nodo di origine per derivare il valore del nodo di destinazione. Le aggregazioni di pooling non hanno quindi stati sottoponibili a training (pesi o distorsioni). Le aggregazioni di pooling supportano tutti gli attributi convoluzionali, ad eccezione di **Sharing**, **MapCount**, e **Weights**.

In genere, i kernel riepilogati da unità di pooling adiacenti non sono sovrapposti. Se Stride[d] equivale a KernelShape[d] in ogni dimensione, il livello ottenuto è il livello di pooling locale tradizionale, usato in genere nelle reti neurali convoluzionali. Ogni nodo di destinazione calcola il valore massimo o medio delle attività del rispettivo kernel nel livello di origine.

L'esempio seguente illustra un'aggregazione di pooling:

	hidden P1 [5, 12, 12]
	  from C1 max pool {
	    InputShape  = [ 5, 24, 24];
	    KernelShape = [ 1,  2,  2];
	    Stride      = [ 1,  2,  2];
	  }  

-	L'arietà dell'aggregazione è pari a 3, ovvero alla lunghezza delle tuple **InputShape**, **KernelShape** e **Stride**. 
-	Il numero di nodi del livello di origine è pari a 5 * 24 * 24 = 2880. 
-	Questo è un livello di pooling locale tradizionale, poiché **KernelShape** e **Stride** sono uguali. 
-	Il numero di nodi del livello di destinazione è pari a 5 * 12 * 12 = 1440.  
	
Per altre informazioni sui livelli di pooling, vedere gli articoli seguenti:

-	[http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf](http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf) (Sezione 3.4)
-	[http://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf](http://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf) 
-	[http://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf](http://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf)
	
##Aggregazioni di normalizzazione delle risposte
La **normalizzazione delle risposte** è uno schema di normalizzazione locale introdotto per la prima volta da Geoffrey Hinton e altri in un articolo relativo alla classificazione ImageNet con reti neurali convoluzionali basate su machine deep learning (vedere la sezione 3.3). La normalizzazione delle risposte viene usata per semplificare la generalizzazione nelle reti neurali. Quando un neurone opera a un livello di attivazione molto elevato, un livello di normalizzazione delle risposte locale sopprime il livello di attivazione dei neuroni circostanti. Per ottenere questo risultato vengono usati tre parametri, ovvero ***α***, ***β*** e ***k***, e una struttura convoluzionale (o forma di intorno). Ogni neurone nel livello di destinazione ***y*** corrisponde a un neurone ***x*** nel livello di origine. Il livello di attivazione di ***y*** è definito dalla formula seguente, dove ***f*** corrisponde al livello di attivazione di un neurone e ***Nx*** è il kernel o l'insieme contenente i neuroni nell'intorno di ***x***, come definito dalla struttura convoluzionale seguente:

![][1]

Le aggregazioni di normalizzazione delle risposte supportano tutti gli attributi convoluzionali, ad eccezione di **Sharing**, **MapCount** e **Weights**.
 
-	Se il kernel contiene neuroni nello stesso mapping di ***x***, lo schema di normalizzazione è definito **normalizzazione nello stesso mapping**. Per definire la normalizzazione nello stesso mapping, la prima coordinata in **InputShape** deve avere valore 1.
-	Se il kernel contiene neuroni nella stessa posizione spaziale di ***x***, ma i neuroni si trovano in mapping diversi, lo schema di normalizzazione sarà definito **normalizzazione tra mapping**. Questo tipo di normalizzazione delle risposte implementa una forma di inibizione laterale ispirata dal tipo trovato nei neuroni reali, creando una competizione per livelli di attivazione elevati tra gli output di neuroni calcolati nei diversi mapping. Per definire la normalizzazione tra mapping, è necessario che la prima coordinata sia superiore a uno e non sia superiore al numero di mapping e che il resto delle coordinate abbia valore 1.  

Poiché le aggregazioni di normalizzazione delle risposte applicano una funzione predefinita ai valori del nodo di origine per determinare il valore del nodo di destinazione, non hanno stati sottoponibili a training (pesi o distorsioni).

**Alert**: i nodi del livello di destinazione corrispondono ai neuroni che costituiscono i nodi centrali dei kernel. Ad esempio, se KernelShape[d] è dispari, KernelShape[d]/2 corrisponderà al nodo centrale del kernel. Se KernelShape[d] è pari, il nodo centrale si trova su KernelShape[d]/2 - 1. Pertanto, se **Padding**[d] è False, il primo e l'ultimo nodo KernelShape[d]/2 non dispongono di nodi corrispondenti nel livello di destinazione. Per evitare questa situazione, definire **Padding** come [true, true, …, true].

Oltre ai quattro attributi descritti precedentemente, le aggregazioni di normalizzazione delle risposte supportano anche gli attributi seguenti:

-	**Alpha**: (obbligatorio) specifica un valore a virgola mobile che corrisponde a ***α*** nella formula precedente. 
-	**Beta**: (obbligatorio) specifica un valore a virgola mobile che corrisponde a ***β*** nella formula precedente. 
-	**Offset** (facoltativo) specifica un valore a virgola mobile che corrisponde a ***k*** nella formula precedente. Assume il valore predefinito 1.  

L'esempio seguente definisce un'aggregazione di normalizzazione delle risposte usando questi attributi:

	hidden RN1 [5, 10, 10]
	  from P1 response norm {
	    InputShape  = [ 5, 12, 12];
	    KernelShape = [ 1,  3,  3];
	    Alpha = 0.001;
	    Beta = 0.75;
	  }  

-	Il livello di origine include cinque mapping, ognuno con una dimensione di 12x12, per un totale di 1440 nodi. 
-	Il valore di **KernelShape** indica che si tratta dello stesso livello di normalizzazione di mapping, in cui l'intorno è un rettangolo 3x3. 
-	Il valore predefinito di **Padding** è False, quindi il livello di destinazione ha solo 10 nodi in ogni dimensione. Per includere un nodo nel livello di destinazione corrispondente a ogni nodo nel livello di origine, aggiungere Padding = [true, true, true]; e modificare le dimensioni di RN1 su [5, 12, 12].  

##Dichiarazione delle condivisioni 
Net# supporta facoltativamente la definizione di più aggregazioni con pesi condivisi. I pesi di qualsiasi aggregazione possono essere condivisi se le loro strutture sono uguali. La sintassi seguente definisce le aggregazioni con i pesi condivisi:

	share-declaration:
	    share    {    layer-list    }
	    share    {    bundle-list    }
	   share    {    bias-list    }
	
	layer-list:
	    layer-name    ,    layer-name
	    layer-list    ,    layer-name
	
	bundle-list:
	   bundle-spec    ,    bundle-spec
	    bundle-list    ,    bundle-spec
	
	bundle-spec:
	   layer-name    =>     layer-name
	
	bias-list:
	    bias-spec    ,    bias-spec
	    bias-list    ,    bias-spec
	
	bias-spec:
	    1    =>    layer-name
	
	layer-name:
	    identifier  

Ad esempio, la dichiarazione di condivisioni seguente specifica i nomi dei livelli, indicando che devono essere condivisi sia i pesi che le distorsioni:

	Const {
	  InputSize = 37;
	  HiddenSize = 50;
	}
	input {
	  Data1 [InputSize];
	  Data2 [InputSize];
	}
	hidden {
	  H1 [HiddenSize] from Data1 all;
	  H2 [HiddenSize] from Data2 all;
	}
	output Result [2] {
	  from H1 all;
	  from H2 all;
	}
	share { H1, H2 } // share both weights and biases  

-	Le funzionalità di input sono partizionate in due livelli di input di dimensioni identiche. 
-	I livelli nascosti calcolano quindi le funzionalità di livello superiore nei due livelli di input. 
-	La dichiarazione delle condivisione specifica che H1 e H2 devono essere calcolati in modo analogo ai rispettivi input.  
 
In alternativa, è possibile specificare questo concetto con due dichiarazioni delle condivisioni separate, come indicato di seguito:

	share { Data1 => H1, Data2 => H2 } // share weights  

<!-- -->

	share { 1 => H1, 1 => H2 } // share biases  

È possibile usare la forma breve solo se i livelli contengono una singola aggregazione. In genere, la condivisione è possibile solo se la struttura rilevante è identica, ovvero se le dimensioni, la geometria convoluzionale e così via sono identiche.

##Esempi di utilizzo di Net#
Questa sezione offre alcuni esempi di utilizzo di Net# per aggiungere livelli nascosti, definire il modo in cui i livelli nascosti interagiscono con altri livelli e creare reti convoluzionali.

###Definire una semplice rete neurale personalizzata, ad esempio "Hello World"
Questo esempio illustra come creare un modello di rete neurale con un singolo livello nascosto.

	input Data [100];
	hidden H [200] from Data all;
	output Out [10] sigmoid from H all;  

L'esempio illustra alcuni comandi di base come indicato di seguito:

-	La prima riga definisce il livello di input, denominato Data, che include 100 nodi, ognuno dei quali rappresenta una funzionalità negli esempi di input.
-	La seconda riga crea il livello nascosto. Il nome H è assegnato al livello nascosto, che include 200 nodi. Questo livello è completamente connesso al livello di input.
-	La terza riga definisce il livello di output (denominato O) e contiene 10 nodi di output. Per le reti neurali di classificazione è disponibile un nodo di output per ogni classe. La parola chiave **sigmoid** indica la funzione di output applicata al livello di output.   

###Definire più livelli nascosti: esempio obiettivo computer
L'esempio seguente illustra come definire una rete neurale leggermente più complessa, con più livelli nascosti personalizzati.

	// Define the input layers 
	input Pixels [10, 20];
	input MetaData [7];
	
	// Define the first two hidden layers, using data only from the Pixels input
	hidden ByRow [10, 12] from Pixels where (s,d) => s[0] == d[0];
	hidden ByCol [5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;
	
	// Define the third hidden layer, which uses as source the hidden layers ByRow and ByCol
	hidden Gather [100] 
	{
	  from ByRow all;
	  from ByCol all;
	}
	
	// Define the output layer and its sources
	output Result [10]  
	{
	  from Gather all;
	  from MetaData all;
	}  

Questo esempio descrive diverse funzionalità del linguaggio di specifica delle reti neurali:

-	La struttura ha due livelli di input, Pixels e MetaData.
-	Il livello Pixels è un livello di origine per due aggregazioni di connessioni, con livelli di destinazione ByRow e ByCol.
-	I livelli Gather e Result sono livelli di destinazione in più aggregazioni di connessioni.
-	Il livello di output, Result, è un livello di destinazione in due aggregazioni di connessione. Il livello di destinazione di una delle aggregazioni è il secondo livello nascosto (Gather) e il livello di destinazione dell'altra aggregazione è il livello di input MetaData.
-	I livelli nascosti, ByRow e ByCol, specificano connettività filtrata usando espressioni del predicato. Più precisamente, il nodo in ByRow su [x, y] è connesso ai nodi in Pixels con la prima coordinata indice uguale alla prima coordinata del nodo, x. Analogamente, il nodo in ByCol su [x, y] è connesso ai nodi in Pixels con la seconda coordinata indice all'interno della seconda coordinata del nodo, y.  

###Definire una rete per la classificazione multiclasse convoluzionale: esempio di riconoscimento cifra
La definizione della rete seguente è progettata per riconoscere numeri e illustra alcune tecniche avanzate per la personalizzazione di una rete neurale.

	input Image [29, 29];
	hidden Conv1 [5, 13, 13] from Image convolve 
	{
	   InputShape  = [29, 29];
	   KernelShape = [ 5,  5];
	   Stride      = [ 2,  2];
	   MapCount    = 5;
	}
	hidden Conv2 [50, 5, 5]
	from Conv1 convolve 
	{
	   InputShape  = [ 5, 13, 13];
	   KernelShape = [ 1,  5,  5];
	   Stride      = [ 1,  2,  2];
	   Sharing     = [false, true, true];
	   MapCount    = 10;
	}
	hidden Hid3 [100] from Conv2 all;
	output Digit [10] from Hid3 all;  


-	La struttura ha un singolo livello di input, Image.
-	La parola chiave **convolve** indica che Conv1 e Conv2 sono livelli convoluzionali. Tutte queste dichiarazioni di livelli sono seguite da un elenco di attributi convoluzionali.
-	La rete ha un terzo livello nascosto, Hid3, che è completamente connesso al secondo livello nascosto, Conv2.
-	Il livello di output, Digit, è connesso solo al terzo livello nascosto, Hid3. La parola chiave **all** indica che il livello di output è completamente connesso a Hid3.
-	L'aretà della convoluzione è uguale a tre, ovvero alla lunghezza delle tuple **InputShape**, **KernelShape**, **Stride** e **Sharing**. 
-	Il numero di pesi per kernel è 1 + **KernelShape**[0] * **KernelShape**[1] * **KernelShape**[2] = 1 + 1 * 5 * 5 = 26. Oppure 26 * 50 = 1300.
-	È possibile calcolare i nodi in ogni livello nascosto come indicato di seguito:
	-	**NodeCount**[0] = (5 - 1) / 1 + 1 = 5.
	-	**NodeCount**[1] = (13 - 5) / 2 + 1 = 5. 
	-	**NodeCount**[2] = (13 - 5) / 2 + 1 = 5. 
-	Il numero totale di nodi può essere calcolato usando la dimensionalità dichiarata del livello, [50, 5, 5], come indicato di seguito: **MapCount** * **NodeCount**[0] * **NodeCount**[1] * **NodeCount**[2] = 10 * 5 * 5 * 5
-	Poiché **Sharing**[d] è impostata su False solo per d == 0, il numero di kernel è **MapCount** * **NodeCount**[0] = 10 * 5 = 50. 

[1]: ./media/machine-learning-azure-ml-netsharp-reference-guide/formula_large.gif

<!---HONumber=58--> 