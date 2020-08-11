---
title: Aumentare le prestazioni di un tipo di nodo di Azure Service Fabric
description: Informazioni su come ridimensionare un cluster di Service Fabric aggiungendo un set di scalabilità di macchine virtuali.
ms.topic: article
ms.date: 02/13/2019
ms.openlocfilehash: a8a8a432d4a11427f2c4f545a0907540af3112bc
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056483"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type-by-adding-a-virtual-machine-scale-set"></a>Aumentare il livello di un Service Fabric tipo di nodo primario del cluster aggiungendo un set di scalabilità di macchine virtuali
In questo articolo viene descritto come aumentare le prestazioni di un tipo di nodo primario di un cluster di Service Fabric aumentando le risorse delle macchine virtuali. Un cluster di Service Fabric è un set di computer fisici o macchine virtuali connessi in rete, in cui vengono distribuiti e gestiti i microservizi. Un computer o una macchina virtuale che fa parte di un cluster viene detto nodo. I set di scalabilità di macchine virtuali sono una risorsa di calcolo di Azure che è possibile usare per distribuire e gestire una raccolta di macchine virtuali come set. Ogni tipo di nodo definito in un cluster di Azure viene [configurato come set di scalabilità di macchine virtuali separato](service-fabric-cluster-nodetypes.md). Ogni tipo di nodo può essere gestito separatamente. Dopo aver creato un cluster di Service Fabric, è possibile scalare un tipo di nodo del cluster in verticale (modificare le risorse dei nodi) o aggiornare il sistema operativo delle macchine virtuali del tipo di nodo.  È possibile ridimensionare il cluster in qualsiasi momento, anche quando sono in esecuzione carichi di lavoro nel cluster.  Quando si ridimensiona il cluster, vengono automaticamente ridimensionate anche le applicazioni.

> [!WARNING]
> Non tentare una procedura di scalabilità verticale del tipo di nodo primario se lo stato del cluster non è integro, perché il cluster verrà ulteriormente destabilizzato.
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="process-to-upgrade-the-size-and-operating-system-of-the-primary-node-type-vms"></a>Processo per aggiornare le dimensioni e del sistema operativo delle VM del tipo di nodo primario
Di seguito è illustrato il processo per l'aggiornamento delle dimensioni e del sistema operativo delle macchine virtuali del tipo di nodo primario.  Dopo l'aggiornamento, le macchine virtuali del tipo di nodo primario sono di dimensioni D4_V2 standard ed eseguono Windows Server 2016 Data Center con contenitori.

> [!WARNING]
> Prima di provare a eseguire questa procedura su un cluster di produzione, è consigliabile esaminare i modelli di esempio e verificare il processo su un cluster di test. Il cluster inoltre risulterà non disponibile per un certo tempo. NON è possibile effettuare modifiche in più set di scalabilità di macchine virtuali dichiarate come lo stesso NodeType in parallelo; sarà necessario eseguire operazioni di distribuzione separate per applicare individualmente le modifiche a ogni set di scalabilità di macchine virtuali NodeType.

1. Distribuire il cluster iniziale con due tipi di nodo e due set di scalabilità (un set di scalabilità per ogni tipo di nodo) usando questi file di esempio di [modelli](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.json) e [parametri](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.parameters.json).  Entrambi i set di scalabilità sono dimensioni D2_V2 standard ed eseguono Windows Server 2012 R2 Datacenter.  Attendere che il cluster completi l'aggiornamento della baseline.   
2. Distribuire un esempio con stato nel cluster (facoltativo).
3. Dopo aver deciso di aggiornare le macchine virtuali del tipo di nodo primario, aggiungere un nuovo set di scalabilità al tipo di nodo primario usando questi file di esempio di [modelli](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json) e [parametri](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json). A questo punto, il tipo di nodo primario ha due set di scalabilità.  Servizi di sistema e applicazioni utente possono eseguire la migrazione tra le macchine virtuali nei due diversi set di scalabilità.  Le macchine virtuali del nuovo set di scalabilità sono di dimensioni D4_V2 standard ed eseguono Windows Server 2016 Datacenter con contenitori.  Con il nuovo set di scalabilità vengono anche aggiunti un nuovo bilanciamento del carico e l'indirizzo IP pubblico.  
    Per trovare il nuovo set di scalabilità nel modello, cercare la risorsa "Microsoft.Compute/virtualMachineScaleSets" tramite il parametro *vmNodeType2Name*.  Il nuovo set di scalabilità verrà aggiunto al tipo di nodo primario usando l'impostazione proprietà -> virtualMachineProfile - > extensionProfile -> estensioni -> proprietà -> impostazioni -> nodeTypeRef.
