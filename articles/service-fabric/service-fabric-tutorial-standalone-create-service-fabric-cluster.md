---
title: Esercitazione sull'installazione del client autonomo di Service Fabric - Azure Service Fabric | Microsoft Docs
description: In questa esercitazione viene illustrato come installare il client autonomo di Service Fabric nel cluster creato nell'esercitazione dell'articolo precedente.
services: service-fabric
documentationcenter: .net
author: david-stanford
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/11/2018
ms.author: dastanfo
ms.custom: mvc
ms.openlocfilehash: 85e6efcc4e213da4d6c650f7543403dd52e8337c
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248436"
---
# <a name="tutorial-install-and-create-service-fabric-cluster"></a>Esercitazione: Installare e creare cluster di Service Fabric

I cluster autonomi di Service Fabric offrono la possibilità di scegliere il proprio ambiente e creare un cluster come parte dell'approccio "qualsiasi sistema operativo, qualsiasi cloud" adottato da Service Fabric. In questa serie di esercitazioni viene creato un cluster autonomo ospitato in AWS e viene installata un'applicazione al suo interno.

Questa è la seconda di una serie di esercitazioni. Questa esercitazione illustra la procedura di creazione di un cluster autonomo di Service Fabric.

Nella seconda parte della serie si apprenderà come:

> [!div class="checklist"]
> * Scaricare e installare il pacchetto autonomo di Service Fabric
> * Creare il cluster di Service Fabric
> * Connettersi al cluster di Service Fabric

## <a name="download-the-service-fabric-for-windows-server-package"></a>Scaricare il pacchetto Service Fabric per Windows Server

