<properties urlDisplayName="Cassandra with Linux" pageTitle="Eseguire Cassandra con Linux in Azure" metaKeywords="" description="Spiega come eseguire un cluster Cassandra in Linux in Macchine virtuali di Azure." metaCanonical="" services="virtual-machines" documentationCenter="nodejs" title="Esecuzione di Cassandra con Linux in Azure e accesso da Node.js" authors="hanuk" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="hanuk" />

# <span></span></a>Esecuzione di Cassandra con Linux in Azure e accesso da Node.js

**Autore:** Hanu Kommalapati

## Sommario

-   [Panoramica][Panoramica]
-   [Schema di distribuzione di Cassandra][Schema di distribuzione di Cassandra]
-   [Distribuzione composita][Distribuzione composita]
-   [Distribuzione di una macchina virtuale di Azure][Distribuzione di una macchina virtuale di Azure]
-   [Attività 1: Distribuire il cluster Linux][Attività 1: Distribuire il cluster Linux]
-   [Attività 2: Configurare Cassandra su ogni macchina virtuale][Attività 2: Configurare Cassandra su ogni macchina virtuale]
-   [Attività 3: Accedere al cluster Cassandra da Node.js][Attività 3: Accedere al cluster Cassandra da Node.js]
-   [Conclusioni][Conclusioni]

## <span id="overview"></span> </a>Panoramica

Azure offre un servizio di database NoSQL tramite l'archiviazione tabelle di Azure che consente l'archiviazione senza schema degli oggetti business. Questo servizio può essere usato da Node.JS, .NET, Java e qualsiasi altro linguaggio in grado di comunicare tramite HTTP e REST. Esistono tuttavia altri popolari database NoSQL, ad esempio Cassandra e Couchbase, che non possono essere eseguiti in Azure PaaS a causa del modello di servizio cloud senza stato. Macchine virtuali di Azure consente ora di eseguire questi database NoSQL in Azure senza modificare la codebase. Lo scopo di questo articolo è di illustrare l'esecuzione di un cluster Cassandra sulle macchine virtuali e l'accesso da Node.js. Non viene spiegata la distribuzione di Cassandra per le operazioni di produzione reali in cui è necessario esaminare il cluster Cassandra a più data center con le strategie associate di backup e ripristino. In questa esercitazione verranno usati la versione Ubuntu 12.04 di Linux e Cassandra 1.0.10. Il processo, tuttavia, può essere variato per qualsiasi distribuzione di Linux.

## <span id="schematic"></span> </a>Schema di distribuzione di Cassandra

La funzionalità Macchine virtuali di Azure consente di eseguire i database NoSQL, ad esempio [Cassandra][Cassandra], sul cloud pubblico Microsoft con la stessa facilità con cui vengono eseguiti in un ambiente cloud privato con la sola differenza della configurazione della rete virtuale specifica dell'infrastruttura di Macchine virtuali di Azure. Al momento della stesura di questo articolo, Cassandra non è disponibile come servizio gestito in Azure e pertanto verrà illustrata la configurazione di un cluster Cassandra in Macchine virtuali e l'accesso da un'altra istanza di Linux anch'essa ospitata in Macchine virtuali. I frammenti di codice node.js indicati possono essere usati anche dall'applicazione Web o dal servizio Web ospitato PaaS. Uno dei principali vantaggi di Azure è l'utilizzo del modello di applicazione composita che consente di usufruire al meglio degli ambienti PaaS e IaaS.

Esistono due modelli di distribuzione adatti all'ambiente dell'applicazione Cassandra: distribuzione di Macchine virtuali completa e distribuzione composita. In una distribuzione composita, un cluster Cassandra ospitato da Macchine virtuali verrà usato da un'applicazione Web (o servizio Web) di Azure ospitata PaaS usando l'interfaccia Thrift tramite il bilanciamento del carico. Anche se ogni nodo di Cassandra usa un proxy per delegare la richiesta ad altri nodi peer nell'eventualità di un errore dello spazio chiave, la funzionalità di bilanciamento del carico supporta il bilanciamento del carico semplice delle richieste. Il bilanciamento del carico crea inoltre una sandbox protetta dal firewall per un miglior controllo dei dati.

