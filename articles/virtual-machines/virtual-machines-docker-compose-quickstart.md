<properties
   pageTitle="Introduzione a Docker e Compose in Macchine virtuali di Azure"
   description="Breve introduzione all'utilizzo di Compose e Docker in Azure"
   services="virtual-machines"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="05/07/2015"
   ms.author="danlep"/>

# Introduzione a Docker e Compose in Macchine virtuali di Azure


È possibile iniziare rapidamente a usare [Compose](http://github.com/docker/compose) (il successore di *Fig*) per definire ed eseguire applicazioni complesse con Docker in una macchina virtuale Linux in Azure. Con Compose si definisce un'applicazione multi-contenitore in un singolo file, quindi si avvia l'applicazione in un unico comando che esegue tutti i passaggi necessari per ottenerne l'esecuzione nella macchina virtuale.




## Creare una macchina virtuale di Azure con l'estensione VM Docker e installare Compose

È possibile utilizzare una serie di procedure di Azure e le immagini disponibili nel Markeplace di Azure per creare una macchina virtuale di Azure e installare Docker e Compose. Ad esempio, vedere [Utilizzo dell’estensione VM Docker dall’interfaccia della riga di comando](virtual-machines-docker-with-xplat-cli) per una procedura rapida per la creazione di una macchina virtuale Ubuntu con estensione VM Docker tramite l’interfaccia della riga di comando per Mac, Linux e Windows (l’interfaccia della riga di comando di Azure). Nell'esempio riportato in questo articolo viene utilizzata l’interfaccia della riga di comando in modalità Gestione servizi (**asm**).


Quando la macchina virtuale Ubuntu è in esecuzione con Docker, connettersi ad essa tramite SSH e quindi installare [Compose](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md) eseguendo due comandi:

```
curl -L https://github.com/docker/compose/releases/download/1.1.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose

chmod +x /usr/local/bin/docker-compose
```
>[AZURE.NOTE]Se si verifica un errore di "Autorizzazione negata", la directory /usr/local/bin non è scrivibile e sarà necessario installare Compose come utente avanzato. Eseguire `sudo -i`, quindi i due comandi precedenti, quindi `exit`.

Per verificare l'installazione di Compose, eseguire

```
docker-compose --version
```

L'output sarà simile a ```
docker-compose 1.2.0
```


## Creare un file di configurazione docker-compose.yml

Compose utilizza un file di configurazione di testo chiamato `docker-compose.yml` per definire i contenitori che verranno eseguiti nella macchina virtuale. Il file specifica l'immagine da eseguire su ciascun contenitore (o potrebbe essere una compilazione da un file Docker), le variabili d’ambiente e le dipendenze necessarie, le porte, i collegamenti tra i contenitori e così via. Per informazioni dettagliate sulla sintassi dei file yml, vedere [riferimento a docker-compose.yml](http://docs.docker.com/compose/yml/).

Creare una directory di lavoro in una macchina virtuale e utilizzare un editor di testo per creare `docker-compose.yml`. Per provare un semplice esempio, copiare il testo seguente nel file. Questa configurazione installa WordPress (il sistema di creazione blog e gestione del contenuto open source) e un database SQL MariaDB di backend collegato utilizzando le immagini del [Registro di sistema DockerHub](https://registry.hub.docker.com/_/wordpress/).

 ``` wordpress: image: wordpress links: - db:mysql ports: - 8080:80

db: image: mariadb environment: MYSQL_ROOT_PASSWORD: <your password>

```

## Start the containers with Compose

In your working directory simply run

```
docker-compose up -d

```

to start the Docker containers specified in `docker-compose.yml`. You'll see output similar to:

```
Creating wordpress_db_1... Creating wordpress_wordpress_1... ```

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

È ora possibile connettersi a WordPress direttamente nella macchina virtuale passando a `http://localhost:8080`. Se si desidera connettersi alla macchina virtuale tramite Internet, configurare innanzitutto un endpoint HTTP nella macchina virtuale che esegue il mapping della porta pubblica 80 alla porta privata 8080. Ad esempio, eseguire il seguente comando dell’interfaccia della riga di comando di Azure:

```
azure vm endpoint create <machine-name> 80 8080

```

Verrà visualizzata la schermata di avvio di WordPress, in cui è possibile completare l'installazione.

![Schermata iniziale di WordPress][wordpress_start]




## Passaggi successivi

* Per ulteriori esempi di compilazione e distribuzione di app multi-contenitore, consultare il [riferimento ai comandi di Compose](http://docs.docker.com/compose/cli/) e il [manuale dell'utente](http://docs.docker.com/compose/).
* Provare a integrare Docker Compose con un cluster [Docker Swarm](virtual-machines-docker-swarm.md). Per gli scenari, vedere [Docker Compose/Swarm integration](https://github.com/docker/compose/blob/master/SWARM.md).
* Utilizzare un modello di Gestione risorse di Azure, quello proprio o uno fornito dalla [community](http://azure.microsoft.com/documentation/templates/), per distribuire una macchina virtuale di Azure con Docker e un'applicazione configurata con Compose. Ad esempio, il [modello di macchina virtuale Ubuntu con Docker e tre contenitori](http://azure.microsoft.com/documentation/templates/docker-simple-on-ubuntu/) consente di distribuire rapidamente tre servizi dalle immagini nel [Registro di sistema DockerHub](https://registry.hub.docker.com/).

<!--Image references-->

[wordpress_start]: ./media/virtual-machines-docker-compose-quickstart/WordPress.png

<!---HONumber=58--> 