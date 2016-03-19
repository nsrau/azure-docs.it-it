<properties
   pageTitle="Distribuzione di un campionatore JUnit per JMeter per il test delle prestazioni di Elasticsearch | Microsoft Azure"
   description="Procedura: usare un campionatore JUnit per generare e caricare dati in un cluster Elasticsearch."
   services=""
   documentationCenter="na"
   authors="mabsimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/18/2016"
   ms.author="masimms"/>
   
# Distribuire un campionatore JUnit per JMeter per il test delle prestazioni di Elasticsearch

Questo articolo fa [parte di una serie](guidance-elasticsearch.md).

Questo documento descrive come creare e usare un campionatore JUnit in grado di generare e caricare i dati in un cluster Elasticsearch come parte di un piano di test di JMeter. Ciò garantisce un approccio estremamente flessibile per il test di carico, in grado di generare grandi quantità di dati di test senza dipendere da file di dati esterni.

> [AZURE.NOTE] I test di carico usati per valutare le prestazioni dell'operazione di inserimento dati descritta nel documento relativo all'ottimizzazione delle prestazioni di inserimento dati con Elasticsearch in Azure sono stati costruiti usando questo approccio. In tale documento sono descritti i dettagli del codice JUnit.

Per il test delle prestazioni di inserimento dei dati, il codice JUnit è stato sviluppato usando Eclipse (Mars) e le dipendenze sono state risolte tramite Maven. Le procedure seguenti descrivono la procedura dettagliata per l'installazione di Eclipse, la configurazione di Maven, la creazione di un test JUnit e la distribuzione del test come campionatore di richiesta JUnit in un test di JMeter.

> [AZURE.NOTE] Per informazioni dettagliate sulla struttura e sulla configurazione dell'ambiente di test, vedere l'articolo relativo alla [creazione di un ambiente di test delle prestazioni di Elasticsearch in Azure][].

## Prerequisiti di installazione

