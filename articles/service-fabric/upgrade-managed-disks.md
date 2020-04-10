---
title: Aggiornare i nodi del cluster per usare i dischi gestiti di AzureUpgrade cluster nodes to use Azure managed disks
description: Ecco come aggiornare un cluster di Service Fabric esistente per usare i dischi gestiti di Azure con tempi di inattività minimi o assi del cluster.
ms.topic: how-to
ms.date: 4/07/2020
ms.openlocfilehash: 5f4698718a35970e47de2a0ee6d053802c8ef919
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991212"
---
# <a name="upgrade-cluster-nodes-to-use-azure-managed-disks"></a>Aggiornare i nodi del cluster per usare i dischi gestiti di AzureUpgrade cluster nodes to use Azure managed disks

[I dischi gestiti](../virtual-machines/windows/managed-disks-overview.md) di Azure sono l'offerta di archiviazione del disco consigliata per l'uso con le macchine virtuali di Azure per l'archiviazione permanente dei dati. È possibile migliorare la resilienza dei carichi di lavoro di Service Fabric aggiornando i set di scalabilità delle macchine virtuali alla base dei tipi di nodo per l'utilizzo di dischi gestiti. Ecco come aggiornare un cluster di Service Fabric esistente per usare i dischi gestiti di Azure con tempi di inattività minimi o assi del cluster.

La strategia generale per l'aggiornamento di un nodo cluster di Service Fabric per l'utilizzo di dischi gestiti consiste nel:The general strategy for upgrading a Service Fabric cluster node to use managed disks is to:

1. Distribuire un set di scalabilità di macchine virtuali altrimenti duplicato di quel tipo di nodo, ma con l'oggetto [managedDisk](https://docs.microsoft.com/azure/templates/microsoft.compute/2019-07-01/virtualmachinescalesets/virtualmachines#ManagedDiskParameters) aggiunto alla `osDisk` sezione del modello di distribuzione del set di scalabilità della macchina virtuale. Il nuovo set di scalabilità deve essere associato allo stesso bilanciamento del carico/IP dell'originale, in modo che i clienti non subiscano un'interruzione del servizio durante la migrazione.

2. Una volta che i set di scalabilità originali e aggiornati sono in esecuzione fianco a fianco, disabilitare le istanze del nodo originale una alla volta in modo che i servizi di sistema (o le repliche dei servizi con stato) eseguano la migrazione al nuovo set di scalabilità.

3. Verificare che il cluster e i nuovi nodi siano integri, quindi rimuovere il set di scalabilità originale e lo stato del nodo per i nodi eliminati.

Questo articolo illustra i passaggi per l'aggiornamento del tipo di nodo primario di un cluster di esempio per l'utilizzo di dischi gestiti, evitando eventuali tempi di inattività del cluster (vedere la nota seguente). Lo stato iniziale del cluster di test di esempio è costituito da un tipo di nodo di [durabilità Argento,](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster)supportato da un singolo set di scalabilità con cinque nodi.

> [!CAUTION]
> Si verificherà un'interruzione di questa procedura solo se si dispone di dipendenze nel DNS del cluster (ad esempio quando si accede a [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)). La procedura consigliata di architettura per i [servizi front-end](https://docs.microsoft.com/azure/architecture/microservices/design/gateway) consiste nel disporre di un tipo di servizio di [bilanciamento del carico](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview) davanti ai tipi di nodo per rendere possibile lo scambio di nodi senza un'interruzione.

