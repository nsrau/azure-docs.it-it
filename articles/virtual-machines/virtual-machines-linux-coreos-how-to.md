<properties
	pageTitle="Come usare CoreOS in Azure"
	description="Descrive CoreOS, come creare una macchina virtuale CoreOS in Azure e il suo uso di base."
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="03/16/2015"
	ms.author="rasquill"/>

# Come usare CoreOS in Azure

Questo argomento descrive [CoreOS] e mostra come creare un cluster di tre macchine virtuali CoreOS in Azure come guida introduttiva per la comprensione di CoreOS. Usa gli elementi di base delle distribuzioni CoreOS e gli esempi contenuti in [CoreOS con Azure], nell'[esercitazione su CoreOS di Tim Park] e nell'[esercitazione su CoreOS di Patrick Chanezon] per mostrare i requisiti minimi assoluti per comprendere la struttura di base di una distribuzione CoreOS e ottenere un cluster di tre macchine virtuali eseguite correttamente.

## <a id='intro'>CoreOS, cluster e contenitori Linux</a>

CoreOS è una versione leggera di Linux progettata per supportare la creazione rapida di cluster di macchine virtuali potenzialmente molto grandi che usano contenitori Linux, inclusi i contenitori [Docker], come unico meccanismo di creazione dei pacchetti. CoreOS è progettato per supportare:

+ un livello molto alto di automazione
+ una distribuzione delle applicazioni più facile e coerente
+ scalabilità a livello di applicazione e di sistema

A livello generale le funzionalità CoreOS che supportano questi obiettivi sono:

