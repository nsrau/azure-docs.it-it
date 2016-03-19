<properties
   pageTitle="Esecuzione di test automatici della resilienza in Elasticsearch | Microsoft Azure"
   description="Descrizione di come è possibile eseguire i test di resilienza nel proprio ambiente."
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

# Esecuzione di test automatici della resilienza in Elasticsearch

Questo articolo fa [parte di una serie](guidance-elasticsearch.md).

[Test di resilienza e ripristino] descrive una serie di test eseguiti in un cluster Elasticsearch di esempio per determinare in che modo il sistema ha risposto ad alcune forme comuni di errore e come è avvenuto il ripristino. Sono stati testati quattro scenari:

- **Errore del nodo e riavvio senza perdita di dati**. Un nodo dati viene arrestato e riavviato dopo 5 minuti. Elasticsearch è stato configurato in modo da non riallocare le partizioni mancanti durante questo intervallo, quindi non si avranno operazioni di I/O aggiuntive per lo spostamento delle partizioni. Al riavvio del nodo il processo di ripristino riaggiorna le partizioni in quel nodo.

- **Errore del nodo con grave perdita dei dati**. Un nodo dati viene arrestato e i dati che contiene vengono cancellati per simulare un errore irreversibile del disco. Il nodo viene quindi riavviato, dopo 5 minuti, agendo di fatto come nodo sostitutivo di quello originale. Il processo di ripristino richiede la ricompilazione dei dati mancanti per questo nodo e potrebbe comportare la rilocazione di partizioni contenute in altri nodi.

- **Errore del nodo e riavvio senza perdita di dati, ma con riallocazione di partizioni**. Un nodo dati viene arrestato e le partizioni che contiene vengono riallocate ad altri nodi. Il nodo viene quindi riavviato e si verifica un'ulteriore riallocazione per ribilanciare il cluster.

- **Aggiornamenti in sequenza**. Ogni nodo del cluster viene arrestato e riavviato dopo un breve intervallo per simulare il riavvio dei computer dopo un aggiornamento software. Viene arrestato un solo nodo alla volta. Le partizioni non vengono riallocate mentre un nodo è inattivo.

I test sono stati inseriti in uno script per consentirne l'esecuzione automatica. Questo documento descrive come è possibile ripetere i test nel proprio ambiente.

## Prerequisiti

I test automatici richiedono gli elementi seguenti:

- Un cluster Elasticsearch.

- Una configurazione dell'ambiente JMeter descritto nell'articolo relativo alle [informazioni aggiuntive sui test delle prestazioni].

- Le aggiunte seguenti installate solo nella VM master di JMeter.

    - Java Runtime 7.

    - Nodejs 4.x.x o versione successiva.

    - Strumenti da riga di comando di Git.

## Funzionamento degli script

Gli script di test sono concepiti per essere eseguiti nella VM master di JMeter Quando si seleziona un test da eseguire, gli script eseguono la sequenza di operazioni seguente:

1.  Avviano un piano di test JMeter passando i parametri specificati.

2.  Copiano uno script che esegue le operazioni richieste dal test in una VM specificata del cluster. Può trattarsi di qualsiasi VM con un indirizzo IP pubblico o della VM *Jumpbox* se il cluster è stato creato con il [modello di avvio rapido di Elasticsearch in Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/elasticsearch).

3.  Eseguire lo script nella macchina virtuale o Jumpbox.

L'immagine seguente illustra la struttura dell'ambiente di test e del cluster Elasticsearch. Si noti che gli script di test usano SSH per connettersi a ogni nodo del cluster ed eseguire varie operazioni di Elasticsearch come l'arresto o il riavvio di un nodo.

![](./media/guidance-elasticsearch/resilience-testing1.png)

## Configurazione dei test di JMeter

Prima di eseguire i test di resilienza, è consigliabile compilare e distribuire i test JUnit disponibili nella cartella *resiliency/jmeter/tests*. A questi test fa riferimento il piano di test di JMeter. Per altre informazioni, vedere la procedura relativa all'importazione di un progetto di test JUnit esistente in Eclipse in [Distribuzione di un campionatore JUnit per JMeter per il test delle prestazioni di Elasticsearch][].

Esistono due versioni dei test JUnit disponibili nelle cartelle seguenti:

- **Elasticsearch17.** Il progetto in questa cartella genera il file Elasticsearch17.jar. Usare il file JAR per il test di Elasticsearch versioni 1.7.x

- **Elasticsearch20**. Il progetto in questa cartella genera il file Elasticsearch20.jar. Usare il file JAR per il test di Elasticsearch versione 2.0.0.0 e successive

Copiare il file JAR appropriato insieme al resto delle dipendenze nelle macchine JMeter. Il processo è descritto nella procedura di distribuzione di un test JUnit in JMeter nell'articolo relativo alle [considerazioni per JMeter].

## Configurazione della sicurezza delle macchine virtuali per ogni nodo

Gli script di test richiedono l'installazione di un certificato di autenticazione in ogni nodo Elasticsearch nel cluster. In questo modo gli script vengono eseguiti automaticamente senza richiedere un nome utente o una password quando si connettono a VM diverse.

