---
title: File di inclusione
description: File di inclusione
services: iot-accelerators
author: avneet723
ms.service: iot-accelerators
ms.topic: include
ms.date: 10/29/2018
ms.author: avneet723
ms.custom: include file
ms.openlocfilehash: 900d75f826830ea7336044a892506d3bec546e30
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2018
ms.locfileid: "51283913"
---
## <a name="download-the-source-code"></a>Scaricare il codice sorgente

I repository di codice sorgente di Monitoraggio remoto includono i file di configurazione Docker necessari per eseguire le immagini Docker dei microservizi.

Per clonare e creare una versione locale del repository, usare l'ambiente della riga di comando per passare a una cartella adatta nel computer locale. Eseguire quindi uno dei set di comandi seguenti per clonare un repository .NET o Java:

Per scaricare la versione più recente delle implementazioni di microservizi .NET, eseguire:

```cmd/sh
git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git

# To retrieve the latest submodules, run the following command:

cd azure-iot-pcs-remote-monitoring-dotnet
git submodule foreach git pull origin master
```

Per scaricare la versione più recente delle implementazioni di microservizi Java, eseguire:

```cmd/sh
git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-java.git

# To retrieve the latest submodules, run the following command:

cd azure-iot-pcs-remote-monitoring-java
git submodule foreach git pull origin master
```

> [!NOTE]
> Questi comandi scaricano il codice sorgente per tutti i microservizi oltre agli script usati per eseguire i microservizi in locale. Nonostante non sia necessario per eseguire i microservizi in Docker, il codice sorgente è utile se si intende modificare in un secondo momento l'acceleratore di soluzione e testare le modifiche in locale.

## <a name="deploy-the-azure-services"></a>Distribuire i servizi di Azure

Nonostante questo articolo illustri come eseguire i microservizi in locale, questi dipendono dai servizi di Azure eseguiti nel cloud. Usare lo script seguente per distribuire i servizi di Azure. Gli esempi di script seguenti presuppongono che si usi il repository .NET in un computer Windows. Se si lavora in un altro ambiente, modificare in modo appropriato i percorsi, le estensioni di file e i separatori di percorso.

### <a name="create-new-azure-resources"></a>Creare nuove risorse di Azure

Se non sono ancora state create le risorse di Azure necessarie, seguire questa procedura:

1. Nell'ambiente della riga di comando passare alla cartella **\services\scripts\local\launch** nella copia clonata del repository.

1. Eseguire i comandi seguenti per installare lo strumento dell'interfaccia della riga di comando **pcs** e accedere all'account di Azure:

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. Eseguire lo script **start.cmd**. Lo script richiede le informazioni seguenti:
    * Nome della soluzione.
    * Sottoscrizione di Azure da usare.
    * Località del data center di Azure da usare.

    Lo script crea un gruppo di risorse in Azure con il nome della soluzione. Questo gruppo di risorse contiene le risorse di Azure usate dall'acceleratore di soluzione. È possibile eliminare il gruppo di risorse nel momento in cui le risorse corrispondenti non sono più necessarie.

    Lo script aggiunge anche un set di variabili di ambiente con un prefisso **PCS** nel computer locale. Quando si avviano i contenitori Docker in locale, i valori di configurazione di questi contenitori vengono letti da queste variabili di ambiente.

> [!TIP]
> Al termine dell'esecuzione dello script, viene visualizzato un elenco di variabili di ambiente. Se si salvano questi valori nel file **services\\scripts\\local\\.env**, è possibile usarli per le future distribuzioni dell'acceleratore di soluzioni. Tutte le variabili di ambiente impostate nel computer locale ignorano i valori nel file **services\\scripts\\local\\.env** quando si esegue **docker-compose**.

### <a name="use-existing-azure-resources"></a>Usare le risorse di Azure esistenti

Se sono già state create le risorse di Azure necessarie, creare le corrispondenti variabili di ambiente nel computer locale. È possibile che questi valori siano stati salvati nel file **services\\scripts\\local\\.env** come parte dell'ultima distribuzione. Tutte le variabili di ambiente impostate nel computer locale ignorano i valori nel file **services\\scripts\\local\\.env** quando si esegue **docker-compose**.