4. Controllare l'integrità del cluster e verificare che tutti i nodi siano integri.
5. Disabilitare i nodi nel set di scalabilità precedente del tipo di nodo primario allo scopo di rimuovere il nodo. È possibile disabilitare tutti i nodi contemporaneamente e le operazioni verranno inserite nella coda. Attendere che tutti i nodi vengano disabilitati. L'operazione potrebbe richiedere un certo tempo.  Poiché i nodi precedenti nel tipo di nodo sono disabilitati, i servizi di sistema e i nodi di inizializzazione eseguono la migrazione alle macchine virtuali del nuovo set di scalabilità nel tipo di nodo primario.
6. Rimuovere il set di scalabilità precedente dal tipo di nodo primario. Dopo che i nodi sono stati disabilitati come nel passaggio 5, nel pannello del set di scalabilità di macchine virtuali nella portale di Azure deallocare i nodi del tipo di nodo precedente uno alla volta.
7. Rimuovere il bilanciamento del carico associato al set di scalabilità precedente. Il cluster non è disponibile durante la configurazione del nuovo indirizzo IP pubblico e del bilanciamento del carico per il nuovo set di scalabilità.  
8. Archiviare in una variabile le impostazioni DNS dell'indirizzo IP pubblico associato al set di scalabilità del tipo di nodo primario precedente e rimuovere tale indirizzo IP pubblico.
9. Sostituire le impostazioni DNS dell'indirizzo IP pubblico associato al nuovo set di scalabilità del tipo di nodo primario con le impostazioni DNS dell'indirizzo IP pubblico eliminato.  Ora il cluster è nuovamente raggiungibile.
10. Rimuovere lo stato dei nodi dal cluster.  Se il livello di durabilità del set di scalabilità precedente era Silver o Gold, questo passaggio viene eseguito automaticamente dal sistema.
11. Se è stata distribuita l'applicazione con stato in un passaggio precedente, verificare che l'applicazione funzioni.

## <a name="set-up-the-test-cluster"></a>Configurare il cluster di test

Per iniziare, scaricare i due set di file necessari per questa esercitazione, il [modello]() e i [parametri]() prima e il [modello]() e i [parametri]() dopo.

Successivamente, accedere all'account Azure.

```powershell
# sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId "<your subscription ID>"
```

