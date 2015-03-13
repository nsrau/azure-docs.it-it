<properties 
	pageTitle="Come usare CoreOS in Azure" 
	description="Descrive CoreOS, come creare una macchina virtuale CoreOS in Azure e il suo uso di base." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="squillace" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.tgt_pltfrm="vm-linux" 
	ms.workload="infrastructure-services" 
	ms.date="10/27/2014" 
	ms.author="rasquill"/>

<!--Si tratta di un modello di base che illustra come usare il markdown per creare un argomento che include un sommario, sezioni con sottotitoli, collegamenti ad altri argomenti azure.microsoft.com, collegamenti ad altri siti, testo in grassetto, testo in corsivo, elenchi puntati e numerati, frammenti di codice e immagini. Per un markdown più interessante, trovare un argomento pubblicato e copiare il markdown o l'HTML desiderato. Per altre informazioni sull'uso del markdown, vedere http://sharepoint/sites/azurecontentguidance/wiki/Pages/Content%20Guidance%20Wiki%20Home.aspx.-->

<!--Sezione proprietà (riportata in precedenza): questa sezione è necessaria in tutti gli argomenti. Completare la sezione. Per informazioni dettagliate, vedere http://sharepoint/sites/azurecontentguidance/wiki/Pages/Markdown%20tagging%20-%20add%20a%20properties%20section%20to%20your%20markdown%20file%20to%20specify%20metadata%20values.aspx. -->

<!-- Tags section (above): this is required in all topics. Please fill it out! See http://sharepoint/sites/azurecontentguidance/wiki/Pages/Markdown%20tagging%20-%20add%20a%20tags%20section%20to%20your%20markdown%20file%20to%20specify%20metadata%20for%20reporting.aspx for details. -->

<!--The next line, with one pound sign at the beginning, is the page title--> 
# Come usare CoreOS in Azure

Questo argomento descrive [CoreOS] e mostra come creare un cluster di tre macchine virtuali CoreOS in Azure come guida introduttiva per la comprensione di CoreOS. Usa gli elementi di base delle distribuzioni CoreOS e gli esempi contenuti in [CoreOS con Azure], nell'[esercitazione su CoreOS di Tim Park] e nell'[esercitazione su CoreOS di Patrick Chanezon] per mostrare i requisiti minimi assoluti per comprendere la struttura di base di una distribuzione CoreOS e ottenere un cluster di tre macchine virtuali eseguite correttamente. 

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->
Questo argomento contiene le seguenti sezioni:

+ [CoreOS, cluster e contenitori di Linux]
+ [Considerazioni relative alla sicurezza]
+ [Come usare CoreOS in Azure]
+ [Passaggi successivi]


## <a id='intro'>CoreOS, cluster e contenitori di Linux</a>

CoreOS è una versione leggera di Linux progettata per supportare la creazione rapida di cluster di macchine virtuali potenzialmente molto grandi che usano contenitori di Linux, inclusi i contenitori [Docker], come unico meccanismo di creazione dei pacchetti. CoreOS è progettato per supportare:

+ un livello molto alto di automazione
+ una distribuzione delle applicazioni più facile e coerente
+ scalabilità a livello di applicazione e di sistema

A livello generale le funzionalità CoreOS che supportano questi obiettivi sono:

