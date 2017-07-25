---
title: Anteprima di Docker Compose di Azure Service Fabric
description: "Azure Service Fabric accetta il formato Docker Compose per orchestrare più facilmente i contenitori esistenti. Questo supporto è attualmente disponibile in versione di anteprima."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/06/2017
ms.author: subramar
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 91b6e98df5df98bb557d7fac0475354322d95640
ms.contentlocale: it-it
ms.lasthandoff: 07/01/2017

---
# <a name="compose-application-support-in-service-fabric-preview"></a>Supporto dell'applicazione Compose in Service Fabric (anteprima)

Docker usa il file [docker-compose.yml](https://docs.docker.com/compose) per la definizione di applicazioni multi-contenitore.
Per consentire ai clienti che hanno familiarità con Docker di orchestrare più facilmente le applicazioni contenitore esistenti in Service Fabric, è stato incluso nella piattaforma il supporto nativo per Docker Compose, in versione di anteprima. Service Fabric può accettare la versione 3(+) dei file `docker-compose.yml`. Poiché questo supporto è disponibile in anteprima, è supportato solo un subset delle direttive Compose. Non sono supportati, ad esempio, gli aggiornamenti dell'applicazione. Tuttavia, è sempre possibile rimuovere e distribuire le applicazioni invece di aggiornarle.

Per usare questa versione di anteprima, è necessario creare il cluster con la versione di anteprima dell'SDK (versione 255.255.x.x) tramite il portale. 

> [!NOTE]
> Questa funzionalità è in versione di anteprima e non è supportata.

## <a name="deploy-a-docker-compose-file-on-service-fabric"></a>Distribuire un file Docker Compose in Service Fabric

Questi comandi creano un'applicazione di Service Fabric (denominata `fabric:/TestContainerApp` nell'esempio precedente) che è possibile monitorare e gestire analogamente a qualsiasi altra applicazione di Service Fabric. Il nome specificato per l'applicazione può essere usato per eseguire query sull'integrità.

### <a name="using-powershell"></a>Tramite PowerShell

Creare un'applicazione Compose di Service Fabric da un file docker-compose.yml eseguendo il comando seguente in PowerShell:

```powershell
New-ServiceFabricComposeApplication -ApplicationName fabric:/TestContainerApp -Compose docker-compose.yml [-RepositoryUserName <>] [-RepositoryPassword <>] [-PasswordEnctypted]
```

`RepositoryUserName` e `RepoistoryPassword` fanno riferimento al nome utente e alla password del registro contenitori. Dopo aver completato l'applicazione, è possibile controllarne lo stato usando il comando seguente:

```powershell
Get-ServiceFabricComposeApplicationStatus -ApplicationName fabric:/TestContainerApp -GetAllPages
```

Per eliminare l'applicazione Compose con PowerShell, usare il comando seguente:

```powershell
Remove-ServiceFabricComposeApplication  -ApplicationName fabric:/TestContainerApp
```

### <a name="using-azure-cli-20"></a>Tramite l'interfaccia della riga di comando di Azure 2.0

In alternativa, usare il comando dell'interfaccia della riga di comando di Azure seguente:

```azurecli
az sf compose create --application-id fabric:/TestContainerApp --compose-file docker-compose.yml [ [ --repo-user --repo-pass --encrypted ] | [ --repo-user ] ] [ --timeout ]
```

Dopo aver completato l'applicazione, è possibile controllarne lo stato usando il comando seguente:

```azurecli
az sf compose status --application-id TestContainerApp [ --timeout ]
```

Per eliminare l'applicazione Compose, usare il comando seguente:

```azurecli
az sf compose remove  --application-id TestContainerApp [ --timeout ]
```

## <a name="supported-compose-directives"></a>Direttive Compose supportate

Questa versione di anteprima supporta un subset delle opzioni di configurazione del formato Compose V3. Sono supportate le primitive seguenti:

* Services->Deploy->replicas
* Services->Deploy->Placement->Constraints
* Services->Deploy->Resources->Limits
*         -cpu-shares
*         -memory
*         -memory-swap
* Services->Commands
* Services->Environment
* Services->Ports
* Services->image
* Services->Isolation (solo per Windows)
* Services->logging->driver
* Services->logging->driver->options
* Volume & Deploy->Volume

Il cluster deve essere configurato per l'applicazione dei limiti delle risorse, come descritto in [Governance delle risorse di Service Fabric](service-fabric-resource-governance.md). Tutte le altre direttive Docker Compose non sono supportate per questa versione di anteprima.

## <a name="servicednsname-computation"></a>Calcolo di ServiceDnsName

Se il nome del servizio specificato nel file Compose è un nome di dominio completo (ovvero contiene un punto, "."), il nome DNS registrato da Service Fabric è `<ServiceName>`, incluso il punto. In caso contrario, ogni segmento di percorso in ApplicationName diventa un'etichetta di dominio nel nome DNS del servizio, con il primo segmento di percorso che diventa l'etichetta di dominio di primo livello.

Se, ad esempio, il nome specificato per l'applicazione è `fabric:/SampleApp/MyComposeApp`, il nome DNS registrato sarà `<ServiceName>.MyComposeApp.SampleApp`.

## <a name="differences-between-compose-instance-definition-and-service-fabric-application-model-type-definition"></a>Differenze tra Compose (definizione di istanza) e il modello di applicazione di Service Fabric (definizione di tipo)

Un file docker-compose.yml descrive un set distribuibile di contenitori, incluse le relative proprietà e configurazioni.
Il file può contenere, ad esempio, variabili di ambiente e porte. Nel file docker-compose.yml possono essere specificati anche i parametri di distribuzione, ad esempio i vincoli di posizionamento, i limiti delle risorse e i nomi DNS.

Il [modello di applicazione di Service Fabric](service-fabric-application-model.md) usa tipi di servizio e tipi di applicazione, in cui possono coesistere numerose istanze dell'applicazione dello stesso tipo. È possibile, ad esempio, che sia presente un'istanza dell'applicazione per ogni cliente. Questo modello basato sul tipo supporta più versioni dello stesso tipo di applicazione registrate con il runtime.
Il cliente A può ad esempio avere un'applicazione di cui è stata creata un'istanza con il tipo 1.0 di AppTypeA e cliente B può avere un'altra applicazione di cui è stata creata un'istanza con lo stesso tipo e la stessa versione. I tipi di applicazione sono definiti nei manifesti dell'applicazione e il nome dell'applicazione e i parametri di distribuzione vengono specificati al momento della creazione dell'applicazione.

Questo modello offre flessibilità, ma è previsto anche il supporto di un modello di distribuzione basato su istanze più semplice, in cui i tipi sono impliciti nel file manifesto. In questo modello, ogni applicazione ottiene il proprio manifesto indipendente. Questa funzionalità viene offerta in anteprima aggiungendo il supporto per docker-compose.yml, che è un formato di distribuzione basato su istanze.

## <a name="next-steps"></a>Passaggi successivi

* Leggere le informazioni sul [modello di applicazione di Service Fabric](service-fabric-application-model.md).

## <a name="related-articles"></a>Articoli correlati

* [Introduzione a Service Fabric e all'interfaccia della riga di comando di Azure 2.0](service-fabric-azure-cli-2-0.md)
* [Introduzione all'interfaccia della riga di comando di XPlat per Service Fabric](service-fabric-azure-cli.md)

