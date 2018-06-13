---
title: Distribuire la soluzione di monitoraggio remoto Java - Azure | Microsoft Docs
description: Questa esercitazione illustra come effettuare il provisioning dell'acceleratore di soluzioni per il monitoraggio remoto tramite l'interfaccia della riga di comando.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 01/29/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: f158af573475964eca1ff168ecf3eadc58c2a394
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
ms.locfileid: "33774228"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-using-the-cli"></a>Distribuire l'acceleratore di soluzioni per il monitoraggio remoto tramite l'interfaccia della riga di comando

Questa esercitazione illustra come effettuare il provisioning dell'acceleratore di soluzioni di monitoraggio remoto. Distribuire la soluzione usando l'interfaccia della riga di comando. È anche possibile distribuire la soluzione usando l'interfaccia utente basata sul Web disponibile all'indirizzo azureiotsuite.com. Per informazioni su questa opzione, vedere [Distribuire l'acceleratore di soluzioni per il monitoraggio remoto](iot-suite-remote-monitoring-deploy.md).

## <a name="prerequisites"></a>prerequisiti

Per distribuire l'acceleratore di soluzioni per il monitoraggio remoto, è necessaria una sottoscrizione di Azure attiva.

Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/).

Per eseguire l'interfaccia della riga di comando, è necessario che [Node.js](https://nodejs.org/) sia installato nel computer locale.

## <a name="install-the-cli"></a>Installare l'interfaccia della riga di comando

Per installare l'interfaccia della riga di comando, eseguire il comando seguente nell'ambiente della riga di comando:

```cmd/sh
npm install iot-solutions -g
```

## <a name="sign-in-to-the-cli"></a>Accedere all'interfaccia della riga di comando

Prima di poter distribuire l'acceleratore di soluzioni, è necessario accedere alla sottoscrizione di Azure tramite l'interfaccia della riga di comando in questo modo:

```cmd/sh
pcs login
```

Seguire le istruzioni visualizzate sullo schermo per completare il processo di accesso.

## <a name="deployment-options"></a>Opzioni di distribuzione

Quando si distribuisce l'acceleratore di soluzioni, sono disponibili diverse opzioni per la configurazione del processo di distribuzione:

| Opzione | Valori | DESCRIZIONE |
| ------ | ------ | ----------- |
| SKU    | `basic`, `standard`, `local` | Una distribuzione _di base_ è destinata agli ambienti di test e demo e distribuisce tutti i microservizi in un'unica macchina virtuale. Una distribuzione _standard_ è destinata agli ambienti di produzione e distribuisce i microservizi in più macchine virtuali. Una distribuzione _local_ configura un contenitore Docker per l'esecuzione dei microservizi nel computer locale e usa servizi di Azure, ad esempio l'archiviazione e Cosmos DB, nel cloud. |
| Runtime | `dotnet`, `java` | Seleziona l'implementazione del linguaggio dei microservizi. |

