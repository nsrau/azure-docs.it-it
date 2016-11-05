---
title: Aggiungere o rimuovere nodi in un cluster di Service Fabric autonomo | Microsoft Docs
description: Informazioni su come aggiungere o rimuovere nodi in un cluster di Azure Service Fabric su una macchina fisica o virtuale che esegue Windows Server in locale o nel cloud.
services: service-fabric
documentationcenter: .net
author: dsk-2015
manager: timlt
editor: ''

ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/20/2016
ms.author: dkshir;chackdan

---
# <a name="add-or-remove-nodes-to-a-standalone-service-fabric-cluster-running-on-windows-server"></a>Aggiungere o rimuovere nodi in un cluster di Service Fabric autonomo eseguito in Windows Server
Dopo aver [creato il cluster di Service Fabric autonomo in computer Windows Server](service-fabric-cluster-creation-for-windows-server.md), le esigenze aziendali possono cambiare e richiedere l'aggiunta o la rimozione di più nodi nel cluster. Questo articolo riporta i passaggi dettagliati per eseguire queste operazioni.

## <a name="add-nodes-to-your-cluster"></a>Aggiungere nodi al cluster
1. Preparare la VM/computer che si desidera aggiungere al cluster seguendo i passaggi illustrati nella sezione [Preparare i computer con i prerequisiti per la distribuzione del cluster](service-fabric-cluster-creation-for-windows-server.md#preparemachines) .
2. Pianificare a quale dominio di errore e dominio di aggiornamento si aggiungerà il computer o la VM.
3. Creare una connessione Desktop remoto (RDP) con il computer o la VM da aggiungere al cluster.
4. Copiare o [scaricare il pacchetto autonomo per Service Fabric per Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) nel computer o nella VM e decomprimerlo.
5. Eseguire PowerShell come amministratore e passare al percorso del pacchetto decompresso.
6. Eseguire lo script di PowerShell *AddNode.ps1* con i parametri che descrivono il nuovo nodo da aggiungere. L'esempio seguente aggiunge un nuovo nodo denominato VM5, con tipo NodeType0 e indirizzo IP 182.17.34.52, in UD1 e FD1. *ExistingClusterConnectionEndPoint* è un endpoint di connessione per un nodo già presente nel cluster esistente. Per questo endpoint è possibile scegliere l'indirizzo IP di *qualsiasi* nodo del cluster.

```
.\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain FD1 -AcceptEULA

```

## <a name="remove-nodes-from-your-cluster"></a>Rimuovere nodi dal cluster
1. A seconda del livello di affidabilità scelto per il cluster, non è possibile rimuovere i primi n (3/5/7/9) nodi del tipo di nodo primario.
2. L'esecuzione del comando RemoveNode non è supportata in un cluster di sviluppo.
3. Creare una connessione Desktop remoto (RDP) con il computer o la VM da rimuovere dal cluster.
4. Copiare o [scaricare il pacchetto autonomo per Service Fabric per Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) e decomprimerlo nel computer o nella VM.
5. Eseguire PowerShell come amministratore e passare al percorso del pacchetto decompresso.
6. Eseguire lo script *RemoveNode.ps1* di PowerShell. L'esempio seguente rimuove il nodo corrente dal cluster. *ExistingClusterConnectionEndPoint* è un endpoint di connessione per un nodo già presente nel cluster esistente. Per questo endpoint è necessario scegliere l'indirizzo IP di *qualsiasi* **altro nodo** del cluster.

```
.\RemoveNode.ps1 -ExistingClusterConnectionEndPoint 182.17.34.50:19000
```

A causa di un difetto noto che verrà corretto nella prossima versione, anche dopo essere stato rimosso un nodo risulta in query e SFX come inattivo. 

## <a name="next-steps"></a>Passaggi successivi
* [Impostazioni di configurazione per un cluster autonomo in Windows](service-fabric-cluster-manifest.md)
* [Proteggere un cluster autonomo in Windows tramite la funzionalità di sicurezza di Windows](service-fabric-windows-cluster-windows-security.md)
* [Proteggere un cluster autonomo in Windows con certificati X.509](service-fabric-windows-cluster-x509-security.md)
* [Creare un cluster di Service Fabric autonomo con VM di Azure che eseguono Windows](service-fabric-cluster-creation-with-windows-azure-vms.md)

<!--HONumber=Oct16_HO2-->


