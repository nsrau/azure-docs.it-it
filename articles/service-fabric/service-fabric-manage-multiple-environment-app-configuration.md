---
title: "Gestire le applicazioni per più ambienti in Azure Service Fabric | Microsoft Docs"
description: "Le applicazioni di Azure Service Fabric possono essere eseguite su cluster con dimensioni che variano da un solo computer a molte migliaia. In alcuni casi è possibile che si voglia configurare l'applicazione in modo diverso per i diversi ambienti. Questo articolo illustra come definire diversi parametri dell'applicazione per ogni ambiente,"
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: 
ms.assetid: f406eac9-7271-4c37-a0d3-0a2957b60537
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/09/2017
ms.author: mikhegn
ms.openlocfilehash: 6bc986236d50106485dce151f1f74622f56400c5
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="manage-applications-for-multiple-environments"></a>Gestire le applicazioni per più ambienti

Azure Service Fabric consente di creare cluster usando un numero variabile di computer, da uno solo fino a molte migliaia. Nella maggior parte dei casi risulta necessario distribuire l'applicazione in più configurazioni cluster, ad esempio cluster di sviluppo locale, di sviluppo condiviso e di produzione. Tutti questi cluster vengono considerati ambienti diversi in cui deve essere eseguito il codice. I file binari dell'applicazione possono essere eseguiti senza modifiche nei diversi ambienti, ma è spesso consigliabile definire diverse configurazioni per l'applicazione.

Si prendano in considerazione due semplici esempi:
  - Il servizio è in ascolto su una porta definita, ma il numero di porta deve essere diverso per i vari ambienti
  - È necessario fornire credenziali di binding diverse per un database nei vari ambienti

## <a name="specifying-configuration"></a>Definizione della configurazione

È possibile definire due diversi tipi di configurazione:

- Configurazione applicabile alla modalità di esecuzione dei servizi
  - Ad esempio, il numero di porta per un endpoint o il numero di istanze di un servizio
  - Questa configurazione è specificata nel file manifesto dell'applicazione o del servizio
- Configurazione applicabile al codice dell'applicazione
  - Ad esempio, le informazioni di binding per un database
  - Questa configurazione può essere specificata tramite file di configurazione o variabili di ambiente

> [!NOTE]
> Non tutti gli attributi nei file manifesto dell'applicazione e del servizio supportano parametri.
> In questi casi, è necessario ricorrere alla sostituzione di stringhe come parte del flusso di lavoro di distribuzione. In Visual Studio Team Services è possibile usare un'estensione come Replace Token https://marketplace.visualstudio.com/items?itemName=qetza.replacetokens oppure in Jenkins è possibile eseguire un'attività script per sostituire i valori.
>

## <a name="specifying-parameters-during-application-creation"></a>Definizione dei parametri durante la creazione dell'applicazione

Quando si creano istanze di un'applicazione denominata in Service Fabric, si ha la possibilità di passare parametri. La modalità di esecuzione di questa operazione dipende da come si crea l'istanza dell'applicazione.

  - In PowerShell il cmdlet [`New-ServiceFabricApplication`](https://docs.microsoft.com/en-us/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) accetta i parametri delle applicazioni come tabella hash.
  - Se si usa sfctl, il comando [`sfctl application create`](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-create) accetta i parametri come stringa JSON. Lo script install.sh usa sfctl.
  - In Visual Studio, nella cartella Parametri del progetto dell'applicazione, è disponibile un set di file di parametri che vengono usati per la pubblicazione da Visual Studio, tramite Visual Studio Team Services o Team Foundation Server. In Visual Studio i file di parametri vengono passati allo script Deploy-FabricApplication.ps1.

## <a name="next-steps"></a>Passaggi successivi
Gli articoli seguenti illustrano come applicare alcuni dei concetti descritti in questo articolo:

- [Come specificare le variabili di ambiente per i servizi in Service Fabric](service-fabric-how-to-specify-environment-variables.md)
- [Come specificare tramite parametri il numero di porta di un servizio in Service Fabric](service-fabric-how-to-specify-port-number-using-parameters.md)
- [Come aggiungere parametri ai file di configurazione](service-fabric-how-to-parameterize-configuration-files.md)

- [Documentazione di riferimento sulle variabili di ambiente](service-fabric-environment-variables-reference.md)
