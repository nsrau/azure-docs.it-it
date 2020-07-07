---
title: Aggiornare la configurazione di un cluster autonomo
description: Informazioni su come aggiornare la configurazione di un cluster autonomo di Service Fabric.
author: dkkapur
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 8d0279cc323f7eee87feb2a596a4c2df0b4667e1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82790848"
---
# <a name="upgrade-the-configuration-of-a-standalone-cluster"></a>Aggiornare la configurazione di un cluster autonomo 

La possibilità di aggiornare un sistema moderno è fondamentale per il successo a lungo termine del prodotto. Un cluster di Azure Service Fabric è una risorsa di cui si è proprietari. Questo articolo descrive come aggiornare le impostazioni di configurazione di un cluster di Service Fabric autonomo.

## <a name="customize-cluster-settings-in-the-clusterconfigjson-file"></a>Personalizzare le impostazioni del cluster nel file ClusterConfig.json
I cluster autonomi vengono configurati tramite il *ClusterConfig.jssu* file. Per altre informazioni sulle diverse impostazioni, vedere [Impostazioni di configurazione per un cluster autonomo in Windows](service-fabric-cluster-manifest.md).

È possibile aggiungere, aggiornare o rimuovere le impostazioni nella `fabricSettings` sezione [Proprietà Cluster](./service-fabric-cluster-manifest.md#cluster-properties) di *ClusterConfig.json*. 

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

Alcune configurazioni non possono essere aggiornate, ad esempio endpoint, nome del cluster, IP del nodo e così via. Il nuovo file JSON di configurazione del cluster viene testato rispetto a quello precedente e genera errori nella finestra di PowerShell in caso di problemi.

## <a name="upgrade-the-cluster-configuration"></a>Aggiornare la configurazione del cluster
Per aggiornare l'aggiornamento della configurazione del cluster, eseguire [Start-ServiceFabricClusterConfigurationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade). L'aggiornamento della configurazione viene eseguito per dominio di aggiornamento.

```powershell
Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
```

## <a name="upgrade-cluster-certificate-configuration"></a>Aggiornare la configurazione del certificato del cluster
Per l'autenticazione tra i nodi del cluster viene usato un certificato del cluster. Il rollover del certificato deve essere eseguito con particolare attenzione perché un errore può bloccare le comunicazioni tra i nodi del cluster.

Sono supportate quattro opzioni:  

* Aggiornamento certificato singolo: il percorso di aggiornamento è "Certificato (primario)-> Certificato B (primario)-> Certificato C (primario)->...".

* Aggiornamento certificato doppio: il percorso di aggiornamento è "Certificato A (primario) -> Certificato A (primario) e B (secondario) -> Certificato B (primario) -> Certificato B (primario) e C (secondario) -> Certificato C (primario) ->...".

* Aggiornamento del tipo di certificato: configurazione dei certificati basati su identificazione personale <-> configurazione dei certificati basati su CommonName. Ad esempio, identificazione personale del certificato A (primario) e identificazione personale B (secondario) -> CommonName del certificato C.

* Aggiornamento dell'identificazione personale dell'autorità di certificazione: il percorso di aggiornamento è "Certificato CN=A,IssuerThumbprint=IT1 (primario) -> Certificato CN=A,IssuerThumbprint=IT1,IT2 (primario) -> Certificato CN=A,IssuerThumbprint=IT2 (primario)".


## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come personalizzare alcune [impostazioni dei cluster di Service Fabric](service-fabric-cluster-fabric-settings.md).
* Informazioni su come [ridimensionare il cluster](service-fabric-cluster-scale-in-out.md).
* Informazioni su come eseguire [aggiornamenti dell'applicazione](service-fabric-application-upgrade.md).

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
