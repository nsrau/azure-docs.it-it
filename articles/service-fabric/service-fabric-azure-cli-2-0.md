---
title: Introduzione a Service Fabric e all'interfaccia della riga di comando di Azure 2.0
description: Come usare il modulo dei comandi di Service Fabric nell'interfaccia della riga di comando di Azure versione 2.0, incluse la connessione al cluster e la gestione delle applicazioni
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: get-started-article
ms.date: 06/21/2017
ms.author: edwardsa
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: c5cc6e54acf27456185eeb48858c4d981aa46b4b
ms.contentlocale: it-it
ms.lasthandoff: 07/01/2017

---
<a id="service-fabric-and-azure-cli-20" class="xliff"></a>

# Service Fabric e interfaccia della riga di comando di Azure 2.0

La nuova interfaccia della riga di comando di Azure 2.0 ora include i comandi per gestire i cluster di Service Fabric. Questa documentazione include i passaggi per iniziare a usare l'interfaccia della riga di comando di Azure.

<a id="install-azure-cli-20" class="xliff"></a>

## Installare l'interfaccia della riga di comando di Azure 2.0

L'interfaccia della riga di comando di Azure ora include i comandi per interagire con i cluster di Service Fabric e per gestirli. È possibile seguire il [processo di installazione standard](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) per ottenere l'interfaccia della riga di comando di Azure più recente.

Per altre informazioni, vedere la [documentazione sull'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/en-us/cli/azure/overview).

<a id="cli-syntax" class="xliff"></a>

## Sintassi dell'interfaccia della riga di comando

Tutti i comandi di Azure Service Fabric sono preceduti da `az sf` nell'interfaccia della riga di comando di Azure. Per altre informazioni generali sui comandi disponibili, è possibile eseguire `az sf -h`. Per informazioni dettagliate su un singolo comando, è invece possibile eseguire `az sf <command> -h`.

I comandi di Azure Service Fabric nell'interfaccia della riga di comando seguono uno criterio di denominazione

```azurecli
az sf <object> <action>
```

In questo caso `<object>` è la destinazione di `<action>`.

<a id="selecting-a-cluster" class="xliff"></a>

## Selezione di un cluster

Prima di poter eseguire qualsiasi operazione, è necessario selezionare un cluster a cui connettersi. Vedere ad esempio il frammento di codice seguente per connettersi a un cluster non protetto.

> [!WARNING]
> Non usare cluster di Service Fabric non protetti per gli ambienti di produzione

```azurecli
az sf cluster select --endpoint http://testcluster.com:19080
```

L'endpoint del cluster deve essere preceduto da `http` o `https` e includere la porta per il gateway HTTP. Questa porta e l'indirizzo sono gli stessi dell'URL di Service Fabric Explorer.

Per i cluster protetti con un certificato, sono supportati i file `pem` o `crt` e `key`.

```azurecli
az sf cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

Per altre informazioni, vedere il [documento dettagliato sulla connessione ai cluster sicuri](service-fabric-connect-to-secure-cluster.md).

> [!NOTE]
> Il comando di selezione non esegue richieste prima della restituzione. Per verificare che un cluster sia stato specificato correttamente, eseguire un comando come `az sf cluster health` e controllare che il comando non restituisca errori.

<a id="performing-basic-operations" class="xliff"></a>

## Esecuzione di operazioni di base

Le informazioni di connessione al cluster sono persistenti in diverse sessioni dell'interfaccia della riga di comando di Azure. Dopo avere selezionato un cluster di Service Fabric, è possibile eseguire qualsiasi comando di Service Fabric.

Per ottenere ad esempio lo stato di integrità di un cluster di Service Fabric, eseguire questo comando

```azurecli
az sf cluster health
```

Il comando restituisce l'output seguente, presupponendo che l'output JSON sia specificato nella configurazione dell'interfaccia della riga di comando di Azure

```json
{
  "aggregatedHealthState": "Ok",
  "applicationHealthStates": [
    {
      "aggregatedHealthState": "Ok",
      "name": "fabric:/System"
    }
  ],
  "healthEvents": [],
  "nodeHealthStates": [
    {
      "aggregatedHealthState": "Ok",
      "id": {
        "id": "66aa824a642124089ee474b398d06a57"
      },
      "name": "_Test_0"
    }
  ],
  "unhealthyEvaluations": []
}
```

<a id="tips-and-faq" class="xliff"></a>

## Suggerimenti e domande frequenti

Ecco alcune informazioni che possono risultare utili quando si verificano problemi con l'uso di comandi di Service Fabric nell'interfaccia della riga di comando di Azure

<a id="converting-a-certificate-from-pfx-to-pem" class="xliff"></a>

### Conversione di un certificato da PFX a PEM

L'interfaccia della riga di comando di Azure supporta i certificati lato client come file PEM (estensione `.pem`). Se si usano file PFX di Windows, questi certificati devono essere convertiti nel formato PEM. Per eseguire la conversione da un file PFX a un file PEM, usare il comando seguente:

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

Per informazioni dettagliate, vedere la [documentazione di OpenSSL](https://www.openssl.org/docs/man1.0.1/apps/pkcs12.html).

<a id="connection-issues" class="xliff"></a>

### Problemi di connessione

Quando si eseguono le operazioni, può verificarsi l'errore seguente:

> `Failed to establish a new connection: [Errno 8] nodename nor servname provided, or not known`

In questo caso, ricontrollare che l'endpoint del cluster specificato sia raggiungibile e in ascolto. Verificare anche che l'interfaccia utente di Service Fabric Explorer sia raggiungibile nell'host e nella porta specificati. Usare `az sf cluster select` per aggiornare l'endpoint.

<a id="getting-detailed-logs" class="xliff"></a>

### Acquisizione di log dettagliati

Quando si esegue il debug o si segnala un problema, è utile includere log dettagliati. L'interfaccia della riga di comando di Azure include un flag `--debug` globale che aumenta il livello di dettaglio dei log.

<a id="command-help-and-syntax" class="xliff"></a>

### Informazioni sui comandi e sintassi

I comandi di Service Fabric seguono la stessa convenzione dell'interfaccia della riga di comando di Azure. Specificare il flag `-h` per ottenere informazioni su uno specifico comando o gruppo di comandi. ad esempio:

```azurecli
az sf application -h
```

oppure

```azurecli
az sf application create -h
```

