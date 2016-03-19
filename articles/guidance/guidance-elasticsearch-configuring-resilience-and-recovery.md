<properties
   pageTitle="Configurazione delle opzioni di resilienza e ripristino di Elasticsearch in Azure"
   description="Considerazioni relative alla resilienza e al ripristino per Elasticsearch."
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
   
# Configurazione delle opzioni di resilienza e ripristino di Elasticsearch in Azure

Questo articolo fa [parte di una serie](guidance-elasticsearch.md).

Una funzionalità chiave di Elasticsearch è il supporto fornito per garantire la resilienza in caso di errori dei nodi e/o di eventi relativi alle partizioni di rete. La replica è il modo più evidente per migliorare la resilienza di qualsiasi cluster. Consente infatti a Elasticsearch di garantire che sia disponibile più di una copia di qualsiasi elemento di dati in nodi diversi nel caso in cui un nodo diventasse inaccessibile. Se un nodo è temporaneamente non disponibile, gli altri nodi che contengono le repliche dei dati del nodo mancante possono rendere disponibili tali dati finché il problema non viene risolto. Nel caso di un problema a lungo termine, il nodo mancante può essere sostituito con uno nuovo ed Elasticsearch può ripristinare nel nuovo nodo i dati dalle repliche.

Questo documento riepiloga le opzioni di resilienza e ripristino disponibili con Elasticsearch ospitato in Azure e descrive alcuni aspetti importanti di un cluster Elasticsearch, che è opportuno prendere in considerazione per ridurre le possibilità di perdita dei dati e il prolungamento dei tempi di ripristino.

Questo articolo illustra anche alcuni test di esempio eseguiti per mostrare gli effetti dei diversi tipi di errori in un cluster Elasticsearch e il modo in cui il sistema risponde durante il ripristino.

Un cluster Elasticsearch usa le repliche per garantire la disponibilità e migliorare le prestazioni di lettura. Le repliche devono essere archiviate in diverse macchine virtuali dalle partizioni primarie che replicano. L'obiettivo è fare in modo che se la VM che ospita un nodo dati non funziona o non è più disponibile, il sistema possa continuare a funzionare usando le macchine virtuali che contengono le repliche.

## Uso di nodi master dedicati

Un nodo in un cluster Elasticsearch viene scelto come nodo master. Lo scopo di questo nodo è l'esecuzione delle operazioni di gestione del cluster, ad esempio:

- Rilevamento di nodi con errori e passaggio alle repliche.

- Rilocazione di partizioni per bilanciare il carico di lavoro dei nodi.

- Ripristino di partizioni quando un nodo viene riportato online.

È consigliabile considerare l'uso di nodi master dedicati nei cluster critici e assicurarsi che siano disponibili 3 nodi dedicati il cui solo ruolo sia quello di master. Questa configurazione riduce la quantità di operazioni a elevato utilizzo di risorse che questi nodi devono eseguire (non archiviano dati né gestiscono query) e contribuisce a migliorare la stabilità del cluster. Verrà scelto solo uno di questi nodi, ma dato che gli altri contengono una copia dello stato del sistema, possono subentrare in caso di errore del master designato.

## Controllo della disponibilità elevata con Azure: domini di aggiornamento e domini di errore 

Più macchine virtuali possono condividere lo stesso hardware fisico. In un data center di Azure un singolo rack può ospitare diverse macchine virtuali e tutte condividono una fonte di alimentazione e un commutatore di rete comuni. Un errore a livello di singolo rack può quindi influire su diverse macchine virtuali. Azure usa il concetto di domini di errore (FD, Fault Domain) per provare a distribuire questo rischio. Un dominio di errore corrisponde approssimativamente a un gruppo di VM che condividono lo stesso rack. Per assicurare che un errore a livello di rack non causi l'arresto anomalo di un nodo e contemporaneamente dei nodi che contengono tutte le relative repliche, è necessario assicurarsi che le VM siano distribuite tra i domini di errore.

