---
title: Distribuire un eseguibile esistente in Azure Service Fabric | Documentazione Microsoft
description: Informazioni su come creare il pacchetto di un'applicazione esistente come eseguibile guest, in modo da consentirne la distribuzione in un cluster di Service Fabric.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: d799c1c6-75eb-4b8a-9f94-bf4f3dadf4c3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 03/15/2018
ms.author: mfussell;mikhegn
ms.openlocfilehash: 328c00697a3c81f5af8488d4303feb7618d81301
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="deploy-an-existing-executable-to-service-fabric"></a>Distribuire un eseguibile esistente in Service Fabric
In Azure Service Fabric distribuito come servizio è possibile eseguire qualsiasi tipo di codice, ad esempio Node.js, Java, e C++. Service Fabric fa riferimento a questi tipi di servizi come eseguibili guest.

Gli eseguibili guest vengono considerati da Service Fabric come servizi senza stato. Di conseguenza, vengono inseriti nei nodi di un cluster in base alla disponibilità e ad altre metriche. Questo articolo descrive come creare un pacchetto e distribuire un eseguibile guest in un cluster di Service Fabric, usando Visual Studio o un'utilità della riga di comando.

## <a name="benefits-of-running-a-guest-executable-in-service-fabric"></a>Vantaggi dell'esecuzione di un'eseguibile guest in Service Fabric
L'esecuzione di un eseguibile guest in un cluster di Service Fabric presenta numerosi vantaggi:

* Disponibilità elevata. Le applicazioni eseguite in Service Fabric sono rese altamente disponibili. Service Fabric garantisce che siano in esecuzione istanze dell'applicazione.
* Monitoraggio dell’integrità. Il monitoraggio dell'integrità di Service Fabric rileva se un'applicazione è in esecuzione e fornisce informazioni di diagnostica in caso di errore.   
* Gestione del ciclo di vita delle applicazioni. Oltre a garantire aggiornamenti senza tempi di inattività, Service Fabric consente il ripristino automatico della versione precedente se durante un aggiornamento viene segnalato un evento di integrità negativo.    
* Densità. È possibile eseguire più applicazioni in un cluster, eliminando la necessità che ogni applicazione venga eseguita nel proprio hardware.
* Individuabilità. Usando REST è possibile chiamare il servizio Service Fabric Naming per trovare altri servizi nel cluster. 

## <a name="samples"></a>Esempi
* [Esempio per la creazione di un pacchetto e distribuzione di un file guest eseguibile](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Esempio di due eseguibili guest (C# e nodejs) che comunicano tramite il servizio Naming usando REST](https://github.com/Azure-Samples/service-fabric-dotnet-containers)

## <a name="overview-of-application-and-service-manifest-files"></a>Panoramica dei file manifesto dell'applicazione e del servizio
Nell'ambito della distribuzione di un eseguibile guest è utile comprendere il modello di creazione di pacchetti e di distribuzione di Service Fabric, descritto nel [modello applicativo](service-fabric-application-model.md). Il modello di creazione di pacchetti di Service Fabric si basa su due file XML: il manifesto dell'applicazione e il manifesto del servizio. La definizione dello schema per i file ApplicationManifest.xml e ServiceManifest.xml viene installata con Service Fabric SDK in *C:\Programmi\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

* **Manifesto dell'applicazione**. Il manifesto dell'applicazione viene usato per descrivere l'applicazione. Elenca i servizi da cui è costituita e altri parametri usati per definire come dovranno essere distribuiti tali servizi, ad esempio il numero di istanze.

  In Service Fabric un'applicazione è un'unità di distribuzione e aggiornamento. Un'applicazione può essere aggiornata come una singola unità in cui vengono gestiti i potenziali errori e i potenziali ripristini dello stato precedente. Service Fabric garantisce che il processo di aggiornamento venga completato o, in caso di errore, che non lasci l'applicazione in uno stato sconosciuto o instabile.
* **Manifesto del servizio** . Include dati come il nome e il tipo di servizio, nonché il codice e la configurazione. Include dati come il nome e il tipo di servizio nonché il codice, la configurazione e i dati del servizio, più alcuni parametri aggiuntivi usati per configurare il servizio una volta distribuito.

## <a name="application-package-file-structure"></a>Struttura del file del pacchetto dell'applicazione
Per distribuire un'applicazione in Service Fabric, l'applicazione deve seguire una struttura di directory predefinita. Di seguito è riportato un esempio di tale struttura.

```
|-- ApplicationPackageRoot
    |-- GuestService1Pkg
        |-- Code
            |-- existingapp.exe
        |-- Config
            |-- Settings.xml
        |-- Data
        |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```

ApplicationPackageRoot contiene il file ApplicationManifest.xml che definisce l'applicazione. Per contenere tutti gli elementi necessari per il servizio viene usata una sottodirectory per ogni servizio incluso nell'applicazione. Tali sottodirectory sono ServiceManifest.xml e, in genere, la seguente:

* *Code*. Contiene il codice del servizio.
* *Config*. Questa directory contiene un file Settings.xml (e altri file, se necessario) a cui il servizio può accedere in fase di esecuzione per recuperare specifiche impostazioni di configurazione.
* *Dati*. Un'altra directory in cui archiviare dati locali aggiuntivi che potrebbero essere necessari al servizio. I dati devono essere usati per archiviare solo dati temporanei. Service Fabric non copia o replica le modifiche alla directory dei dati se il servizio deve essere trasferito, ad esempio durante il failover.

> [!NOTE]
> Non è necessario creare le directory `config` e `data` se non sono necessarie.
>
>

## <a name="next-steps"></a>Passaggi successivi
Per informazioni e attività correlate, vedere gli articoli seguenti.
* [Distribuire un eseguibile guest](service-fabric-deploy-existing-app.md)
* [Distribuire più eseguibili guest](service-fabric-deploy-multiple-apps.md)
* [Creare la prima applicazione eseguibile guest con Visual Studio](quickstart-guest-app.md)
* [Esempio per la creazione del pacchetto e la distribuzione di un file guest eseguibile ](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started), con un collegamento alla versione preliminare dello strumento per la creazione di pacchetti
* [Esempio di due eseguibili guest (C# e nodejs) che comunicano tramite il servizio Naming usando REST](https://github.com/Azure-Samples/service-fabric-containers)

