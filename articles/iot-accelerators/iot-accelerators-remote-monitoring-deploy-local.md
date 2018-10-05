---
title: Distribuire la soluzione di monitoraggio remoto in locale - Azure | Microsoft Docs
description: Questa guida pratica illustra come distribuire l'acceleratore di soluzioni di monitoraggio remoto in un computer locale a scopo di test e sviluppo.
author: asdonald
manager: timlt
ms.author: asdonald
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 09/26/2018
ms.topic: conceptual
ms.openlocfilehash: 7007b1406dbcfab3af4700418ac2ce07b9e521c0
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2018
ms.locfileid: "47407434"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally"></a>Distribuire l'acceleratore di soluzioni di monitoraggio remoto localmente

Questo articolo illustra come distribuire l'acceleratore di soluzioni di monitoraggio remoto in un computer locale a scopo di test e sviluppo. L'approccio descritto in questo articolo prevede la distribuzione dei microservizi in un contenitore Docker locale e l'uso dei servizi Hub IoT, Cosmos DB e Azure Time Series Insights nel cloud. Per informazioni su come eseguire l'acceleratore di soluzione di monitoraggio remoto in un'IDE nel computer locale, vedere [Starting Microservices on local environment](https://github.com/Azure/remote-monitoring-services-java/blob/master/docs/LOCAL_DEPLOYMENT.md) (Avvio di microservizi in ambiente locale) su GitHub.

## <a name="prerequisites"></a>Prerequisiti

Per distribuire i servizi di Azure usati dall'acceleratore di soluzioni di monitoraggio remoto, è necessaria una sottoscrizione di Azure attiva.

Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/).