Per altre informazioni su come usare la distribuzione local, vedere [Running the remote monitoring solution locally](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Running-the-Remote-Monitoring-Solution-Locally#deploy-azure-services-and-set-environment-variables) (Esecuzione della soluzione di monitoraggio remota in locale).

## <a name="basic-vs-standard-deployments"></a>Distribuzioni di base e distribuzioni standard

### <a name="basic"></a>Basic
La distribuzione di base è orientata verso la presentazione della soluzione. Per ridurre il costo di questa dimostrazione, tutti i microservizi vengono distribuiti in una singola macchina virtuale; questa non viene considerata un'architettura di produzione.

L'opzione di distribuzione Standard dovrebbe essere usata quando si è pronti per personalizzare un'architettura di produzione, creata per la scalabilità e l'estendibilità.

Quando si crea una soluzione di base, viene effettuato il provisioning dei servizi di Azure seguenti nella sottoscrizione dell'utente a questo costo: 

| Conteggio | Risorsa                       | type         | Utilizzo |
|-------|--------------------------------|--------------|----------|
| 1     | [Macchina virtuale Linux](https://azure.microsoft.com/services/virtual-machines/) | Standard D1 V2  | Microservizi di hosting |
| 1     | [Hub IoT Azure](https://azure.microsoft.com/services/iot-hub/)                  | S1 - Livello base | Comunicazione e gestione dei dispositivi |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)              | Standard        | Archiviazione dei dati di configurazione e di telemetria dei dispositivi quali regole, avvisi e messaggi |  
| 1     | [Account di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)  | Standard        | Archiviazione per le macchine virtuali e i checkpoint di streaming |
| 1     | [Applicazione Web](https://azure.microsoft.com/services/app-service/web/)        |                 | Hosting dell'applicazione web front-end |

### <a name="standard"></a>Standard
La distribuzione standard è una distribuzione di produzione pronta che uno sviluppatore può personalizzare ed estendere in modo da soddisfare le proprie esigenze. Per scopi di affidabilità e scalabilità, i microservizi dell'applicazione vengono creati come contenitori Docker e distribuiti usando un agente di orchestrazione (per impostazione predefinita, [Kubernetes](https://kubernetes.io/)). L'agente di orchestrazione è responsabile della distribuzione, del ridimensionamento e della gestione dell'applicazione.

Quando si crea una soluzione standard, viene effettuato il provisioning dei servizi di Azure nella sottoscrizione dell'utente al costo seguente:

| Conteggio | Risorsa                                     | SKU / Dimensioni      | Utilizzo |
|-------|----------------------------------------------|-----------------|----------|
| 4     | [Macchine virtuali Linux](https://azure.microsoft.com/services/virtual-machines/)   | Standard D2 V2  | 1 master e 3 agenti per microservizi di hosting con ridondanza |
| 1     | [Servizio contenitore di Azure](https://azure.microsoft.com/services/container-service/) |                 | Agente di orchestrazione [Kubernetes](https://kubernetes.io) |
| 1     | [Hub IoT di Azure][https://azure.microsoft.com/services/iot-hub/]                     | S1 - Livello base | Gestione, comando e controllo dei dispositivi |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)                 | Standard        | Archiviazione dei dati di configurazione e di telemetria dei dispositivi quali regole, avvisi e messaggi |
| 5     | [Account di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)    | Standard        | 4 per l'archiviazione di macchine virtuali e 1 per i checkpoint di streaming |
| 1     | [Servizio app](https://azure.microsoft.com/services/app-service/web/)             | S1 Standard     | Gateway applicazione su SSL |

> Le informazioni sui prezzi di questi servizi sono reperibili [qui](https://azure.microsoft.com/pricing). I dati di utilizzo e i dettagli di fatturazione per la sottoscrizione sono reperibili nel [portale di Azure](https://portal.azure.com/).

## <a name="deploy-the-solution-accelerator"></a>Distribuire l'acceleratore di soluzioni

### <a name="example-deploy-net-version"></a>Esempio: Distribuire la versione .NET

L'esempio seguente mostra come distribuire la versione .NET di base dell'acceleratore di soluzioni di monitoraggio remoto:

```cmd/sh
pcs -t remotemonitoring -s basic -r dotnet
```

### <a name="example-deploy-java-version"></a>Esempio: Distribuire la versione Java

L'esempio seguente mostra come distribuire la versione Java standard dell'acceleratore di soluzioni di monitoraggio remoto:

```cmd/sh
pcs -t remotemonitoring -s standard -r java
```

### <a name="pcs-command-options"></a>Opzioni del comando pcs

Quando si esegue il comando `pcs` per distribuire una soluzione, vengono chiesti gli elementi seguenti:

- Nome per la soluzione. Il nome deve essere univoco.
- Sottoscrizione di Azure da usare.
- Posizione.
- Credenziali per le macchine virtuali che ospitano i microservizi. È possibile usare queste credenziali per accedere alle macchine virtuali per la risoluzione dei problemi.

Al termine del comando `pcs`, viene visualizzato l'URL della distribuzione del nuovo acceleratore di soluzioni. Il comando `pcs` crea anche un file `{deployment-name}-output.json` con informazioni aggiuntive, tra cui il nome dell'hub IoT di cui è stato effettuato automaticamente il provisioning.

Per altre informazioni sui parametri della riga di comando, eseguire:

```cmd/sh
pcs -h
```

Per altre informazioni sull'interfaccia della riga di comando, vedere [How to use the CLI](https://github.com/Azure/pcs-cli/blob/master/README.md) (Come usare l'interfaccia della riga di comando).

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Configurare l'acceleratore di soluzioni
> * Distribuire l'acceleratore di soluzioni
> * Accedere all'acceleratore di soluzioni

Ora che è stata distribuita la soluzione di monitoraggio remoto, il passaggio successivo consiste nell'[esplorare le funzionalità del dashboard della soluzione](./iot-suite-remote-monitoring-deploy.md).

<!-- Next tutorials in the sequence -->