<properties
   pageTitle="Connettersi a un cluster del servizio contenitore di Azure | Microsoft Azure"
   description="Connettersi a un cluster del servizio contenitore di Azure usando un tunnel SSH."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, contenitori, Micro-Service, controller di dominio/sistema operativo, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/12/2016"
   ms.author="rogardle"/>


# Connettersi a un cluster del servizio contenitore di Azure

I cluster DC/OS e Swarm distribuiti dal servizio contenitore di Azure espongono gli endpoint REST. Questi endpoint, tuttavia, non sono aperti all'esterno. Per gestire questi endpoint, è necessario crear un tunnel Secure Shell (SSH). Dopo aver creato il tunnel SSH, è possibile eseguire comandi sugli endpoint del cluster e visualizzare l'interfaccia utente del cluster con un browser nel proprio sistema. Questo documento illustra nel dettaglio la creazione di un tunnel SSH da Linux, OS X e Windows.

>[AZURE.NOTE] È anche possibile creare una sessione SSH con un sistema di gestione cluster, ma non è consigliabile farlo. Lavorare direttamente in un sistema di gestione espone al rischio di modifiche accidentali della configurazione.

## Creare un tunnel SSH in Linux o OS X

Quando si crea un tunnel SSH in Linux o OS X, prima di tutto è necessario individuare il nome DNS pubblico dei master con carico bilanciato. A tale scopo, espandere il gruppo di risorse in modo che vengano visualizzate tutte le risorse. Trovare e selezionare l'indirizzo IP pubblico del master. Verrà aperto un pannello contenente informazioni sull'indirizzo IP pubblico, che include il nome DNS. Salvare questo nome per usarlo in seguito. <br />


![Nome DNS pubblico](media/pubdns.png)

Ora aprire una shell ed eseguire il comando seguente, dove:

**PORT** è la porta dell'endpoint da esporre. Per Swarm, è la porta 2375. Per DC/OS usare la porta 80. **USERNAME** è il nome utente specificato al momento della distribuzione del cluster. **DNSPREFIX** è il prefisso DNS specificato al momento della distribuzione del cluster. **REGION** è l'area in cui si trova il gruppo di risorse. **PATH\_TO\_PRIVATE\_KEY** [FACOLTATIVO] è il percorso della chiave privata che corrisponde alla chiave pubblica specificata durante la creazione del cluster del servizio contenitore. Usare questa opzione con il flag -i.

```bash
# ssh sample

ssh -L PORT:localhost:PORT -f -N [USERNAME]@[DNSPREFIX]mgmt.[REGION].cloudapp.azure.com -p 2200
```
> La porta di connessione SSH è la 2200, non la porta 22 standard.

## Tunnel DC/OS

Per aprire un tunnel per gli endpoint correlati a DC/OS, eseguire un comando simile al seguente:

```bash
# ssh sample

sudo ssh -L 80:localhost:80 -f -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Ora è possibile accedere agli endpoint correlati a DC/OS da:

- Controller di dominio/sistema operativo: `http://localhost/`
- Marathon: `http://localhost/marathon`
- Mesos: `http://localhost/mesos`

In modo analogo, è possibile raggiungere le API REST per ogni applicazione attraverso questo tunnel.

## Tunnel Swarm

Per aprire un tunnel per l'endpoint Swarm, eseguire un comando simile al seguente:

```bash
# ssh sample

ssh -L 2375:localhost:2375 -f -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

A questo punto è possibile impostare la variabile di ambiente DOCKER\_HOST come indicato di seguito e continuare a usare l'interfaccia della riga di comando di Docker come di consueto.

```bash
export DOCKER_HOST=:2375
```

## Creare un tunnel SSH in Windows

Esistono più opzioni per creare i tunnel SSH in Windows. Questo documento descrive come usare PuTTY a questo scopo.

Scaricare PuTTY nel sistema Windows ed eseguire l'applicazione.

Immettere un nome host che include il nome utente dell'amministratore cluster e il nome DNS pubblico del primo master nel cluster. **Host name** sarà simile a `adminuser@PublicDNS`. Immettere 2200 nel campo **Port**.

![Configurazione PuTTY 1](media/putty1.png)

Selezionare `SSH` e `Authentication`. Aggiungere il file di chiave privata per l'autenticazione.

![Configurazione PuTTY 2](media/putty2.png)

Selezionare `Tunnels` e configurare le porte inoltrate seguenti:
- **Source Port:** corrisponde alla preferenza dell'utente. Usare 80 per il controller di dominio/sistema operativo o 2375 per Swarm.
- **Destination:** usare localhost:80 per il controller di dominio/sistema operativo o localhost:2375 per Swarm.

L'esempio seguente è configurato per DC/OS, ma avrà un aspetto simile anche per Docker Swarm.

>[AZURE.NOTE] La porta 80 non deve essere usata quando si crea il tunnel.

![Configurazione PuTTY 3](media/putty3.png)

Al termine, salvare la configurazione di connessione e connettere la sessione PuTTY. Dopo la connessione, è possibile visualizzare la configurazione della porta nel registro eventi di PuTTY.

![Log eventi di PuTTY](media/putty4.png)

Dopo avere configurato il tunnel per DC/OS, è possibile accedere all'endpoint correlato da:

- Controller di dominio/sistema operativo: `http://localhost/`
- Marathon: `http://localhost/marathon`
- Mesos: `http://localhost/mesos`

Dopo avere configurato il tunnel per Docker Swarm, è possibile accedere al cluster Swarm dall'interfaccia della riga di comando di Docker. È necessario prima di tutto configurare una variabile di ambiente Windows denominata `DOCKER_HOST` con un valore ` :2375`.

## Passaggi successivi

Distribuire e gestire contenitori con DC/OS o Swarm.

[Utilizzo del servizio contenitore di Azure e del controller di dominio/sistema operativo](container-service-mesos-marathon-rest.md) [Utilizzo del servizio contenitore di Azure e Docker Swarm](container-service-docker-swarm.md)

<!---HONumber=AcomDC_0525_2016-->