Ecco i [modelli e i cmdlet](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade-no-outage) per Azure Resource Manager che useremo per completare lo scenario di aggiornamento. Le modifiche al modello verranno illustrate in Distribuire un set di [scalabilità aggiornato per il tipo di nodo primario](#deploy-an-upgraded-scale-set-for-the-primary-node-type) riportato di seguito.

## <a name="set-up-the-test-cluster"></a>Configurare il cluster di test

È possibile configurare il cluster di test iniziale di Service Fabric.Let up the initial Service Fabric test cluster. Scaricare innanzitutto [i](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade-no-outage) modelli di esempio di Gestione risorse di Azure che verranno utilizzati per completare questo scenario.

Accedere quindi all'account Azure.Next, sign in to your Azure account.

```powershell
# Sign in to your Azure account
Login-AzAccount -SubscriptionId "<subscription ID>"
```

I comandi seguenti illustrano come generare un nuovo certificato autofirmato e distribuire il cluster di test. Se si dispone già di un certificato che si desidera utilizzare, passare a [Utilizzare un certificato esistente per distribuire il cluster](#use-an-existing-certificate-to-deploy-the-cluster).

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>Generare un certificato autofirmato e distribuire il cluster

Assegnare innanzitutto le variabili necessarie per la distribuzione del cluster di Service Fabric.First, assign the variables you'll need for Service Fabric cluster deployment. Regolare i `resourceGroupName` `certSubjectName`valori `parameterFilePath`per `templateFilePath` , , e per l'account e l'ambiente specifici:

```powershell
# Assign deployment variables
$resourceGroupName = "sftestupgradegroup"
$certOutputFolder = "c:\certificates"
$certPassword = "Password!1" | ConvertTo-SecureString -AsPlainText -Force
$certSubjectName = "sftestupgrade.southcentralus.cloudapp.azure.com"
$templateFilePath = "C:\Initial-1NodeType-UnmanagedDisks.json"
$parameterFilePath = "C:\Initial-1NodeType-UnmanagedDisks.parameters.json"
```

> [!NOTE]
> Verificare `certOutputFolder` che il percorso esista nel computer locale prima di eseguire il comando per distribuire un nuovo cluster di Service Fabric.

Aprire quindi il file [*Initial-1NodeType-UnmanagedDisks.parameters.json*](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Initial-1NodeType-UnmanagedDisks.parameters.json) `clusterName` e `dnsName` modificare i valori per e corrispondono ai valori dinamici impostati in PowerShell e salvare le modifiche.

Distribuire quindi il cluster di test di Service Fabric:Then deploy the Service Fabric test cluster:

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

Al termine della distribuzione, individuare il`$certPfx`file con estensione *pfx* ( ) nel computer locale e importarlo nell'archivio certificati:

```powershell
cd c:\certificates
$certPfx = ".\sftestupgradegroup20200312121003.pfx"

Import-PfxCertificate `
     -FilePath $certPfx `
     -CertStoreLocation Cert:\CurrentUser\My `
     -Password (ConvertTo-SecureString Password!1 -AsPlainText -Force)
```

L'operazione restituirà l'identificazione personale del certificato, che verrà usata per [connettersi al nuovo cluster](#connect-to-the-new-cluster-and-check-health-status) e verificarne lo stato di integrità. Ignorare la sezione seguente, ovvero un approccio alternativo alla distribuzione del cluster.

### <a name="use-an-existing-certificate-to-deploy-the-cluster"></a>Usare un certificato esistente per distribuire il clusterUse an existing certificate to deploy the cluster

È anche possibile usare un certificato dell'insieme di chiavi di Azure esistente per distribuire il cluster di test. A tale scopo, è necessario [ottenere riferimenti all'insieme](#obtain-your-key-vault-references) di credenziali delle chiavi e all'identificazione personale del certificato.

```powershell
# Key Vault variables
$certUrlValue = "https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
$sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
```

Aprire il file [*Initial-1NodeType-UnmanagedDisks.parameters.json*](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Initial-1NodeType-UnmanagedDisks.parameters.json) e `clusterName` `dnsName` modificare i valori per e in qualcosa di univoco.

Infine, designare un nome di gruppo `templateFilePath` `parameterFilePath` di risorse per il cluster e impostare i percorsi e dei file *Initial-1NodeType-UnmanagedDisks:*

> [!NOTE]
> Il gruppo di risorse designato deve già esistere e trovarsi nella stessa area dell'insieme di credenziali delle chiavi.

```powershell
# Deploy the new scale set (upgraded to use managed disks) into the primary node type.
$resourceGroupName = "sftestupgradegroup"
$templateFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.json"
$parameterFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json"
```

Infine, eseguire il comando seguente per distribuire il cluster di test iniziale:

```powershell
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

### <a name="connect-to-the-new-cluster-and-check-health-status"></a>Connettersi al nuovo cluster e controllare lo stato di integrità

Connettersi al cluster e assicurarsi che tutti e `clusterName` cinque `thumb` i nodi siano integri (sostituendo le variabili e per il cluster):

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

Con questo, siamo pronti per iniziare la procedura di aggiornamento.

## <a name="deploy-an-upgraded-scale-set-for-the-primary-node-type"></a>Distribuire un set di scalabilità aggiornato per il tipo di nodo primarioDeploy an upgraded scale set for the primary node type

Per eseguire l'aggiornamento o *scalare verticalmente*un tipo di nodo, è necessario distribuire una copia del set di scalabilità della macchina `nodeTypeRef`virtuale `subnet`di `loadBalancerBackendAddressPools`tale tipo di nodo, che in caso contrario è identica al set di scalabilità originale (incluso il riferimento allo stesso , e ) ad eccezione del fatto che include l'aggiornamento/modifiche desiderata e la propria subnet separata e il pool di indirizzi NAT in ingresso. Poiché si sta aggiornando un tipo di nodo primario, il nuovo set di scalabilità verrà contrassegnato come primario (`isPrimary: true`), proprio come il set di scalabilità originale. Per gli aggiornamenti del tipo di nodo non primario, è sufficiente omettere questo.)

Per comodità, le modifiche necessarie sono già state apportate per l'utente nel [modello](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Upgrade-1NodeType-2ScaleSets-ManagedDisks.json) *Upgrade-1NodeType-2ScaleSets-ManagedDisks* e nei file di [parametri.](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json)

Le sezioni seguenti illustrano in dettaglio le modifiche apportate al modello. Se si preferisce, è possibile ignorare la spiegazione e continuare [con il passaggio successivo della procedura](#obtain-your-key-vault-references)di aggiornamento.

### <a name="update-the-cluster-template-with-the-upgraded-scale-set"></a>Aggiornare il modello di cluster con il set di scalabilità aggiornatoUpdate the cluster template with the upgraded scale set

Di seguito sono riportate le modifiche sezione per sezione del modello di distribuzione cluster originale per l'aggiunta di un set di scalabilità aggiornato per il tipo di nodo primario.

#### <a name="parameters"></a>Parametri

Aggiungere i parametri per il nome dell'istanza, il conteggio e le dimensioni del nuovo set di scalabilità. Si `vmNodeType1Name` noti che è univoco per il nuovo set di scalabilità, mentre i valori di conteggio e dimensione sono identici al set di scalabilità originale.

**File modello**

```json
"vmNodeType1Name": {
    "type": "string",
    "defaultValue": "NTvm2",
    "maxLength": 9
},
"nt1InstanceCount": {
    "type": "int",
    "defaultValue": 5,
    "metadata": {
        "description": "Instance count for node type"
    }
},
"vmNodeType1Size": {
    "type": "string",
    "defaultValue": "Standard_D2_v2"
},
```

**File dei parametri**

```json
"vmNodeType1Name": {
    "value": "NTvm2"
},
"nt1InstanceCount": {
    "value": 5
},
"vmNodeType1Size": {
    "value": "Standard_D2_v2"
}
```

### <a name="variables"></a>variables

Nella sezione `variables` del modello di distribuzione aggiungere una voce per il pool di indirizzi NAT in ingresso del nuovo set di scalabilità.

**File modello**

```json
"lbNatPoolID1": "[concat(variables('lbID0'),'/inboundNatPools/LoadBalancerBEAddressNatPool1')]", 
```

### <a name="resources"></a>Risorse

Nella sezione *risorse* del modello di distribuzione aggiungere il nuovo set di scalabilità della macchina virtuale, tenendo presente quanto segue:In the deployment template resources section, add the new virtual machine scale set, keeping in mind these things:

* Il nuovo set di scalabilità fa riferimento allo stesso tipo di nodo dell'originale:The new scale set references the same node type as the original:

    ```json
    "nodeTypeRef": "[parameters('vmNodeType0Name')]",
    ```

* Il nuovo set di scalabilità fa riferimento allo stesso indirizzo back-end e subnet del servizio di bilanciamento del carico (ma usa un pool NAT in ingresso del servizio di bilanciamento del carico diverso):The new scale set references the same load balancer back-end address and subnet (but uses a different load balancer inbound NAT pool):

   ```json
    "loadBalancerBackendAddressPools": [
        {
            "id": "[variables('lbPoolID0')]"
        }
    ],
    "loadBalancerInboundNatPools": [
        {
            "id": "[variables('lbNatPoolID1')]"
        }
    ],
    "subnet": {
        "id": "[variables('subnet0Ref')]"
    }
   ```

* Come il set di scalabilità originale, il nuovo set di scalabilità viene contrassegnato come tipo di nodo primario. Durante l'aggiornamento di tipi di nodo non primari, omettere questa modifica.

    ```json
    "isPrimary": true,
    ```

* A differenza del set di scalabilità originale, il nuovo set di scalabilità viene aggiornato per l'utilizzo di dischi gestiti.

    ```json
    "managedDisk": {
        "storageAccountType": "[parameters('storageAccountType')]"
    }
    ```

Dopo aver implementato tutte le modifiche nei file di modello e parametri, passare alla sezione successiva per acquisire i riferimenti dell'insieme di credenziali delle chiavi e distribuire gli aggiornamenti nel cluster.

### <a name="obtain-your-key-vault-references"></a>Ottenere i riferimenti dell'insieme di chiavi

Per distribuire la configurazione aggiornata, è innanzitutto necessario ottenere diversi riferimenti al certificato del cluster archiviato nell'insieme di credenziali delle chiavi. Il modo più semplice per trovare questi valori è tramite il portale di Azure.The easiest way to find these values is through Azure portal. Sono necessari gli elementi seguenti:

* **URL dell'insieme di credenziali delle chiavi del certificato del cluster.** Dall'insieme di credenziali delle chiavi nel portale di Azure selezionare Certificati**identificatore segreto***del certificato desiderato:From* > your Key Vault in Azure portal, select **Certificates** > Your desired certificate Secret Identifier:

    ```powershell
    $certUrlValue="https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
    ```

* **Identificazione personale del certificato del cluster.** È probabile che questa opzione sia già [collegata al cluster iniziale](#connect-to-the-new-cluster-and-check-health-status) per verificarne lo stato di integrità. Dallo stesso pannello**certificato** > ( Certificati*certificato desiderato*) nel portale di Azure copiare **l'identificazione personale X.509 SHA-1 (esadecimale):**

    ```powershell
    $thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
    ```

* **ID risorsa dell'insieme di credenziali delle chiavi.** Dall'insieme di credenziali delle chiavi nel portale di Azure selezionare ID risorsa **proprietà:From** > your Key Vault in Azure portal, select Properties**Resource ID:**

    ```powershell
    $sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
    ```

### <a name="deploy-the-updated-template"></a>Distribuire il modello aggiornato

Regolare `parameterFilePath` `templateFilePath` il e in base alle esigenze e quindi eseguire il comando seguente:

```powershell
# Deploy the new scale set (upgraded to use managed disks) into the primary node type.
$templateFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.json"
$parameterFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

Al termine della distribuzione, controllare nuovamente l'integrità del cluster e assicurarsi che tutti i dieci nodi (cinque sul set di scalabilità originale e cinque nel nuovo set di scalabilità) siano integri.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-seed-nodes-to-the-new-scale-set"></a>Eseguire la migrazione dei nodi di cui al nuovo set di scalabilitàMigrate seed nodes to the new scale set

Ora siamo pronti per iniziare a disabilitare i nodi del set di scalabilità originale. Quando questi nodi vengono disabilitati, i servizi di sistema e i nodi di seeding eseguire il migrazione alle macchine virtuali del nuovo set di scalabilità perché è contrassegnato anche come tipo di nodo primario.

```powershell
# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

Usare Service Fabric Explorer per monitorare la migrazione dei nodi di serie al nuovo set di scalabilità e l'avanzamento dei nodi nel set di scalabilità originale dallo stato *Disabilitazione* a *Disabilitato.*

![Esplora utilità di servizio che mostra lo stato dei nodi disabilitatiService Fabric Explorer showing status of disabled nodes](./media/upgrade-managed-disks/service-fabric-explorer-node-status.png)

> [!NOTE]
> Il completamento dell'operazione di disabilitazione in tutti i nodi del set di scalabilità originale potrebbe richiedere del tempo. Per garantire la coerenza dei dati, è possibile modificare un solo nodo di seedina alla volta. Ogni modifica del nodo di seeding richiede un aggiornamento del cluster. Pertanto la sostituzione di un nodo di serie richiede due aggiornamenti del cluster (uno per l'aggiunta e la rimozione dei nodi). L'aggiornamento dei cinque nodi di seeding in questo scenario di esempio comporterà dieci aggiornamenti del cluster.

## <a name="remove-the-original-scale-set"></a>Rimuovere il set di scalabilità originale

Al termine dell'operazione di disabilitazione, rimuovere il set di scalabilità.

```powershell
# Remove the original scale set
$scaleSetName = "NTvm1"

Remove-AzVmss `
    -ResourceGroupName $resourceGroupName `
    -VMScaleSetName $scaleSetName `
    -Force

Write-Host "Removed scale set $scaleSetName"
```

In Esplora utilità service Fabric i nodi rimossi (e quindi *lo stato di integrità*del cluster ) verranno visualizzati nello stato *Errore.*

![Esplora infrastruttura del servizio che mostra i nodi disabilitati in stato di erroreService Fabric Explorer showing disabled nodes in error state](./media/upgrade-managed-disks/service-fabric-explorer-disabled-nodes-error-state.png)

Rimuovere i nodi obsoleti dal cluster Service Fabric per ripristinare lo stato di integrità del cluster in *OK*.

```powershell
# Remove node states for the deleted scale set
foreach($name in $nodeNames){
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

![Service Fabric Explorer con nodi inattivi in stato di errore rimossi](./media/upgrade-managed-disks/service-fabric-explorer-healthy-cluster.png)

## <a name="next-steps"></a>Passaggi successivi

In questa procedura dettagliata è stato illustrato come aggiornare i set di scalabilità delle macchine virtuali di un cluster di Service Fabric per usare i dischi gestiti evitando interruzioni del servizio durante il processo. Per altre info su argomenti correlati, consulta le risorse seguenti.

Viene illustrato come:

* [Aumentare le prestazioni di un tipo di nodo primario di un cluster di Service Fabric](service-fabric-scale-up-node-type.md)

* [Convertire un modello di set di scalabilità per usare i dischi gestitiConvert a scale set template to use managed disks](../virtual-machine-scale-sets/virtual-machine-scale-sets-convert-template-to-md.md)

* [Rimuovere un tipo di nodo di Service Fabric](service-fabric-how-to-remove-node-type.md)

Vedere anche la pagina relativa alla

* [Esempio: aggiornare i nodi del cluster per usare i dischi gestiti di AzureSample: Upgrade cluster nodes to use Azure managed disks](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade-no-outage)

* [Considerazioni sul ridimensionamento verticale](service-fabric-best-practices-capacity-scaling.md#vertical-scaling-considerations)