## <span id="composite"></span> </a>Distribuzione composita

L'obiettivo di una distribuzione composita è di ottimizzare l'utilizzo di PaaS in modo che le macchine virtuali occupino la minor superficie possibile per cercare di ridurre il sovraccarico imposto dalla gestione dell'infrastruttura delle macchine virtuali. A causa del sovraccarico in termini di gestione server, distribuire solo i componenti che richiedono un comportamento con stato che non può essere facilmente modificato per diversi motivi, inclusi i tempi di introduzione sul mercato, la mancanza di visibilità sul codice sorgente e l'accesso di basso livello al sistema operativo.

![Diagramma di distribuzione composita][Diagramma di distribuzione composita]

## <span id="deployment"></span> </a>Distribuzione di una macchina virtuale di Azure

![Distribuzione di una macchina virtuale][Distribuzione di una macchina virtuale]

Nei diagrammi precedenti un cluster Cassandra a 4 nodi viene distribuito in Macchine virtuali dietro un bilanciamento del carico configurato in modo da consentire il traffico Thrift. L'applicazione PaaS ospitata in Azure accede al cluster usando le raccolte Thrift specifiche del linguaggio. Sono disponibili raccolte per diversi linguaggi, inclusi Java, C#, Node.js, Python e C++. La distribuzione completa di Macchine virtuali illustrata nel secondo diagramma usa i dati tramite le applicazioni in esecuzione all'interno di un altro servizio cloud ospitato in Macchine virtuali.

## <span id="task1"></span> </a>Attività 1: Distribuire il cluster Linux

La sequenza tipica per la creazione di un cluster è:

![Diagramma della sequenza per la creazione di un cluster][Diagramma della sequenza per la creazione di un cluster]

**Passaggio 1: Generare la coppia di chiavi SSH**

In fase di provisioning Azure richiede una chiave pubblica X509 con codifica PEM o DER. Generare una coppia di chiavi pubbliche/private seguendo le istruzioni contenute in [Come usare SSH con Linux in Azure][Come usare SSH con Linux in Azure]. Se si prevede di usare putty.exe come client SSH in Windows o Linux, è necessario convertire la chiave privata RSA con codifica PEM nel formato PPK mediante puttygen.exe. Le istruzioni relative a questa operazione sono disponibili nella pagina sulla [generazione della coppia di chiavi SSH per la distribuzione di macchine virtuali Linux in Windows Azure][generazione della coppia di chiavi SSH per la distribuzione di macchine virtuali Linux in Windows Azure].

**Passaggio 2: Creare una macchina virtuale Ubuntu**

Per creare la prima macchina virtuale Ubuntu, accedere al portale Azure Preview, fare clic su **New**, su **Virtual Machine**, su **From Gallery**, quindi su **Unbuntu Server 12.xx** e infine sulla freccia a destra. Per un'esercitazione che descrive come creare una macchina virtuale Linux, vedere [Creazione di una macchina virtuale che esegue Linux][Creazione di una macchina virtuale che esegue Linux].

Immettere quindi le informazioni seguenti nella schermata VM Configuration:

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Nome campo</th>
<th align="left">Valore campo</th>
<th align="left">Osservazioni</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Virtual Machine Name</td>
<td align="left">hk-cas1</td>
<td align="left">Nome host della macchina virtuale</td>
</tr>
<tr class="even">
<td align="left">New User Name</td>
<td align="left">localadmin</td>
<td align="left">&quot;admin&quot; è un nome utente riservato in Ubuntu 12.xx</td>
</tr>
<tr class="odd">
<td align="left">New Password</td>
<td align="left"><em>password complessa</em></td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">Confirm Password</td>
<td align="left"><em>password complessa</em></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">Size</td>
<td align="left">Small</td>
<td align="left">Selezionare la macchina virtuale in base alle esigenze di IO.</td>
</tr>
<tr class="even">
<td align="left">Secure using SSH Key for Authentication</td>
<td align="left">Fare clic sulla casella di controllo</td>
<td align="left">Selezionare se si desidera applicare la protezione con una chiave SSH</td>
</tr>
<tr class="odd">
<td align="left">Certificate</td>
<td align="left"><em>nome file del certificato di chiave pubblica</em></td>
<td align="left">Chiave pubblica SSH con codifica DER o PEM generata con OpenSSL o altri strumenti</td>
</tr>
</tbody>
</table>

