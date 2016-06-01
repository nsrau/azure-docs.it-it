<properties
   pageTitle="Servizio Marathon specifico per un'applicazione o un'utente | Microsoft Azure"
   description="Creare un servizio Marathon specifico per un'applicazione o un'utente"
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Contenitori, Marathon, Micro-Service, controller di dominio/sistema operativo, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/12/2016"
   ms.author="rogardle"/>

# Creare un servizio Marathon specifico per un'applicazione o un'utente

Il servizio contenitore di Azure fornisce un set di server master in cui vengono preconfigurati Apache Mesos e Marathon. È possibile usarli per orchestrare le applicazioni sul cluster, ma è consigliabile non usare i server master per questa finalità. La modifica della configurazione di Marathon richiede ad esempio l'accesso ai server master stessi per apportare modifiche. Per questa operazione sono consigliabili server master univoci, che risultano leggermente diversi dai server standard e devono essere gestiti in modo specifico e indipendente. La configurazione necessaria per un team, inoltre, potrebbe non essere ottimale per un altro team. Questo articolo illustra come aggiungere un servizio Marathon specifico per un utente o un'applicazione.

Poiché questo servizio apparterrà a un singolo utente o team, sarà possibile configurarlo in base alle esigenze specifiche dell'utente o del team. Il servizio contenitore di Azure assicurerà inoltre la continuazione dell'esecuzione del servizio. In caso di errore, il servizio verrà riavviato dal servizio contenitore di Azure. Nella maggior parte dei casi il tempo di inattività non verrà percepito dall'utente.

## Prerequisiti

[Distribuire un'istanza del servizio contenitore di Azure](container-service-deployment.md) con agente di orchestrazione di tipo DCOS, [assicurarsi che il client possa connettersi al cluster](container-service-connect.md) e[AZURE.INCLUDE [installare l'interfaccia della riga di comando del controller di dominio/sistema operativo](../../includes/container-service-install-dcos-cli-include.md)].

## Creazione di un servizio Marathon specifico per un'applicazione o un'utente

Creare prima di tutto un file di configurazione JSON che definisce il nome del servizio dell'applicazione da creare. In questo caso viene usato `marathon-alice` come nome del framework. Salvare il file con un nome analogo a `marathon-alice.json`:

```json
{"marathon": {"framework-name": "marathon-alice" }}
```

Usare quindi l'interfaccia della riga di comando del controller di dominio/sistema operativo per installare l'istanza di Marathon con le opzioni impostate nel file di configurazione:

```bash
dcos package install --options=marathon-alice.json marathon
```

Dovrebbe essere visualizzato il servizio `marathon-alice` in esecuzione nella scheda dei servizi dell'interfaccia della riga di comando del controller di dominio/sistema operativo. L'interfaccia utente sarà `http://<hostname>/service/marathon-alice/`, se si vuole accedervi direttamente.

## Impostazione dell'interfaccia della riga di comando del controller di dominio/sistema operativo per accedere al servizio

È facoltativamente possibile configurare l'interfaccia della riga di comando del controller di dominio/sistema operativo per accedere a questo nuovo servizio, impostando la proprietà `marathon.url` in modo che faccia riferimento all'istanza `marathon-alice`, come illustrato di seguito:

```bash
dcos config set marathon.url http://<hostname>/service/marathon-alice/
```

È possibile verificare l'istanza di Marathon usata dall'interfaccia della riga di comando eseguendo il comando `dcos config show` ed è possibile ripristinare l'uso del servizio Marathon master con il comando `dcos config unset marathon.url`.

<!---HONumber=AcomDC_0525_2016-->