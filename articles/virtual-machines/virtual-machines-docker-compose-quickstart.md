<properties
   pageTitle="Docker e compose in una macchina virtuale | Microsoft Azure"
   description="Breve introduzione all'utilizzo di Compose e Docker nelle macchine virtuali di Azure"
   services="virtual-machines"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="08/07/2015"
   ms.author="danlep"/>

# Introduzione a Docker e Compose in una macchina virtuale di Azure

In questo articolo viene illustrato come iniziare a utilizzare Docker e [Compose](http://github.com/docker/compose) per definire ed eseguire un'applicazione complessa in una macchina virtuale Linux in Azure. Con Compose (il successore di *Fig*), si utilizza un file di testo semplice per definire un'applicazione costituita da più contenitori Docker. Quindi si avvia l'applicazione con un unico comando che esegue le operazioni necessarie per l'esecuzione dell’applicazione nella macchina virtuale. Ad esempio, in questo articolo viene illustrato come configurare rapidamente un blog WordPress con un database SQL MariaDB back-end, ma è anche possibile utilizzare Compose per configurare applicazioni più complesse.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Questo articolo si applica alle macchine virtuali tramite il gestore di risorse e i modelli di distribuzione classici.

Se non si ha familiarità con Docker e i contenitori, vedere le [informazioni di livello elevato su Docker](http://azure.microsoft.com/documentation/videos/docker-high-level-whiteboard/).

## Passaggio 1: configurare una macchina virtuale Linux come host Docker

È possibile utilizzare una serie di procedure di Azure e le immagini disponibili nel Markeplace di Azure per creare una macchina virtuale Linux da configurare come host Docker. Ad esempio, vedere [Utilizzo dell’estensione VM Docker dall’interfaccia della riga di comando](virtual-machines-docker-with-xplat-cli-install.md) per una procedura rapida per la creazione di una macchina virtuale Ubuntu con estensione VM Docker. Quando si utilizza l'estensione VM Docker, la macchina virtuale viene automaticamente configurata come host Docker. Nell'esempio riportato in questo articolo viene illustrato come utilizzare l'[interfaccia della riga di comando di Azure per Mac, Linux e Windows](../xplat-cli-install.md)(CLI Azure) in modalità di gestione dei servizi per creare la VM.

## Passaggio 2: installare Compose

Quando la VM Linux è in esecuzione con Docker, connettersi dal computer client tramite SSH. Se necessario, installare [Compose](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md) eseguendo i due comandi seguenti.

>[AZURE.TIP]Se è stata utilizzata l'estensione VM Docker per creare la macchina virtuale, Compose è stato già installato automaticamente. Ignorare questi comandi e andare al passaggio 3. È necessario installare Compose solo se Docker è stato installato manualmente nella macchina virtuale.

```
$ curl -L https://github.com/docker/compose/releases/download/1.1.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose

$ chmod +x /usr/local/bin/docker-compose
```
>[AZURE.NOTE]Se si verifica un errore di "autorizzazione negata", probabilmente non è possibile scrivere nella directory /usr/local/bin ed è necessario installare Compose come utente avanzato. Eseguire `sudo -i`, quindi i due comandi precedenti, quindi `exit`.

Per verificare l'installazione di Compose, eseguire i comandi indicati di seguito.

```
$ docker-compose --version
```

L'output sarà simile a ```
docker-compose 1.3.2
```


## Passaggio 3: creare un file di configurazione docker-compose.yml

Successivamente, verrà creato un file `docker-compose.yml`, che è semplicemente un file di configurazione di testo, per definire i contenitori Docker ed eseguirli nella macchina virtuale. Il file specifica l'immagine da eseguire su ciascun contenitore (o potrebbe essere una compilazione da un file Docker), le variabili d’ambiente e le dipendenze necessarie, le porte, i collegamenti tra i contenitori e così via. Per informazioni dettagliate sulla sintassi dei file yml, vedere [riferimento a docker-compose.yml](http://docs.docker.com/compose/yml/).

Creare una directory di lavoro in una macchina virtuale e utilizzare un editor di testo per creare `docker-compose.yml`. Per provare un semplice esempio, copiare il testo seguente nel file. Questa configurazione utilizza le immagini del [Registro di sistema DockerHub](https://registry.hub.docker.com/_/wordpress/) per installare WordPress (il sistema di creazione blog e gestione del contenuto open source) e un database SQL MariaDB back-end collegato.

 ``` wordpress: image: wordpress links: - db:mysql ports: - 8080:80

db: image: mariadb environment: MYSQL\_ROOT\_PASSWORD: <your password>

```

## Step 4: Start the containers with Compose

In the working directory on your VM, simply run the following command.

```
$ docker-compose up -d

```

This starts the Docker containers specified in `docker-compose.yml`. You'll see output similar to:

```
Creating wordpress\_db\_1... Creating wordpress\_wordpress\_1... ```

>[AZURE.NOTE]Assicurarsi di utilizzare l’opzione **-d** all'avvio in modo che i contenitori vengano eseguiti in background continuamente.

Per verificare che i contenitori siano attivi, digitare `docker-compose ps`. Dovrebbe essere visualizzata una schermata analoga alla seguente:

```
Name             Command             State              Ports
-------------------------------------------------------------------------
wordpress_db_1     /docker-           Up                 3306/tcp
             entrypoint.sh
             mysqld
wordpress_wordpr   /entrypoint.sh     Up                 0.0.0.0:8080->80
ess_1              apache2-for ...                       /tcp
```

È ora possibile connettersi a WordPress direttamente nella macchina virtuale passando a `http://localhost:8080`. Se si desidera connettersi alla macchina virtuale tramite Internet, configurare innanzitutto un endpoint HTTP nella macchina virtuale che esegue il mapping della porta pubblica 80 alla porta privata 8080. Ad esempio, in una distribuzione di Gestione servizi di Azure, eseguire il seguente comando di dell’interfaccia della riga di comando di Azure:

```
$ azure vm endpoint create <machine-name> 80 8080

```

Viene visualizzata la schermata di avvio di WordPress, in cui è possibile completare l'installazione e iniziare a utilizzare l’applicazione.

![Schermata iniziale di WordPress][wordpress_start]


## Passaggi successivi

* Per ulteriori esempi di compilazione e distribuzione di app multi-contenitore, consultare il [riferimento CLI di Compose](http://docs.docker.com/compose/cli/) e il [manuale dell'utente](http://docs.docker.com/compose/).
* Utilizzare un modello di Gestione risorse di Azure, quello proprio o uno fornito dalla [community](http://azure.microsoft.com/documentation/templates/), per distribuire una macchina virtuale di Azure con Docker e un'applicazione configurata con Compose. Ad esempio, il modello [Distribuire un blog WordPress con Docker](https://azure.microsoft.com/documentation/templates/docker-wordpress-mysql/) utilizza Docker e Compose per distribuire rapidamente WordPress con un back-end MySQL in una VM Ubuntu.
* Provare a integrare Docker Compose con un cluster [Docker Swarm](virtual-machines-docker-swarm.md). Per gli scenari, vedere [Docker Compose/Swarm integration](https://github.com/docker/compose/blob/master/SWARM.md).

<!--Image references-->

[wordpress_start]: ./media/virtual-machines-docker-compose-quickstart/WordPress.png

<!---HONumber=Oct15_HO2-->