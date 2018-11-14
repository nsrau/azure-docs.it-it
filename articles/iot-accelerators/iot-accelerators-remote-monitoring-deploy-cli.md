---
title: Distribuire la soluzione di monitoraggio remoto tramite l'interfaccia della riga di comando di Azure | Microsoft Docs
description: Questa guida procedurale illustra come effettuare il provisioning dell'acceleratore di soluzioni di monitoraggio remoto tramite l'interfaccia della riga di comando.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/30/2018
ms.topic: conceptual
ms.openlocfilehash: 5704cc21b14d83ebc30cd29f52102c751cfb11f2
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51248011"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-using-the-cli"></a>Distribuire l'acceleratore di soluzioni di monitoraggio remoto tramite l'interfaccia della riga di comando

Questa guida procedurale illustra come distribuire l'acceleratore di soluzioni di monitoraggio remoto. Distribuire la soluzione usando l'interfaccia della riga di comando. È possibile distribuire la soluzione anche usando l'interfaccia utente basata sul Web disponibile all'indirizzo azureiotsuite.com. Per informazioni su questa opzione, vedere [Distribuire l'acceleratore di soluzioni per il monitoraggio remoto](quickstart-remote-monitoring-deploy.md).

## <a name="prerequisites"></a>Prerequisiti

Per distribuire l'acceleratore di soluzioni di monitoraggio remoto, è necessaria una sottoscrizione di Azure attiva.

Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

Per eseguire l'interfaccia della riga di comando, è necessario che [Node.js](https://nodejs.org/) sia installato nel computer locale.

## <a name="install-the-cli"></a>Installare l'interfaccia della riga di comando

Per installare l'interfaccia della riga di comando, eseguire il comando seguente nell'ambiente della riga di comando:

```cmd/sh
npm install iot-solutions -g
```

## <a name="sign-in-to-the-cli"></a>Accedere all'interfaccia della riga di comando

Prima di poter distribuire l'acceleratore di soluzioni, è necessario accedere alla sottoscrizione di Azure tramite l'interfaccia della riga di comando:

```cmd/sh
pcs login
```

Seguire le istruzioni visualizzate sullo schermo per completare il processo di accesso.

## <a name="deployment-options"></a>Opzioni di distribuzione

Quando si distribuisce l'acceleratore di soluzioni, sono disponibili diverse opzioni per la configurazione del processo di distribuzione:

| Opzione | Valori | DESCRIZIONE |
| ------ | ------ | ----------- |
| SKU    | `basic`, `standard`, `local` | Una distribuzione _di base_ è destinata agli ambienti di test e demo e distribuisce tutti i microservizi in un'unica macchina virtuale. Una distribuzione _standard_ è destinata agli ambienti di produzione e distribuisce i microservizi a più macchine virtuali. Una distribuzione _locale_ configura un contenitore Docker per l'esecuzione dei microservizi nel computer locale e usa servizi cloud di Azure, ad esempio l'archiviazione e Cosmos DB. |
| Runtime | `dotnet`, `java` | Seleziona l'implementazione del linguaggio dei microservizi. |

Per informazioni su come usare l'opzione di distribuzione locale, vedere [Running the remote monitoring solution locally](iot-accelerators-remote-monitoring-deploy-local.md) (Esecuzione della soluzione di monitoraggio remota in locale).

## <a name="basic-and-standard-deployments"></a>Distribuzioni di base e standard

Questa sezione riepiloga le differenze principali tra una distribuzione di base e una standard.

### <a name="basic"></a>Basic

La distribuzione di base è orientata verso la presentazione della soluzione. Per ridurre i costi, tutti i microservizi vengono distribuiti in una singola macchina virtuale. Questa distribuzione non usa un'architettura pronta per l'ambiente di produzione.

Una distribuzione di base crea i servizi seguenti nella sottoscrizione di Azure:

| Conteggio | Risorsa                       | type         | Utilizzo |
|-------|--------------------------------|--------------|----------|
| 1     | [Macchina virtuale Linux](https://azure.microsoft.com/services/virtual-machines/) | Standard D1 V2  | Microservizi di hosting |
| 1     | [Hub IoT Azure](https://azure.microsoft.com/services/iot-hub/)                  | S1 - livello Standard | Comunicazione e gestione dei dispositivi |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)              | Standard        | Archiviazione dei dati di configurazione, delle regole, degli allarmi e di altre risorse di archiviazione offline sicura |  
| 1     | [Account di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)  | Standard        | Archiviazione per le macchine virtuali e i checkpoint di streaming |
| 1     | [Applicazione Web](https://azure.microsoft.com/services/app-service/web/)        |                 | Hosting dell'applicazione web front-end |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | Gestione della sicurezza e delle identità utente |
| 1     | [Mappe di Azure](https://azure.microsoft.com/services/azure-maps/)        | Standard                | Visualizzazione della posizione degli asset |
| 1     | [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)        |   3 unità              | Abilitazione dell'analisi in tempo reale |
| 1     | [Servizio Device Provisioning di Azure](https://docs.microsoft.com/azure/iot-dps/)        |       S1          | Provisioning dei dispositivi su larga scala |
| 1     | [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)        |   Unità S1 – 1              | Archiviazione dei dati e abilitazione dell'analisi dettagliata dei dati di telemetria |

### <a name="standard"></a>Standard

Una distribuzione standard è una distribuzione pronta per l'ambiente di produzione che uno sviluppatore può personalizzare ed estendere. Usare l'opzione di distribuzione standard quando si è pronti a personalizzare un'architettura per la produzione, creata per la scalabilità e l'estendibilità. I microservizi dell'applicazione vengono creati come contenitori Docker e distribuiti tramite il servizio Kubernetes di Azure (AKS). L'agente di orchestrazione Kubernetes distribuisce, ridimensiona e gestisce i microservizi.

Una distribuzione standard crea i servizi seguenti nella sottoscrizione di Azure:

| Conteggio | Risorsa                                     | SKU / Dimensioni      | Utilizzo |
|-------|----------------------------------------------|-----------------|----------|
| 1     | [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service)| Usa un servizio di orchestrazione di contenitori Kubernetes completamente gestito, con un'impostazione predefinita di tre agenti|
| 1     | [Hub IoT Azure](https://azure.microsoft.com/services/iot-hub/)                     | S2 - livello Standard | Gestione, comando e controllo dei dispositivi |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)                 | Standard        | Archiviazione dei dati di configurazione e di telemetria dei dispositivi quali regole, avvisi e messaggi |
| 5     | [Account di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)    | Standard        | 4 per l'archiviazione di macchine virtuali e 1 per i checkpoint di streaming |
| 1     | [Servizio app](https://azure.microsoft.com/services/app-service/web/)             | S1 Standard     | Gateway applicazione su SSL |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | Gestione della sicurezza e delle identità utente |
| 1     | [Mappe di Azure](https://azure.microsoft.com/services/azure-maps/)        | Standard                | Visualizzazione della posizione degli asset |
| 1     | [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)        |   3 unità              | Abilitazione dell'analisi in tempo reale |
| 1     | [Servizio Device Provisioning di Azure](https://docs.microsoft.com/azure/iot-dps/)        |       S1          | Provisioning dei dispositivi su larga scala |
| 1     | [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)        |   Unità S1 – 1              | Archiviazione dei dati e abilitazione dell'analisi dettagliata dei dati di telemetria |

> [!NOTE]
> Le informazioni sui prezzi di questi servizi sono reperibili sul sito [https://azure.microsoft.com/pricing](https://azure.microsoft.com/pricing). I dettagli sull'utilizzo e la fatturazione per la sottoscrizione sono reperibili nel [portale di Azure](https://portal.azure.com/).

## <a name="deploy-the-solution-accelerator"></a>Distribuire l'acceleratore di soluzioni

Esempi di distribuzione:

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

Quando si esegue il comando `pcs` per distribuire una soluzione, vengono richieste le informazioni seguenti:

- Nome per la soluzione. Il nome deve essere univoco.
- Sottoscrizione di Azure da usare.
- Posizione.
- Credenziali per le macchine virtuali che ospitano i microservizi. È possibile usare queste credenziali per accedere alle macchine virtuali per la risoluzione dei problemi.

Al termine dell'esecuzione del comando `pcs`, viene visualizzato l'URL del nuovo acceleratore di soluzioni. Il comando `pcs` crea anche un file `{deployment-name}-output.json` che contiene informazioni come il nome dell'hub IoT creato.

Per altre informazioni sui parametri della riga di comando, eseguire:

```cmd/sh
pcs -h
```

Per altre informazioni sull'interfaccia della riga di comando, vedere [How to use the CLI](https://github.com/Azure/pcs-cli/blob/master/README.md) (Come usare l'interfaccia della riga di comando).

## <a name="next-steps"></a>Passaggi successivi

In questa guida procedurale si è appreso come:

> [!div class="checklist"]
> * Configurare l'acceleratore di soluzioni
> * Distribuire l'acceleratore di soluzioni
> * Accedere all'acceleratore di soluzioni

Ora che è stata distribuita la soluzione di monitoraggio remoto, il passaggio successivo consiste nell'[esplorare le funzionalità del dashboard della soluzione](./quickstart-remote-monitoring-deploy.md).

<!-- Next how-to guides in the sequence -->
