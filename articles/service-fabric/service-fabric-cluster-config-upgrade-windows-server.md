---
title: Aggiornare la configurazione di un cluster autonomo di Azure Service Fabric | Microsoft Docs
description: Informazioni su come aggiornare la configurazione di un cluster autonomo di Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: 66296cc6-9524-4c6a-b0a6-57c253bdf67e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: f99c1ebb64bf881bcd42f15e13bb81b96ccfa064
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60387129"
---
# <a name="upgrade-the-configuration-of-a-standalone-cluster"></a>Aggiornare la configurazione di un cluster autonomo 

La possibilità di aggiornare un sistema moderno è fondamentale per il successo a lungo termine del prodotto. Un cluster di Azure Service Fabric è una risorsa di cui si è proprietari. Questo articolo descrive come aggiornare le impostazioni di configurazione di un cluster di Service Fabric autonomo.

## <a name="customize-cluster-settings-in-the-clusterconfigjson-file"></a>Personalizzare le impostazioni del cluster nel file ClusterConfig.json
I cluster autonomi vengono configurati tramite il file *ClusterConfig.json*. Per altre informazioni sulle diverse impostazioni, vedere [Impostazioni di configurazione per un cluster autonomo in Windows](service-fabric-cluster-manifest.md).

È possibile aggiungere, aggiornare o rimuovere impostazioni nella sezione `fabricSettings` all'interno della sezione [Cluster properties](./service-fabric-cluster-manifest.md#cluster-properties) del file *ClusterConfig.json*. 

Ad esempio, il codice JSON seguente aggiunge una nuova impostazione *MaxDiskQuotaInMB* alla sezione *Diagnostics* in `fabricSettings`:

```json
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

Dopo aver modificato le impostazioni nel file ClusterConfig.json, [eseguire il test della configurazione del cluster](#test-the-cluster-configuration) e quindi [aggiornare la configurazione del cluster](#upgrade-the-cluster-configuration) per applicare le impostazioni al cluster. 

## <a name="test-the-cluster-configuration"></a>Eseguire il test della configurazione del cluster
Prima di avviare l'aggiornamento della configurazione, è possibile testare il nuovo file JSON di configurazione cluster eseguendo lo script di PowerShell seguente nel pacchetto autonomo:

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File>
```

Oppure, usare questo script:

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File> -FabricRuntimePackagePath <Path to the .cab file which you want to test the configuration against>
```

Alcune configurazioni non possono essere aggiornate, ad esempio gli endpoint, il nome del cluster, l'IP del nodo e così via. Il nuovo file JSON di configurazione cluster verrà testato confrontandolo con quello precedente e, in caso di problemi, verranno generati errori nella finestra di PowerShell.

## <a name="upgrade-the-cluster-configuration"></a>Aggiornare la configurazione del cluster
Per aggiornare la configurazione del cluster, eseguire [Start-ServiceFabricClusterConfigurationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade). L'aggiornamento della configurazione viene eseguito per dominio di aggiornamento.

```powershell
Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
```

## <a name="upgrade-cluster-certificate-configuration"></a>Aggiornare la configurazione del certificato del cluster
Per l'autenticazione tra i nodi del cluster viene usato un certificato del cluster. Il rollover del certificato deve essere eseguito con particolare attenzione perché un errore può bloccare le comunicazioni tra i nodi del cluster.

Sono supportate quattro opzioni:  

* Aggiornamento certificato singolo: Il percorso di aggiornamento è certificato A (primario) -> certificato B (primario) -> certificato C (primario) ->...

* Aggiornamento certificato doppio: Il percorso di aggiornamento è certificato A (primario) -> certificato A (primario) e B (secondario) -> certificato B (primario) -> certificato B (primario) e C (secondario) -> certificato C (primario) ->...

* Aggiornamento di tipo certificato: Configurazione di certificati basati su CommonName configurazione <> – basati su identificazione personale del certificato. Ad esempio, identificazione personale del certificato A (primario) e identificazione personale B (secondario) -> CommonName del certificato C.

* Aggiornamento dell'identificazione personale dell'autorità emittente del certificato: Il percorso di aggiornamento è certificato CN = A, IssuerThumbprint CN=a,issuerthumbprint=IT1 (primario) -> certificato CN=A,ISSUERTHUMBPRINT=IT1,IT2 = A, IssuerThumbprint CN=a,issuerthumbprint=IT1, IT2 (primario) -> certificato CN=A,ISSUERTHUMBPRINT=IT1,IT2 = A, IssuerThumbprint CN=a,issuerthumbprint=it2 (primario).


## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come personalizzare alcune [impostazioni dei cluster di Service Fabric](service-fabric-cluster-fabric-settings.md).
* Informazioni su come [aumentare o ridurre le istanze del cluster](service-fabric-cluster-scale-up-down.md).
* Informazioni su come eseguire [aggiornamenti dell'applicazione](service-fabric-application-upgrade.md).

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