1. Sistema a un pacchetto: CoreOS esegue solo immagini del contenitore Linux in esecuzione nei contenitori Linux per assicurare velocità, uniformità e facilità di distribuzione
2. Aggiornamenti al sistema operativo eseguiti in modo atomico in modo che i sistemi operativi vengano aggiornati come un'unica entità e se ne possa eseguire facilmente il rollback a uno stato noto
3. I daemon (servizi) predefiniti [etcd](https://github.com/coreos/etcd) e [fleet](https://github.com/coreos/fleet) per VM dinamica e comunicazione e gestione dei cluster

Si tratta di una descrizione generale di CoreOS e delle relative funzionalità. Per informazioni più complete su CoreOS, vedere la [panoramica di CoreOS].

## <a id='security'>Considerazioni relative alla sicurezza</a>
Attualmente CoreOS presuppone che gli utenti con accesso SSH al cluster dispongano delle autorizzazioni per gestirlo. Ne risulta che i cluster CoreOS senza modifiche sono ideali per gli ambienti di test e sviluppo, ma richiedono ulteriori misure di sicurezza per l'uso in ambienti di produzione.

## <a id='usingcoreos'>Come usare CoreOS su Azure</a>

In questa sezione viene descritto come creare un servizio cloud di Azure con tre macchine virtuali CoreOS utilizzando l'[interfaccia della riga di comando di Azure]. I passaggi di base sono i seguenti:

1. Creare i certificati SSH e le chiavi per proteggere la comunicazione con la macchina virtuale CoreOS
2. Ottenere l'ID etcd del cluster per l'intercomunicazione
3. Creare un file cloud-config in formato [YAML]
4. Utilizzare l’interfaccia della riga di comando per creare un nuovo servizio cloud di Azure e tre macchine virtuali CoreOS
5. Testare il cluster CoreOS da una macchina virtuale di Azure
6. Testare il cluster CoreOS da localhost

### Creare chiavi pubbliche e private per la comunicazione

Usare le istruzioni in [Come usare SSH con Linux in Azure](virtual-machines-linux-use-ssh-key.md) per creare una chiave pubblica e una chiave privata per SSH. I passaggi di base sono contenuti nelle istruzioni seguenti. Queste chiavi vengono usate per connettersi alle macchine virtuali nel cluster e verificare che funzionino correttamente e comunichino tra di loro.

> [AZURE.NOTE]In questo argomento si presuppone che non si disponga delle chiavi e viene richiesto di creare i file `myPrivateKey.pem` e `myCert.pem` per maggiore chiarezza. Se una coppia di chiavi pubblica e privata è già salvata in `~/.ssh/id_rsa`, è possibile digitare semplicemente `openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out myCert.pem` per ottenere il file con estensione .pem che deve essere caricato su Azure.

1. In una directory di lavoro, digitare `openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem` per creare una chiave privata e il certificato X.509 associato.

2. Per verificare che il proprietario della chiave privata possa leggere o scrivere nel file, digitare `chmod 600 myPrivateKey.key`.

Ora dovrebbero essere disponibili un file `myPrivateKey.key` e un file `myCert.pem` nella directory di lavoro.


### Ottenere l'ID etcd del cluster

Il daemon `etcd` di CoreOS richiede un ID di individuazione per eseguire automaticamente le query per tutti i nodi nel cluster. Per recuperare l'ID di individuazione e salvarlo in un file `etcdid`, digitare

```
curl https://discovery.etcd.io/new | grep ^http.* > etcdid
```

### Creare un file cloud-config

Nella stessa directory di lavoro, creare un file con l’editor di testo preferito con il seguente testo e salvarlo come `cloud-config.yaml`. Il file può essere salvato con qualsiasi nome, ma durante la creazione delle macchine virtuali nel passaggio successivo sarà necessario fare riferimento al nome del file nell'opzione **--custom-data** per il comando **azure create vm**.

> [AZURE.NOTE]Ricordare di digitare `cat etcdid` per recuperare l'ID di individuazione etcd dal file `etcdid` creato in precedenza e di sostituire `<token>` nel file `cloud-config.yaml` seguente con il numero generato dal file `etcdid`. Se non è possibile eseguire la convalida del cluster alla fine della procedura, questo potrebbe essere uno dei passaggi trascurati.

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

### Utilizzo dell’interfaccia della riga di comando di Azure per creare una nuova macchina virtuale CoreOS
<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

1. Installare l'[interfaccia della riga di comando di Azure] se non è già presente, quindi accedere con un ID di lavoro o di scuola oppure scaricare un file .publishsettings e importarlo nell'account.
2. Individuare l'immagine CoreOS. Per individuare le immagini disponibili in qualsiasi momento, digitare `azure vm image list | grep CoreOS`. Verrà visualizzato un elenco di risultati simile al seguente:

	data: 2b171e93f07c4903bcad35bda10acf22__CoreOS-Stable-522.6.0 Public Linux

3. Creare un servizio cloud per il cluster di base digitando `azure service create <cloud-service-name>` dove *<cloud-service-name>* è il nome del servizio cloud CoreOS. In questo esempio viene utilizzato il nome **`coreos-cluster`**: è necessario riutilizzare il nome scelto durante la creazione delle istanze della macchina virtuale CoreOS all'interno del servizio cloud.

Nota: se si osserva il lavoro svolto finora nel [portale](https://portal.azure.com), si noterà che il nome del servizio cloud viene utilizzato sia per il gruppo di risorse sia per il dominio, come mostrato nell'immagine seguente:

![][CloudServiceInNewPortal] 4. Connettere il servizio cloud e creare una nuova macchina virtuale CoreOS usando il comando **azure vm create**. La posizione del certificato X.509 viene spostata nell'opzione **--ssh-cert**. Creare la prima immagine VM digitando quanto segue e ricordando di sostituire **coreos-cluster** con il nome del servizio cloud creato:

```
azure vm create --custom-data=cloud-config.yaml --ssh=22 --ssh-cert=./myCert.pem --no-ssh-password --vm-name=node-1 --connect=coreos-cluster --location="West US" 2b171e93f07c4903bcad35bda10acf22__CoreOS-Stable-522.6.0 core
```

5. Creare il secondo nodo ripetendo il comando nel passaggio 4, sostituendo il valore **--vm-name** con **node-2** e il valore della porta **--ssh** con 2022.

6. Creare il terzo nodo ripetendo il comando nel passaggio 4, sostituendo il valore **--vm-name** con **node-3** e il valore della porta **--ssh** con 3022.

Nell'immagine seguente viene visualizzato l'aspetto del cluster CoreOS nel nuovo portale.

![][EmptyCoreOSCluster]

### Testare il cluster CoreOS da una macchina virtuale di Azure

Per testare il cluster, verificare di essere nella directory di lavoro, quindi connettersi a **node-1** usando **ssh** e passare la chiave privata digitando:

	ssh core@coreos-cluster.cloudapp.net -p 22 -i ./myPrivateKey.key

Una volta effettuata la connessione, digitare `sudo fleetctl list-machines` per verificare se il cluster ha già identificato tutte le macchine virtuali nel cluster. Dovrebbe essere visualizzato un messaggio simile al seguente:


	core@node-1 ~ $ sudo fleetctl list-machines
	MACHINE		IP		METADATA
	442e6cfb...	100.71.168.115	-
	a05e2d7c...	100.71.168.87	-
	f7de6717...	100.71.188.96	-


### Testare il cluster CoreOS da localhost

Infine il cluster CoreOS viene testato dal client Linux locale installando **fleet**. **fleet** richiede **golang**, quindi potrebbe essere necessario installarlo per primo digitando:

`sudo apt-get install golang`

Quindi clonare il repository **fleet** da github digitando:

`git clone https://github.com/coreos/fleet.git`

Compilare **fleet** modificando con la directory `fleet` e digitando

`./build`

Infine, spostare **fleet** in una posizione di facile accesso (a seconda della configurazione potrebbe essere necessario **sudo**):

`cp bin/fleetctl /usr/local/bin`

Assicurarsi che **fleet** disponga dell’accesso a `myPrivateKey.key` nella directory di lavoro digitando:

`ssh-add ./myPrivateKey.key`

> [AZURE.NOTE]Se la chiave **`~/.ssh/id_rsa`** viene già utilizzata, aggiungerla con `ssh-add ~/.ssh/id_rsa`.

Ora è possibile eseguire il test in remoto con lo stesso comando **fleetctl** usato da **node-1**, ma passando alcuni argomenti remoti:

`fleetctl --tunnel coreos-cluster.cloudapp.net:22 list-machines`

I risultati dovrebbero essere identici:


	MACHINE		IP		METADATA
	442e6cfb...	100.71.168.115	-
	a05e2d7c...	100.71.168.87	-
	f7de6717...	100.71.188.96	-

## Passaggi successivi

Ora dovrebbe essere presente un cluster CoreOS a tre nodi in esecuzione in Azure. A questo punto, è possibile scoprire come creare cluster più complessi, utilizzare Docker e creare applicazioni più interessanti. Per provare alcuni esempi rapidi, vedere [Introduzione a fleet su CoreOS in Azure].

<!--Anchors-->
[CoreOS, Clusters, and Linux Containers]: #intro
[Security Considerations]: #security
[How to use CoreOS on Azure]: #usingcoreos
[Subheading 3]: #subheading-3
[Next steps]: #next-steps

<!--Image references-->
[CloudServiceInNewPortal]: ./media/virtual-machines-linux-coreos-how-to/cloudservicefromnewportal.png
[EmptyCoreOSCluster]: ./media/virtual-machines-linux-coreos-how-to/endresultemptycluster.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[interfaccia della riga di comando di Azure]: ../xplat-cli.md
[CoreOS]: https://coreos.com/
[panoramica di CoreOS]: https://coreos.com/using-coreos/
[CoreOS con Azure]: https://coreos.com/docs/running-coreos/cloud-providers/azure/
[esercitazione su CoreOS di Tim Park]: https://github.com/timfpark/coreos-azure
[esercitazione su CoreOS di Patrick Chanezon]: https://github.com/chanezon/azure-linux/tree/master/coreos/cloud-init
[Docker]: http://docker.io
[YAML]: http://yaml.org/
[Introduzione a fleet su CoreOS in Azure]: virtual-machines-linux-coreos-fleet-get-started.md
 

<!---HONumber=July15_HO4-->