È necessario installare [Java Runtime Environment](http://www.java.com/en/download/ie_manual.jsp) nel computer di sviluppo. È inoltre necessario installare [Eclipse IDE for Java EE Developers](https://www.eclipse.org/downloads/index.php?show_instructions=TRUE).

> [AZURE.NOTE] Se come ambiente di sviluppo si usa la VM Master di JMeter descritta nell'articolo relativo alla [creazione di un ambiente di test delle prestazioni di Elasticsearch in Azure][], scaricare la versione per Windows a 32 bit del programma di installazione di Eclipse.

## Creazione di un progetto di test JUnit per il test di carico di Elasticsearch

Avviare l'ambiente Eclipse IDE se non è già in esecuzione, quindi chiudere la pagina *iniziale*. Nel menu *File* fare clic su *New* e quindi su *Java Project*.

![](./media/guidance-elasticsearch/jmeter-deploy7.png)

Nella finestra *New Java Project* immettere un nome di progetto, selezionare *Use default JRE* e quindi fare clic su *Finish*.

![](./media/guidance-elasticsearch/jmeter-deploy8.png)

Nella finestra *Package Explorer* espandere il nodo denominato come il progetto. Verificare che contenga una cartella denominata *src* e un riferimento all'ambiente JRE specificato.

![](./media/guidance-elasticsearch/jmeter-deploy9.png)

Fare clic con il pulsante destro del mouse sulla cartella *src*, fare clic su *New* e quindi su *JUnit Test Case*.

![](./media/guidance-elasticsearch/jmeter-deploy10.png)

Nella finestra *New JUnit Test Case* selezionare *New Junit 4 test*, immettere un nome per il pacchetto (può essere lo stesso nome del progetto, ma per convenzione deve iniziare con una lettera minuscola) e un nome per la classe di test, quindi selezionare le opzioni che generano gli stub di metodo richiesti per il test. Lasciare vuota la casella *Class under test* e quindi fare clic su *Finish*.

![](./media/guidance-elasticsearch/jmeter-deploy11.png)

Se viene visualizzata la finestra di dialogo *New JUnit Test Case* seguente, specificare l'opzione per aggiungere la libreria JUnit 4 al percorso di compilazione e quindi fare clic su *OK*.

![](./media/guidance-elasticsearch/jmeter-deploy12.png)

Verificare che venga generato lo scheletro di codice per il test JUnit e che venga visualizzato nella finestra dell'editor Java.

![](./media/guidance-elasticsearch/jmeter-deploy13.png)

In *Package Explorer* fare clic con il pulsante destro del mouse sul nodo del progetto, fare clic su *Configure* e quindi su *Convert to Maven Project*.

> [AZURE.NOTE] Maven consente di gestire più facilmente le dipendenze esterne, ad esempio le librerie client Java per Elasticsearch dalle quali dipende un progetto.

![](./media/guidance-elasticsearch/jmeter-deploy14.png)

Nella finestra di dialogo *Create new POM*, nell'elenco a discesa *Packaging*, selezionare *jar* e quindi fare clic su *Finish*.

![](./media/guidance-elasticsearch/jmeter-deploy15.png)

Nel riquadro sotto l'editor POM è possibile che venga visualizzato l'avviso *Build path specifies execution environment J2SE-1.5. Nell'area di lavoro non sono installati JRE strettamente compatibili con questo ambiente*, a seconda della versione di Java installata nel computer di sviluppo. Se è presente una versione di Java successiva alla versione 1.5, ignorare questo avviso.

![](./media/guidance-elasticsearch/jmeter-deploy16.png)

Nell'editor POM espandere *Properties* e quindi fare clic su *Create*.

![](./media/guidance-elasticsearch/jmeter-deploy17.png)

Nella finestra di dialogo *Add Property* digitare *es.version* nella casella *Name* e *1.7.2* nella casella *Value* e quindi fare clic su *OK*. Questa è la versione della libreria client Java per Elasticsearch da usare. Questa versione potrebbe essere sostituita in futuro, pertanto se si definisce la versione come proprietà POM e si fa riferimento a questa proprietà in un altro punto del progetto, sarà possibile modificare rapidamente la versione.

![](./media/guidance-elasticsearch/jmeter-deploy18.png)

Fare clic sulla scheda *Dependencies* alla base dell'editor POM e quindi fare clic su *Add* accanto alla casella di riepilogo *Dependencies*.

![](./media/guidance-elasticsearch/jmeter-deploy19.png)

Nella finestra di dialogo *Select Dependency* digitare *org.elasticsearch* nella casella *Group Id*, *elasticsearch* nella casella *Artifact Id* e *\\${es.version}* nella casella *Version* e quindi fare clic su *OK*. Le informazioni sulla libreria client Java per Elasticsearch sono conservate nell'archivio centrale Maven online e questa configurazione scaricherà automaticamente la libreria e le relative dipendenze al momento della compilazione del progetto.

![](./media/guidance-elasticsearch/jmeter-deploy20.png)

Scegliere *Save All* dal menu *File*. Questa azione consentirà di salvare e compilare il progetto, scaricando le dipendenze specificate da Maven. Verificare che la cartella *Maven Dependencies* venga visualizzata in Package Explorer. Espandere questa cartella per visualizzare i file con estensione jar scaricati per supportare la libreria client Java per Elasticsearch.

![](./media/guidance-elasticsearch/jmeter-deploy21.png)

## Importazione di un progetto di test JUnit esistente in Eclipse

Per questa procedura si presuppone che sia stato scaricato un progetto Maven creato in precedenza tramite Eclipse.

Avviare l'ambiente Eclipse IDE. Scegliere *Import* dal menu *File*.

![](./media/guidance-elasticsearch/jmeter-deploy22.png)

Nella finestra di dialogo *Select* espandere la cartella *Maven*, fare clic su *Existing Maven Projects* e quindi su *Next*.

![](./media/guidance-elasticsearch/jmeter-deploy23.png)

Nella finestra *Maven Projects* specificare la cartella che contiene il progetto (la cartella contenente il file POM.XML), fare clic su *Select All* e quindi su *Finish*.

![](./media/guidance-elasticsearch/jmeter-deploy24.png)

Nella finestra *Package Explorer* espandere il nodo corrispondente al progetto. Verificare che il progetto contenga una cartella denominata *src*. Questa cartella contiene il codice sorgente per il test JUnit. Per compilare e distribuire il progetto, seguire le istruzioni seguenti.

![](./media/guidance-elasticsearch/jmeter-deploy25.png)

## Distribuzione di un test JUnit in JMeter

Per questo progetto si presuppone che sia stato creato un progetto denominato LoadTest contenente una classe di test JUnit denominata `BulkLoadTest.java` che accetta i parametri di configurazione passati come stringa singola a un costruttore (si tratta del meccanismo previsto da JMeter).

Nell'ambiente Eclipse IDE, in Package Explorer, fare clic con il pulsante destro del mouse sul nodo del progetto e quindi fare clic su *Export*.

![](./media/guidance-elasticsearch/jmeter-deploy26.png)

In *Export Wizard*, nella pagina *Select*, espandere il nodo *Java*, fare clic su *JAR file* e quindi su *Next*.

![](./media/guidance-elasticsearch/jmeter-deploy27.png)

Nella pagina *JAR File Specification*, nella casella *Select the resources to export*, espandere il progetto da esportare e deselezionare tutte le opzioni eccetto la cartella *src*, quindi deselezionare *.classpath*, *.project* e *pom.xml*. Nella casella *JAR file* specificare il nome del file e il percorso del file con estensione jar (è consigliabile assegnare al file l'estensione jar) e quindi fare clic su *Finish*.

![](./media/guidance-elasticsearch/jmeter-deploy28.png)

Usando Esplora risorse, copiare il file con estensione jar appena creato nella macchina virtuale Master di JMeter e salvarlo nella cartella *apache-jmeter-2.13\\lib\\junit* all'interno della cartella in cui è stato installato JMeter. Per altre informazioni, vedere la procedura relativa alla [creazione della macchina virtuale Master di JMeter](guidance-elasticsearch-creating-performance-testing-environment.md#creating-the-jmeter-master-virtual-machine).

Tornare a Eclipse, espandere la finestra Package Explorer e prendere nota di tutti i file con estensione jar e dei relativi percorsi elencati nella cartella *Maven Dependencies* del progetto. Si noti che i file visualizzati nell'immagine seguente potrebbero variare, a seconda della versione di Elasticsearch in uso:

![](./media/guidance-elasticsearch/jmeter-deploy29.png)

Usando Esplora risorse, copiare i file con estensione jar a cui si fa riferimento nella cartella Maven Dependencies nella cartella *apache-jmeter-2.13\\lib\\junit* all'interno della VM Master di JMeter.

Se la cartella *lib\\junit* contiene già le versioni precedenti dei file con estensione jar, rimuoverle. Se vengono lasciate nella cartella, il test JUnit potrebbe non funzionare in quanto i riferimenti vengono risolti nei file con estensione jar errati.

Nella VM Master di JMeter arrestare JMeter, se è in esecuzione. Avviare JMeter. In JMeter fare clic con il pulsante destro del mouse su *Test Plan*, fare clic su *Add*, quindi su *Threads (Users)* e infine su *Thread Group*.

![](./media/guidance-elasticsearch/jmeter-deploy30.png)

Nel nodo *Test Plan* fare clic con il pulsante destro del mouse su *Thread-Group*, su *Add*, quindi su *Sampler* e infine su *JUnit Request*.

![](./media/guidance-elasticsearch/jmeter-deploy31.png)

Nella pagina *JUnit Request* selezionare *Search for JUnit4 annotations (instead of JUnit 3)*. Nell'elenco a discesa *Classname* selezionare la classe di test di carico JUnit (verrà elencata nel formato *&lt;package&gt;.&lt;class&gt;*), nell'elenco a discesa *Test Method* selezionare il metodo di test JUnit (si tratta del metodo che esegue effettivamente le operazioni associate al test e deve essere contrassegnato con l'annotazione *@test* nel progetto Eclipse) e immettere i valori da passare al costruttore nella casella *Constructor String Label*. I dettagli mostrati nell'immagine seguente sono solo esempi, pertanto i valori in *Classname*, *Test Method* e *Constructor String Label* saranno probabilmente diversi da quelli visualizzati.

![](./media/guidance-elasticsearch/jmeter-deploy32.png)

Se la classe non viene visualizzata nell'elenco a discesa *Classname*, probabilmente il file con estensione jar non è stato esportato correttamente o non è stato inserito nella cartella *lib\\junit* oppure nella cartella *lib\\junit* mancano alcuni file con estensione jar dipendenti. In questo caso, esportare nuovamente il progetto da Eclipse e assicurarsi di aver selezionato la risorsa *src*, copiare il file con estensione jar nella cartella *lib\\junit* e quindi verificare di aver copiato tutti i file con estensione jar dipendenti elencati da Maven nella cartella *lib*.

Chiudere JMeter. Non è necessario salvare il piano di test. Copiare il file con estensione jar contenente la classe di test JUnit nella cartella */home/&lt;username&gt;/apache-jmeter-2.13/lib/junit* in ciascuna delle VM subordinate di JMeter (*&lt;username&gt;* è il nome dell'utente amministratore specificato durante la creazione della VM). Per altre informazioni, vedere l'articolo relativo alla [creazione di macchine virtuali subordinate di JMeter](guidance-elasticsearch-creating-performance-testing-environment.md#creating-the-jmeter-subordinate-virtual-machines).

Copiare i file con estensione jar dipendenti necessari per la classe di test JUnit nella cartella `/home/[username]/apache-jmeter-2.13/lib/junit` in ciascuna delle VM subordinate di JMeter. Assicurarsi prima di rimuovere le versioni precedenti dei file con estensione jar da questa cartella.

È possibile usare l'utilità `pscp` per copiare file da un computer Windows in Linux.

[creazione di un ambiente di test delle prestazioni di Elasticsearch in Azure]: guidance-elasticsearch-creating-performance-testing-environment.md

<!---HONumber=AcomDC_0224_2016-->