In modo analogo, le macchine virtuali possono essere disattivate dal [controller di infrastruttura di Azure](https://azure.microsoft.com/documentation/videos/fabric-controller-internals-building-and-updating-high-availability-apps/) per eseguire aggiornamenti del sistema operativo e di manutenzione pianificati. Azure alloca le macchine virtuali a domini di aggiornamento (UD, Update Domain). Quando si verifica un evento di manutenzione pianificata, sono interessate solo le macchine virtuali in un singolo dominio di aggiornamento alla volta. Le VM in altri domini di aggiornamento rimangono in esecuzione fino a quando le VM nel dominio di aggiornamento che viene aggiornato non saranno riportate online. È quindi necessario assicurarsi che le VM che ospitano i nodi e le repliche appartengano a domini di aggiornamento diversi, quando possibile.

> [AZURE.NOTE] Per altre informazioni sulle macchine virtuali e sui domini di errore e di aggiornamento, vedere [Gestione della disponibilità delle macchine virtuali][].

Non è possibile allocare in modo esplicito una macchina virtuale a un dominio di aggiornamento e un dominio di errore specifici. Questa allocazione viene controllata da Azure al momento della creazione delle VM. Per altre informazioni, vedere il documento [Gestione della disponibilità delle macchine virtuali][]. È però possibile specificare che le macchine virtuali devono essere create come parte di un set di disponibilità. Le VM nello stesso set di disponibilità saranno distribuite tra domini di aggiornamento e domini di errore. Se si creano VM manualmente, Azure assocerà ogni set di disponibilità a due domini di errore e cinque domini di aggiornamento. Le VM saranno allocate a questi domini di errore e di aggiornamento ciclicamente, man mano che viene eseguito il provisioning di altre VM, come indicato di seguito:

- La prima VM con provisioning nel set di disponibilità viene inserita nel primo dominio di errore (FD 0) e nel primo dominio di aggiornamento (UD 0).
- La seconda VM con provisioning nel set di disponibilità verrà inserita in FD 1 e UD 1.
- La terza VM con provisioning nel set di disponibilità verrà inserita in FD 0 e UD 2. -La quarta VM con provisioning nel set di disponibilità verrà inserita in FD 1 e UD 3.
- La quinta VM con provisioning nel set di disponibilità verrà inserita in FD 0 e UD 4.
- La sesta VM con provisioning nel set di disponibilità verrà inserita in FD 1 e UD 0.
- La settima VM con provisioning nel set di disponibilità verrà inserita in FD 0 e UD 1.

> [AZURE.IMPORTANT] Se si creano macchine virtuali con Azure Resource Manager, è possibile allocare fino a 3 domini di errore e 20 domini di aggiornamento per ogni set di disponibilità. Questo è un valido motivo per ricorrere all'uso di Azure Resource Manager.

In generale, inserire tutte le VM che hanno lo stesso scopo nello stesso set di disponibilità, ma creare set di disponibilità diversi per le VM che eseguono funzioni diverse. Ciò significa che con Elasticsearch è necessario considerare la possibilità di creare set di disponibilità distinti almeno per:

- VM che ospitano nodi dati.
- VM che ospitano nodi client (se in uso).
- VM che ospitano nodi master.

È anche necessario assicurarsi che ogni nodo del cluster riconosca il dominio di aggiornamento e il dominio di errore a cui appartiene. Queste informazioni possono essere utili per assicurarsi che Elasticsearch non crei partizioni e le relative repliche negli stessi domini di errore e di aggiornamento, riducendo così il rischio che una partizione e le relative repliche vengano disattivate contemporaneamente. È possibile configurare un nodo Elasticsearch per riflettere la distribuzione di componenti hardware del cluster tramite la configurazione del [riconoscimento di allocazione delle partizioni](https://www.elastic.co/guide/en/elasticsearch/reference/current/allocation-awareness.html#allocation-awareness). Ad esempio, è possibile definire una coppia di attributi di nodo personalizzati denominati *faultDomain* e *updateDomain* nel file elasticsearch.yml, come indicato di seguito:

```yaml
node.faultDomain: \${FAULTDOMAIN}
node.updateDomain: \${UPDATEDOMAIN}
```

In questo caso, gli attributi vengono impostati usando i valori inclusi nelle variabili di ambiente *\\${FAULTDOMAIN}* e *\\${UPDATEDOMAIN}* all'avvio di Elasticsearch. È anche necessario aggiungere le voci seguenti al file Elasticsearch.yml per indicare che *faultDomain* e *updateDomain* sono attributi di riconoscimento di allocazione e specificare i set di valori accettabili per questi attributi:

```yaml
cluster.routing.allocation.awareness.force.updateDomain.values: 0,1,2,3,4
cluster.routing.allocation.awareness.force.faultDomain.values: 0,1
cluster.routing.allocation.awareness.attributes: updateDomain, faultDomain
```

È possibile usare il riconoscimento di allocazione delle partizioni insieme al [filtro di allocazione delle partizioni](https://www.elastic.co/guide/en/elasticsearch/reference/2.0/shard-allocation-filtering.html#shard-allocation-filtering) per specificare in modo esplicito i nodi che possono ospitare partizioni per un dato indice.

Se è necessario aumentare il numero di domini di errore e di aggiornamento in un set di disponibilità, è possibile creare macchine virtuali in set di disponibilità aggiuntivi. Si deve tuttavia sapere che i nodi in set di disponibilità diversi possono essere disattivati contemporaneamente per manutenzione. È consigliabile assicurarsi che ogni partizione e almeno una delle relative repliche siano contenute nello stesso set di disponibilità.

> [AZURE.NOTE] Attualmente è previsto un limite di 100 VM per set di disponibilità. Per informazioni, vedere [Sottoscrizione ad Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md).

### Backup e ripristino

L'uso delle repliche non fornisce la protezione completa da errori irreversibili, ad esempio l'eliminazione accidentale dell'intero cluster. È necessario assicurarsi che venga eseguito regolarmente il backup dei dati in un cluster e che sia stata provata e testata una strategia per il ripristino del sistema da questi backup.

Usare le [API Snapshot e Restore](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-snapshots.html) di Elasticsearch per eseguire il backup e il ripristino degli indici. Gli snapshot possono essere salvati in un file system condiviso. In alternativa, sono disponibili plug-in che consentono la scrittura di snapshot in HDFS, come il [plug-in HDFS](https://github.com/elasticsearch/elasticsearch-hadoop/tree/master/repository-hdfs), o nell'archiviazione di Azure, ovvero il [plug-in Azure Cloud](https://github.com/elasticsearch/elasticsearch-cloud-azure#azure-repository).

Quando si seleziona il meccanismo di archiviazione snapshot, considerare i punti seguenti:

- È possibile usare l'[archiviazione file di Azure](https://azure.microsoft.com/services/storage/files/) per implementare un file system condiviso e accessibile da tutti i nodi.

- Usare il plug-in HDFS solo se si esegue Elasticsearch insieme ad Hadoop.

- Con il plug-in HDFS è necessario disabilitare Java Security Manager in esecuzione all'interno dell'istanza Elasticsearch della JVM.

- Il plug-in HDFS supporta qualsiasi file system compatibile con HDFS, purché si usi la configurazione corretta di Hadoop con Elasticsearch.

  
## Gestione della connettività intermittente tra i nodi

Problemi di rete intermittenti, riavvii della VM dopo la manutenzione di routine nel data center e altri eventi simili possono rendere i nodi temporaneamente inaccessibili. In queste situazioni, in cui l'evento è probabilmente di breve durata, il sovraccarico di ribilanciamento delle partizioni si verifica due volte in rapida successione, una volta quando viene rilevato l'errore e di nuovo quando il nodo diventa visibile al master, e può diventare un sovraccarico significativo che influisce sulle prestazioni. È possibile impedire che l'inaccessibilità temporanea del nodo causi il ribilanciamento del cluster da parte del master impostando la proprietà *delayed\_timeout* di un indice o di tutti gli indici. L'esempio seguente imposta il ritardo su 5 minuti:

```http
PUT /_all/settings
{
	"settings": {
    "index.unassigned.node_left.delayed_timeout": "5m"
	}
}
```

Per altre informazioni, vedere l'articolo relativo al [ritardo dell'allocazione quando un nodo esce dal cluster](https://www.elastic.co/guide/en/elasticsearch/reference/current/delayed-allocation.html).

In una rete soggetta a interruzioni è anche possibile modificare i parametri che configurano un master, in modo che rilevi quando un altro nodo non è più accessibile. Questi parametri fanno parte del modulo [Zen Discovery](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-discovery-zen.html#modules-discovery-zen) fornito con Elasticsearch e possono essere impostati nel file Elasticsearch.yml. Ad esempio, il parametro *discovery.zen.fd.ping.retries* specifica quante volte un nodo master proverà a effettuare il ping di un altro nodo del cluster prima di stabilire che non è accessibile. L'impostazione predefinita di questo parametro è 3, ma è possibile modificarlo come indicato di seguito:

```yaml
discovery.zen.fd.ping_retries: 6
```

## Controllo del ripristino

Quando la connettività a un nodo viene ripristinata dopo un errore, le eventuali partizioni in quel nodo dovranno essere recuperate per consentirne l'aggiornamento. Per impostazione predefinita, Elasticsearch ripristina le partizioni nell'ordine seguente:

- Per data di creazione dell'indice in ordine inverso. Gli indici più recenti vengono recuperati prima di quelli precedenti.

- Per nome di indice in ordine inverso. Verranno ripristinati per primi gli indici con nomi alfanumericamente maggiori rispetto ad altri.

Se alcuni degli indici sono più importanti di altri, ma non soddisfano questi criteri, è possibile ignorare la precedenza degli indici impostando la proprietà *index.priority*. Gli indici con un valore superiore per questa proprietà saranno recuperati prima di quelli con un valore inferiore:

```http
PUT low_priority_index
{
	"settings": {
		"index.priority": 1
	}
}

PUT high_priority_index
{
	"settings": {
		"index.priority": 10
	}
}
```

Per altre informazioni, vedere l'articolo relativo alla [definizione di priorità per il ripristino degli indici](https://www.elastic.co/guide/en/elasticsearch/reference/2.0/recovery-prioritization.html#recovery-prioritization).

È possibile monitorare il processo di ripristino per uno o più indici tramite l'API *\_recovery*:

```http
GET /high_priority_index/_recovery?pretty=true
```

Per altre informazioni,vedere l'articolo relativo al [ripristino degli indici](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-recovery.html#indices-recovery).

> [AZURE.NOTE] Lo stato di un cluster con partizioni che richiedono il ripristino sarà *giallo* per indicare che non tutte le partizioni sono attualmente disponibili. Quando tutte le partizioni sono disponibili, lo stato del cluster ritornerà *verde*. Un cluster con stato *rosso* indica che una o più partizioni sono fisicamente mancanti. Potrebbe essere necessario ripristinare i dati da un backup.

## Prevenzione dello "split brain" 

Uno "split brain" può verificarsi se le connessioni tra i nodi non riescono. Se un nodo master diventa irraggiungibile per parte del cluster, si verifica un'elezione nel segmento di rete che rimane contattabile e un altro nodo diventa il master. In un cluster non configurato correttamente è possibile che per ogni parte del cluster siano disponibili master diversi, generando incoerenze o il danneggiamento dei dati. Questo fenomeno è noto come *"split brain"*.

È possibile ridurre le probabilità di uno "split brain" configurando la proprietà *minimum\_master\_nodes* del modulo di individuazione nel file elasticsearch.yml. Questa proprietà specifica quanti nodi devono essere disponibili per consentire l'elezione di un master. L'esempio seguente imposta il valore di questa proprietà su 2:

```yaml
discovery.zen.minimum_master_nodes: 2
```

Questo valore deve essere impostato sulla maggioranza più bassa del numero di nodi in grado di soddisfare il ruolo di master. Ad esempio, se il cluster ha 3 nodi master, *minimum\_master\_nodes* deve essere impostata su 2. Se si hanno 5 nodi master, *minimum\_master\_nodes* deve essere impostata su 3. Idealmente, si dovrà aver un numero dispari di nodi master.

**Nota:** è possibile che si verifichi uno "split brain" se vengono avviati contemporaneamente più nodi master nello stesso cluster. Anche se questa situazione si verifica raramente, è possibile evitarla avviando i nodi in serie con un breve ritardo, ad esempio 5 secondi, tra di essi.

## Gestione degli aggiornamenti in sequenza

Se si esegue manualmente un aggiornamento dei nodi, ad esempio la migrazione a una versione più recente o l'esecuzione di una patch, potrebbe essere necessario intervenire sui singoli nodi che dovranno quindi essere portati offline, mantenendo disponibile il resto del cluster. In questo caso, è possibile implementare il processo seguente:

Assicurarsi che la riallocazione delle partizioni sia sufficientemente ritardata per impedire che il master designato proceda al ribilanciamento delle partizioni da un nodo mancante nel resto del cluster. Per impostazione predefinita, la riassegnazione delle partizioni viene ritardata di 1 minuto, ma è possibile aumentare la durata, se è probabile che un nodo non sia disponibile per un periodo più lungo. L'esempio seguente aumenta il ritardo a 5 minuti:

```http
PUT /_all/_settings
{
	"settings": {
		"index.unassigned.node_left.delayed_timeout": "5m"
	}
}
```

> [AZURE.IMPORTANT] È anche possibile disabilitare completamente la riallocazione delle partizioni impostando la proprietà *cluster.routing.allocation.enable* del cluster su *none*. È tuttavia consigliabile evitare di usare questo approccio se si prevede che vengano creati nuovi indici mentre il nodo è offline, perché ciò può causare un errore di allocazione dell'indice e la conseguente visualizzazione di un cluster con stato rosso.

Arrestare Elasticsearch nel nodo da gestire. Se Elasticsearch è in esecuzione come servizio, è possibile interrompere il processo in modo controllato tramite un comando del sistema operativo. L'esempio seguente illustra come interrompere il servizio Elasticsearch in un singolo nodo in Ubuntu:

```bash
service elasticsearch stop
```

In alternativa, è possibile usare l'API Shutdown direttamente nel nodo:

```http
POST /_cluster/nodes/_local/_shutdown
```

Eseguire la manutenzione necessaria del nodo, quindi riavviarlo e attendere che venga aggiunto al cluster.

Riabilitare l'allocazione di partizioni:

```http
PUT /_cluster/settings
{
	"transient": {
		"cluster.routing.allocation.enable": "all"
	}
}
```

> [AZURE.NOTE] Se è necessario mantenere più nodi, ripetere i passaggi 2, 3 e 4 in ogni nodo prima di riabilitare l'allocazione di partizioni.

Se possibile, arrestare l'indicizzazione di nuovi dati durante il processo. Ciò consente di ridurre al minimo il tempo di ripristino quando i nodi vengono riportati online e aggiunti di nuovo al cluster.

Prestare attenzione agli aggiornamenti automatici di elementi come la JVM, per i quali sarebbe opportuno disabilitare tali aggiornamenti, specialmente quando Elasticsearch viene eseguito in Windows. L'agente di aggiornamento Java può scaricare automaticamente la versione più recente di Java, ma può richiedere il riavvio di Elasticsearch per rendere effettivo l'aggiornamento. Ciò può comportare la perdita temporanea non coordinata di nodi, a seconda della configurazione dell'agente di aggiornamento di Java. Questa situazione può anche determinare la creazione di istanze diverse di Elasticsearch nello stesso cluster che eseguono versioni diverse della JVM, con possibili problemi di compatibilità.

## Test e analisi di resilienza e ripristino di Elasticsearch

Questa sezione descrive una serie di test eseguiti per valutare la resilienza e il ripristino di un cluster Elasticsearch costituito da tre nodi dati e tre nodi master.

Sono stati testati quattro scenari:

1.  Errore del nodo e riavvio senza perdita di dati. Un nodo dati viene arrestato e riavviato dopo 5 minuti. Elasticsearch è stato configurato in modo da non riallocare le partizioni mancanti durante questo intervallo, quindi non si avranno operazioni di I/O aggiuntive per lo spostamento delle partizioni. Al riavvio del nodo il processo di ripristino riaggiorna le partizioni in quel nodo.

2.  Errore del nodo con grave perdita dei dati. Un nodo dati viene arrestato e i dati che contiene vengono cancellati per simulare un errore irreversibile del disco. Il nodo viene quindi riavviato, dopo 5 minuti, agendo di fatto come nodo sostitutivo di quello originale. Il processo di ripristino richiede la ricompilazione dei dati mancanti per questo nodo e potrebbe comportare la rilocazione di partizioni contenute in altri nodi.

3.  Errore del nodo e riavvio senza perdita di dati, ma con riallocazione di partizioni. Un nodo dati viene arrestato e le partizioni che contiene vengono riallocate ad altri nodi. Il nodo viene quindi riavviato e si verifica un'ulteriore riallocazione per ribilanciare il cluster.

4.  Aggiornamenti in sequenza. Ogni nodo del cluster viene arrestato e riavviato dopo un breve intervallo per simulare il riavvio dei computer dopo un aggiornamento software. Viene arrestato un solo nodo alla volta. Le partizioni non vengono riallocate mentre un nodo è inattivo.

Ogni scenario è stato sottoposto allo stesso carico di lavoro costituito da una combinazione di attività di inserimento dei dati, aggregazioni e query di filtro, mentre i nodi sono stati portati offline e quindi ripristinati. Le operazioni di inserimento in blocco nel carico di lavoro hanno archiviato ognuna 1000 documenti e sono state eseguite su un unico indice, mentre le aggregazioni e le query di filtro hanno usato un indice separato contenente diversi milioni di documenti. Questo approccio ha consentito di valutare le prestazioni delle query separatamente dagli inserimenti in blocco. Ogni indice era composto da cinque partizioni e una replica.

Le seguenti sezioni riepilogano i risultati dei test, annotando ogni riduzione delle prestazioni quando un nodo è offline o in fase di ripristino, nonché gli eventuali errori segnalati. I risultati sono presentati graficamente, evidenziando i punti in cui uno o più nodi sono mancanti e stimando il tempo impiegato dal sistema per il ripristino completo e ottenere un livello di prestazioni simile a quello presente prima che i nodi venissero portati offline.

> [AZURE.NOTE] I test harness usati per eseguire questi test sono disponibili online. È possibile adattare e usare questi harness per verificare la resilienza e la capacità di ripristino delle configurazioni cluster personalizzate. Per altre informazioni, vedere [Esecuzione di test automatici della resilienza in Elasticsearch][].

## Errore del nodo e riavvio senza perdita di dati: risultati

<!-- TODO; reformat this pdf for display inline -->

I risultati del test sono visualizzati nel file [ElasticsearchRecoveryScenario1.pdf](https://github.com/mspnp/azure-guidance/blob/master/figures/Elasticsearch/ElasticsearchRecoveryScenario1.pdf). I grafici mostrano il profilo delle prestazioni del carico di lavoro e delle risorse fisiche per ogni nodo del cluster. La parte iniziale dei grafici mostra la normale esecuzione del sistema per circa 20 minuti. A quel punto il nodo 0 viene arrestato per 5 minuti prima di essere riavviato. Vengono illustrate le statistiche per altri 20 minuti. Il ripristino e la stabilizzazione del sistema richiedono circa 10 minuti. Questo comportamento è illustrato dalla frequenza delle transazioni e dai tempi di risposta per diversi carichi di lavoro.

Tenere presente quanto segue:

- Durante il test non sono stati segnalati errori. Non si sono verificate perdite di dati e tutte le operazioni sono state completate correttamente.

- La frequenza delle transazioni per tutti e tre i tipi di operazione, ovvero inserimento in blocco, query di aggregazione e query di filtro, è diminuita e i tempi di risposta medi sono aumentati mentre il nodo 0 era offline.

- Durante il periodo di ripristino, la frequenza delle transazioni e i tempi di risposta per le operazioni delle query di aggregazione e di filtro sono stati ristabiliti gradualmente. Le prestazioni di inserimento in blocco si sono ristabilite per un breve intervallo, per poi diminuire. Questo comportamento è probabilmente dovuto al volume di dati che causa la crescita dell'indice usato per l'inserimento in blocco, con il conseguente rallentamento della frequenza delle transazioni ancora prima che il nodo 0 sia portato offline.

- Il grafico relativo all'utilizzo della CPU per il nodo 0 mostra un'attività ridotta durante la fase di ripristino. Ciò è dovuto all'aumento dell'attività del disco e della rete causato dal meccanismo di ripristino. Il nodo deve recuperare tutti i dati non acquisiti mentre era offline e aggiornare le partizioni che contiene.

- Le partizioni per gli indici non sono distribuite in modo esattamente uniforme in tutti i nodi. Sono presenti due indici che includono 5 partizioni e una replica ognuno, per un totale di 20 partizioni. Due nodi conterranno quindi 6 partizioni, mentre gli altri due ne contengono 7 ognuno. Ciò risulta evidente nel grafico relativo all'utilizzo della CPU durante il periodo iniziale di 20 minuti. L'attività del nodo 0 è inferiore rispetto agli altri due. Al termine del ripristino, si verifica un'apparente attività di scambio perché il carico del nodo 2 sembra diventare quello leggermente più ridotto.

    
## Errore del nodo con grave perdita dei dati: risultati

<!-- TODO; reformat this pdf for display inline -->

I risultati del test sono illustrati nel file [ElasticsearchRecoveryScenario2.pdf](https://github.com/mspnp/azure-guidance/blob/master/figures/Elasticsearch/ElasticsearchRecoveryScenario2.pdf). Come per il primo test, la parte iniziale dei grafici mostra la normale esecuzione del sistema per circa 20 minuti. A quel punto il nodo 0 viene arrestato per 5 minuti. Durante questo intervallo, i dati di Elasticsearch in questo nodo vengono rimossi, simulando una grave perdita dei dati, prima del riavvio del nodo. Il ripristino completo richiede circa 12-15 minuti prima del ripristino dei livelli delle prestazioni rilevati precedentemente al test.

Tenere presente quanto segue:

- Durante il test non sono stati segnalati errori. Non si sono verificate perdite di dati e tutte le operazioni sono state completate correttamente.

- La frequenza delle transazioni per tutti e tre i tipi di operazione, ovvero inserimento in blocco, query di aggregazione e query di filtro, è diminuita e i tempi di risposta medi sono aumentati mentre il nodo 0 era offline. A questo punto, il profilo delle prestazioni del test è simile al primo scenario, ma ciò non è sorprendente perché gli scenari, fin qui, sono uguali.

- Durante il periodo di ripristino la frequenza delle transazioni e i tempi di risposta sono stati ripristinati, anche se le cifre sono risultate molto più volatili. Questo è molto probabilmente dovuto alle attività aggiuntive eseguite dai nodi del cluster per fornire i dati necessari per il ripristino delle partizioni mancanti. Questo lavoro aggiuntivo risulta evidente nei grafici relativi all'utilizzo della CPU, all'attività del disco e all'attività di rete.

- Il grafico relativo all'utilizzo della CPU per i nodi 0 e 1 mostra un'attività ridotta durante la fase di ripristino. Ciò è dovuto all'aumento dell'attività del disco e di rete per il processo di ripristino. Nel primo scenario solo il nodo in fase di ripristino ha mostrato questo comportamento, ma in questo scenario è probabile che la maggior parte dei dati mancanti per il nodo 0 venga ripristinata dal nodo 1.

- L'attività di I/O per il nodo 0 è effettivamente ridotta rispetto al primo scenario. Questo comportamento può dipendere dall'efficienza dell'attività di I/O che copia semplicemente i dati per un'intera partizione invece della serie di richieste di I/O più piccole necessarie per aggiornare una partizione esistente.

- L'attività di rete per tutti e tre i nodi indica la presenza di picchi di attività durante la trasmissione e la ricezione dei dati tra nodi. Nello scenario 1 solo il nodo 0 presentava una tale attività di rete, che però veniva mantenuta per un periodo più lungo. Anche in questo caso, la differenza può essere dovuta all'efficienza ottenuta con la trasmissione di tutti i dati per una partizione come una singola richiesta, invece della serie di richieste più piccole ricevute durante il ripristino di una partizione.

## Errore del nodo e riavvio con riallocazione di partizioni: risultati

<!-- TODO; reformat this pdf for display inline -->

I risultati del test sono illustrati nel file [ElasticsearchRecoveryScenario3.pdf](https://github.com/mspnp/azure-guidance/blob/master/figures/Elasticsearch/ElasticsearchRecoveryScenario3.pdf). Come per il primo test, la parte iniziale dei grafici mostra la normale esecuzione del sistema per circa 20 minuti. A quel punto il nodo 0 viene arrestato per 5 minuti. Il cluster Elasticsearch prova ora a ricreare le partizioni mancanti e a ribilanciare le partizioni tra i nodi rimanenti. Dopo 5 minuti il nodo 0 viene riportato online e il cluster deve di nuovo ribilanciare le partizioni. Le prestazioni vengono ripristinate dopo 12-15 minuti.

Tenere presente quanto segue:

- Durante il test non sono stati segnalati errori. Non si sono verificate perdite di dati e tutte le operazioni sono state completate correttamente.

- La frequenza delle transazioni per tutti e tre i tipi di operazione, ovvero inserimento in blocco, query di aggregazione e query di filtro, è diminuita e i tempi di risposta medi sono aumentati significativamente mentre il nodo 0 era offline rispetto ai due test precedenti. Ciò è dovuto alla maggiore attività del cluster per ricreare le partizioni mancanti e ribilanciare il cluster, come evidenziato dall'aumento delle cifre per l'attività del disco e di rete per i nodi 1 e 2 in questo intervallo.

- Durante il periodo successivo al ripristino online del nodo 0 la frequenza delle transazioni e i tempi di risposta rimangono volatili.

- I grafici relativi all'attività del disco e all'utilizzo della CPU per il nodo 0 mostrano un'azione iniziale molto ridotta durante la fase di ripristino. Questo avviene perché a questo punto il nodo 0 non gestisce dati. Dopo un intervallo di circa 5 minuti, l'azione del nodo riprende come risulta dall'aumento improvviso dell'attività della CPU, del disco e di rete. Questo è probabilmente dovuto alla distribuzione delle partizioni tra i nodi dal parte del cluster. Il nodo 0 mostra quindi un'attività normale.
  
## Aggiornamenti in sequenza: risultati

<!-- TODO; reformat this pdf for display inline -->

I risultati del test nel file [ElasticsearchRecoveryScenario4.pdf](https://github.com/mspnp/azure-guidance/blob/master/figures/Elasticsearch/ElasticsearchRecoveryScenario4.pdf) mostrano come ogni nodo venga portato offline e quindi ripristinato nuovamente in successione. Ogni nodo viene arrestato per 5 minuti prima di essere riavviato, quindi viene arrestato il nodo successivo nella sequenza.

Tenere presente quanto segue:

- Mentre ogni nodo è stato arrestato e riavviato, le prestazioni in termini di velocità effettiva e tempi di risposta rimangono ragionevolmente uniformi.

- L'attività del disco per ogni nodo aumenta per un breve intervallo di tempo mentre viene riportato online. Ciò è probabilmente dovuto al processo di ripristino che esegue il roll forward di tutte le modifiche che si sono verificate mentre il nodo era inattivo.

- Quando un nodo viene portato offline, si verificano picchi dell'attività di rete nei nodi rimanenti. I picchi vengono rilevati anche quando un nodo viene riavviato.

- Dopo l'arresto e il riavvio finale del nodo, il sistema entra in un periodo di volatilità significativa. Ciò è probabilmente causato dal processo di ripristino che deve sincronizzare le modifiche in ogni nodo e verificare che tutte le repliche e le partizioni corrispondenti siano coerenti. A un certo punto, il lavoro richiesto causa il timeout e l'errore delle operazioni di inserimento in blocco successive. Ecco gli errori segnalati per ogni caso:

```
Failure -- BulkDataInsertTest17(org.apache.jmeter.protocol.java.sampler.JUnitSampler$AnnotatedTestCase): java.lang.AssertionError: failure in bulk execution:
[1]: index [systwo], type [logs], id [AVEg0JwjRKxX_sVoNrte], message [UnavailableShardsException[[systwo][2] Primary shard is not active or isn't assigned to a known node. Timeout: [1m], request: org.elasticsearch.action.bulk.BulkShardRequest@787cc3cd]]

```

La sperimentazione successiva ha dimostrato che l'introduzione di un ritardo di pochi minuti tra l'arresto e il riavvio di ogni nodo ha eliminato questo errore, che probabilmente era causato da una contesa tra il processo di ripristino che prova a ripristinare più nodi contemporaneamente e le operazioni di inserimento in blocco che provano ad archiviare migliaia di nuovi documenti.


## Riepilogo

I test eseguiti indicano che:

- Elasticsearch è altamente resiliente alle più comuni modalità di errore che possono verificarsi in un cluster.

- Elasticsearch può eseguire il ripristino rapidamente se in un cluster progettato correttamente si verifica una grave perdita dei dati in un nodo. Questo può accadere se si configura Elasticsearch per il salvataggio dei dati nell'archiviazione temporanea e quindi viene effettuato di nuovo il provisioning del nodo dopo un riavvio. I risultati mostrano che anche in questo caso i rischi che derivano dall'uso dell'archiviazione temporanea sono per lo più superati dai vantaggi in termini di prestazioni offerti da questa classe di archiviazione.

- Nei primi tre scenari non si sono verificati errori nei carichi di lavoro di inserimento in blocco, aggregazione e query di filtro simultanei mentre un nodo veniva portato offline e ripristinato.

- Solo lo scenario 4 indica una potenziale perdita di dati, che tuttavia ha interessato solo i nuovi dati aggiunti. Nelle applicazioni che eseguono l'inserimento dei dati è buona norma ridurre questa probabilità ripetendo le operazioni di inserimento non riuscite, perché il tipo di errore segnalato è molto probabilmente temporaneo.

- I risultati del test 4 mostrano anche che se si esegue la manutenzione pianificata dei nodi in un cluster, si può avere un vantaggio in termini di prestazioni impostando un intervallo di alcuni minuti tra un arresto e riavvio di un nodo e il successivo. In una situazione non pianificata, ad esempio nel caso di un arresto e riavvio dei nodi nel data center dopo un aggiornamento del sistema operativo, si ha meno controllo sulla modalità e il momento in cui i nodi vengono arrestati e riavviati. La contesa che si verifica quando Elasticsearch prova a ripristinare lo stato del cluster, dopo le interruzioni in sequenza dei nodi, può causare timeout ed errori.

[Gestione della disponibilità delle macchine virtuali]: ../articles/virtual-machines/virtual-machines-manage-availability.md
[Esecuzione di test automatici della resilienza in Elasticsearch]: guidance-elasticsearch-running-automated-resilience-tests.md

<!---HONumber=AcomDC_0224_2016-->