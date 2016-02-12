<properties
   pageTitle="Usare componenti Python in una topologia Storm in HDinsight | Microsoft Azure"
   description="Informazioni su come usare i componenti Python con Apache Storm in Azure HDInsight. Si apprenderà come usare i componenti Python da una topologia Storm basata su Java e su Clojure."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="python"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/01/2016"
   ms.author="larryfr"/>

#Sviluppare topologie Apache Storm con Python in HDInsight

Apache Storm supporta più linguaggi, consentendo di combinare in un'unica topologia componenti in più lingue. In questo documento si apprenderà come usare i componenti Python nelle topologie Storm basate su Clojure e su Java in HDInsight.

##Prerequisiti

* Python 2.7 o versione successiva

* Java JDK 1.7 o versione successiva

* [Leiningen](http://leiningen.org/)

##Supporto per più linguaggi in Storm

Storm è stato progettato per funzionare con componenti scritti in qualsiasi linguaggio di programmazione, ma ciò richiede che i componenti riconoscano come interagire con la [definizione di Thrift per Storm](https://github.com/apache/storm/blob/master/storm-core/src/storm.thrift). Per Python viene fornito un modulo, come parte del progetto Apache Storm, che consente di interfacciarsi facilmente con Storm. Questo modulo è disponibile all'indirizzo [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py).

Poiché Apache Storm è un processo Java eseguito in Java Virtual Machine (JVM), i componenti scritti in altri linguaggi vengono eseguiti come sottoprocessi. I bit di Storm eseguiti nella JVM comunicano con questi sottoprocessi usando messaggi JSON inviati tramite stdin/stdout. Altri dettagli sulla comunicazione tra i componenti sono disponibili nella documentazione relativa al [protocollo Multi-lang](https://storm.apache.org/documentation/Multilang-protocol.html).

###Modulo Storm

Il modulo storm (https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py,) include i bit necessari per creare i componenti Python che funzionano con Storm.

Fornisce elementi come `storm.emit` per l'emissione di tuple e `storm.logInfo` per la scrittura nei log. È consigliabile leggere l'intero contenuto di questo file per comprendere quali elementi fornisce.

##Sfide

Con il modulo __storm.py__ è possibile creare spout Python che utilizzano i dati e bolt che li elaborano. Tuttavia, la definizione complessiva della topologia Storm che fornisce il collegamento per la comunicazione tra i componenti è comunque scritta con Java o Clojure. Se si usa Java, è necessario creare anche i componenti Java che agiscono come interfaccia per i componenti Python.

Poiché inoltre i cluster Storm vengono eseguiti in modalità distribuita, è necessario assicurarsi che tutti i moduli richiesti dai componenti Python siano disponibili in tutti i nodi di lavoro del cluster. Storm non offre un modo semplice per eseguire questa operazione per le risorse in più linguaggi, quindi è necessario includere tutte le dipendenze come parte del file JAR per la topologia oppure installare manualmente le dipendenze in ogni nodo di lavoro del cluster.

Sono disponibili alcuni progetti che provano a superare questi aspetti negativi, ad esempio [Pyleus](https://github.com/Yelp/pyleus) e [Streamparse](https://github.com/Parsely/streamparse). Mentre entrambi possono essere eseguiti in cluster HDInsight basati su Linux, non costituiscono l'argomento principale di questo documento, perché richiedono una personalizzazione durante la configurazione del cluster e non sono completamente testati nei cluster HDInsight. Alla fine di questo documento sono presenti note sull'uso di questi framework con HDInsight.

###Definizione della topologia Java rispetto a Clojure

Dei due metodi per la definizione di una topologia, Clojure è di gran lunga il più facile e chiaro, perché consente di fare riferimento direttamente ai componenti Python nella definizione della topologia. Per le definizioni della topologia basata su Java, è necessario specificare anche i componenti Java che gestiscono elementi come la dichiarazione dei campi nelle tuple restituite dai componenti Python.

Entrambi i metodi sono descritti in questo documento insieme ai progetti di esempio.

##Componenti Python con una topologia Java

> [AZURE.NOTE] Questo esempio è disponibile all'indirizzo [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount) nella directory __JavaTopology__. Si tratta di un progetto basato su Maven. Se non si ha familiarità con Maven, vedere [Sviluppare topologie basate su Java con Apache Storm in HDInsight](hdinsight-storm-develop-java-topology.md) per altre informazioni sulla creazione di un progetto Maven per una topologia Storm.

Una topologia basata su Java che usa Python, o altri componenti del linguaggio JVM, inizialmente sembra che usi i componenti Java, ma osservando ognuno degli spout o dei bolt Java, si noterà codice simile al seguente:

    public SplitBolt() {
        super("python", "countbolt.py");
    }

È a questo punto che Java richiama Python ed esegue lo script che contiene la logica dei bolt effettiva. Gli spout o i bolt Java, in questo esempio, dichiarano semplicemente i campi nella tupla che sarà generata dal componente Python sottostante.

In questo esempio i file Python effettivi sono archiviati nella directory `/multilang/resources`. Alla directory `/multilang` viene fatto riferimento nel file __pom.xml__:

<resources> <resource> <!-- Where the Python bits are kept --> <directory>${basedir}/multilang</directory> </resource> </resources>

Include tutti i file presenti nella cartella `/multilang` nel file JAR che sarà compilato da questo progetto.

> [AZURE.IMPORTANT] Tenere presente che viene specificata solo la directory `/multilang`, non `/multilang/resources`. Storm prevede che le risorse non JVM siano inserite in una directory `resources`, quindi ne viene già effettuata la ricerca internamente. L'inserimento di componenti in questa cartella consente di farvi riferimento per nome nel codice Java. Ad esempio, `super("python", "countbolt.py");`. Da un altro punto di vista, Storm considera la directory `resources` come radice (/) quando accede alle risorse in più linguaggi.
>
> Per questo progetto di esempio il modulo `storm.py` è incluso nella directory `/multilang/resources`.

###Compilare ed eseguire il progetto

Per eseguire il progetto localmente, usare semplicemente il comando Maven seguente per compilarlo ed eseguirlo in modalità locale:

    mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCount

Per terminare il processo, usare CTRL+C.

Per distribuire il progetto in un cluster HDInsight che esegue Apache Storm, seguire questa procedura:

1. Compilare un file uberjar:

        mvn package

    Verrà creato un file denominato __WordCount--1.0-SNAPSHOT.jar__ nella directory `/target` di questo progetto.

2. Caricare il file JAR nel cluster Hadoop usando uno dei metodi seguenti:

    * Per i cluster HDInsight __basati su Linux__: Usare `scp WordCount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:WordCount-1.0-SNAPSHOT.jar` per copiare il file JAR nel cluster, sostituendo USERNAME con il nome utente SSH e CLUSTERNAME con il nome del cluster HDInsight.

        Una volta che il file è stato caricato, connettersi al cluster tramite SSH e avviare la topologia con `storm jar WordCount-1.0-SNAPSHOT.jar com.microsoft.example.WordCount wordcount`

    * Per i cluster HDInsight __basati su Windows__: Connettersi al Dashboard di Storm passando a HTTPS://CLUSTERNAME.azurehdinsight.net/ nel browser. Sostituire CLUSTERNAME con il nome del cluster HDInsight e fornire il nome e la password dell'amministratore, quando richiesto.

        Usando il modulo, eseguire le azioni seguenti:

        * __Jar File__: selezionare __Browse__,quindi il file __WordCount-1.0-SNAPSHOT.jar__.
        * __Class Name__: immettere `com.microsoft.example.WordCount`.
        * __Additional Paramters__: immettere un nome descrittivo, ad esempio `wordcount`, per identificare la topologia.

        Infine selezionare __Submit__ per avviare la topologia.

> [AZURE.NOTE] Una volta avviata una topologia Storm, questa rimane in esecuzione finché non viene arrestata (terminata). Per arrestare la topologia, usare il comando `storm kill TOPOLOGYNAME` dalla riga di comando, ad esempio una sessione SSH con un cluster Linux, oppure usare l'interfaccia utente di Storm, selezionare la topologia e quindi fare clic sul pulsante __Kill__.

##Componenti Python con una topologia Clojure

> [AZURE.NOTE] Questo esempio è disponibile all'indirizzo [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount) nella directory __ClojureTopology__.

Questa topologia è stata create usando [Leiningen](http://leiningen.org) per [creare un nuovo progetto Clojure](https://github.com/technomancy/leiningen/blob/stable/doc/TUTORIAL.md#creating-a-project). Successivamente, sono state apportate le modifiche seguenti al progetto con scaffolding:

* __project.clj__: sono state aggiunte dipendenze per Storm ed esclusioni per gli elementi che potrebbero causare un problema durante la distribuzione nel server HDInsight.
* __resources/resources__: Leiningen crea una directory `resources` predefinita, tuttavia i file aggiunti qui appaiono come aggiunti alla radice del file JAR creato da questo progetto, mentre Storm prevede la presenza dei file in una sottodirectory denominata `resources`. È stata quindi aggiunta una sottodirectory e i file Python vengono archiviati in `resources/resources`. In fase di esecuzione sarà considerata come la radice (/) per l'accesso ai componenti di Python.
* __src/wordcount/core.clj__: questo file, a cui fa riferimento il file __project.clj__, contiene la definizione della topologia. Per altre informazioni sull'uso di Clojure per definire una topologia Storm, vedere [Clojure DSL](https://storm.apache.org/documentation/Clojure-DSL.html).

###Compilare ed eseguire il progetto

__Per compilare ed eseguire il progetto localmente__, usare il comando seguente:

    lein do clean, run

Per arrestare la topologia, usare __CTRL+C__.

__Per compilare un file uberjar e distribuirlo in HDInsight__, seguire questa procedura:

1. Creare un file uberjar contenente la topologia e le dipendenze necessarie:

        lein uberjar

    Verrà creato un nuovo file denominato `wordcount-1.0-SNAPSHOT.jar` nella directory `target\uberjar+uberjar`.
    
2. Usare uno dei metodi seguenti per distribuire ed eseguire la topologia in un cluster HDInsight:

    * __HDInsight basato su Linux__
    
        1. Copiare il file nel nodo head del cluster HDInsight tramite `scp`. Ad esempio:
        
                scp wordcount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:wordcount-1.0-SNAPSHOT.jar
                
            Sostituire USERNAME con un utente SSH per il cluster e CLUSTERNAME con il nome del cluster HDInsight.
            
        2. Dopo aver copiato il file nel cluster, usare SSH per connettersi al cluster e inviare il processo. Per informazioni sull'uso di SSH con HDInsight, vedere uno degli argomenti seguenti:
        
            * [Usare SSH con HDInsight basato su Linux da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
            * [Usare SSH con HDInsight basato su Linux da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
            
        3. Dopo la connessione, usare il comando seguente per avviare la topologia:
        
                storm jar wordcount-1.0-SNAPSHOT.jar wordcount.core wordcount
    
    * __HDInsight basato su Windows__
    
        1. Connettersi al Dashboard di Storm passando a HTTPS://CLUSTERNAME.azurehdinsight.net/ nel browser. Sostituire CLUSTERNAME con il nome del cluster HDInsight e fornire il nome e la password dell'amministratore, quando richiesto.

        2. Usando il modulo, eseguire le azioni seguenti:

            * __Jar File__: selezionare __Browse__,quindi il file __wordcount-1.0-SNAPSHOT.jar__.
            * __Class Name__: immettere `wordcount.core`.
            * __Additional Paramters__: immettere un nome descrittivo, ad esempio `wordcount`, per identificare la topologia.

            Infine selezionare __Submit__ per avviare la topologia.

> [AZURE.NOTE] Una volta avviata una topologia Storm, questa rimane in esecuzione finché non viene arrestata (terminata). Per arrestare la topologia, usare il comando `storm kill TOPOLOGYNAME` dalla riga di comando, in una sessione SSH con un cluster Linux, oppure usare l'interfaccia utente di Storm, selezionare la topologia e quindi fare clic sul pulsante __Kill__.

##Framework Pyleus

[Pyleus](https://github.com/Yelp/pyleus) è un framework il cui scopo è facilitare l'uso di Python con Storm offrendo gli elementi seguenti:

* __Definizioni della topologia basata su YAML__: offre un modo più semplice per definire la topologia, senza richiedere la conoscenza di Java o Clojure.
* __Serializzatore basato su MessagePack__: MessagePack viene usato come metodo di serializzazione predefinito, al posto di JSON e può velocizzare la messaggistica tra i componenti.
* __Gestione delle dipendenze__: Virtualenv viene usato per assicurare che le dipendenze di Python siano distribuite a tutti i nodi di lavoro. Ciò richiede l'installazione di Virtualenv nei nodi di lavoro.

> [AZURE.IMPORTANT] Pyleus richiede la disponibilità di Storm nell'ambiente di sviluppo. L'uso della distribuzione di Apache Storm 0.9.3 di base produce file JAR incompatibili con la versione di Storm fornita con HDInsight. La procedura seguente usa quindi il cluster HDInsight come ambiente di sviluppo.

Le topologie Pyleus di esempio possono essere compilate correttamente usando il nodo head di HDInsight come ambiente di compilazione:

1. Quando si effettua il provisioning di un nuovo progetto Storm in un cluster HDInsight, è necessario assicurarsi che Python Virtualenv sia presente nei nodi del cluster. Quando si crea un nuovo cluster HDInsight basato su Linux, usare le impostazioni dell'azione script con la [personalizzazione del cluster](hdinsight-hadoop-customize-cluster.md):

    * __Name__: fornire semplicemente un nome descrittivo.
    * \_\_ Script URI\_\_: usare `https://hditutorialdata.blob.core.windows.net/customizecluster/pythonvirtualenv.sh` come valore. Questo script installerà Python Virtualenv nei nodi.
    
        > [AZURE.NOTE] Creerà anche alcune directory che saranno usate dal framework Streamparse più avanti in questo documento.
        
    * __Nimbus__: selezionare questa voce in modo che lo script venga applicato ai nodi head di Nimbus.
    * __Supervisor__: selezionare questa voce in modo che lo script venga applicato ai nodi di lavoro del supervisore.
    
    Lasciare vuote le altre voci.

1. Una volta creato il cluster, connettersi tramite SSH:

    * [Usare SSH con HDInsight basato su Linux da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Usare SSH con HDInsight basato su Linux da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Dalla connessione SSH usare il codice seguente per creare un nuovo ambiente virtuale e installare Pyleus:

        virtualenv pyleus_venv
        source pyleus_venv
        pip install pyleus

3. Scaricare quindi il repository Git Pyleus e compilare l'esempio WordCount:

        sudo apt-get install git
        git clone https://github.com/Yelp/pyleus.git
        pyleus build pyleus/examples/word_count/pyleus_topology.yaml
    
    Una volta completata la compilazione, si avrà un nuovo file denominato `word_count.jar` nella directory corrente.
    
4. Per inviare la topologia al cluster Storm, usare il comando seguente:

        pyleus submit -n localhost word_count.jar
    
    Il parametro `-n` specifica l'host Nimbus. Poiché ci si trova nel nodo head, è possibile usare `localhost`.
    
    È anche possibile usare il comando `pyleus` per eseguire altre azioni Storm. Usare il comando seguente per elencare le topologie in esecuzione e quindi terminare la topologia `word_count`:
    
        pyleus list -n localhost
        pyleus kill -n localhost word_count

##Framework Streamparse

[Streamparse](https://github.com/Parsely/streamparse) è un framework il cui scopo è facilitare l'uso di Python con Storm offrendo gli elementi seguenti:

* __Scaffolding__: consente di creare facilmente lo scaffolding per un progetto e quindi di modificare i file per aggiungere la logica.
* __Funzioni di Clojure DSL__: riducono il livello di dettaglio relativo all'uso di componenti Python nella definizione di una topologia Clojure.
* __Gestione delle dipendenze__: Virtualenv viene usato per assicurare che le dipendenze di Python siano distribuite a tutti i nodi di lavoro. Ciò richiede l'installazione di Virtualenv nei nodi di lavoro.
* __Distribuzione remota__: Streamparse può usare l'automazione SSH per distribuire i componenti ai nodi di lavoro e crea un tunnel SSH per comunicare con Nimbus. Si può quindi eseguire facilmente la distribuzione dall'ambiente di sviluppo al cluster basato su Linux, ad esempio HDInsight.

> [AZURE.IMPORTANT] Streamparse è basato su componenti che prevedono [segnali Unix](https://en.wikipedia.org/wiki/Unix_signal), non disponibili in Windows. L'ambiente di sviluppo deve essere Linux, Unix o OS X e il cluster HDInsight deve essere basato su Linux.

1. Quando si effettua il provisioning di un nuovo progetto Storm in un cluster HDInsight, è necessario assicurarsi che Python Virtualenv sia presente nei nodi del cluster. Quando si crea un nuovo cluster HDInsight basato su Linux, usare le impostazioni dell'azione script con la [personalizzazione del cluster](hdinsight-hadoop-customize-cluster.md):

    * __Name__: fornire semplicemente un nome descrittivo.
    * \_\_ Script URI\_\_: usare `https://hditutorialdata.blob.core.windows.net/customizecluster/pythonvirtualenv.sh` come valore. Questo script installerà Python Virtualenv nei nodi e creerà le directory usate da Streamparse.
    * __Nimbus__: selezionare questa voce in modo che lo script venga applicato ai nodi head di Nimbus.
    * __Supervisor__: selezionare questa voce in modo che lo script venga applicato ai nodi di lavoro del supervisore.
    
    Lasciare vuote le altre voci.
    
    > [AZURE.WARNING] È anche necessario usare una __chiave pubblica__ per proteggere l'utente SSH per il cluster HDInsight, in modo da eseguire la distribuzione in remoto tramite Streamparse.
    >
    > Per altre informazioni sull'uso di SSH con HDInsight, vedere i documenti seguenti:
    >
    > * [Usare SSH con HDInsight basato su Linux da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
    > * [Usare SSH con HDInsight basato su Linux da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Durante il provisioning del cluster, installare Streamparse nell'ambiente di sviluppo usando il comando seguente:

        pip install streamparse
        
3. Una volta installato Streamparse, usare il comando seguente per creare il progetto di esempio:

        sparse quickstart wordcount
        
    Verrà creata una nuova directory denominata `wordcount`, che sarà popolata con un progetto Word Count di esempio.

4. Passare alla directory `wordcount` e avviare la topologia in modalità locale:

        cd wordcount
        sparse run

    Usare CTRL+C per arrestare la topologia.

###Distribuire la topologia

Dopo aver creato il cluster HDInsight basato su Linux, seguire questa procedura per distribuire la topologia nel cluster:

1. Usare il comando seguente per trovare i nomi di dominio completi dei nodi di lavoro per il cluster:

        curl -u admin:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts | grep '"host_name" : "worker'
    
    Verranno recuperate le informazioni sugli host per il cluster, verrà inviata la pipe a grep e saranno restituite le voci per i nodi di lavoro. I risultati visualizzati dovrebbero essere simili ai seguenti:
    
        "host_name" : "workernode0.1kft5e4nx2tevg5b2pdwxqx1fb.jx.internal.cloudapp.net"
    
    Salvare le informazioni `"workernode0.1kft5e4nx2tevg5b2pdwxqx1fb.jx.internal.cloudapp.net"` che saranno usate nel passaggio successivo.

2. Aprire il file __config.json__ nella directory `wordcount` e modificare le voci seguenti:

    * __user__: impostare questo valore sul nome dell'account utente SSH configurato per il cluster HDInsight. Verrà usato per l'autenticazione con il cluster durante la distribuzione del progetto.
    * __nimbus__: impostare su `CLUSTERNAME-ssh.azurehdinsight.net`. Sostituire CLUSTERNAME con il nome del cluster. Verrà usato per la comunicazione con il nodo Nimbus, ovvero il nodo head del cluster.
    * __workers__: popolare questa voce con i nomi host dei nodi di lavoro recuperati con curl. Ad esempio:
    
        ```
"workers": [
    "workernode0.1kft5e4nx2tevg5b2pdwxqx1fb.jx.internal.cloudapp.net",
    "workernode1.1kft5e4nx2tevg5b2pdwxqx1fb.jx.internal.cloudapp.net"
    ]
        ```
    
    * __virtualenv\_root__: impostare su "/virtualenv".
    
    Verrà configurato il progetto per il cluster HDInsight, inclusa la directory `/virtualenv` creata durante il provisioning tramite l'azione script.

4. Poiché la distribuzione di Streamparse in HDInsight richiede l'inoltro dell'autenticazione dell'utente tramite il nodo head ai nodi di lavoro, sarà necessario avviare `ssh-agent` sulla workstation. Per la maggior parte dei sistemi operativi, l'avvio avviene automaticamente. Usare il comando seguente per verificare che sia in esecuzione:

        echo "$SSH_AUTH_SOCK"
    
    Se `ssh-agent` è in esecuzione, verrà restituita una risposta simile alla seguente:
    
        /tmp/ssh-rfSUL1ldCldQ/agent.1792
    
    > [AZURE.NOTE] Il percorso completo potrebbe essere diverso, a seconda del sistema operativo in uso. Ad esempio, in OS X il percorso può essere simile a `/private/tmp/com.apple.launchd.vq2rfuxaso/Listeners`. Se l'agente è in esecuzione, dovrà comunque essere restituito un percorso.
    
    Se non viene restituita una risposta, usare il comando `ssh-agent` per avviare l'agente.
    
5. Verificare che l'agente riconosca la chiave che verrà usata dall'utente per autenticarsi con il server HDInsight. Usare il comando seguente per elencare le chiavi disponibili per l'agente:

        ssh-add -L
    
    Verranno restituire le chiavi private aggiunte per l'agente. È possibile confrontare i risultati con il contenuto della chiave privata generata al momento della creazione di un chiave SSH per autenticarsi con HDInsight.
    
    Se non vengono restituite informazioni o se le informazioni restituite non corrispondono alla chiave privata, usare il comando seguente per aggiungere la chiave privata all'agente:
    
        ssh-add /path/to/key/file
    
    Ad esempio, `ssh-add ~/.ssh/id_rsa`.

4. È anche necessario configurare SSH in modo che rilevi che per il cluster HDInsight dovrà essere usato l'inoltro. Aggiungere il codice seguente a `~/.ssh/config` Se il file non esiste, crearlo e usare il codice seguente come contenuto:

        Host *.azurehdinsight.net
          ForwardAgent yes
        
        Host *.internal.cloudapp.net
          ProxyCommand ssh CLUSTERNAME-ssh.azurehdinsight.net nc %h %p
    
    Sostituire CLUSTERNAME con il nome del cluster HDInsight:
    
    L'agente SSH verrà configurato nella workstation per abilitare l'inoltro delle credenziali SSH tramite qualsiasi sistema *.azurehdinsight.net a cui ci si connette. In questo caso, il nodo head del cluster. Verrà quindi configurato il comando usato per inoltrare il traffico SSH dal nodo head ai singoli nodi di lavoro (internal.cloudapp.net.) In questo modo Streamparse potrà connettesi al nodo head e da qui a ogni nodo di lavoro usando l'autenticazione con chiave per l'account SSH.
    
5. Usare infine il comando seguente per inviare la topologia dall'ambiente di sviluppo locale al cluster HDInsight:

        sparse submit
    
    Verrà stabilita la connessione al cluster HDInsight, verrà distribuita la topologia e le eventuali dipendenze di Python e quindi verrà avviata la topologia.

##Passaggi successivi

In questo documento si è appreso come usare i componenti Python da una topologia Storm. Vedere i documenti seguenti per altri modi di usare Python con HDInsight:

* [Come usare Python per il flusso di processi MapReduce](hdinsight-hadoop-streaming-python.md)
* [Come usare funzioni definite dall'utente Python in Pig e Hive](hdinsight-python.md)

<!---HONumber=AcomDC_0204_2016-->