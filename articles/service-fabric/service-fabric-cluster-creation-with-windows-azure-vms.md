<properties
   pageTitle="Creare un cluster di Service Fabric autonomo con VM di Azure che eseguono Windows| Microsoft Azure"
   description="Informazioni su come creare e gestire un cluster di Azure Service Fabric su macchine virtuali di Azure che eseguono Windows Server."
   services="service-fabric"
   documentationCenter=".net"
   authors="dsk-2015"
   manager="timlt"
   editor=""/>  

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/05/2016"
   ms.author="dkshir;chackdan"/>  



# Creare un cluster di Service Fabric autonomo a tre nodi con macchine virtuali di Azure che eseguono Windows Server

Questo articolo descrive come creare un cluster su macchine virtuali (VM) di Windows Azure usando il programma di installazione di Service Fabric autonomo per Windows Server. Questo è un caso speciale di [creazione e gestione di un cluster che esegue Windows Server](service-fabric-cluster-creation-for-windows-server.md) in cui le VM sono [VM di Azure che eseguono Windows Server](../virtual-machines/virtual-machines-windows-hero-tutorial.md), tuttavia non si crea [un cluster di Service Fabric di Azure basato su cloud](service-fabric-cluster-creation-via-portal.md). La differenza è che il cluster di Service Fabric autonomo creato dai passaggi seguenti è interamente gestito dall'utente, mentre i cluster di Service Fabric di Azure basati su cloud vengono gestiti e aggiornati dal provider di risorse di Service Fabric.


## Passaggi per creare il cluster autonomo

1. Accedere al portale di Azure e creare una nuova VM Windows Server 2012 R2 Datacenter in un gruppo di risorse. Per altre informazioni, leggere l'articolo [Creare una VM Windows nel portale di Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md).
2. Aggiungere altre due VM Windows Server 2012 R2 Datacenter allo stesso gruppo di risorse. Assicurarsi che ogni VM abbia al momento della creazione lo stesso nome utente e la stessa password di amministratore. Al termine della creazione, tutte e tre le VM dovrebbero essere visualizzate nella stessa rete virtuale.
3. Connettersi a ogni VM e disattivare Windows Firewall con il [dashboard Server locale di Server Manager](https://technet.microsoft.com/library/jj134147.aspx). Ciò garantisce la comunicazione del traffico di rete tra le macchine. Durante la connessione a ogni macchina, ottenere l'indirizzo IP aprendo un prompt dei comandi e digitando `ipconfig`. In alternativa, è possibile visualizzare l'indirizzo IP di ogni macchina selezionando la risorsa rete virtuale del gruppo di risorse nel portale di Azure.
4. Connettersi a una delle VM e verificare di poter effettuare correttamente il ping delle altre due.
5. Connettersi a una delle VM, [scaricare il pacchetto Service Fabric autonomo per Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) in una nuova cartella nella macchina ed estrarre il pacchetto.
6. Aprire il file *ClusterConfig.Unsecure.MultiMachine.json* nel Blocco note e quindi modificare ogni nodo con i tre indirizzi IP delle macchine. Cambiare il nome del cluster nella parte superiore e salvare il file. Di seguito è riportato un esempio parziale del manifesto del cluster.

    ```
    {
        "name": "TestCluster",
        "clusterManifestVersion": "1.0.0",
        "apiVersion": "2015-01-01-alpha",
        "nodes": [
        {
            "nodeName": "vm0",
        	"metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.5",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD0"
        },
        {
            "nodeName": "vm1",
        	"metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.4",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc2/r0",
            "upgradeDomain": "UD1"
        },
        {
            "nodeName": "vm2",
        	"metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.6",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc3/r0",
            "upgradeDomain": "UD2"
        }
    ],
    ```

7. Aprire una [finestra di PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise). Passare alla cartella in cui è stato estratto il pacchetto di installazione autonomo scaricato e salvato il file manifesto del cluster. Eseguire il comando di PowerShell seguente.

    ```
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab
    ```

8. Dovrebbero essere visualizzate l'esecuzione di PowerShell, la connessione a ogni macchina e la creazione di un cluster. Dopo circa un minuto è possibile controllare che il cluster sia operativo connettendosi a Service Fabric Explorer in uno degli indirizzi IP delle macchine, ad esempio usando `http://10.7.0.5:19080/Explorer/index.html`. Poiché si tratta di un cluster autonomo che usa VM di Azure, per rendere sicuro il cluster è necessario [distribuire certificati nelle VM di Azure](service-fabric-windows-cluster-x509-security.md)oppure configurare una delle macchine come [controller Active Directory di Windows Server per l'autenticazione di Windows](service-fabric-windows-cluster-windows-security.md) così come si farebbe in locale.


## Passaggi successivi
- [Creare cluster autonomi di Service Fabric in Windows Server o Linux](service-fabric-deploy-anywhere.md)
- [Aggiungere o rimuovere nodi in un cluster di Service Fabric autonomo](service-fabric-cluster-windows-server-add-remove-nodes.md)
- [Impostazioni di configurazione per un cluster autonomo in Windows](service-fabric-cluster-manifest.md)
- [Proteggere un cluster autonomo in Windows tramite la funzionalità di sicurezza di Windows](service-fabric-windows-cluster-windows-security.md)
- [Proteggere un cluster autonomo in Windows con certificati X.509](service-fabric-windows-cluster-x509-security.md)

<!---HONumber=AcomDC_0810_2016-->