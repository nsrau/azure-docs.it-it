---
title: Creare un cluster autonomo con macchine virtuale di Azure che eseguono Windows| Microsoft Docs
description: Informazioni su come creare e gestire un cluster di Azure Service Fabric su macchine virtuali di Azure che eseguono Windows Server.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 7eeb40d2-fb22-4a77-80ca-f1b46b22edbc
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/24/2017
ms.author: ryanwi;chackdan
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: ba1f6e5662700c309fcf198a4e114fd9b2b47c5f
ms.lasthandoff: 03/29/2017


---
# <a name="create-a-three-node-standalone-service-fabric-cluster-with-azure-virtual-machines-running-windows-server"></a>Creare un cluster di Service Fabric autonomo a tre nodi con macchine virtuali di Azure che eseguono Windows Server
Questo articolo descrive come creare un cluster su macchine virtuali (VM) di Windows Azure usando il programma di installazione di Service Fabric autonomo per Windows Server. Lo scenario è un caso speciale di [creazione e gestione di un cluster che esegue Windows Server](service-fabric-cluster-creation-for-windows-server.md) in cui le VM sono [VM di Azure che eseguono Windows Server](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Non si crea tuttavia [un cluster di Azure Service Fabric basato su cloud](service-fabric-cluster-creation-via-portal.md). La differenza nel seguire questo schema è che il cluster di Service Fabric autonomo creato dai passaggi seguenti è interamente gestito dall'utente, mentre i cluster di Service Fabric di Azure basati su cloud vengono gestiti e aggiornati dal provider di risorse di Service Fabric.

## <a name="steps-to-create-the-standalone-cluster"></a>Passaggi per creare il cluster autonomo
1. Accedere al portale di Azure e creare una nuova VM Windows Server 2012 R2 Datacenter o Windows Server 2016 Datacenter in un gruppo di risorse. Per altre informazioni, leggere l'articolo [Creare una VM Windows nel portale di Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) .
2. Aggiungere altre due VM allo stesso gruppo di risorse. Assicurarsi che ogni VM abbia al momento della creazione lo stesso nome utente e la stessa password di amministratore. Al termine della creazione, tutte e tre le VM dovrebbero essere visualizzate nella stessa rete virtuale.
3. Connettersi a ogni VM e disattivare Windows Firewall con il [dashboard Server locale di Server Manager](https://technet.microsoft.com/library/jj134147.aspx). Ciò garantisce la comunicazione del traffico di rete tra le macchine. Durante la connessione a ogni macchina, ottenere l'indirizzo IP aprendo un prompt dei comandi e digitando `ipconfig`. In alternativa è possibile visualizzare l'indirizzo IP di ogni computer nel portale, selezionando la risorsa di rete virtuale per il gruppo di risorse e verificando le interfacce di rete create per ogni computer.
4. Connettersi a una delle VM e verificare di poter effettuare correttamente il ping delle altre due.
5. Connettersi a una delle VM, [scaricare il pacchetto Service Fabric autonomo per Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) in una nuova cartella nella macchina ed estrarre il pacchetto.
6. Aprire il file *ClusterConfig.Unsecure.MultiMachine.json* nel Blocco note e quindi modificare ogni nodo con i tre indirizzi IP delle macchine. Cambiare il nome del cluster nella parte superiore e salvare il file.  Di seguito è riportato un esempio parziale del manifesto del cluster.
   
    ```
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "01-2017",
        "nodes": [
        {
            "nodeName": "standalonenode0",
            "iPAddress": "10.1.0.4",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD0"
        },
        {
            "nodeName": "standalonenode1",
            "iPAddress": "10.1.0.5",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc2/r0",
            "upgradeDomain": "UD1"
        },
        {
            "nodeName": "standalonenode2",
            "iPAddress": "10.1.0.6",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc3/r0",
            "upgradeDomain": "UD2"
        }
        ],
    ```
7. Se si vuole che questo sia un cluster protetto, decidere la misura di sicurezza da usare e seguire i passaggi disponibili al collegamento associato: [certificato X509](service-fabric-windows-cluster-x509-security.md) o [protezione di Windows](service-fabric-windows-cluster-windows-security.md). Se si configura il cluster con la sicurezza di Windows, è necessario configurare un controller di dominio per gestire Active Directory. Si noti che l'uso di un computer controller di dominio come nodo di Service Fabric non è supportato.
8. Aprire una [finestra di PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise). Passare alla cartella in cui è stato estratto il pacchetto di installazione autonomo scaricato e salvato il file di configurazione del cluster. Eseguire il comando di PowerShell seguente per distribuire il cluster:
   
    ```
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
    ```

Lo script configurerà in modalità remota il cluster Service Fabric e segnalerà lo stato di avanzamento durante la distribuzione.

9. Dopo circa un minuto è possibile controllare che il cluster sia operativo connettendosi a Service Fabric Explorer mediante uno degli indirizzi IP delle macchine, ad esempio usando `http://10.1.0.5:19080/Explorer/index.html`. 

## <a name="next-steps"></a>Passaggi successivi
* [Creare cluster autonomi di Service Fabric in Windows Server o Linux](service-fabric-deploy-anywhere.md)
* [Aggiungere o rimuovere nodi in un cluster di Service Fabric autonomo](service-fabric-cluster-windows-server-add-remove-nodes.md)
* [Impostazioni di configurazione per un cluster autonomo in Windows](service-fabric-cluster-manifest.md)
* [Proteggere un cluster autonomo in Windows tramite la funzionalità di sicurezza di Windows](service-fabric-windows-cluster-windows-security.md)
* [Proteggere un cluster autonomo in Windows con certificati X.509](service-fabric-windows-cluster-x509-security.md)


