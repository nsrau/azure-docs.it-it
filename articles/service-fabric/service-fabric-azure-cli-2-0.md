---
title: Introduzione ad Azure Service Fabric e all'interfaccia della riga di comando di Azure 2.0
description: Informazioni su come usare il modulo comandi di Azure Service Fabric nell'interfaccia della riga di comando di Azure versione 2.0 e su come connettersi al cluster e gestire le applicazioni.
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: get-started-article
ms.date: 06/21/2017
ms.author: edwardsa
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: ee3302b984ca2f5509755dc17b0a5fd06ace0afe
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="azure-service-fabric-and-azure-cli-20"></a>Azure Service Fabric e interfaccia della riga di comando di Azure 2.0

L'interfaccia della riga di comando di Azure versione 2.0 è uno strumento da riga di comando che include comandi utili per gestire i cluster di Azure Service Fabric. Questo articolo illustra come iniziare a usare l'interfaccia della riga di comando di Azure e Service Fabric.

## <a name="install-azure-cli-20"></a>Installare l'interfaccia della riga di comando di Azure 2.0

È possibile usare i comandi dell'interfaccia della riga di comando di Azure 2.0 per interagire con i cluster di Service Fabric e gestirli. Per ottenere l'ultima versione dell'interfaccia della riga di comando di Azure, seguire il [processo di installazione standard dell'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli).

Per altre informazioni, vedere la [panoramica dell'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/en-us/cli/azure/overview).

## <a name="azure-cli-syntax"></a>Sintassi dell'interfaccia della riga di comando di Azure

Nell'interfaccia della riga di comando di Azure, tutti i comandi di Service Fabric sono preceduti da `az sf`. Per informazioni generali sui comandi utilizzabili, usare `az sf -h`. Per informazioni su un singolo comando, usare `az sf <command> -h`.

I comandi di Service Fabric nell'interfaccia della riga di comando di Azure seguono questo modello di denominazione:

```azurecli
az sf <object> <action>
```

`<object>` è la destinazione di `<action>`.

## <a name="select-a-cluster"></a>Selezionare un cluster

Prima di eseguire qualsiasi operazione, è necessario selezionare un cluster a cui connettersi. Per un esempio, vedere il codice seguente. Il codice si connette a un cluster non protetto.

> [!WARNING]
> Non usare cluster di Service Fabric non protetti in un ambiente di produzione.

```azurecli
az sf cluster select --endpoint http://testcluster.com:19080
```

L'endpoint del cluster deve essere preceduto da `http` o `https` e deve includere la porta per il gateway HTTP. La porta e l'indirizzo sono gli stessi dell'URL di Service Fabric Explorer.

Per i cluster protetti con un certificato, è possibile usare file con estensione pem non crittografati oppure file con estensione crt o key. Ad esempio:

```azurecli
az sf cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

Per altre informazioni, vedere l'articolo su come [connettersi a un cluster di Azure Service Fabric sicuro](service-fabric-connect-to-secure-cluster.md).

> [!NOTE]
> Il comando `select` non determina alcuna operazione sulle richieste prima di essere completato. Per verificare di aver specificato correttamente un cluster, usare un comando come `az sf cluster health`. Verificare che il comando non restituisca errori.

## <a name="basic-operations"></a>Operazioni di base

Le informazioni di connessione al cluster vengono mantenute per più sessioni dell'interfaccia della riga di comando di Azure. Dopo aver selezionato un cluster di Service Fabric, è possibile eseguirvi qualsiasi comando di Service Fabric.

Per ottenere lo stato di integrità di un cluster di Service Fabric, ad esempio, usare il comando seguente:

```azurecli
az sf cluster health
```

Il comando restituisce l'output seguente, presupponendo che l'output JSON sia specificato nella configurazione dell'interfaccia della riga di comando di Azure:

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

Le informazioni seguenti potrebbero risultare utili se si verificano problemi durante l'uso dei comandi di Service Fabric nell'interfaccia della riga di comando di Azure.

### <a name="convert-a-certificate-from-pfx-to-pem-format"></a>Convertire un certificato dal formato PFX al formato PEM

L'interfaccia della riga di comando di Azure supporta i certificati lato client come file con estensione pem. Se si usano file PFX di Windows, è necessario convertire tali certificati nel formato PEM. Per convertire un file PFX in un file PEM, usare il comando seguente:

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

Per altre informazioni, vedere la [documentazione di OpenSSL](https://www.openssl.org/docs/).

### <a name="connection-issues"></a>Problemi di connessione

Alcune operazioni potrebbero generare il messaggio seguente:

`Failed to establish a new connection: [Errno 8] nodename nor servname provided, or not known`

Verificare che l'endpoint del cluster specificato sia disponibile e in ascolto. Verificare anche che l'interfaccia utente di Service Fabric Explorer sia disponibile nell'host e nella porta specificati. Per aggiornare l'endpoint, usare `az sf cluster select`.

### <a name="detailed-logs"></a>Log dettagliati

I log dettagliati si rivelano spesso utili per il debug o la segnalazione di un problema. L'interfaccia della riga di comando di Azure include un flag `--debug` globale che aumenta il livello di dettaglio dei file di log.

### <a name="command-help-and-syntax"></a>Informazioni sui comandi e sintassi

I comandi di Service Fabric seguono le stesse convenzioni dell'interfaccia della riga di comando di Azure. Per informazioni su un comando o un gruppo di comandi specifico, usare il flag `-h`:

```azurecli
az sf application -h
```

Di seguito è riportato un altro esempio:

```azurecli
az sf application create -h
```

