
<properties
   pageTitle="Introduzione all'uso di docker con swarm in Azure"
   description="Viene descritto come creare un gruppo di macchine virtuali con l'estensione della VM Docker e usare swarm per creare un cluster Docker."
   services="virtual-machines"
   documentationCenter="virtual-machines"
   authors="squillace"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="05/24/2015"
   ms.author="rasquill"/>

# Come usare docker con swarm

In questo argomento viene descritto un modo molto semplice di utilizzare [Docker](https://www.docker.com/) con [Swarm](https://github.com/docker/swarm) per creare un cluster gestito da Swarm in Azure. Vengono create quattro macchine virtuali in Azure, una da usare come gestore di swarm e tre come parte del cluster di host docker. Al termine dell'operazione, sarà possibile usare swarm per visualizzare il cluster e quindi iniziare a usare docker con swarm. Inoltre, le chiamate dell’interfaccia della riga di comando di Azure in questo argomento utilizzano la modalità di gestione servizi (asm).

> [AZURE.NOTE] Trattandosi di una versione precedente del software, verificare gli aggiornamenti sull'uso di questa versione in Azure per creare cluster bilanciati, controllati e di grandi dimensioni di contenitori Docker, nonché controllare la documentazione di Docker Swarm per individuare tutte le funzionalità.
<!-- -->
> In questo argomento, inoltre, vengono utilizzati Docker con Swarm e l’interfaccia della riga di comando di Azure *senza* utilizzare **Docker Machine** al fine di illustrare come i diversi strumenti interagiscono restando indipendenti. **Docker Machine** presenta opzioni **--swarm** che consentono di utilizzare **Docker Machine** per aggiungere direttamente nodi a uno Swarm. Per un esempio, vedere la documentazione di [Docker Machine](https://github.com/docker/machine). Per informazioni sull’esecuzione di **Docker Machine** con Macchine virtuali di Azure, vedere [Come usare Docker Machine in Azure](virtual-machines-docker-machine.md).

## Creare host docker con macchine virtuali di Azure

In questo argomento vengono create quattro macchine virtuali, ma è possibile usare il numero di macchine virtuali desiderato. Chiamare il comando seguente sostituendo *&lt;password&gt;* con la password scelta.

    azure vm docker create swarm-master -l "East US" -e 22 $imagename ops <password>
    azure vm docker create swarm-node-1 -l "East US" -e 22 $imagename ops <password>
    azure vm docker create swarm-node-2 -l "East US" -e 22 $imagename ops <password>
    azure vm docker create swarm-node-3 -l "East US" -e 22 $imagename ops <password>

Al termine dell'operazione, sarà possibile usare **azure vm list** per visualizzare le macchine virtuali di Azure:

    $ azure vm list | grep "swarm-[mn]"
    data:    swarm-master     ReadyRole           East US       swarm-master.cloudapp.net                               100.78.186.65
    data:    swarm-node-1     ReadyRole           East US       swarm-node-1.cloudapp.net                               100.66.72.126
    data:    swarm-node-2     ReadyRole           East US       swarm-node-2.cloudapp.net                               100.72.18.47  
    data:    swarm-node-3     ReadyRole           East US       swarm-node-3.cloudapp.net                               100.78.24.68  

## Installazione di swarm sulla macchina virtuale swarm master

In questo argomento viene utilizzato il [modello contenitore dell’installazione presente nella documentazione di Swarm](https://github.com/docker/swarm#1---docker-image), ma è possibile usare anche SSH su **swarm-master**. In questo modello, **Swarm** viene scaricato come contenitore Docker su cui è in esecuzione Swarm. Di seguito, questo passaggio viene eseguito *in remoto dal laptop usando Docker* per collegarsi alla macchina virtuale **swarm-master**. Viene quindi indicato alla macchina virtuale di usare il comando di creazione dell'ID cluster, vale a dire **swarm create**. L'ID cluster indica come **Swarm** individua i membri del gruppo di Swarm. (È anche possibile clonare il repository e compilarlo autonomamente, il che consente di avere un controllo completo e di attivare il debug).

    $ docker --tls -H tcp://swarm-master.cloudapp.net:2376 run --rm swarm create
    Unable to find image 'swarm:latest' locally
    511136ea3c5a: Pull complete
    a8bbe4db330c: Pull complete
    9dfb95669acc: Pull complete
    0b3950daf974: Pull complete
    633f3d9a9685: Pull complete
    bba5f98a0414: Pull complete
    defbc1ab4462: Pull complete
    92d78d321ff2: Pull complete
    swarm:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
    Status: Downloaded newer image for swarm:latest
    36731c17189fd8f450c395db8437befd

L'ultima riga è l'ID cluster; si consiglia di copiarla in quanto verrà usata nuovamente quando le macchine virtuali del nodo vengono collegate allo swarm master per creare lo "swarm". In questo esempio, l'ID cluster è **36731c17189fd8f450c395db8437befd**.

> [AZURE.NOTE] Per maggiore chiarezza, viene usata l'installazione di Docker locale per collegarsi alla macchina virtuale **swarm-master** in Azure e indicare a **swarm-master** di scaricare, installare ed eseguire il comando **create**. Tale comando restituisce l'ID cluster che verrà usato successivamente per scopi di individuazione.
<!-- -->
> Per verificare ciò, eseguire `docker -H tcp://`*&lt;nomehost&gt;* ` images` per visualizzare i processi del contenitore sulla macchina **swarm-master** e su un altro nodo a scopo di confronto. Il comando Swarm precedente è stato eseguito con l'opzione **--rm**, il contenitore è stato rimosso dopo l'operazione, per cui usando **docker ps -a** non viene restituito alcun risultato:


        $ docker --tls -H tcp://swarm-master.cloudapp.net:2376 images
        REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
        swarm               latest              92d78d321ff2        11 days ago         7.19 MB
        $ docker --tls -H tcp://swarm-node-1.cloudapp.net:2376 images
        REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
        $
<P />
> Se si ha familiarità con **Docker**, si è a conoscenza che gli altri nodi non presentano alcuna voce perché non è stata ancora scaricata ed eseguita alcuna immagine.

## Collegare le macchine virtuali del nodo al cluster docker

Per ciascun nodo, visualizzare le informazioni sull'endpoint usando l’interfaccia della riga di comando di Azure. Di seguito, questa operazione viene eseguita per l'host Docker **swarm-node-1** al fine di ottenere la parte Docker del nodo.

    $ azure vm endpoint list swarm-node-1
    info:    Executing command vm endpoint list
    + Getting virtual machines
    data:    Name    Protocol  Public Port  Private Port  Virtual IP      EnableDirectServerReturn  Load Balanced
    data:    ------  --------  -----------  ------------  --------------  ------------------------  -------------
    data:    docker  tcp       2376         2376          138.91.112.194  false                     No
    data:    ssh     tcp       22           22            138.91.112.194  false                     No
    info:    vm endpoint list command OK


Usando **Docker** e l'opzione `-H` per puntare il client Docker alla macchina virtuale del nodo, collegare questo nodo allo Swarm che viene creato passando l'ID cluster e la porta Docker del nodo (l'ultima, usando **--addr**):

    $ docker --tls -H tcp://swarm-node-1.cloudapp.net:2376 run -d swarm join --addr=138.91.112.194:2376 token://36731c17189fd8f450c395db8437befd
    Unable to find image 'swarm:latest' locally
    511136ea3c5a: Pull complete
    a8bbe4db330c: Pull complete
    9dfb95669acc: Pull complete
    0b3950daf974: Pull complete
    633f3d9a9685: Pull complete
    bba5f98a0414: Pull complete
    defbc1ab4462: Pull complete
    92d78d321ff2: Pull complete
    swarm:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
    Status: Downloaded newer image for swarm:latest
    bbf88f61300bf876c6202d4cf886874b363cd7e2899345ac34dc8ab10c7ae924

Il risultato è ottimo. Per verificare che **Swarm** sia in esecuzione su **swarm-node-1**, digitare:

    $ docker --tls -H tcp://swarm-node-1.cloudapp.net:2376 ps -a
        CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS               NAMES
        bbf88f61300b        swarm:latest        "/swarm join --addr=   13 seconds ago      Up 12 seconds       2375/tcp            angry_mclean

Ripetere la procedura per tutti gli altri nodi nel cluster. In questo caso, l'operazione viene eseguita per **swarm-node-2** e **swarm-node-3**.

## Iniziare a gestire il cluster swarm,

    $ docker --tls -H tcp://swarm-master.cloudapp.net:2376 run -d -p 2375:2375 swarm manage token://36731c17189fd8f450c395db8437befd
    d7e87c2c147ade438cb4b663bda0ee20981d4818770958f5d317d6aebdcaedd5

sarà quindi possibile visualizzare i nodi presenti nel cluster:

    ralph@local:~$ docker --tls -H tcp://swarm-master.cloudapp.net:2376 run --rm swarm list token://73f8bc512e94195210fad6e9cd58986f
    54.149.104.203:2375
    54.187.164.89:2375
    92.222.76.190:2375

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi

Iniziare a eseguire operazioni sullo swarm. Per trarre ispirazione, vedere [https://github.com/docker/swarm/](https://github.com/docker/swarm/) o un [video](https://www.youtube.com/watch?v=EC25ARhZ5bI).

<!-- links -->

[docker-machine-azure]: virtual-machines-docker-machine.md
 

<!---HONumber=August15_HO6-->