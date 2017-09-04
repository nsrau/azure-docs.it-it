---
title: Introduzione all'interfaccia della riga di comando di Azure Service Fabric (sfctl)
description: Informazioni su come usare l'interfaccia della riga di comando di Azure Service Fabric e su come connettersi al cluster e gestire le applicazioni.
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: get-started-article
ms.date: 08/22/2017
ms.author: edwardsa
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 5ce9adf6c82e3a5521883c5de1e0689d5bf0d94e
ms.contentlocale: it-it
ms.lasthandoff: 08/24/2017

---
# <a name="azure-service-fabric-command-line"></a>Riga di comando di Azure Service Fabric

L'interfaccia della riga di comando di Azure Service Fabric (sfctl) è un'utilità della riga di comando per l'interazione e la gestione delle entità di Azure Service Fabric. È possibile usare sfctl con cluster Windows o Linux. Sfctl viene eseguito solo sulle piattaforme che supportano Python.

## <a name="prerequisites"></a>Prerequisiti

Prima dell'installazione, verificare che nell'ambiente siano installati sia Python che pip. Per informazioni, vedere la [documentazione rapida di pip](https://pip.pypa.io/en/latest/quickstart/) e la [documentazione sull'installazione di Python](https://wiki.python.org/moin/BeginnersGuide/Download) ufficiale.

Anche se sono supportati sia Python 2.7 che 3.6, è consigliabile usare Python 3.6.

## <a name="install"></a>Installa

L'interfaccia della riga di comando di Azure Service Fabric (sfctl) è un pacchetto Python. Per installare la versione più recente, eseguire:

```bash
pip install sfctl
```

Dopo l'installazione, eseguire `sfctl -h` per ottenere informazioni sui comandi disponibili.

## <a name="cli-syntax"></a>Sintassi dell'interfaccia della riga di comando

I comandi sono sempre preceduti da `sfctl`. Per informazioni generali su tutti i comandi utilizzabili, usare `sfctl -h`. Per informazioni su un singolo comando, usare `sfctl <command> -h`.

I comandi seguono una struttura ripetibile, in cui la destinazione del comando precede il verbo o l'azione:

```azurecli
sfctl <object> <action>
```

In questo esempio `<object>` è la destinazione per `<action>`.

## <a name="select-a-cluster"></a>Selezionare un cluster

Prima di eseguire qualsiasi operazione, è necessario selezionare un cluster a cui connettersi. Usare, ad esempio, quanto segue per selezionare e connettersi al cluster con il nome `testcluster.com`.

> [!WARNING]
> Non usare cluster di Service Fabric non protetti in un ambiente di produzione.

```azurecli
sfctl cluster select --endpoint http://testcluster.com:19080
```

L'endpoint del cluster deve essere preceduto da `http` o `https` e deve includere la porta per il gateway HTTP. La porta e l'indirizzo sono gli stessi dell'URL di Service Fabric Explorer.

Per i cluster protetti da un certificato, è possibile specificare un certificato con codifica PEM. Il certificato può essere specificato come file singolo o come coppia di certificato e chiave.

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

Per altre informazioni, vedere l'articolo su come [connettersi a un cluster di Azure Service Fabric sicuro](service-fabric-connect-to-secure-cluster.md).

## <a name="basic-operations"></a>Operazioni di base

Le informazioni di connessione al cluster vengono mantenute per più sessioni sfctl. Dopo aver selezionato un cluster di Service Fabric, è possibile eseguirvi qualsiasi comando di Service Fabric.

Per ottenere lo stato di integrità di un cluster di Service Fabric, ad esempio, usare il comando seguente:

```azurecli
sfctl cluster health
```

Il comando restituisce l'output seguente:

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

## <a name="tips-and-troubleshooting"></a>Suggerimenti e risoluzione dei problemi

Alcuni suggerimenti per risolvere i problemi comuni.

### <a name="convert-a-certificate-from-pfx-to-pem-format"></a>Convertire un certificato dal formato PFX al formato PEM

L'interfaccia della riga di comando di Service Fabric supporta i certificati lato client come file con estensione pem. Se si usano file PFX di Windows, è necessario convertire tali certificati nel formato PEM. Per convertire un file PFX in un file PEM, usare il comando seguente:

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

Per altre informazioni, vedere la [documentazione di OpenSSL](https://www.openssl.org/docs/).

### <a name="connection-issues"></a>Problemi di connessione

Alcune operazioni potrebbero generare il messaggio seguente:

`Failed to establish a new connection: [Errno 8] nodename nor servname provided, or not known`

Verificare che l'endpoint del cluster specificato sia disponibile e in ascolto. Verificare anche che l'interfaccia utente di Service Fabric Explorer sia disponibile nell'host e nella porta specificati. Per aggiornare l'endpoint, usare `sfctl cluster select`.

### <a name="detailed-logs"></a>Log dettagliati

I log dettagliati si rivelano spesso utili per il debug o la segnalazione di un problema. È presente un flag `--debug` globale che aumenta il livello di dettaglio dei file di log.

### <a name="command-help-and-syntax"></a>Informazioni sui comandi e sintassi

Per informazioni su un comando o un gruppo di comandi specifico, usare il flag `-h`:

```azurecli
sfctl application -h
```

Un altro esempio:

```azurecli
sfctl application create -h
```

## <a name="next-steps"></a>Passaggi successivi

* [Deploy an application with the Azure Service Fabric CLI (Distribuire un'applicazione con l'interfaccia della riga di comando di Azure Service Fabric)](service-fabric-application-lifecycle-sfctl.md)
* [Introduzione a Service Fabric in Linux](service-fabric-get-started-linux.md)