Service Fabric offre un pacchetto di installazione per la creazione di cluster di Service Fabric autonomi.  [Scaricare il pacchetto di installazione](http://go.microsoft.com/fwlink/?LinkId=730690) nel computer locale.  Dopo che è stato scaricato correttamente, copiarlo al posto della connessione RDP all'istanza EC2 e incollarlo nel desktop.

Selezionare il file con estensione zip e aprire il menu di scelta rapida, quindi selezionare **Estrai tutto** > **Estrai**.  Mentre si estraggono i file, viene generata una cartella sul desktop che corrisponde al nome del file con estensione zip.

È possibile trovare altri dettagli sui [contenuti del pacchetto di installazione](service-fabric-cluster-standalone-package-contents.md).

## <a name="set-up-your-configuration-file"></a>Definire il file di configurazione

Si sta compilando un cluster Windows a tre nodi, pertanto è necessario modificare il file `ClusterConfig.Unsecure.MultiMachine.json`.

Successivamente, aggiornare le tre righe ipAddress 8, 15 e 22 nel file, modificandole negli indirizzi IP per ciascuna delle istanze.

Dopo l'aggiornamento, i nodi vengono visualizzati come segue:

```json
        {
            "nodeName": "vm0",
            "ipAddress": "172.31.27.1",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD0"
        }
```

È quindi necessario aggiornare un paio delle proprietà.  Alla riga 34, è necessario modificare la stringa di connessione per l'archivio di diagnostica, che dopo la modifica dovrebbe avere questo aspetto, con l'indirizzo IP sostituito in `"connectionstring": "\\\\172.31.27.1\\c$\\DiagnosticsStore"`

Dopo aver aggiornato la stringa di connessione, assicurarsi di creare la cartella.  La cartella verrà creata dal comando seguente; assicurarsi di sostituire l'indirizzo IP di seguito con l'indirizzo IP che è stato inserito nella stringa di connessione:

```powershell
mkdir \\172.31.27.1\c$\DiagnosticsStore
```

Infine, nella sezione `nodeTypes` della configurazione aggiungere una nuova sezione per eseguire il mapping delle porte temporanee che verranno usate da Windows.  Il file di configurazione deve avere un aspetto simile al seguente:

```json
"applicationPorts": {
    "startPort": "20001",
    "endPort": "20031"
},
"ephemeralPorts": {
    "startPort": "20606",
    "endPort": "20861"
},
"isPrimary": true
```

## <a name="validate-the-environment"></a>Convalidare l'ambiente

Lo script *TestConfiguration.ps1* nel pacchetto autonomo viene usato come Best Practices Analyzer per verificare se un cluster possa essere distribuito in un determinato ambiente. La [preparazione della distribuzione](service-fabric-cluster-standalone-deployment-preparation.md) elenca i prerequisiti e i requisiti dell'ambiente. Eseguire lo script per verificare se è possibile creare il cluster di sviluppo:

```powershell
cd .\Desktop\Microsoft.Azure.ServiceFabric.WindowsServer.6.2.274.9494\
.\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
```

Verrà visualizzato un output simile al seguente: Se per il campo in basso "Passed" viene restituito un valore `True`, sono stati superati i controlli di integrità ed è possibile distribuire il cluster in base alla configurazione di input.

```powershell
Trace folder already exists. Traces will be written to existing trace folder: C:\Users\Administrator\Desktop\Microsoft.Azure.ServiceFabric.WindowsServer.6.2.274.9494\DeploymentTraces
Running Best Practices Analyzer...
Best Practices Analyzer completed successfully.


LocalAdminPrivilege        : True
IsJsonValid                : True
IsCabValid                 :
RequiredPortsOpen          : True
RemoteRegistryAvailable    : True
FirewallAvailable          : True
RpcCheckPassed             : True
NoConflictingInstallations : True
FabricInstallable          : True
DataDrivesAvailable        : True
NoDomainController         : True
Passed                     : True
```

## <a name="create-the-cluster"></a>Creare il cluster

Dopo aver convalidato correttamente il file di configurazione del cluster, eseguire lo script *CreateServiceFabricCluster.ps1* per distribuire il cluster di Service Fabric alle macchine virtuali nel file di configurazione.

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -AcceptEULA
```

Se tutto funziona correttamente, si riceverà un output simile al seguente:

```powershell
Your cluster is successfully created! You can connect and manage your cluster using Microsoft Azure Service Fabric Explorer or PowerShell. To connect through PowerShell, run 'Connect-ServiceFabricCluster [ClusterConnectionEndpoint]'.
```

> [!NOTE]
> Le tracce di distribuzione vengono scritte nella VM o nel computer in cui è stato eseguito lo script PowerShell CreateServiceFabricCluster.ps1. Sono disponibili nella sottocartella DeploymentTraces all'interno della directory da cui è stato eseguito lo script. Per verificare se Service Fabric è stato distribuito correttamente in un computer, individuare i file installati nella directory FabricDataRoot, come descritto in dettaglio nella sezione FabricSetting del file di configurazione del cluster. Per impostazione predefinita la directory è c:\ProgramData\SF. I processi FabricHost.exe e Fabric.exe possono essere visualizzati in esecuzione in Gestione attività.
>
>

### <a name="bring-up-service-fabric-explorer"></a>Visualizzare Service Fabric Explorer

È ora possibile connettersi al cluster con Service Fabric Explorer direttamente da uno dei computer con http://localhost:19080/Explorer/index.html o in modalità remota con http://<*IPAddressofaMachine*>:19080/Explorer/index.html.

## <a name="add-and-remove-nodes"></a>Aggiungere e rimuovere ruoli

È possibile aggiungere o rimuovere nodi nel cluster di Service Fabric autonomo al variare delle esigenze aziendali. Per i passaggi dettagliati, leggere le informazioni su [aggiunta e rimozione di nodi in un cluster Service Fabric autonomo](service-fabric-cluster-windows-server-add-remove-nodes.md).

## <a name="next-steps"></a>Passaggi successivi

Nella seconda parte della serie si è appreso come caricare grandi quantità di dati casuali in parallelo in un account di archiviazione, ad esempio come:

> [!div class="checklist"]
> * Configurare la stringa di connessione
> * Compilare l'applicazione.
> * Eseguire l'applicazione
> * Convalidare il numero di connessioni

Passare alla terza parte della serie per installare un'applicazione nel cluster che è stato creato.

> [!div class="nextstepaction"]
> [Installare l'applicazione nel cluster di Service Fabric](service-fabric-tutorial-standalone-install-an-application.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png