1. Sistema a un pacchetto: CoreOS esegue solo immagini del contenitore di Linux in esecuzione nei contenitori di Linux per assicurare velocità, uniformità e facilità di distribuzione
2. Aggiornamenti al sistema operativo eseguiti in modo atomico in modo che i sistemi operativi vengano aggiornati come un'unica entità e se ne possa eseguire facilmente il rollback a uno stato noto
3. Daemon (servizi) predefiniti [etcd](https://github.com/coreos/etcd) e [fleet](https://github.com/coreos/fleet) per la comunicazione e la gestione dinamica di VM e cluster

Si tratta di una descrizione generale di CoreOS e delle relative funzionalità. Per informazioni più complete su CoreOS, vedere la [panoramica di CoreOS].

## <a id='security'>Considerazioni relative alla sicurezza</a>
Attualmente CoreOS presuppone che gli utenti con accesso SSH al cluster dispongano delle autorizzazioni per gestirlo. Ne risulta che i cluster CoreOS senza modifiche sono ideali per gli ambienti di test e sviluppo, ma richiedono ulteriori misure di sicurezza per l'uso in ambienti di produzione. 

## <a id='usingcoreos'>Come usare CoreOS in Azure</a>

Questa sezione descrive come creare un servizio cloud di Azure con tre macchine virtuali CoreOS usando l'[interfaccia multipiattaforma di Azure (xplat-cli)]. I passaggi di base sono i seguenti

1. Creare i certificati SSH e le chiavi per proteggere la comunicazione con la macchina virtuale CoreOS
2. Ottenere l'ID etcd del cluster per l'intercomunicazione
3. Creare un file cloud-config in formato [YAML]
4. Usare xplat-cli per creare un nuovo servizio cloud di Azure e tre macchine virtuali CoreOS
5. Testare il cluster CoreOS dalla macchina virtuale di Azure
6. Testare il cluster CoreOS da localhost 

### Creare chiavi pubbliche e private per la comunicazione
 
Usare le istruzioni presenti in [Come usare SSH con Linux in Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-ssh-key/) per creare una chiave pubblica e una chiave privata per SSH. I passaggi di base sono contenuti nelle istruzioni seguenti. Queste chiavi vengono usate per connettersi alle macchine virtuali nel cluster e verificare che funzionino correttamente e comunichino tra di loro.

> [AZURE.NOTE] In questo argomento si presuppone che non si disponga delle chiavi e che si debbano creare i file **`myPrivateKey.pem`** e **`myCert.pem`** per maggiore chiarezza. Se una coppia di chiavi pubblica e privata è già salvata in **`~/.ssh/id_rsa`**, è possibile digitare semplicemente `openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out myCert.pem` per ottenere il file PEM necessario per il caricamento in Azure.

1. In una directory di lavoro digitare `openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem` per creare la chiave privata e il certificato X.509 associato. 

2. Per verificare che il proprietario della chiave privata possa leggere o scrivere nel file, digitare `chmod 600 myPrivateKey.key`. 

Ora dovrebbero essere disponibili un file **`myPrivateKey.key`** e un file **`myCert.pem`** nella directory di lavoro. 


### Ottenere l'ID etcd del cluster

Il daemon **etcd** di CoreOS richiede un ID di individuazione per eseguire automaticamente le query per tutti i nodi nel cluster. Per recuperare l'ID di individuazione e salvarlo in un file **etcdid**, digitare

```
curl https://discovery.etcd.io/new | grep ^http.* > etcdid
```

### Creare un file cloud-config

Nella stessa directory di lavoro creare un file con l'editor di testo preferito con il seguente testo e salvarlo come **`cloud-config.yaml`**. Il file può essere salvato con qualsiasi nome, ma durante la creazione delle macchine virtuali nel passaggio successivo sarà necessario fare riferimento al nome del file nell'opzione **--custom-data** per il comando **azure create vm**.

> [AZURE.NOTE] Ricordare di digitare `cat etcdid` per recuperare l'ID di individuazione etcd dal file `etcdid` creato in precedenza e di sostituire **`<token>`** nel seguente file **cloud-config.yaml** con il numero generato dal file `etcdid`. Se non è possibile eseguire la convalida del cluster alla fine della procedura, questo potrebbe essere uno dei passaggi trascurati.

```
#cloud-config

coreos:
  etcd:
    # generate a new token for each unique cluster from https://discovery.etcd.io/new
    discovery: https://discovery.etcd.io/<token>
    # deployments across multiple cloud services will need to use $public_ipv4
    addr: $private_ipv4:4001
    peer-addr: $private_ipv4:7001
  units:
    - name: etcd.service
      command: start
    - name: fleet.service
      command: start
```

Per informazioni più complete sul file cloud-config, vedere l'articolo relativo all'[uso di Cloud-Config](https://coreos.com/docs/cluster-management/setup/cloudinit-cloud-config/) nella documentazione di CoreOS.

### Usare xplat-cli per creare una nuova macchina virtuale CoreOS
<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

1. Installare l'[interfaccia multipiattaforma di Azure (xplat-cli)] se non è già presente, quindi accedere con un ID di lavoro o di scuola oppure scaricare un file .publishsettings e importarlo nell'account.
2. Individuare l'immagine CoreOS. Al momento è presente solo un'immagine -- **`2b171e93f07c4903bcad35bda10acf22__CoreOS-Alpha-475.1.0`** -- ma per individuare le immagini disponibili in qualsiasi momento, digitare `azure vm image list | grep .*CoreOS.*` and you should see one or more results similar to:
`data:    2b171e93f07c4903bcad35bda10acf22__CoreOS-Alpha-475.1.0              Public    Linux`
3. Creare un servizio cloud per il cluster di base digitando
`azure service create <cloud-service-name>` dove *cloud-service-name* è il nome del servizio cloud CoreOS. Questo esempio usa il nome **`coreos-cluster`**. È necessario riusare il nome scelto durante la creazione delle istanze della macchina virtuale CoreOS all'interno del servizio cloud. 

Nota: se si osserva il lavoro svolto finora nel [nuovo portale](https://portal.azure.com), il nome del servizio cloud è visualizzato sia per il gruppo di risorse che per il dominio, come mostrato nell'immagine seguente:

![][CloudServiceInNewPortal]  
4. Connettere il servizio cloud e creare una nuova macchina virtuale CoreOS usando il comando **azure vm create**. La posizione del certificato X.509 viene spostata nell'opzione **--ssh-cert**. Creare la prima immagine VM digitando quanto segue e ricordando di sostituire **coreos-cluster** con il nome del servizio cloud creato:

```
azure vm create --custom-data=cloud-config.yaml --ssh=22 --ssh-cert=./myCert.pem --no-ssh-password --vm-name=node-1 --connect=coreos-cluster --location='West US' 2b171e93f07c4903bcad35bda10acf22__CoreOS-Alpha-475.1.0 core
```

5. Creare il secondo nodo ripetendo il comando nel passaggio 4, sostituendo il valore **--vm-name** con **node-2** e il valore della porta **--ssh** con 2022. 
 
6. Creare il terzo nodo ripetendo il comando nel passaggio 4, sostituendo il valore **--vm-name** con **node-3** e il valore della porta **--ssh** con 3022. 
 
Nell'immagine seguente viene visualizzato l'aspetto del cluster CoreOS nel nuovo portale.

![][EmptyCoreOSCluster]

### Testare il cluster CoreOS da una macchina virtuale di Azure

Per testare il cluster, verificare di essere nella directory di lavoro, quindi connettersi a **node-1** usando **ssh** e passare la chiave privata digitando:

`ssh core@coreos-cluster.cloudapp.net -p 22 -i ./myPrivateKey.key`

Una volta connessi, digitare `sudo fleetctl list-machines` per vedere se il cluster ha già identificato tutte le macchine virtuali nel cluster. Dovrebbe essere visualizzato un messaggio simile al seguente:

```
core@node-1 ~ $ sudo fleetctl list-machines
MACHINE		IP		METADATA
442e6cfb...	100.71.168.115	-
a05e2d7c...	100.71.168.87	-
f7de6717...	100.71.188.96	-
```

### Testare il cluster CoreOS da localhost

Infine il cluster CoreOS viene testato dal client Linux locale installando **fleet**. **fleet** richiede **golang**, quindi potrebbe essere necessario installarlo per primo digitando:

`sudo apt-get install golang` 

Quindi clonare il repository **fleet** da github digitando:

`git clone https://github.com/coreos/fleet.git`

Compilare **fleet** digitando

`./build`

Infine, spostare **fleet** in una posizione di facile accesso (a seconda della configurazione potrebbe essere necessario **sudo**):

`cp bin/fleetctl /usr/local/bin`

Verificare che **fleet** abbia accesso a `myPrivateKey.key` nella directory di lavoro digitando:

`ssh-add ./myPrivateKey.key`

> [AZURE.NOTE] Se si usa già la chiave **`~/.ssh/id_rsa`**, aggiungerla con `ssh-add ~/.ssh/id_rsa`.

Ora è possibile eseguire il test in remoto con lo stesso comando **fleetctl** usato da **node-1**, ma passando alcuni argomenti remoti:

`fleetctl --tunnel coreos-cluster.cloudapp.net:22 list-machines`

I risultati dovrebbero essere identici:

```
MACHINE		IP		METADATA
442e6cfb...	100.71.168.115	-
a05e2d7c...	100.71.168.87	-
f7de6717...	100.71.188.96	-
```

## Passaggi successivi

Ora dovrebbe essere presente un cluster CoreOS a tre nodi in esecuzione in Azure. È ora possibile passare ad argomenti in cui viene descritto come creare cluster più complessi, usare Docker e creare applicazioni più interessanti. Leggere l'[esercitazione su CoreOS di Tim Park], l'[esercitazione su CoreOS di Patrick Chanezon], la documentazione di [Docker] e la [panoramica di CoreOS].

<!--Anchors-->
[CoreOS, cluster e contenitori di Linux]: #intro
[Considerazioni relative alla sicurezza]: #security
[Come usare CoreOS in Azure]: #usingcoreos
[Sottotitolo 3]: #subheading-3
[Passaggi successivi]: #next-steps

<!--Image references-->
[CloudServiceInNewPortal]: ./media/virtual-machines-linux-coreos-how-to/cloudservicefromnewportal.png
[EmptyCoreOSCluster]: ./media/virtual-machines-linux-coreos-how-to/endresultemptycluster.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[Interfaccia multipiattaforma di Azure (xplat-cli)]: ../xplat-cli/

[CoreOS]: https://coreos.com/
[Panoramica di CoreOS]: https://coreos.com/using-coreos/
[CoreOS con Azure]: https://coreos.com/docs/running-coreos/cloud-providers/azure/
[Esercitazione su CoreOS di Tim Park]: https://github.com/timfpark/coreos-azure
[Esercitazione su CoreOS di Patrick Chanezon]: https://github.com/chanezon/azure-linux/tree/master/coreos/cloud-init
[Docker]: http://docker.io
[YAML]: http://yaml.org/




<!--HONumber=42-->