Iniziare accedendo a uno dei nodi del cluster Elasticsearch, o alla VM Jumpbox, quindi eseguire il comando seguente per generare una chiave di autenticazione:

```Shell
ssh-keygen -t rsa
```

Durante la connessione al nodo Elasticsearch, o alla Jumpbox, eseguire i comandi seguenti per ogni nodo del cluster Elasticsearch. Sostituire `<username>` con il nome di un utente valido in ogni macchina virtuale, quindi sostituire `<nodename>` con il nome DNS dell'indirizzo IP della macchina virtuale che ospita il nodo Elasticsearch. Si noti che verrà richiesta la password dell'utente durante l'esecuzione di questi comandi. Per altre informazioni, vedere la pagina relativa all'[accesso a SSH senza password](http://www.linuxproblem.org/art_9.html):

```Shell
ssh <username>@<nodename> mkdir -p .ssh (
cat .ssh/id\_rsa.pub | ssh <username>*@<nodename> 'cat &gt;&gt; .ssh/authorized\_keys'
```

## Download e configurazione degli script di test

Gli script di test vengono forniti in un repository Git. Usare la procedura seguente per scaricare e configurare gli script.

Nel macchina Master JMeter in cui si eseguirà il test aprire una finestra del desktop Git (Git BASH) e clonare il repository che contiene gli script, come indicato di seguito:

```Shell
git clone https://github.com/mspnp/azure-guidance.git
```

Passare alla cartella *resiliency-tests* ed eseguire il comando seguente per installare le dipendenze richieste per eseguire i test:

```Shell
npm install
```

Se la VM master di JMeter è in esecuzione in Windows, [scaricare PLINK](http://the.earth.li/~sgtatham/putty/latest/x86/plink.exe) e copiarlo nella cartella *resiliency-tests/lib*.

Se la VM master di JMeter è in esecuzione in Linux, non è necessario scaricare PLINK ma occorre configurare SSH senza password tra la VM master di JMeter e il nodo Elasticsearch o la Jumpbox usata seguendo i passaggi descritti nella procedura [Configurazione della sicurezza delle macchine virtuali per ogni nodo](#configuring-vm-security-for-each-node).

Modificare il file `config.js` e i parametri di configurazione seguenti in modo che corrispondano al proprio ambiente di test e al cluster Elasticsearch. Questi parametri sono comuni a tutti i test:

| Nome | Descrizione | Valore predefinito |
| ---- | ----------- | ------------- |
| `jmeterPath` | Percorso locale in cui si trova jmeter. | `C:/apache-jmeter-2.13` |
| `resultsPath` | Directory relativa in cui lo script scarica il risultato. | `results` |
| `verbose` | Indica se lo script restituisce o meno output in modalità dettagliata. | `true` |
| `remote` | Indica se i test jmeter vengono eseguiti localmente o in server remoti. | `true` |
| `cluster.clusterName` | Nome del cluster Elasticsearch. | `elasticsearch` |
| `cluster.jumpboxIp` | Indirizzo IP della VM Jumpbox. |-| 
| `cluster.username` | Utente amministratore creato durante la distribuzione del cluster. |-| 
| `cluster.password` | Password per l'utente amministratore. |-| 
| `cluster.loadBalancer.ip` | Indirizzo IP del servizio di bilanciamento del carico di Elasticsearch. |-| 
| `cluster.loadBalancer.url` | URL di base del servizio di bilanciamento del carico. |-|

## Esecuzione dei test

Passare alla cartella *resiliency-tests* ed eseguire il comando seguente:

```Shell
node app.js
```

Verrà visualizzato il menu seguente:

![](./media/guidance-elasticsearch/resilience-testing2.png)

Immettere il numero dello scenario che si vuole eseguire: `11`, `12`, `13` o `21`.

Dopo aver selezionato uno scenario, il test verrà eseguito automaticamente. I risultati vengono archiviati come set di file CSV in una cartella creata nella directory *results*. Ogni esecuzione ha la propria cartella di risultati. È possibile usare Excel per analizzare i dati e creare un grafico.

[Running Elasticsearch on Azure]: guidance-elasticsearch-running-on-azure.md
[Tuning Data Ingestion Performance for Elasticsearch on Azure]: guidance-elasticsearch-tuning-data-ingestion-performance.md
[informazioni aggiuntive sui test delle prestazioni]: guidance-elasticsearch-performance-testing-environment.md
[JMeter guidance]: guidance-elasticsearch-implementing-jmeter.md
[considerazioni per JMeter]: guidance-elasticsearch-deploy-jmeter-junit-sampler.md
[Query aggregation and performance]: guidance-elasticsearch-query-aggregation-performance.md
[Resilience and Recovery]: guidance-elasticsearch-resilience-recovery.md
[Test di resilienza e ripristino]: guidance-elasticsearch-resilience-testing.md
[Distribuzione di un campionatore JUnit per JMeter per il test delle prestazioni di Elasticsearch]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md

<!---HONumber=AcomDC_0224_2016-->
