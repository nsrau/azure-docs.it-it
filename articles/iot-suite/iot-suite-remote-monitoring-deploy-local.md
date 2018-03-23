---
title: Distribuire la soluzione di monitoraggio remoto in locale - Azure | Microsoft Docs
description: Questa esercitazione illustra come distribuire la soluzione preconfigurata di monitoraggio remoto in un computer locale a scopo di test e sviluppo.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 03/07/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 77f40e2f10cbdb9930a22a4248e19bb3356af7af
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="deploy-the-remote-monitoring-preconfigured-solution-locally"></a>Distribuire la soluzione preconfigurata di monitoraggio remoto in locale

Questo articolo illustra come distribuire la soluzione preconfigurata di monitoraggio remoto in un computer locale a scopo di test e sviluppo. Questo approccio prevede la distribuzione dei microservizi in un contenitore Docker locale e l'uso di Hub IoT, di Cosmos DB e dei servizi di archiviazione di Azure nel cloud. Per distribuire i servizi cloud di Azure si usa l'interfaccia della riga di comando per le soluzioni preconfigurate (PCS).

## <a name="prerequisites"></a>Prerequisiti

Per distribuire i servizi di Azure usati dalla soluzione preconfigurata di monitoraggio remoto, è necessaria una sottoscrizione di Azure attiva.

Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/).

Per completare la distribuzione in locale, è necessario che nel computer di sviluppo locale siano installati gli strumenti seguenti:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Docker Compose](https://docs.docker.com/compose/install/)
* [Node.js](https://nodejs.org/) (prerequisito per l'interfaccia della riga di comando PCS)
* Interfaccia della riga di comando PCS

> [!NOTE]
> Questi strumenti sono disponibili in numerose piattaforme, tra cui Windows, Linux e iOS.

### <a name="install-the-pcs-cli"></a>Installare l'interfaccia della riga di comando PCS

Per installare l'interfaccia della riga di comando, eseguire il comando seguente nell'ambiente della riga di comando:

```cmd/sh
npm install iot-solutions -g
```

Per altre informazioni sull'interfaccia della riga di comando, vedere [How to use the CLI](https://github.com/Azure/pcs-cli/blob/master/README.md) (Come usare l'interfaccia della riga di comando).

## <a name="deploy-the-azure-services"></a>Distribuire i servizi di Azure

Nonostante questo articoli illustri come eseguire i microservizi in locale, questi dipendono da tre servizi di Azure eseguiti nel cloud. È possibile distribuire questi servizi di Azure manualmente tramite il portale di Azure oppure con l'interfaccia della riga di comando PCS. Questo articolo illustra come usare lo strumento `pcs`.

### <a name="sign-in-to-the-cli"></a>Accedere all'interfaccia della riga di comando

Prima di poter distribuire la soluzione preconfigurata, è necessario accedere alla sottoscrizione di Azure tramite l'interfaccia della riga di comando in questo modo:

```cmd/sh
pcs login
```

Seguire le istruzioni visualizzate sullo schermo per completare il processo di accesso.

### <a name="run-a-local-deployment"></a>Eseguire una distribuzione in locale

Usare il comando seguente per avviare la distribuzione in locale:

```cmd/pcs
pcs -s local
```

Lo script richiede le informazioni seguenti:

* Nome della soluzione.
* Sottoscrizione di Azure da usare.
* Località del data center di Azure da usare.

Lo script crea un'istanza di Hub IoT, un'istanza di Cosmos DB e un account di archiviazione di Azure in un gruppo di risorse nella sottoscrizione di Azure. Il nome del gruppo di risorse è il nome della soluzione scelto durante l'esecuzione dello strumento `pcs`.

L'esecuzione dello script richiede alcuni minuti. Al termine, verrà visualizzato il messaggio `Copy the following environment variables to /scripts/local/.env file:`. Copiare le definizioni delle variabili di ambiente riportate dopo il messaggio. Verranno usate in un passaggio successivo.

## <a name="download-the-source-code"></a>Scaricare il codice sorgente

Il repository di codice sorgente per il monitoraggio remoto include i file di configurazione di Docker necessari per scaricare, configurare ed eseguire le immagini Docker contenenti i microservizi. Per clonare il repository, passare a una cartella idonea nel computer locale ed eseguire uno dei comandi seguenti.

Per installare le implementazioni Java dei microservizi, eseguire:

```cmd/sh
git clone --recursive https://github.com/Azure/azure-iot-pcs-remote-monitoring-java
```

Per installare le implementazioni .NET dei microservizi, eseguire:

```cmd\sh
git clone --recursive https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet
```

Questi comandi scaricano il codice sorgente per tutti i microservizi. Nonostante non sia necessario per eseguire i microservizi in Docker, il codice sorgente è utile se si intende modificare in un secondo momento la soluzione preconfigurata e testare le modifiche in locale.

## <a name="run-the-microservices-in-docker"></a>Eseguire i microservizi in Docker

Per eseguire i microservizi in Docker, per prima cosa modificare il file **scripts\\local\\.env** nella copia locale del repository. Sostituire l'intero contenuto del file con le definizioni delle variabili di ambiente di cui si è preso nota in precedenza durante l'esecuzione del comando `pcs`. Queste variabili di ambiente consentono ai microservizi nel contenitore Docker di connettersi ai servizi di Azure creati dallo strumento `pcs`.

Per eseguire la soluzione preconfigurata, passare alla cartella **scripts\local** nell'ambiente della riga di comando ed eseguire questo comando:

```cmd\sh
docker-compose up
```

Alla prima esecuzione di questo comando, Docker scarica le immagini dei microservizi dall'hub Docker per creare i contenitori in locale. Alle esecuzioni successive, Docker esegue immediatamente i contenitori.

È possibile usare una shell separata per visualizzare i log del contenitore. Per prima cosa trovare l'ID contenitore con il comando `docker ps -a`. Usare quindi `docker logs {container-id} --tail 1000` per visualizzare le ultime 1000 voci di log per il contenitore specificato.

Per accedere al dashboard della soluzione di monitoraggio remoto, passare a [http://localhost:8080](http://localhost:8080) nel browser.

## <a name="tidy-up"></a>Operazioni finali

Per evitare l'addebito di costi non necessari, al termine del test rimuovere i servizi cloud dalla sottoscrizione di Azure. Il modo più semplice per rimuovere i servizi consiste nell'usare il portale di Azure per eliminare il gruppo di risorse creato dallo strumento `pcs`.

Usare il comando `docker-compose down --rmi all` per rimuovere le immagini Docker e liberare spazio nel computer locale. È anche possibile eliminare la copia locale del repository per il monitoraggio remoto che è stata creata clonando il codice sorgente da GitHub.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Configurare la soluzione preconfigurata
> * Distribuire la soluzione preconfigurata
> * Accedere alla soluzione preconfigurata

Ora che è stata distribuita la soluzione di monitoraggio remoto, il passaggio successivo consiste nell'[esplorare le funzionalità del dashboard della soluzione](./iot-suite-remote-monitoring-deploy.md).

<!-- Next tutorials in the sequence -->