Immettere le informazioni seguenti nella schermata VM Mode:

| Nome campo                            | Valore campo                           | Osservazioni                                                                                                                        |
|---------------------------------------|----------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| Standalone Virtual VM                 | Selezionare il pulsante di opzione     | Questa opzione è per la prima macchina virtuale. Per le macchine virtuali successive verrà usata l'opzione "Connect to Existing VM" |
| Nome DNS                              | *nome univoco*.cloudapp.net            | Assegnare un nome bilanciamento del carico indipendente dal computer                                                                |
| Account di archiviazione              | *account di archiviazione predefinito* | Usare l'account di archiviazione predefinito creato                                                                                 |
| Region/Affinity Group/Virtual Network | Stati Uniti occidentali                | Selezionare un'area da cui le applicazioni Web accedono al cluster Cassandra                                                        |

Ripetere il processo precedente per tutte le macchine virtuali che faranno parte del cluster Cassandra. Tutti i computer faranno ora parte della stessa rete e potranno effettuare il ping reciproco. Se il ping non funziona, controllare la configurazione del firewall (ad esempio, iptables) della macchina virtuale per assicurarsi che ICMP sia consentito. Assicurarsi di disabilitare ICMP dopo il test della connettività della rete per ridurre il vettore di attacco.

**Passaggio 3: Aggiungere un Thrift con carico bilanciato**

Dopo i passaggi 1 e 2, per ogni macchina virtuale l'endpoint SSH risulterà già definito. Verrà ora aggiunto l'endpoint Thrift con carico bilanciato con la porta pubblica 9160. Di seguito è riportata la sequenza:

a. Nella vista dei dettagli della prima macchina virtuale fare clic su "Aggiungi endpoint"

b. Nella schermata relativa all'aggiunta di un endpoint a una macchina virtuale selezionare il pulsante di opzione "Aggiungi endpoint"

c. Fare clic sulla freccia destra

d. Nella schermata relativa alla specifica dei dettagli dell'endpoint immettere quanto segue

<table>

<tr>

<th>
Nome campo

</th>

<th>
Valore campo

</th>

<th>
Osservazioni

</th>

</tr>

<tr>

<td>
Nome

</td>

<td>
cassandra

</td>

<td>
Qualsiasi nome endpoint univoco

</td>

</tr>

<tr>

<td>
Protocol

</td>

<td>
TCP

</td>

<td>
</td>

</tr>

<tr>

<td>
Public Port

</td>

<td>
9160

</td>

<td>
Porta Thrift predefinita

</td>

</tr>

<tr>

<td>
Private Port

</td>

<td>
9160

</td>

<td>
A meno che non si sia modificato questo valore in cassandra.yaml

</td>

</tr>

</table>
Dopo queste operazioni, il campo relativo al carico bilanciato dell'endpoint cassandra della prima macchina virtuale sarà impostato su "NO". Ignorare per il momento questo valore perché verrà impostato su "YES" dopo aver aggiunto l'endpoint alle successive macchine virtuali

</p>
e. Selezionare ora la seconda macchina virtuale e aggiungere l'endpoint ripetendo il processo precedente selezionando però "Load-balance traffic on an existing endpoint" e usando "cassandra-960" nella casella a discesa. A questo punto, lo stato LOAD BALANCED dell'endpoint con mapping a entrambe le macchine virtuali passerà da "NO" a "YES".