Questa esercitazione illustra lo scenario di creazione di un certificato autofirmato. Per usare un certificato esistente da Azure Key Vault, ignorare il passaggio seguente ed eseguire invece il mirroring dei passaggi descritti in [Usare un certificato esistente per distribuire il cluster](./upgrade-managed-disks.md#use-an-existing-certificate-to-deploy-the-cluster).

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>Generare un certificato autofirmato e distribuire il cluster

Per prima cosa, assegnare le variabili necessarie per la distribuzione del cluster Service Fabric. Modificare i valori per `resourceGroupName`, `certSubjectName`, `parameterFilePath`e `templateFilePath` per l'account e l'ambiente specifici:

```powershell
# Assign deployment variables
$resourceGroupName = "sftestupgradegroup"
$certOutputFolder = "c:\certificates"
$certPassword = "Password!1" | ConvertTo-SecureString -AsPlainText -Force
$certSubjectName = "sftestupgrade.southcentralus.cloudapp.azure.com"
$templateFilePath = "C:\Deploy-2NodeTypes-2ScaleSets.json"
$parameterFilePath = "C:\Deploy-2NodeTypes-2ScaleSets.parameters.json"
```

> [!NOTE]
> Prima di eseguire il comando per distribuire un nuovo cluster Service Fabric, verificare che nel computer locale esista il percorso `certOutputFolder`.

Aprire quindi il file *Deploy-2NodeTypes-2ScaleSets.parameters.json* e modificare i valori per `clusterName` e `dnsName` in modo che corrispondano ai valori dinamici impostati in PowerShell e salvare le modifiche.

Quindi distribuire il cluster di test di Service Fabric:

```powershell
# Deploy the initial test cluster
New-AzServiceFabricCluster `
    -ResourceGroupName $resourceGroupName `
    -CertificateOutputFolder $certOutputFolder `
    -CertificatePassword $certPassword `
    -CertificateSubjectName $certSubjectName `
    -TemplateFile $templateFilePath `
    -ParameterFile $parameterFilePath
```

Al termine della distribuzione, individuare il file *.pfx* (`$certPfx`) nel computer locale e importarlo nell'archivio certificati:

```powershell
cd c:\certificates
$certPfx = ".\sftestupgradegroup20200312121003.pfx"

Import-PfxCertificate `
     -FilePath $certPfx `
     -CertStoreLocation Cert:\CurrentUser\My `
     -Password (ConvertTo-SecureString Password!1 -AsPlainText -Force)
```

L'operazione restituisce l'identificazione personale del certificato, che verrà usata per connettersi al nuovo cluster e controllarne lo stato di integrità.

### <a name="connect-to-the-new-cluster-and-check-health-status"></a>Connettersi al nuovo cluster e controllarne lo stato di integrità

Connettersi al cluster e assicurarsi che tutti i relativi nodi siano integri (sostituendo le variabili `clusterName` e `thumb` per il cluster):

```powershell
# Connect to the cluster
$clusterName = "sftestupgrade.southcentralus.cloudapp.azure.com:19000"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"

Connect-ServiceFabricCluster `
    -ConnectionEndpoint $clusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My

# Check cluster health
Get-ServiceFabricClusterHealth
```

È ora possibile iniziare la procedura di aggiornamento.

## <a name="upgrade-the-primary-node-type-vms"></a>Eseguire l'aggiornamento delle macchine virtuali con tipo di nodo primario

Dopo aver deciso di aggiornare le macchine virtuali del tipo di nodo primario, aggiungere un nuovo set di scalabilità al tipo di nodo primario in modo che il tipo di nodo primario ora abbia due set di scalabilità. Per visualizzare le modifiche necessarie, sono stati forniti file [modello](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json) e [parametri](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json) di esempio. Le macchine virtuali del nuovo set di scalabilità sono di dimensioni D4_V2 standard ed eseguono Windows Server 2016 Datacenter con contenitori. Con il nuovo set di scalabilità vengono anche aggiunti un nuovo bilanciamento del carico e l'indirizzo IP pubblico. 

Per trovare il nuovo set di scalabilità nel modello, cercare la risorsa "Microsoft.Compute/virtualMachineScaleSets" tramite il parametro vmNodeType2Name. Il nuovo set di scalabilità verrà aggiunto al tipo di nodo primario usando l'impostazione proprietà -> virtualMachineProfile - > extensionProfile -> estensioni -> proprietà -> impostazioni -> nodeTypeRef.

### <a name="deploy-the-updated-template"></a>Distribuire il modello aggiornato

Modificare `parameterFilePath` e `templateFilePath` in base alle esigenze quindi eseguire il comando seguente:

```powershell
# Deploy the new scale set into the primary node type along with a new load balancer and public IP
$templateFilePath = "C:\Deploy-2NodeTypes-3ScaleSets.json"
$parameterFilePath = "C:\Deploy-2NodeTypes-3ScaleSets.parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

Al termine della distribuzione, controllare di nuovo l'integrità del cluster e assicurarsi che tutti i nodi (sul set di scalabilità originale e su quello nuovo) siano integri.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-nodes-to-the-new-scale-set"></a>Eseguire la migrazione dei nodi al nuovo set di scalabilità

A questo punto è possibile avviare la disabilitazione dei nodi del set di scalabilità originale. Poiché tali nodi diventano disabilitati, i servizi di sistema e i nodi di inizializzazione eseguono la migrazione alle macchine virtuali del nuovo set di scalabilità poiché è anche contrassegnato come tipo di nodo primario.

Per la scalabilità verticale dei tipi di nodo non primari, in questo passaggio è necessario modificare il vincolo di posizionamento del servizio in modo da includere il nuovo tipo di nodo/set di scalabilità di macchine virtuali e quindi ridurre il numero di istanze del set di scalabilità di macchine virtuali precedente a zero, un nodo alla volta (per garantire che la rimozione dei nodi non influisca sull'affidabilità dei

```powershell
# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

Usare Service Fabric Explorer per monitorare la migrazione dei nodi di inizializzazione al nuovo set di scalabilità e l'avanzamento dei nodi nel set di scalabilità originale dallo stato *Disattivazione* a quello *Disattivato*.

> [!NOTE]
> Il completamento dell'operazione di disattivazione in tutti i nodi del set di scalabilità originale potrebbe richiedere del tempo. Per garantire la coerenza dei dati, è possibile modificare un solo nodo di inizializzazione alla volta. Per ogni modifica del nodo di inizializzazione è necessario un aggiornamento del cluster. La sostituzione di un nodo di inizializzazione richiede quindi due aggiornamenti del cluster, uno ciascuno per l'aggiunta e la rimozione di nodi. L'aggiornamento dei cinque nodi di inizializzazione in questo scenario di esempio comporterà dieci aggiornamenti del cluster.

## <a name="remove-the-original-scale-set"></a>Rimuovere il set di scalabilità originale

Al termine dell'operazione di disattivazione, rimuovere il set di scalabilità.

```powershell
# Remove the original scale set
$scaleSetName = "NTvm1"

Remove-AzVmss `
    -ResourceGroupName $resourceGroupName `
    -VMScaleSetName $scaleSetName `
    -Force

Write-Host "Removed scale set $scaleSetName"
```

In Service Fabric Explorer i nodi rimossi (e di conseguenza lo *stato di integrità del cluster*) verranno visualizzati nello stato *Errore*.

## <a name="remove-the-old-load-balancer-and-update-dns-settings"></a>Rimuovere il servizio di bilanciamento del carico precedente e aggiornare le impostazioni DNS

A questo punto, è possibile rimuovere le risorse correlate al tipo di nodo primario precedente, a partire dal servizio di bilanciamento del carico e dall'IP pubblico precedente. 

```powershell
$lbname="LB-sfupgradetest-NTvm1"
$oldPublicIpName="PublicIP-LB-FE-0"
$newPublicIpName="PublicIP-LB-FE-2"

# Store DNS settings of public IP address related to old Primary NodeType into variable 
$oldprimaryPublicIP = Get-AzPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname

$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel

$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

# Remove Load Balancer related to old Primary NodeType. This will cause a brief period of downtime for the cluster
Remove-AzLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force

# Remove the old public IP
Remove-AzPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force
```

Successivamente, si aggiornano le impostazioni DNS del nuovo IP pubblico per rispecchiare le impostazioni dall'IP pubblico del tipo di nodo primario precedente.

```powershell
# Replace DNS settings of Public IP address related to new Primary Node Type with DNS settings of Public IP address related to old Primary Node Type
$PublicIP = Get-AzPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzPublicIpAddress -PublicIpAddress $PublicIP
```

Ancora una volta, controllare l'integrità del cluster

```powershell
# Check the cluster health
Get-ServiceFabricClusterHealth
```

Infine, rimuovere lo stato del nodo per ognuno dei nodi correlati. Se il livello di durabilità del set di scalabilità precedente era Silver o Gold, questo passaggio verrà eseguito automaticamente.

```powershell
# Remove node state for the deleted nodes.
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

Il tipo di nodo primario del cluster è stato aggiornato. Verificare che tutte le applicazioni distribuite funzionino correttamente e che l'integrità del cluster sia accettabile.

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [aggiungere un tipo di nodo a un cluster](virtual-machine-scale-set-scale-node-type-scale-out.md)
* Informazioni sulla [scalabilità delle applicazioni](service-fabric-concepts-scalability.md).
* [Ridimensionamento orizzontale di un cluster di Azure](service-fabric-tutorial-scale-cluster.md).
* [Ridimensionamento di un cluster di Azure a livello di codice](service-fabric-cluster-programmatic-scaling.md) tramite l'SDK di calcolo di Azure Fluent.
* [Ridimensionamento orizzontale di un cluster autonomo](service-fabric-cluster-windows-server-add-remove-nodes.md).
