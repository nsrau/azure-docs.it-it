<properties
   pageTitle="Creazione di un ambiente di test delle prestazioni per Elasticsearch | Microsoft Azure"
   description="Come configurare un ambiente per i test delle prestazioni di un cluster Elasticsearch."
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
   
# Creazione di un ambiente di test delle prestazioni per Elasticsearch in Azure

Questo articolo fa [parte di una serie](guidance-elasticsearch.md).

Questo documento illustra come configurare un ambiente per i test delle prestazioni di un cluster Elasticsearch. La configurazione è stata usata per testare le prestazioni delle operazioni di inserimento dei dati e dei carichi di lavoro di query come descritto in [Ottimizzazione delle prestazioni di inserimento dei dati per Elasticsearch in Azure][].

Nel processo di test delle prestazioni è stato usato [Apache JMeter](http://jmeter.apache.org/) con il [set Standard](http://jmeter-plugins.org/wiki/StandardSet/) di plug-in installato in una configurazione master/secondario usando un set di macchine virtuali dedicate, non appartenenti al cluster Elasticsearch, configurate appositamente per lo scopo.

[PerfMon Server Agent](http://jmeter-plugins.org/wiki/PerfMonAgent/) è stato installato in ogni nodo Elasticsearch. Le sezioni seguenti forniscono istruzioni per ricreare l'ambiente di test che consente di eseguire test delle prestazioni personalizzati con JMeter. Queste istruzioni presuppongono che sia già stato creato un cluster Elasticsearch con nodi connessi tramite una rete virtuale di Azure.

Si noti che l'ambiente di test viene eseguito anche come set di macchine virtuali di Azure gestite con un singolo gruppo di risorse di Azure.

È stato anche installato e configurato [Marvel](https://www.elastic.co/products/marvel) che consente di monitorare e analizzare più facilmente gli aspetti interni del cluster Elasticsearch. Se le statistiche di JMeter hanno rilevato un picco o un calo delle prestazioni, le informazioni disponibili tramite Marvel possono essere particolarmente utili per determinare la causa delle fluttuazioni.

L'immagine seguente illustra la struttura dell'intero sistema.

![elasticsearch-arch](./media/guidance-elasticsearch/performance-structure.png)

Tenere presente quanto segue:

- La VM master di JMeter esegue Windows Server per fornire l'ambiente dell'interfaccia utente grafica per la console JMeter. La VM master di JMeter fornisce l'interfaccia utente grafica, ovvero l'applicazione *jmeter*, per consentire a un tester di creare ed eseguire test e visualizzare i risultati. Questa VM interagisce con le macchine virtuali di JMeter Server che inviano effettivamente le richieste che costituiscono i test.

- Le VM secondarie di JMeter eseguono Ubuntu Server (Linux). Non sono previsti requisiti relativi all'interfaccia utente grafica per queste macchine virtuali. Le VM di JMeter Server eseguono il software JMeter Server, ovvero l'applicazione *jmeter-server*, per inviare richieste al cluster Elasticsearch.

- Non sono stati usati nodi client dedicati, bensì nodi master dedicati.

- Il numero di nodi dati del cluster può variare, a seconda dello scenario da testare.

- Tutti i nodi del cluster Elasticsearch eseguono Marvel per osservare le prestazioni in fase di esecuzione e JMeter Server Agent per raccogliere i dati di monitoraggio per le analisi successive.

- Durante il test di Elasticsearch 2.0.0 e versioni successive, uno dei nodi dati esegue anche Kibana. Questo componente è richiesto dalla versione di Marvel eseguita in Elasticsearch 2.0.0 e versioni successive.

## Creazione di un gruppo di risorse di Azure per le macchine virtuali

La VM master di JMeter deve potersi connettere direttamente a ogni nodo del cluster Elasticsearch per raccogliere i dati sulle prestazioni. Se la rete virtuale di JMeter VNet è diversa dalla rete virtuale del cluster Elasticsearch, sarà necessario configurare ogni nodo Elasticsearch con un indirizzo IP pubblico. Se questo costituisce un problema per la configurazione di Elasticsearch, considerare la possibilità di implementare le VM di JMeter nella stessa rete virtuale del cluster Elasticsearch usando lo stesso gruppo di risorse. In questo caso è possibile omettere la prima procedura.

Prima di tutto [creare un gruppo di risorse](../articles/resource-group-portal/#create-resource-group-and-resources). Questo documento presuppone che il gruppo di risorse sia denominato *JMeterPerformanceTest*. Per eseguire le VM di JMeter nella stessa rete virtuale del cluster Elasticsearch, usare lo stesso gruppo di risorse del cluster invece di crearne uno nuovo.

## Creazione della macchina virtuale master di JMeter

A questo punto, [creare una macchina virtuale Windows](../articles/virtual-machines-windows-tutorial/) con l'immagine di *Windows Server 2008 R2 SP1*. È consigliabile selezionare le dimensioni di una macchina virtuale con core e memoria sufficienti per eseguire i test delle prestazioni. Idealmente si tratterà di una VM con almeno 2 core e 3,5 GB di RAM (A2 Standard o superiore).

<!-- TODO add info on why disabling diagnostics is positive -->

È consigliabile disabilitare la diagnostica. Quando si crea la macchina virtuale nel portale, eseguire questa operazione nel pannello *Impostazioni*, nella sezione *Monitoraggio* in *Diagnostica*. Lasciare i valori predefiniti per le altre impostazioni.

Verificare che la macchina virtuale e tutte le risorse associate siano state create correttamente [esaminando il gruppo di risorse](../articles/resource-group-portal/#browse-resource-groups) nel portale. Le risorse elencate devono essere costituite da una macchina virtuale, un gruppo di sicurezza di rete e un indirizzo IP pubblico tutti con lo stesso nome, mentre l'account di archiviazione e l'interfaccia di rete dovranno avere nomi basati su quello della VM.

## Creazione delle macchine virtuali secondarie di JMeter

A questo punto [creare una VM Linux](../articles/virtual-machines-linux-tutorial-portal-rm/) usando l'immagine di *Ubuntu Server 14.04 LTS*. Come per la VM master di JMeter, selezionare le dimensioni di una macchina virtuale con core e memoria sufficienti per eseguire i test delle prestazioni. Idealmente si tratterà di una VM con almeno 2 core e almeno 3,5 GB di RAM (A2 Standard o superiore).

Anche in questo caso, è consigliabile disabilitare la diagnostica.

È possibile creare tutte le VM secondarie necessarie.

## Installazione di JMeter Server nelle macchine virtuali secondarie di JMeter

Le VM secondarie di JMeter eseguono Linux e, per impostazione predefinita, non è possibile connettersi a queste VM aprendo una connessione Desktop remoto (RDP). È invece possibile [usare PuTTY per aprire una finestra della riga di comando](../articles/virtual-machines-linux-how-to-log-on/) in ogni macchina virtuale.

Dopo la connessione a una delle VM secondarie, si userà bash per configurare JMeter.

Prima di tutto installare Java Runtime Environment richiesto per l'esecuzione di JMeter.

```bash
sudo add-apt-repository ppa:webupd8team/java
sudo apt-get update
sudo apt-get install oracle-java8-installer
```

Quindi scaricare il software JMeter fornito come file ZIP.

```bash
wget http://apache.mirror.anlx.net/jmeter/binaries/apache-jmeter-2.13.zip
```

Installare il comando unzip, quindi usarlo per espandere il software JMeter. Il software viene copiato in una cartella denominata **apache-jmeter-2.13**.

```bash
sudo apt-get install unzip
unzip apache-jmeter-2.13.zip
```

Passare alla directory *bin* che contiene i file eseguibili di JMeter ed eseguire i programmi *jmeter-server* e *jmeter*.

```bash
cd apache-jmeter-2.13/bin
chmod u+x jmeter-server
chmod u+x jmeter
```

A questo punto, è necessario modificare il file `jmeter.properties` che si trova nella cartella corrente. Usare l'editor di testo più familiare, ad esempio *vi* o *vim*. Trovare le righe seguenti:

```yaml
...
client.rmi.localport=0
...
server.rmi.localport=4000
...
```

Rimuovere il commento (rimuovere i caratteri ## iniziali) e modificare queste righe come illustrato di seguito, quindi salvare il file e chiudere l'editor:

```yaml
...
client.rmi.localport=4441
...
server.rmi.localport=4440
```

Ora eseguire i comandi seguenti per aprire la porta 4441 al traffico TCP in ingresso. Questa è la porta di ascolto appena configurata per *jmeter-server*:

```bash
sudo iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 4441 -j ACCEPT
```

Scaricare un file ZIP contenente la raccolta standard di plug-in per JMeter, che forniscono i contatori di monitoraggio delle prestazioni, e quindi decomprimere il file. La decompressione del file in questa posizione inserisce i plug-nella cartella corretta.

Se viene chiesto di sostituire il file LICENSE, digitare A per tutto:

```bash
wget http://jmeter-plugins.org/downloads/file/JMeterPlugins-Standard-1.3.0.zip
unzip JMeterPlugins-Standard-1.3.0.zip
```

Usare `nohup` per avviare JMeter Server in background. Risponderà visualizzando un ID processo e un messaggio per indicare che ha creato un oggetto remoto ed è pronto per iniziare a ricevere comandi.

```bash
nohup bin/jmeter-server &
```

> [AZURE.NOTE] Se si arresta la macchina virtuale, il programma JMeter Server viene terminato. Sarà necessario connettersi alla macchina virtuale e riavviarlo manualmente. In alternativa, è possibile configurare il sistema per l'esecuzione automatica del comando *jmeter-server* all'avvio, aggiungendo i comandi seguenti al file `/etc/rc.local`, prima del comando *exit 0*:

```bash
sudo -u <username> bash << eoc
cd /home/<username>/apache-jmeter-2.13/bin
nohup ./jmeter-server &
eoc
```

Sostituire `<username>` con il proprio nome di accesso:

Può risultare utile tenere aperta la finestra del terminale per poter monitorare lo stato di JMeter Server durante l'esecuzione del test.

È necessario ripetere questi passaggi per ogni VM secondaria di JMeter.

## Installare JMeter Server Agent nei nodi Elasticsearch

Questa procedura presuppone che sia disponibile l'accesso ai nodi Elasticsearch. Se il cluster è stato creato con il modello di Azure Resource Manager, è possibile connettersi a ogni nodo tramite la VM JumpBox, come illustrato nella sezione [Topologie di Elasticsearch](guidance-elasticsearch-running-on-azure.md#elasticsearch-topologies) che descrive la topologia di produzione di Azure. È possibile connettersi anche alla JumpBox usando PuTTY.

Da qui è possibile usare il comando *ssh* per accedere a tutti i nodi del cluster Elasticsearch.

Accedere a uno dei nodi Elasticsearch come amministratore. Al prompt dei comandi bash immettere i comandi seguenti per creare una cartella che conterrà JMeter Server Agent e passare a tale cartella:

```bash
mkdir server-agent
cd server-agent
```

Eseguire i comandi seguenti per installare il comando *unzip*, se non è già installato, scaricare il software JMeter Server Agent e decomprimerlo:

```bash
sudo apt-get install unzip
wget http://jmeter-plugins.org/downloads/file/ServerAgent-2.2.1.zip
unzip ServerAgent-2.2.1.zip
```
 
Eseguire il comando seguente per configurare il firewall e abilitare il passaggio del traffico TCP sulla porta 4444, ovvero la porta usata da JMeter Server Agent:

```bash
sudo iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 4444 -j ACCEPT
```

Eseguire il comando seguente per avviare JMeter Server Agent in background:

```bash
nohup ./startAgent.sh &
```

JMeter Server Agent risponderà con messaggi che indicano che è stato avviato ed è in ascolto sulla porta 4444. Premere INVIO per aprire una finestra del prompt di comandi ed eseguire il comando seguente. Sostituire `<nodename>` con il nome del nodo. Se non si è certi del nome del nodo, è possibile trovarlo eseguendo il comando `hostname`:

```bash
telnet <nodename> 4444
```

Questo comando apre una connessione telnet alla porta 4444 nel computer locale. È possibile usare questa connessione per verificare che JMeter Server Agent venga eseguito correttamente.

Se JMeter Server Agent non è in esecuzione, verrà visualizzata la risposta

`*telnet: Unable to connect to remote host: Connection refused*.`

Se JMeter Server Agent è in esecuzione e la porta 4444 è stata configurata correttamente, verrà visualizzata la risposta seguente:

![](./media/guidance-elasticsearch/performance-telnet-server.png)

> [AZURE.NOTE] La sessione telnet non fornisce alcun tipo di richiesta una volta che è stata stabilita la connessione.

Nella sessione telnet digitare il comando seguente:

``` 
test
```

Se JMeter Server Agent è configurato correttamente e in attesa, indicherà che ha ricevuto il comando rispondendo con il messaggio *Yep*.

> [AZURE.NOTE] È possibile digitare altri comandi per ottenere dati di monitoraggio delle prestazioni. Ad esempio, il comando `metric-single:cpu:idle` visualizzerà la percentuale corrente di tempo della CPU inattiva (si tratta di uno snapshot). Per un elenco completo dei comandi, vedere la pagina[PerfMon Server Agent](http://jmeter-plugins.org/wiki/PerfMonAgent/).

Nella sessione telnet digitare il comando seguente per chiudere la sessione e tornare al prompt dei comandi bash:

``` 
exit
```

> [AZURE.NOTE] Come per le VM secondarie di JMeter, se ci si disconnette o se il computer viene arrestato e riavviato, JMeter Server Agent dovrà essere riavviato manualmente con il comando `startAgent.sh`. Se si vuole che JMeter Server Agent venga avviato automaticamente, aggiungere il comando seguente alla fine del file `/etc/rc.local`, prima del comando *exit 0*. Sostituire `<username>` con il proprio nome di accesso:

```bash
sudo -u <username> bash << eoc
cd /home/<username>/server-agent
nohup ./startAgent.sh &
eoc
```

Sostituire `<username>` con il proprio nome di accesso:

Ora si può ripetere l'intero processo per ogni altro nodo del cluster Elasticsearch oppure si può usare il comando `scp` per copiare la cartella server-agent e il contenuto di ogni altro nodo e usare il comando `ssh` per avviare JMeter Server Agent come illustrato di seguito. Sostituire `<username>` con il proprio nome utente e `<nodename>` con il nome del nodo in cui si vuole copiare ed eseguire il software. È possibile che venga richiesto di fornire la password all'esecuzione di ogni comando:

```bash
scp -r ~/server-agent <username>@<nodename>:~
ssh <nodename> sudo iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 4444 -j ACCEPT
ssh <nodename> -n -f 'nohup ~/server-agent/startAgent.sh'
```

## Installazione e configurazione di JMeter nella VM master di JMeter

Nel portale di Azure fare clic su *Gruppi di risorse*. Nel pannello *Gruppi di risorse* fare clic sul gruppo di risorse che contiene le VM master e secondarie di JMeter. Nel pannello *Gruppo di risorse* fare clic sulla VM master di JMeter. Sulla barra degli strumenti nel pannello della macchina virtuale fare clic su *Connetti*. Quando richiesto, aprire il file RDP nel Web browser. Windows crea una connessione Desktop remoto alla macchina virtuale. Quando richiesto, immettere il nome utente e la password per la VM.

Nella macchina virtuale usare Internet Explorer per passare alla pagina [Download di Java per Windows](http://www.java.com/en/download/ie_manual.jsp). Seguire le istruzioni per scaricare ed eseguire il programma di installazione di Java.

Nel Web browser passare alla pagina [Download Apache JMeter](http://jmeter.apache.org/download_jmeter.cgi) e scaricare il file ZIP contenente i file binari più recenti. Salvare il file ZIP in un percorso pratico nella macchina virtuale.

Accedere al sito [Custom Plugins for Apache JMeter](http://jmeter-plugins.org/) e scaricare il set Standard di plug-in. Salvare il file ZIP nella stessa cartella di download di JMeter dal passaggio precedente.

In Esplora risorse passare alla cartella che contiene il file ZIP apache-jmeter-*xx*, dove *xx* è la versione corrente di JMeter, e quindi estrarre i file nella cartella corrente. Il file ZIP creerà una sottocartella denominata apache-jmeter-*xxx*.

Estrarre i file da JMeterPlugins-Standard-*yyy*.zip, dove *yyy* è la versione corrente del plug-in, nella cartella pache-jmeter-*xxx*. I plug-in verranno aggiunti nella cartella corretta per JMeter. È possibile unire le cartelle lib e sovrascrivere i file di licenza e leggimi, se richiesto.

Passare alla cartella apache-jmeter-*xxx*/bin e modificare il file jmeter.properties nel Blocco note. Nel file `jmeter.properties` trovare la sezione con l'etichetta *Remote hosts and RMI configuration*. In questa sezione del file trovare la riga seguente:

```yaml
remote_hosts=127.0.0.1
```

Modificare questa riga e sostituire l'indirizzo IP 127.0.0.1 con un elenco delimitato da virgole di indirizzi IP o nomi host per ogni server secondario di JMeter. Ad esempio:

```yaml
remote_hosts=JMeterSub1,JMeterSub2
```

Trovare la riga seguente, quindi rimuovere il carattere `#` all'inizio della riga e modificare il valore delle impostazioni client.rmi.localport da:

```yaml
#client.rmi.localport=0
```

in:

```yaml
client.rmi.localport=4440
```

Salvare il file e chiudere il Blocco note.

Sulla barra degli strumenti di Windows fare clic su *Start*, su *Strumenti di amministrazione* e quindi su *Windows Firewall con sicurezza avanzata*. In *Windows Firewall con sicurezza avanzata* nel riquadro sinistro fare clic con il pulsante destro del mouse su *Regole connessioni in entrata* e quindi scegliere *Nuova regola*.

In *Creazione guidata nuova regola* *connessioni in entrata* selezionare *Porta* nella pagina *Tipo di regola* e quindi fare clic su *Avanti*. Nella pagina *Protocolli e porte* selezionare *TCP*, selezionare *Porte locali specifiche*, digitare `4440-4444` nella casella di testo e quindi fare clic su *Avanti*. Nella schermata *Azione* selezionare *Consenti la connessione* e quindi fare clic su *Avanti*. Nella pagina *Profilo* lasciare tutte le opzioni selezionate e quindi fare clic su *Avanti*. Nella pagina *Nome* digitare *JMeter* nella casella di testo *Nome* e quindi fare clic su *Fine*. Chiudere la finestra *Windows Firewall con sicurezza avanzata*.

In Esplora risorse fare doppio clic sul file batch di Windows *jmeter* nella cartella apache-jmeter-*xx*/bin per avviare l'interfaccia utente grafica. Verrà visualizzata l'interfaccia utente:

![](./media/guidance-elasticsearch/performance-image17.png)

Nella barra dei menu fare clic su *Run*, fare clic su *Remote Start* e verificare che siano elencate le due VM secondarie di JMeter:

![](./media/guidance-elasticsearch/performance-image18.png)

A questo punto si è pronti iniziare il test delle prestazioni.

## Installazione e configurazione di Marvel

Il modello di avvio rapido di Elasticsearch per Azure installerà e configurerà automaticamente la versione corretta di Marvel se durante la creazione del cluster i parametri MARVEL e KIBANA sono stati impostati su true:

![](./media/guidance-elasticsearch/performance-image19.png)

Se si aggiunge Marvel a un cluster esistente, è necessario eseguire manualmente l'installazione e il processo sarà diverso a seconda se si usa Elasticsearch versione 1.7. x o 2. x, come descritto nelle procedure seguenti.

### Installazione di Marvel con Elasticsearch 1.73 o versione precedente

Se si usa Elasticsearch 1.7.3 o versione precedente, seguire questa procedura *in ogni nodo* del cluster:

- Accedere al nodo e passare alla home directory di Elasticsearch. In Linux la home directory è solitamente `/usr/share/elasticsearch`.

-  Eseguire il comando seguente per scaricare e installare il plug-in Marvel per Elasticsearch:

```bash
sudo bin/plugin -i elasticsearch/marvel/latest
```

- Arrestare e riavviare Elasticsearch nel nodo:

```bash
sudo service elasticsearch restart
```

- Per verificare che Marvel sia stato installato correttamente, aprire un Web browser e passare all'URL `http://<server>:9200/_plugin/marvel`. Sostituire `<server>` con il nome o l'indirizzo IP di qualsiasi server Elasticsearch disponibile nel cluster. Verificare che sia visualizzata una pagina simile alla seguente:

![](./media/guidance-elasticsearch/performance-image20.png)


### Installazione di Marvel con Elasticsearch 2.0.0 o versione successiva

Se si usa Elasticsearch 2.0.0 o versione successiva, seguire questa procedura *in ogni nodo* del cluster:

Accedere al nodo e passare alla home directory di Elasticsearch, in genere `/usr/share/elasticsearch`. Eseguire i comandi seguenti per scaricare e installare il plug-in Marvel per Elasticsearch:

```bash
sudo bin/plugin install license
sudo bin/plugin install marvel-agent
```

Arrestare e riavviare Elasticsearch nel nodo:

```bash
sudo service elasticsearch restart
```

Nella procedura seguente, sostituire `<kibana-version>` con 4.2.2 se si usa Elasticsearch 2.0.0 o Elasticsearch 2.0.1 oppure con 4.3.1 se si usa Elasticsearch 2.1.0 o versione successiva. Sostituire `<marvel-version>` con 2.0.0 se si usa Elasticsearch 2.0.0 o Elasticsearch 2.0.1 o con 2.1.0 se si usa Elasticsearch 2.1.0 o versione successiva. Eseguire le operazioni seguenti *in un nodo* del cluster:

Accedere al nodo e scaricare la build di Kibana appropriata per la versione di Elasticsearch dal [sito Web di download di Elasticsearch](https://www.elastic.co/downloads/past-releases), quindi estrarre il pacchetto:

```bash
wget https://download.elastic.co/kibana/kibana/kibana-<kibana-version>-linux-x64.tar.gz
tar xvzf kibana-<kibana-version>-linux-x64.tar.gz
```

Aprire la porta 5601 per accettare le richieste in ingresso:

```bash
sudo iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 5601 -j ACCEPT
```

Passare alla cartella config di Kibana (`kibana-<kibana-version>-linux-x64/config`), modificare il file `kibana.yml` e aggiungere la riga seguente. Sostituire `<server>` con il nome o l'indirizzo IP di un server Elasticsearch disponibile nel cluster:

```yaml
elasticsearch.url: "http://<server>:9200"
```

Passare alla cartella bin di Kibana (`kibana-<kibana-version>-linux-x64/bin`) ed eseguire il comando seguente per integrare il plug-in Marvel in Kibana:

```bash
sudo ./kibana plugin --install elasticsearch/marvel/<marvel-version>
```

Avviare Kibana:

```bash
sudo nohup ./kibana &
```

Per verificare l'installazione di Marvel, aprire un Web browser e passare all'URL `http://<server>:5601/app/marvel`. Sostituire `<server>` con il nome o l'indirizzo IP del server che esegue Kibana.

Verificare che venga visualizzata una pagina simile a quella riportata di seguito. Il nome del cluster sarà probabilmente diverso da quello illustrato nella figura.

![](./media/guidance-elasticsearch/performance-image21.png)

Fare clic sul collegamento che corrisponde al proprio cluster (elasticsearch210 nell'immagine precedente). Verrà visualizzata una pagina simile alla seguente:

![](./media/guidance-elasticsearch/performance-image22.png)

[Ottimizzazione delle prestazioni di inserimento dei dati per Elasticsearch in Azure]: guidance-elasticsearch-tuning-data-ingestion-performance.md

<!---HONumber=AcomDC_0224_2016-->