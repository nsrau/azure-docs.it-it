<properties
   pageTitle="Connettersi a un cluster del servizio contenitore di Azure | Microsoft Azure"
   description="Connettersi a un cluster del servizio contenitore di Azure usando un tunnel SSH."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, Contenitori, Micro-servizi, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="rogardle"/>


# Connettersi a un cluster del servizio contenitore di Azure

I cluster Mesos e Swarm distribuiti dal servizio contenitore di Azure espongono gli endpoint REST. Questi endpoint, tuttavia, non sono aperti all'esterno. Per gestire questi endpoint, è necessario crear un tunnel Secure Shell (SSH). Dopo aver creato il tunnel SSH, è possibile eseguire comandi sugli endpoint del cluster e visualizzare l'interfaccia utente del cluster con un browser nel proprio sistema. Questo documento illustra nel dettaglio la creazione di un tunnel SSH da Linux, OS X e Windows.

>[AZURE.NOTE] È anche possibile creare una sessione SSH con un sistema di gestione cluster, ma non è consigliabile farlo. Lavorare direttamente in un sistema di gestione espone al rischio di modifiche accidentali della configurazione.

## Creare un tunnel SSH in Linux o OS X

Quando si crea un tunnel SSH in Linux o OS X, prima di tutto è necessario individuare il nome DNS pubblico dei master con carico bilanciato. A tale scopo, espandere il gruppo di risorse in modo che vengano visualizzate tutte le risorse. Trovare e selezionare l'indirizzo IP pubblico del master. Verrà aperto un pannello contenente informazioni sull'indirizzo IP pubblico, che include il nome DNS. Salvare questo nome per usarlo in seguito. <br />

![Nome DNS pubblico](media/pubdns.png)

Ora aprire una shell ed eseguire il comando seguente, dove:

**PORT** è la porta dell'endpoint da esporre. Per Swarm, è la porta 2375. Per Mesos, usare la porta 80. **USERNAME** è il nome utente specificato al momento della distribuzione del cluster. **DNSPREFIX** è il prefisso DNS specificato al momento della distribuzione del cluster. **REGION** è l'area in cui si trova il gruppo di risorse.

```
ssh -L PORT:localhost:PORT -N [USERNAME]@[DNSPREFIX]man.[REGION].cloudapp.azure.com -p 2200
```
### Tunnel Mesos

Per aprire un tunnel per gli endpoint correlati a Mesos, eseguire un comando simile al seguente:

```
ssh -L 80:localhost:80 -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Ora è possibile accedere agli endpoint correlati a Mesos da:

- Mesos: `http://localhost/mesos`
- Marathon: `http://localhost/marathon`
- Chronos: `http://localhost/chronos`

Analogamente, è possibile raggiungere le API REST per ogni applicazione attraverso questo tunnel, Marathon: `http://localhost/marathon/v2`. Per altre informazioni sulle diverse API disponibili, vedere la documentazione di Mesosphere per l'[API Marathon](https://mesosphere.github.io/marathon/docs/rest-api.html). Vedere l'[API Chronos](https://mesos.github.io/chronos/docs/api.html) e la documentazione di Apache sull'[API dell'utilità di pianificazione Mesos](http://mesos.apache.org/documentation/latest/scheduler-http-api/).

### Tunnel Swarm

Per aprire un tunnel per l'endpoint Swarm, eseguire un comando simile al seguente:

```
ssh -L 2375:localhost:2375 -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

A questo punto è possibile impostare la variabile di ambiente DOCKER\_HOST come indicato di seguito e continuare a usare l'interfaccia della riga di comando di Docker come di consueto.

```
export DOCKER_HOST=:2375
```

## Creare un tunnel SSH in Windows

Esistono più opzioni per creare i tunnel SSH in Windows. Questo documento descrive come usare PuTTY a questo scopo.

Scaricare PuTTY nel sistema Windows ed eseguire l'applicazione.

Immettere un nome host che include il nome utente dell'amministratore cluster e il nome DNS pubblico del primo master nel cluster. **Host name** sarà simile a `adminuser@PublicDNS`. Immettere 2200 nel campo **Port**.

![Configurazione PuTTY 1](media/putty1.png)

Selezionare `SSH` e `Authentication`. Aggiungere il file di chiave privata per l'autenticazione.

![Configurazione PuTTY 2](media/putty2.png)

Selezionare `Tunnels` e configurare per le porte inoltrate seguenti:
- **Source Port:** corrisponde alla preferenza dell'utente. Usare 80 per Mesos o 2375 per Swarm.
- **Destination:** usare localhost:80 per Mesos o localhost:2375 per Swarm.

L'esempio seguente è configurato per Mesos, ma avrà un aspetto simile anche per Docker Swarm.

>[AZURE.NOTE] La porta 80 non deve essere usata quando si crea il tunnel.

![Configurazione PuTTY 3](media/putty3.png)

Al termine, salvare la configurazione di connessione e connettere la sessione PuTTY. Dopo la connessione, è possibile visualizzare la configurazione della porta nel registro eventi di PuTTY.

![Log eventi di PuTTY](media/putty4.png)

Dopo avere configurato il tunnel per Mesos, è possibile accedere all'endpoint correlato da:

- Mesos: `http://localhost/mesos`
- Marathon: `http://localhost/marathon`
- Chronos: `http://localhost/chronos`

Dopo avere configurato il tunnel per Docker Swarm, è possibile accedere al cluster Swarm dall'interfaccia della riga di comando di Docker. È necessario prima di tutto configurare una variabile di ambiente Windows denominata `DOCKER_HOST` con un valore ` :2375`.

## Passaggi successivi

Distribuire e gestire contenitori con Mesos o Swarm.

- [Uso del servizio contenitore di Azure e di Mesos](./container-service-mesos-marathon-rest.md)

<!---HONumber=AcomDC_0323_2016-->