Ripetere il passaggio "e" per i successivi nodi del cluster.

Ora che le macchine virtuali sono pronte, è possibile configurare Cassandra su ogni macchina virtuale. Poiché Cassandra non è un componente standard di diverse distribuzioni Linux, si ricorrerà a un processo di distribuzione manuale.

[Si noti che verrà usato un approccio manuale per l'installazione software in ogni macchina virtuale. È tuttavia possibile velocizzare il processo configurando una macchina virtuale Cassandra completamente funzionante, acquisendola come immagine di base e creando ulteriori istanze da questa immagine di base. Le istruzioni per acquisire un'immagine Linux sono disponibili in [Come acquisire un'immagine di una macchina virtuale che esegue Linux][Si noti che verrà usato un approccio manuale per l'installazione software in ogni macchina virtuale. È tuttavia possibile velocizzare il processo configurando una macchina virtuale Cassandra completamente funzionante, acquisendola come immagine di base e creando ulteriori istanze da questa immagine di base. Le istruzioni per acquisire un'immagine Linux sono disponibili in [Come acquisire un'immagine di una macchina virtuale che esegue Linux].]

## <span id="task2"></span> </a>Attività 2: Configurare Cassandra su ogni macchina virtuale

**Passaggio 1: Installare i prerequisiti**

Poiché Cassandra richiede Java Virtual Machine, installare l'ambiente JRE più recente usando il comando seguente per le distribuzioni derivate da Debian, incluso Ubuntu:

    sudo add-apt-repository ppa:webupd8team/java
    sudo apt-get update
    sudo apt-get install oracle-java7-installer

**Passaggio 2: Installazione di Cassandra**

1.  Accedere usando SSH all'istanza della macchina virtuale Linux (Ubuntu).

2.  Usare wget per scaricare i bit di Cassandra dal mirror suggerito all'indirizzo (http://cassandra.apache.org/download/)[http://cassandra.apache.org/download/] nella directory "~/downloads" come apache-cassandra-bin.tar.gz. Si noti che il numero di versione non è incluso nel file scaricato per essere certi che la pubblicazione sia indipendente dalla versione.

3.  Decomprimere il file tarball nella directory di accesso predefinita eseguendo il comando seguente:

        tar -zxvf downloads/apache-cassandra-bin.tar.gz

    Questo comando espanderà l'archivio nella directory apache-cassandra- [versione] directory.

4.  Creare le due directory predefinite seguenti in cui inserire i log e i dati:

        $ sudo chown -R /var/lib/cassandra
        $ sudo chown -R /var/log/cassandra

5.  Concedere le autorizzazioni di scrittura all'identità utente con cui verrà eseguito Cassandra

        a.  sudo chown -R <user>:<group> /var/lib/cassandra
        b.  sudo chown -R <user>:<group> /var/log/cassandra
        To use current user context, replace the <user> and <group> with $USER and $GROUP

6.  Avviare Cassandra dalla directory apache-cassandra-[versione]/bin usando il comando seguente:

        $ ./cassandra

Questo comando avvierà il nodo Cassandra come processo in background. Usare -cassandra "f" per avviare il processo in modalità primo piano.

Nel log verrà visualizzato l'errore relativo a mx4j. Cassandra funzionerà correttamente senza mx4j, che è tuttavia necessario per gestire l'installazione di Cassandra. Arrestare il processo di Cassandra prima del passaggio successivo.

