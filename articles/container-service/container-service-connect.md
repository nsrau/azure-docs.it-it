<properties
   pageTitle="Connettersi a un cluster del servizio contenitore di Azure | Microsoft Azure"
   description="Connettersi a un cluster del servizio contenitore di Azure con un tunnel SSH."
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

I cluster Mesos e Swarm distribuiti dal servizio contenitore di Azure espongono endpoint REST, che però non sono aperti al mondo esterno. Per gestire questi endpoint è necessario creare un tunnel SSH. Dopo aver creato il tunnel SSH, è possibile eseguire comandi sugli endpoint del cluster e visualizzare l'interfaccia utente del cluster con un browser nel proprio sistema. Questo documento illustra nel dettaglio la creazione di un tunnel SSH da Linux, OSX e Windows.

> Anche se è possibile creare una sessione SSH con un sistema di gestione cluster, non è consigliabile. Lavorare direttamente in un sistema di gestione espone al rischio di modifiche accidentali della configurazione.

## Tunnel SSH in Linux e OSX

Prima di tutto è necessario trovare il nome DNS pubblico dei master con carico bilanciato. A tale scopo, espandere il gruppo di risorse in modo che vengano visualizzate tutte le risorse. Trovare e selezionare l'indirizzo IP pubblico del master. Verrà aperto un pannello contenente informazioni sull'indirizzo IP pubblico, che include il nome DNS. Salvare questo nome per usarlo in seguito. <br />

![Connessione PuTTY](media/pubdns.png)

Ora aprire una shell ed eseguire il comando seguente, dove:

**PORT** è la porta dell'endpoint da esporre. Per Swarm usare la porta 2375, per Mesos usare la porta 80. **USERNAME** è il nome utente specificato al momento della distribuzione del cluster. **DNSPREFIX** è il prefisso DNS specificato al momento della distribuzione del cluster. **REGION** è l'area in cui si trova il gruppo di risorse.

```
ssh -L PORT:localhost:PORT -N [USERNAME]@[DNSPREFIX]man.[REGION].cloudapp.azure.com -p 2200
```
### Tunnel Mesos

Per aprire un tunnel per gli endpoint correlati a Mesos, eseguire un comando simile al seguente.

```
ssh -L 80:localhost:80 -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Gli endpoint correlati a Mesos sono ora accessibili da:

- Mesos - `http://localhost/mesos`
- Marathon - `http://localhost/marathon`
- Chronos - `http://localhost/chronos` 

Analogamente, le API REST per ogni applicazione sono raggiungibili attraverso questo tunnel, Marathon - `http://localhost/marathon/v2`. Per altre informazioni sulle varie API disponibili, vedere la documentazione di Mesosphere per l'[API Marathon](https://mesosphere.github.io/marathon/docs/rest-api.html) e l'[API Chronos](https://mesos.github.io/chronos/docs/api.html), nonché la documentazione di Apache per l'[API dell'utilità di pianificazione Mesos](http://mesos.apache.org/documentation/latest/scheduler-http-api/).

### Tunnel Swarm

Per aprire un tunnel per l'endpoint Swarm, eseguire un comando simile al seguente.

```
ssh -L 2375:localhost:2375 -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

A questo punto è possibile impostare la variabile di ambiente DOCKER\_HOST come indicato di seguito e continuare a usare l'interfaccia della riga di comando di Docker come di consueto.

```
export DOCKER_HOST=:2375
```

## Tunnel SSH in Windows

Per la creazione di tunnel SSH in Windows sono disponibili più opzioni. Questo documento descrive in dettaglio l'uso di PuTTY.

Scaricare PuTTY nel sistema Windows ed eseguire l'applicazione.

Immettere un nome host che include il nome utente dell'amministratore cluster e il nome DNS pubblico del primo master nel cluster. Il nome Host sarà simile a `adminuser@PublicDNS`. Immettere 2200 nel campo Port.

![Connessione PuTTY](media/putty1.png)

Selezionare `SSH` e `Authentication` e aggiungere il file di chiave privata per l'autenticazione.

![Connessione PuTTY](media/putty2.png)

Selezionare `Tunnels` ed eseguire il comando `configure` per le porte inoltrate seguenti: - **Source Port:** specificare 80 per Mesos e 2375 per Swarm. - **Destination:** localhost:80 per Mesos e localhost:2375 per Swarm.

L'esempio seguente è configurato per Mesos, ma avrebbe un aspetto simile anche per Docker Swarm.

> La porta 80 non deve essere usata al momento della creazione del tunnel.

![Connessione PuTTY](media/putty3.png)

Al termine, salvare la configurazione di connessione e connettere la sessione PuTTY. Dopo la connessione verrà visualizzata la configurazione della porta nel registro eventi di PuTTY.

![Connessione PuTTY](media/putty4.png)

Se il tunnel è configurato per Mesos, l'endpoint correlato è accessibile da:

- Mesos - `http://localhost/mesos`
- Marathon - `http://localhost/marathon`
- Chronos - `http://localhost/chronos` 

Se il tunnel è configurato per Docker Swarm, il cluster Swarm è accessibile dall'interfaccia della riga di comando di Docker. È necessario prima di tutto configurare una variabile di ambiente Windows denominata `DOCKER_HOST` con un valore ` :2375`.

## Passaggi successivi
 
Distribuire e gestire contenitori con Mesos o Swarm.
 
- [Uso di ACS e Mesos](./container-service-mesos-marathon-rest.md)

<!---HONumber=AcomDC_0218_2016-->