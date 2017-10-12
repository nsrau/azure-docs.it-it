---
title: Pacchetto autonomo di Azure Service Fabric per Windows Server | Documentazione Microsoft
description: Descrizione e contenuto del pacchetto autonomo di Azure Service Fabric per Windows Server.
services: service-fabric
documentationcenter: .net
author: maburlik
manager: timlt
editor: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/10/2017
ms.author: chackdan;maburlik
ms.openlocfilehash: 6a6bacedde04419449a061554d5ce9ad37259a2f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="contents-of-service-fabric-standalone-package-for-windows-server"></a>Contenuto del pacchetto autonomo di Service Fabric per Windows Server
Nel pacchetto autonomo di Service Fabric [scaricato](http://go.microsoft.com/fwlink/?LinkId=730690) sono disponibili i seguenti file:

| **Nome file** | **Descrizione breve** |
| --- | --- |
| CreateServiceFabricCluster.ps1 |Script di PowerShell che crea il cluster usando le impostazioni di ClusterConfig.json. |
| RemoveServiceFabricCluster.ps1 |Script di PowerShell che rimuove un cluster usando le impostazioni di ClusterConfig.json. |
| AddNode.ps1 |Uno script di PowerShell per l'aggiunta di un nodo a un cluster distribuito esistente nel computer corrente. |
| RemoveNode.ps1 |Uno script di PowerShell per la rimozione di un nodo da un cluster distribuito esistente dal computer corrente. |
| CleanFabric.ps1 |Script di PowerShell per rimuovere un'installazione autonoma di Service Fabric dal computer in uso. Le installazioni MSI precedenti devono essere rimosse usando i programmi di disinstallazione associati. |
| TestConfiguration.ps1 |Script di PowerShell per l'analisi dell'infrastruttura specificata in cluster.json. |
| DownloadServiceFabricRuntimePackage.ps1 |Uno script di PowerShell usato per il download del pacchetto di runtime più recente fuori banda negli scenari in cui il computer di distribuzione non è connesso a Internet. |
| DeploymentComponentsAutoextractor.exe |Archivio autoestraente contenente i componenti di distribuzione usati dagli script del pacchetto autonomo. |
| EULA_ENU.txt |Condizioni di licenza per l'uso del pacchetto autonomo Microsoft Azure Service Fabric per Windows Server. È possibile [scaricare una copia del contratto di licenza](http://go.microsoft.com/fwlink/?LinkID=733084) ora. |
| Readme.txt |Collegamento alle note sulla versione e alle istruzioni di installazione base. Si tratta di un sottoinsieme delle istruzioni disponibili in questo documento. |
| ThirdPartyNotice.rtf |Informativa sul software di terze parti presente nel pacchetto. |
| Tools\Microsoft.Azure.ServiceFabric.WindowsServer.SupportPackage.zip |File StandaloneLogCollector.exe, eseguito on demand per raccogliere e caricare i log di analisi per Microsoft a scopo di supporto. |
| Tools\ServiceFabricUpdateService.zip |Strumento usato per abilitare l'aggiornamento del codice automatico per i cluster che non hanno accesso a Internet. Altri dettagli sono disponibili [qui](service-fabric-cluster-upgrade-windows-server.md)|

**Modelli** 
| **Nome file** | **Descrizione breve** |
| --- | --- |
| ClusterConfig.Unsecure.DevCluster.json |File di configurazione del cluster di esempio contenente le impostazioni per un cluster di sviluppo a tre nodi non protetto con singolo computer (o macchina virtuale), con le informazioni per ogni nodo incluso nel cluster. |
| ClusterConfig.Unsecure.MultiMachine.json |File di configurazione del cluster di esempio contenente le impostazioni per un cluster non protetto con più computer o macchine virtuali, con le informazioni per ogni computer incluso nel cluster. |
| ClusterConfig.Windows.DevCluster.json |File di configurazione del cluster di esempio contenente tutte le impostazioni per un cluster di sviluppo a tre nodi non protetto con singolo computer (o macchina virtuale), con le informazioni per ogni nodo incluso nel cluster. Il cluster è protetto con [identità di Windows](https://msdn.microsoft.com/library/ff649396.aspx). |
| ClusterConfig.Windows.MultiMachine.json |File di configurazione del cluster di esempio contenente tutte le impostazioni per un cluster protetto con più computer o macchine virtuali, che usa la funzionalità di sicurezza di Windows, con le informazioni per ogni computer incluso nel cluster protetto. Il cluster è protetto con [identità di Windows](https://msdn.microsoft.com/library/ff649396.aspx). |
| ClusterConfig.x509.DevCluster.json |File di configurazione del cluster di esempio contenente tutte le impostazioni per un cluster di sviluppo a tre nodi non protetto con singolo computer (o macchina virtuale), con le informazioni per ogni nodo presente nel cluster. Il cluster è protetto tramite certificati X.509 di Windows. |
| ClusterConfig.x509.MultiMachine.json |File di configurazione del cluster di esempio contenente tutte le impostazioni per un cluster sicuro con più computer o macchine virtuali, con le informazioni per ogni nodo del cluster sicuro. Il cluster è protetto tramite certificati X.509 di Windows. |
| ClusterConfig.gMSA.Windows.MultiMachine.json |File di configurazione del cluster di esempio contenente tutte le impostazioni per un cluster sicuro con più computer o macchine virtuali, con le informazioni per ogni nodo del cluster sicuro. Il cluster è protetto usando gli [account del servizio gestito del gruppo](https://technet.microsoft.com/en-us/library/jj128431(v=ws.11).aspx). |

## <a name="cluster-configuration-samples"></a>Esempi di configurazione del cluster
Le versioni più recenti dei modelli di configurazione del cluster sono disponibili nella pagina di GitHub: [Standalone Cluster Configuration Samples](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

## <a name="independent-runtime-package"></a>Pacchetto di Runtime indipendente
Il pacchetto di runtime più recente viene scaricato automaticamente durante la distribuzione del cluster da [Collegamento per il download - Runtime di Service Fabric - Windows Server](https://go.microsoft.com/fwlink/?linkid=839354).

## <a name="related"></a>Risorse correlate
* [Creare un cluster autonomo di Azure Service Fabric](service-fabric-cluster-creation-for-windows-server.md)
* [Scenari di sicurezza di un cluster di Service Fabric](service-fabric-windows-cluster-windows-security.md)