Per completare la distribuzione in locale, è necessario che nel computer di sviluppo locale siano installati gli strumenti seguenti:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Docker Compose](https://docs.docker.com/compose/install/)
* [Node.js v8](https://nodejs.org/) - Questo software è un prerequisito per l'interfaccia della riga di comando di PCS usata dagli script per creare le risorse di Azure. Non usare Node.js v10.

> [!NOTE]
> Questi strumenti sono disponibili in numerose piattaforme, tra cui Windows, Linux e iOS.

### <a name="download-the-source-code"></a>Scaricare il codice sorgente

Il repository GitHub del codice sorgente per il monitoraggio remoto include i file di configurazione di Docker necessari per scaricare, configurare ed eseguire le immagini Docker contenenti i microservizi. Per clonare e creare una versione locale del repository, usare l'ambiente della riga di comando per passare a una cartella adatta nel computer locale e quindi eseguire uno dei comandi seguenti:

Per scaricare la versione più recente delle implementazioni di microservizi Java, eseguire:

```cmd/sh
git clone https://github.com/Azure/remote-monitoring-services-java.git
```

Per scaricare la versione più recente delle implementazioni di microservizi .NET, eseguire:

```cmd\sh
git clone https://github.com/Azure/remote-monitoring-services-dotnet.git
```

> [!NOTE]
> Questi comandi scaricano il codice sorgente per tutti i microservizi oltre agli script usati per eseguire i microservizi in locale. Nonostante non sia necessario per eseguire i microservizi in Docker, il codice sorgente è utile se si intende modificare in un secondo momento l'acceleratore di soluzione e testare le modifiche in locale.

## <a name="deploy-the-azure-services"></a>Distribuire i servizi di Azure

Nonostante questo articolo illustri come eseguire i microservizi in locale, questi dipendono dai servizi di Azure eseguiti nel cloud. È possibile distribuire questi servizi di Azure [manualmente tramite il portale di Azure](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Manual-steps-to-create-azure-resources-for-local-setup) oppure usare lo script fornito. Gli esempi di script seguenti presuppongono che si usi il repository .NET in un computer Windows. Se si lavora in un altro ambiente, modificare in modo appropriato i percorsi, le estensioni di file e i separatori di percorso. Per usare gli script forniti per:

### <a name="create-new-azure-resources"></a>Creare nuove risorse di Azure

Se non sono ancora state create le risorse di Azure necessarie, seguire questa procedura:

1. Nell'ambiente della riga di comando, passare alla cartella **remote-monitoring-services-dotnet\scripts\local\launch** nella copia clonata del repository.

2. Eseguire lo script **start.cmd** e seguire le indicazioni. Lo script richiede di accedere al proprio account Azure e riavviare lo script. Lo script richiede quindi le informazioni seguenti:
    * Nome della soluzione.
    * Sottoscrizione di Azure da usare.
    * Località del data center di Azure da usare.

    Lo script crea un gruppo di risorse in Azure con il nome della soluzione. Questo gruppo di risorse contiene le risorse di Azure usate dall'acceleratore di soluzione.

3. Al termine dell'esecuzione dello script, viene visualizzato un elenco di variabili di ambiente. Seguire le istruzioni nell'output del comando per salvare queste variabili nel file **remote-monitoring-services-dotnet\\scripts\\local\\.env**.

### <a name="use-existing-azure-resources"></a>Usare le risorse di Azure esistenti

Se sono già state create le risorse di Azure necessarie, modificare le definizioni delle variabili di ambiente nel file **remote-monitoring-services-dotnet\\scripts\\local\\.env** con i valori richiesti. Il file con estensione **env** contiene informazioni dettagliate su dove trovare i valori richiesti.

## <a name="run-the-microservices-in-docker"></a>Eseguire i microservizi in Docker

I microservizi in esecuzione nei contenitori Docker locali devono accedere ai servizi in esecuzione in Azure. È possibile testare la connettività Internet dell'ambiente Docker usando il comando seguente che avvia un contenitore di piccole dimensioni e tenta di effettuare il ping di un indirizzo Internet:

```cmd/sh
docker run --rm -ti library/alpine ping google.com
```

Per eseguire l'acceleratore di soluzioni, passare alla cartella **remote-monitoring-services-dotnet\\scripts\\local** nell'ambiente della riga di comando ed eseguire il comando seguente:

```cmd\sh
docker-compose up
```

Alla prima esecuzione di questo comando, Docker scarica le immagini dei microservizi dall'hub Docker per creare i contenitori in locale. Alle esecuzioni successive, Docker esegue immediatamente i contenitori.

È possibile usare una shell separata per visualizzare i log del contenitore. Per prima cosa trovare l'ID contenitore con il comando `docker ps -a`. Usare quindi `docker logs {container-id} --tail 1000` per visualizzare le ultime 1000 voci di log per il contenitore specificato.

Per accedere alla dashboard della soluzione di monitoraggio remoto, passare a [http://localhost:8080](http://localhost:8080) nel browser.

## <a name="clean-up"></a>Eseguire la pulizia

Per evitare l'addebito di costi non necessari, al termine del test rimuovere i servizi cloud dalla sottoscrizione di Azure. Il modo più semplice per rimuovere i servizi consiste nel passare al [portale di Azure](https://ms.portal.azure.com) ed eliminare il gruppo di risorse creato quando è stato eseguito lo script **start.cmd**.

Usare il comando `docker-compose down --rmi all` per rimuovere le immagini Docker e liberare spazio nel computer locale. È anche possibile eliminare la copia locale del repository per il monitoraggio remoto che è stata creata clonando il codice sorgente da GitHub.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Configurare un ambiente di sviluppo locale
> * Configurare l'acceleratore di soluzioni
> * Distribuire l'acceleratore di soluzioni
> * Accedere all'acceleratore di soluzioni

Ora che è stata distribuita la soluzione di monitoraggio remoto, il passaggio successivo consiste nell'[esplorare le funzionalità della dashboard della soluzione](quickstart-remote-monitoring-deploy.md).

<!-- Next tutorials in the sequence -->