**Passaggio 3: Installare mx4j**

    a)  Download mx4j: wget [http://sourceforge.net/projects/mx4j/files/MX4J%20Binary/3.0.2/mx4j-3.0.2.tar.gz/download](http://sourceforge.net/projects/mx4j/files/MX4J%20Binary/3.0.2/mx4j-3.0.2.tar.gz/download) -O mx4j.tar.gz
    b)  tar -zxvf mx4j.tar.gz
    c)  cp mx4j-23.0.2/lib/*.jar ~/apache-cassandra-<version>/lib
    d)  rm -rf mx4j-23.0.2
    e)  rm mx4j.tar.gz

A questo punto riavviare il processo di Cassandra

**Passaggio 4: Testare l'installazione di Cassandra**

Eseguire il comando seguente dalla directory bin di Cassandra per stabilire la connessione usando il client thrift:

    cassandra-cli -h localhost -p 9160

**Passaggio 5: Abilitare Cassandra per le connessioni esterne**

Per impostazione predefinita, Cassandra è configurato solo per restare in ascolto dell'indirizzo di loopback e pertanto questa modifica è obbligatoria per le connessioni esterne.

Modificare "conf/cassandra.yaml" in modo da sostituire **listen\_address** e **rpc\_address** con l'indirizzo IP o il nome host del server. In questo modo il nodo corrente sarà visibile per gli altri nodi oltre che per i servizi di bilanciamento del carico esterni.

Ripetere i passaggi da 1 a 5 per tutti i nodi del cluster.

Ora che ogni singola macchina virtuale è pronta con il software necessario, è possibile stabilire la comunicazione tra i nodi tramite la configurazione dei valori iniziali. Per i dettagli sulla configurazione di un cluster a più nodi, vedere le informazioni disponibili all'indirizzo <http://wiki.apache.org/cassandra/MultinodeCluster>.

**Passaggio 6: Configurare il cluster a più nodi**

Modificare cassandra.yaml in modo da cambiare le proprietà seguenti in tutte le macchine virtuali:

**a) cluster\_name**

Il nome cluster predefinito è impostato su "Test Cluster". Sostituirlo con un nome che faccia riferimento all'applicazione. Esempio: "AppStore". Se il cluster era già stato avviato con "Test Cluster" per il test durante l'installazione, verrà visualizzato un errore di mancata corrispondenza con il nome cluster. Per correggere questo errore, eliminare tutti i file nella directory /var/lib/cassandra/data/system.

**b) seeds**

Gli indirizzi IP qui specificati verranno usati dai nuovi nodi per conoscere la topologia della sequenza. Impostare i nodi più affidabili come valori iniziali in un formato separato da virgole: "*host1*,*host2*". Impostazione di esempio: "hk-ub1,hk-ub2".

Verranno accettati i token predefiniti forniti dai server di inizializzazione, non essendo fondamentali in questa esercitazione. Per la generazione dei token ottimali, vedere lo script Python disponibile all'indirizzo:
<http://wiki.apache.org/cassandra/GettingStarted>.

Riavviare Cassandra in tutti i nodi per applicare le modifiche precedenti.

**Passaggio 7: Testare il cluster a più nodi**

Nodetool installato nella directory bin di Cassandra agevolerà le operazioni cluster. Nodetool verrà usato per verificare la configurazione del cluster tramite il formato di comando seguente:

    $ bin/nodetool -h <hostname> -p 7199 ring

Se la configurazione è corretta, verranno visualizzate le informazioni seguenti per un cluster a 3 nodi:

<table>
<colgroup>
<col width="12%" />
<col width="12%" />
<col width="12%" />
<col width="12%" />
<col width="12%" />
<col width="12%" />
<col width="12%" />
<col width="12%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left">Address</td>
<td align="left">DC</td>
<td align="left">Rack</td>
<td align="left">Status</td>
<td align="left">State</td>
<td align="left">Load</td>
<td align="left">Owns</td>
<td align="left">Token</td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">149463697837832744402916220269706844972</td>
</tr>
<tr class="odd">
<td align="left">10.26.196.68</td>
<td align="left">datacenter1</td>
<td align="left">rack1</td>
<td align="left">Up</td>
<td align="left">Normal</td>
<td align="left">15,69 KB</td>
<td align="left">25.98%</td>
<td align="left">114445918355431753244435008039926455424</td>
</tr>
<tr class="even">
<td align="left">10.26.198.81</td>
<td align="left">datacenter1</td>
<td align="left">rack1</td>
<td align="left">Up</td>
<td align="left">Normal</td>
<td align="left">15,69 KB</td>
<td align="left">53.44%</td>
<td align="left">70239176883275351288292106998553981501</td>
</tr>
<tr class="odd">
<td align="left">10.26.198.84</td>
<td align="left">datacenter1</td>
<td align="left">rack1</td>
<td align="left">Up</td>
<td align="left">Normal</td>
<td align="left">18,35 KB</td>
<td align="left">25.98%</td>
<td align="left">149463697837832744402916220269706844972</td>
</tr>
</tbody>
</table>

A questo punto, il cluster è pronto per i client Thrift tramite l'URL del servizio cloud (nome DNS assegnato durante la creazione della prima macchina virtuale) creato durante l'attività "Distribuire il cluster Linux".

## <span id="task3"></span> </a>Attività 3: Accedere al cluster Cassandra da Node.js

Creare una macchina virtuale Linux in Azure usando il processo descritto nelle attività precedenti. Verificare che questa macchina virtuale sia autonoma perché verrà usata come client per accedere al cluster Cassandra. Prima di connettersi al cluster Cassandra da questa macchina virtuale, verranno installati Node.js, NPM e [cassandra-client][cassandra-client] da github:

**Passaggio 1: Installare Node.js e NPM**

a) Installare i prerequisiti

    sudo apt-get install g++ libssl-dev apache2-utils make

b) Per la compilazione e l'installazione, verrà usata l'origine da GitHub. Per poter clonare l'archivio, è necessario installare il runtime principale git:

    sudo apt-get install git-core

c) Clonare l'archivio Node

    git clone git://github.com/joyent/node.git

d) Il comando precedente creerà la directory con il nome "node". Per compilare e installare node.js, eseguire la sequenza di comandi seguente:

    cd node
    ./configure
    make
    sudo make install

e) Installare NPM da file binari stabili eseguendo il comando seguente

    curl http://npmjs.org/install.sh | sh

**Passaggio 2: Installare il pacchetto cassandra-client**

    npm cassandra-client 

**Passaggio 3: Preparare l'archiviazione di Cassandra**

L'archiviazione di Cassandra usa i concetti di KEYSPACE e COLUMNFAMILY, che sono paragonabili alle strutture DATABASE e TABLE del lessico RDBMS. KEYSAPCE conterrà un set di definizioni COLUMNFAMILY. Ogni COLUMNFAMILY conterrà un set di righe e a sua volta ogni riga contiene diverse colonne, come illustrato nella vista composita seguente:

![Righe e colonne][Righe e colonne]

Verrà usato il cluster Cassandra distribuito in precedenza per illustrare l'accesso a node.js creando ed eseguendo una query delle strutture di dati più sopra. Verrà creato un semplice script di node.js che eseguirà la preparazione di base del cluster per archiviare i dati dei clienti. Le tecniche illustrate nello script sono facilmente utilizzabili nei servizi Web o in un'applicazione Web node.js. Tenere presente che i frammenti hanno il solo scopo di mostrare il funzionamento generale e che, per le soluzioni reali, è possibile apportare diversi miglioramenti al codice indicato (ad esempio, sicurezza, registrazione, scalabilità e così via).

Verranno ora definite le variabili necessarie nell'ambito dello script in modo da includere PooledConnection dal modulo cassandra-client, il nome keyspace usato di frequente e i parametri di connessione keyspace:

    casdemo.js: 
    var pooledCon = require('cassandra-client').PooledConnection;
    var ksName = "custsupport_ks";
    var ksConOptions = { hosts: ['<azure_svc_name>.cloudapp.net:9160'], 
                         keyspace: ksName, use_bigints: false };

Per prepararsi ad archiviare i dati del cliente, è innanzitutto necessario creare un oggetto KEYSPACE usando l'esempio di script seguente:

    casdemo.js: 
    function createKeyspace(callback){
       var cql = 'CREATE KEYSPACE ' + ksName + ' WITH 
       strategy_class=SimpleStrategy AND strategy_options:replication_factor=1';
       var sysConOptions = { hosts: ['<azure_svc_name>.cloudapp.net:9160'],  
                             keyspace: 'system', use_bigints: false };
       var con = new pooledCon(sysConOptions);
       con.execute(cql,[],function(err) {
       if (err) {
         console.log("Failed to create Keyspace: " + ksName);
         console.log(err);
       }
       else {
         console.log("Created Keyspace: " + ksName);
         callback(ksConOptions, populateCustomerData);
       }
       });
       con.shutdown();
    } 

La funzione createKeysapce accetta una funzione di callback come argomento inteso a eseguire la funzione di creazione di COLUMNFAMILY in quanto KEYSPACE è un prerequisito per la creazione della famiglia di colonne. Si noti che è necessario connettersi al KEYSPACE "system" per la definizione del KEYSPACE dell'applicazione. [Cassandra Query Language (CQL)][Cassandra Query Language (CQL)] viene usato in modo coerente nell'interazione con il cluster in tutti questi frammenti. Dal momento che il linguaggio CQL composto nello script precedente non dispone di indicatori di parametro, verrà usata una raccolta di parametri vuoti ("[]") con il metodo PooledConnection.execute().

Al termine della creazione dell'oggetto keyspace, verrà eseguita la funzione createColumnFamily(), indicata nel frammento seguente, per creare le definizioni COLUMNFAMILY:

    casdemo.js: 
    //Creates COLUMNFAMILY
    function createColumnFamily(ksConOptions, callback){
      var params = ['customers_cf','custid','varint','custname',
                    'text','custaddress','text'];
      var cql = 'CREATE COLUMNFAMILY ? (? ? PRIMARY KEY,? ?, ? ?)';
    var con =  new pooledCon(ksConOptions);
      con.execute(cql,params,function(err) {
          if (err) {
             console.log("Failed to create column family: " + params[0]);
             console.log(err);
          }
          else {
             console.log("Created column family: " + params[0]);
             callback();
          }
      });
      con.shutdown();
    } 

Il modello CQL con parametri verrà combinato con l'oggetto params per generare il linguaggio CQL valido per la creazione di COLUMNFAMILY. Al termine della creazione di COLUMNFAMILY, il callback specificato, in questo caso populateCustomerData(), verrà chiamato come parte della catena di chiamate asincrone.

    casdemo.js: 
    //populate Data
    function populateCustomerData() {
       var params = ['John','Infinity Dr, TX', 1];
       updateCustomer(ksConOptions,params);

       params = ['Tom','Fermat Ln, WA', 2];
       updateCustomer(ksConOptions,params);
    }

    //update will also insert the record if none exists
    function updateCustomer(ksConOptions,params)
    {
      var cql = 'UPDATE customers_cf SET custname=?,custaddress=? where 
                 custid=?';
      var con = new pooledCon(ksConOptions);
      con.execute(cql,params,function(err) {
          if (err) console.log(err);
          else console.log("Inserted customer : " + params[0]);
      });
      con.shutdown();
    }

populateCustomerData() inserisce un paio di righe nell'oggetto COLUMNFAMILY denominato customers\_cf. In Cassandra Query Language, UPDATE inserirà il record se il record non è già presente nel processo rendendo ridondante l'istruzione INSERT CQL.

Finora è stata collegata la catena di callback: da createKeyspace() a createColumnFamily() a populateCustomerData(). È ora necessario eseguire il codice tramite il frammento di codice seguente:

    casdemo.js:
    var pooledCon = require('cassandra-client').PooledConnection;
    var ksName = "custsupport_ks";
    var ksConOptions = { hosts: ['<azure_svc_name>.cloudapp.net:9160'], 
                         keyspace: ksName, use_bigints: false };

    createKeyspace(createColumnFamily);
    //rest of the not shown

Eseguire il comando seguente al prompt della shell per eseguire lo script:

    //the following command will create the KEYSPACE, COLUMNFAMILY and //inserts two customer records
    $ node casdemo.js

Il metodo readCustomer() accederà al cluster ospitato in Azure e visualizzerà il frammento JSON recuperato dopo l'esecuzione della query CQL:

    casdemo.js: 
    //read the two rows inserted above
    function readCustomer(ksConOptions)
    {
      var cql = 'SELECT * FROM customers_cf WHERE custid IN (1,2)';
      var con = new pooledCon(ksConOptions);
      con.execute(cql,[],function(err,rows) {
          if (err) 
             console.log(err);
          else 
             for (var i=0; i<rows.length; i++)
                console.log(JSON.stringify(rows[i]));
        });
       con.shutdown();
    } 

Modificare casdemo.js in modo da aggiungere la funzione sopra e chiamarla dopo aver commentato il metodo createKeyspace() chiamato in precedenza come indicato di seguito:

    casdemo.js: 
    var pooledCon = require('cassandra-client').PooledConnection;
    var ksName = "custsupport_ks";
    var ksConOptions = { hosts: ['<azure_svc_name>.cloudapp.net:9160'], 
                         keyspace: ksName, use_bigints: false };

    //createKeyspace(createColumnFamily);
    readCustomer(ksConOptions)
    //rest of the code below not shown
        

## <span id="conclusion"></span> </a>Conclusioni

La funzionalità Macchine virtuali di Azure consente la creazione di macchine virtuali Linux (immagini fornite dai partner Microsoft) e Windows che consentono la migrazione di applicazioni e prodotti server esistenti senza apportare modifiche. Il server database NoSQL Cassandra illustrato in questo articolo ne è un esempio. Il cluster Cassandra configurato in questo articolo è accessibile tramite servizi cloud ospitati in Azure, cloud pubblici di terze parti e cloud privati, da ambienti di sistemi operativi sia Windows che Linux. In questo articolo si è parlato di node.js come client. Cassandra è tuttavia accessibile da ambienti .NET, Java e di altri linguaggi.

  [Panoramica]: #overview
  [Schema di distribuzione di Cassandra]: #schematic
  [Distribuzione composita]: #composite
  [Distribuzione di una macchina virtuale di Azure]: #deployment
  [Attività 1: Distribuire il cluster Linux]: #task1
  [Attività 2: Configurare Cassandra su ogni macchina virtuale]: #task2
  [Attività 3: Accedere al cluster Cassandra da Node.js]: #task3
  [Conclusioni]: #conclusion
  [Cassandra]: http://wiki.apache.org/cassandra/
  [Diagramma di distribuzione composita]: ./media/virtual-machines-linux-nodejs-running-cassandra/cassandra-linux1.png
  [Distribuzione di una macchina virtuale]: ./media/virtual-machines-linux-nodejs-running-cassandra/cassandra-linux2.png
  [Diagramma della sequenza per la creazione di un cluster]: ./media/virtual-machines-linux-nodejs-running-cassandra/cassandra-linux4.png
  [Come usare SSH con Linux in Azure]: http://www.windowsazure.com/it-it/manage/linux/how-to-guides/ssh-into-linux/
  [generazione della coppia di chiavi SSH per la distribuzione di macchine virtuali Linux in Windows Azure]: http://blogs.msdn.com/b/hanuk/archive/2012/06/07/generating-ssh-key-pair-for-linux-vm-deployment-on-windows-azure.aspx
  [Creazione di una macchina virtuale che esegue Linux]: http://www.windowsazure.com/it-it/manage/linux/tutorials/virtual-machine-from-gallery/
  [Come acquisire un'immagine di una macchina virtuale che esegue Linux]: https://www.windowsazure.com/it-it/manage/linux/how-to-guides/capture-an-image/
  [cassandra-client]: https://github.com/racker/node-cassandra-client
  [Righe e colonne]: ./media/virtual-machines-linux-nodejs-running-cassandra/cassandra-linux3.png
  [Cassandra Query Language (CQL)]: http://cassandra.apache.org/doc/cql/CQL.html
