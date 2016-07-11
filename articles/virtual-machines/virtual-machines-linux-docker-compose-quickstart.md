<properties
   pageTitle="Docker e compose in una macchina virtuale | Microsoft Azure"
   description="Breve introduzione all'utilizzo di Compose e Docker nelle macchine virtuali Linux in Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="06/10/2016"
   ms.author="danlep"/>

# Introduzione a Docker e Compose per definire ed eseguire un'applicazione multi-contenitore in una macchina virtuale di Azure

Introduzione all’utilizzo di Docker e [Compose](http://github.com/docker/compose) per definire ed eseguire un'applicazione complessa in una macchina virtuale Linux in Azure. Con Compose (il successore di *Fig*), si utilizza un file di testo semplice per definire un'applicazione costituita da più contenitori Docker. Quindi si avvia l'applicazione con un unico comando che esegue le operazioni necessarie per l'esecuzione dell’applicazione nella macchina virtuale.

Ad esempio, in questo articolo viene illustrato come configurare rapidamente un blog WordPress con un database SQL MariaDB back-end in una VM di Ubuntu, ma è anche possibile usare Compose per configurare applicazioni più complesse.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] Informazioni su come [eseguire questa procedura con il modello di Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql).

Se non si ha familiarità con Docker e i contenitori, vedere le [informazioni di livello elevato su Docker](https://azure.microsoft.com/documentation/videos/docker-high-level-whiteboard/).

## Passaggio 1: configurare una macchina virtuale Linux come host Docker

È possibile usare una serie di procedure di Azure e le immagini o i modelli di Resource Manager disponibili nel Markeplace di Azure per creare una VM Linux da configurare come host Docker. Ad esempio, vedere [Uso dell'estensione di VM Docker per distribuire l'ambiente](virtual-machines-linux-dockerextension.md) per una procedura rapida per la creazione di una macchina virtuale Ubuntu con l'estensione di VM Docker di Azure tramite un [modello di avvio rapido](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). Quando si usa l'estensione di VM Docker, la macchina virtuale viene automaticamente configurata come host Docker e Compose è già installato. L'esempio riportato in questo articolo illustra come usare l'[interfaccia della riga di comando di Azure per Mac, Linux e Windows](../xplat-cli-install.md) in modalità Resource Manager per creare la macchina virtuale.

## Passaggio 2: Verificare che Compose sia installato

Quando la VM Linux è in esecuzione con Docker, connettersi dal computer client tramite SSH.

Per verificare l'installazione di Compose nella VM, eseguire questo comando.

```
$ docker-compose --version
```

L'output sarà simile a `docker-compose 1.6.2, build 4d72027`

>[AZURE.TIP] Se è stato usato un altro metodo per creare un host Docker ed è necessario installare Compose manualmente, vedere la [documentazione di Compose](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md).


## Passaggio 3: creare un file di configurazione docker-compose.yml

Successivamente, verrà creato un file `docker-compose.yml`, che è semplicemente un file di configurazione di testo, per definire i contenitori Docker ed eseguirli nella macchina virtuale. Il file specifica l'immagine da eseguire su ciascun contenitore (o potrebbe essere una compilazione da un file Docker), le variabili d’ambiente e le dipendenze necessarie, le porte, i collegamenti tra i contenitori e così via. Per informazioni dettagliate sulla sintassi dei file YML, vedere [Compose file reference](http://docs.docker.com/compose/yml/) (Informazioni di riferimento sui file di Compose).

Creare una directory di lavoro in una macchina virtuale e utilizzare un editor di testo per creare `docker-compose.yml`. Per provare un semplice esempio per un modello di verifica, copiare il testo seguente nel file. Questa configurazione utilizza le immagini del [Registro di sistema DockerHub](https://registry.hub.docker.com/_/wordpress/) per installare WordPress (il sistema di creazione blog e gestione del contenuto open source) e un database SQL MariaDB back-end collegato.

 ```
 wordpress:
  image: wordpress
  links:
    - db:mysql
  ports:
    - 80:80

db:
  image: mariadb
  environment:
    MYSQL_ROOT_PASSWORD: <your password>

```

## Passaggio 4: avviare i contenitori con Compose

Nella directory di lavoro nella VM, eseguire semplicemente il comando seguente. A seconda dell'ambiente, potrebbe essere necessario eseguire `docker-compose` usando `sudo`.

```
$ docker-compose up -d

```

Verranno avviati i contenitori Docker specificati in `docker-compose.yml`. L'output sarà simile a:

```
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
```

>[AZURE.NOTE] Assicurarsi di utilizzare l’opzione **-d** all'avvio in modo che i contenitori vengano eseguiti in background continuamente.

Per verificare che i contenitori siano attivi, digitare `docker-compose ps`. Dovrebbe essere visualizzata una schermata analoga alla seguente:

```
Name             Command             State              Ports
-------------------------------------------------------------------------
wordpress_db_1     /docker-           Up                 3306/tcp
             entrypoint.sh
             mysqld
wordpress_wordpr   /entrypoint.sh     Up                 0.0.0.0:80->80
ess_1              apache2-for ...                       /tcp
```

Ora è possibile connettersi a WordPress direttamente nella VM dalla porta 80. Se è stato usato un modello di Resource Manager per creare la macchina virtuale, provare a connettersi a `http://<dnsname>.<region>.cloudapp.azure.com` o, se la macchina virtuale è stata creata usando il modello di distribuzione classica, provare a connettersi a `http://<cloudservicename>.cloudapp.net`. Viene visualizzata la schermata di avvio di WordPress, in cui è possibile completare l'installazione e iniziare a utilizzare l’applicazione.

![Schermata iniziale di WordPress][wordpress_start]


## Passaggi successivi

* Vedere la [Docker VM extension user guide](https://github.com/Azure/azure-docker-extension/blob/master/README.md) (Guida dell'utente dell'estensione VM Docker) per altre opzioni di configurazione di Docker e Compose nella VM Docker. Ad esempio, un'opzione consiste nell'inserire il file YML di Compose (convertito in JSON) direttamente nella configurazione dell'estensione della VM Docker.
* Per altri esempi di compilazione e distribuzione di app multi-contenitore, vedere le [Compose command-line reference](http://docs.docker.com/compose/reference/) (Informazioni di riferimento ai comandi di Compose) e la [guida dell'utente](http://docs.docker.com/compose/).
* Utilizzare un modello di Gestione risorse di Azure, quello proprio o uno fornito dalla [community](https://azure.microsoft.com/documentation/templates/), per distribuire una macchina virtuale di Azure con Docker e un'applicazione configurata con Compose. Ad esempio, il modello [Distribuire un blog WordPress con Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) utilizza Docker e Compose per distribuire rapidamente WordPress con un back-end MySQL in una VM Ubuntu.
* Provare a integrare Docker Compose con un cluster [Docker Swarm](virtual-machines-linux-docker-swarm.md). Vedere [Using Compose with Swarm](https://docs.docker.com/compose/swarm/) (Uso di Swarm con Compose) per gli scenari.

<!--Image references-->

[wordpress_start]: ./media/virtual-machines-linux-docker-compose-quickstart/WordPress.png

<!---HONumber=AcomDC_0629_2016-->