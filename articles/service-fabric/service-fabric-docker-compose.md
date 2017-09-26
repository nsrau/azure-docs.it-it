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
ms.date: 09/25/2017
ms.author: subramar
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: e05d1a3d6111e3bbc34008226bcd1fdf35935450
ms.contentlocale: it-it
ms.lasthandoff: 08/24/2017

---
# <a name="docker-compose-application-support-in-azure-service-fabric-preview"></a>Supporto dell'applicazione Docker Compose in Azure Service Fabric (anteprima)

Docker usa il file [docker-compose.yml](https://docs.docker.com/compose) per la definizione di applicazioni multi-contenitore. Per consentire ai clienti che hanno familiarità con Docker di coordinare più facilmente le applicazioni contenitore presenti in Azure Service Fabric, è stato incluso nella piattaforma il supporto nativo per Docker Compose, in versione di anteprima. Service Fabric può accettare la versione 3 (o successiva) dei file `docker-compose.yml`. 

Poiché questo supporto è disponibile in anteprima, è supportato solo un subset delle direttive Compose. Non sono supportati, ad esempio, gli aggiornamenti dell'applicazione. Tuttavia, è sempre possibile rimuovere e distribuire le applicazioni invece di aggiornarle.

Per usare questa versione di anteprima, creare il cluster con la versione 5.7 o versione successiva del runtime di Service Fabric tramite il portale di Azure con l'SDK corrispondente. 

> [!NOTE]
> Questa funzionalità è in versione di anteprima e non è supportata in produzione.

## <a name="deploy-a-docker-compose-file-on-service-fabric"></a>Distribuire un file Docker Compose in Service Fabric

I comandi seguenti creano un'applicazione di Service Fabric (denominata `fabric:/TestContainerApp` nell'esempio precedente) che è possibile monitorare e gestire analogamente a qualsiasi altra applicazione di Service Fabric. Per eseguire query sull'integrità, è possibile usare il nome dell'applicazione specificato.

### <a name="use-powershell"></a>Usare PowerShell

Creare un'applicazione Compose di Service Fabric da un file docker-compose.yml eseguendo il comando seguente in PowerShell:

```powershell
New-ServiceFabricComposeDeployment -DeploymentName fabric:/TestContainerApp -Compose docker-compose.yml [-RegistryUserName <>] [-RegistryPassword <>] [-PasswordEncrypted]
```

`RegistryUserName` e `RegistryPassword` fanno riferimento al nome utente e alla password del registro contenitori. Dopo aver completato l'applicazione, è possibile controllarne lo stato usando il comando seguente:

```powershell
Get-ServiceFabricComposeDeploymentStatus -DeploymentName fabric:/TestContainerApp -GetAllPages
```

Per eliminare l'applicazione Compose tramite PowerShell, usare il comando seguente:

```powershell
Remove-ServiceFabricComposeDeployment  -DeploymentName fabric:/TestContainerApp
```

### <a name="use-azure-service-fabric-cli-sfctl"></a>Usare l'interfaccia della riga di comando di Azure Service Fabric (sfctl)

In alternativa, è possibile usare il comando dell'interfaccia della riga di comando di Service Fabric seguente:

```azurecli
sfctl compose create --application-id fabric:/TestContainerApp --compose-file docker-compose.yml [ [ --repo-user --repo-pass --encrypted ] | [ --repo-user ] ] [ --timeout ]
```

Dopo aver creato l'applicazione, è possibile controllarne lo stato usando il comando seguente:

```azurecli
sfctl compose status --application-id TestContainerApp [ --timeout ]
```

Per eliminare l'applicazione Compose, usare il comando seguente:

```azurecli
sfctl compose remove  --application-id TestContainerApp [ --timeout ]
```

## <a name="supported-compose-directives"></a>Direttive Compose supportate

Questa versione di anteprima supporta un subset delle opzioni di configurazione a partire dal formato Compose versione 3, incluse le primitive seguenti:

* Services > Deploy > Replicas
* Services > Deploy > Placement > Constraints
* Services > Deploy > Resources > Limits
    * -cpu-shares
    * -memory
    * -memory-swap
* Services > Commands
* Services > Environment
* Services > Ports
* Services > Image
* Services > Isolation (solo per Windows)
* Services > Logging > Driver
* Services > Logging > Driver > Options
* Volume & Deploy > Volume

Configurare il cluster in modo da applicare i limiti delle risorse, come descritto in [Governance delle risorse di Service Fabric](service-fabric-resource-governance.md). Tutte le altre direttive Docker Compose non sono supportate per questa versione di anteprima.

## <a name="servicednsname-computation"></a>Calcolo di ServiceDnsName

Se il nome del servizio specificato nel file Compose è un nome di dominio completo (ovvero contiene un punto [.]), il nome DNS registrato da Service Fabric è `<ServiceName>`, incluso il punto. In caso contrario, ogni segmento di percorso nel nome dell'applicazione diventa un'etichetta di dominio nel nome DNS del servizio, con il primo segmento di percorso che diventa l'etichetta di dominio di primo livello.

Se, ad esempio, il nome specificato per l'applicazione è `fabric:/SampleApp/MyComposeApp`, il nome DNS registrato sarà `<ServiceName>.MyComposeApp.SampleApp`.

## <a name="differences-between-compose-instance-definition-and-service-fabric-application-model-type-definition"></a>Differenze tra Compose (definizione di istanza) e il modello di applicazione di Service Fabric (definizione di tipo)

Un file docker-compose.yml descrive un set distribuibile di contenitori, incluse le relative proprietà e configurazioni.
Il file può contenere, ad esempio, variabili di ambiente e porte. Nel file docker-compose.yml è possibile specificare anche i parametri di distribuzione, ad esempio i vincoli di posizionamento, i limiti delle risorse e i nomi DNS.

Il [modello di applicazione di Service Fabric](service-fabric-application-model.md) usa tipi di servizio e tipi di applicazione, in cui possono coesistere numerose istanze dell'applicazione dello stesso tipo. È possibile, ad esempio, che sia presente un'istanza dell'applicazione per ogni cliente. Questo modello basato sul tipo supporta più versioni dello stesso tipo di applicazione registrato con il runtime.

Il cliente A, ad esempio, può avere un'applicazione di cui è stata creata un'istanza con il tipo 1.0 di AppTypeA e il cliente B può avere un'altra applicazione di cui è stata creata un'istanza con lo stesso tipo e la stessa versione. I tipi di applicazione vengono definiti nei manifesti dell'applicazione e il nome dell'applicazione e i parametri di distribuzione vengono specificati al momento della creazione dell'applicazione.

Questo modello offre flessibilità, ma è previsto anche il supporto di un modello di distribuzione basato su istanze più semplice, in cui i tipi sono impliciti nel file manifesto. In questo modello, ogni applicazione ottiene il proprio manifesto indipendente. Questa funzionalità viene offerta in anteprima aggiungendo il supporto per docker-compose.yml, che è un formato di distribuzione basato su istanze.

## <a name="next-steps"></a>Passaggi successivi

* Leggere le informazioni sul [modello di applicazione di Service Fabric](service-fabric-application-model.md)
* [Introduzione all'interfaccia della riga di comando di Service Fabric](service-fabric-cli.md)

