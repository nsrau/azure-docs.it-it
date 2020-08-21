---
title: Aggiornare i nodi del cluster per l'uso di Azure Managed Disks
description: Ecco come aggiornare un cluster di Service Fabric esistente per usare i dischi gestiti di Azure con un tempo di inattività minimo o insufficiente per il cluster.
ms.topic: how-to
ms.date: 4/07/2020
ms.openlocfilehash: 152bdaea121e65de8332fcde8543b8158ff11714
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2020
ms.locfileid: "88717524"
---
# <a name="upgrade-cluster-nodes-to-use-azure-managed-disks"></a>Aggiornare i nodi del cluster per l'uso di Azure Managed Disks

[Azure Managed disks](../virtual-machines/managed-disks-overview.md) è l'offerta di archiviazione su disco consigliata per l'uso con macchine virtuali di Azure per l'archiviazione permanente dei dati. Per migliorare la resilienza dei carichi di lavoro di Service Fabric, è possibile aggiornare i set di scalabilità di macchine virtuali sottostanti ai tipi di nodo per l'uso di Managed Disks. Ecco come aggiornare un cluster di Service Fabric esistente per usare i dischi gestiti di Azure con un tempo di inattività minimo o insufficiente per il cluster.

La strategia generale per l'aggiornamento di un nodo del cluster Service Fabric per l'uso di Managed disks è:

1. Distribuire un set di scalabilità di macchine virtuali altrimenti duplicato di quel tipo di nodo, ma con l'oggetto [managedDisk](/azure/templates/microsoft.compute/2019-07-01/virtualmachinescalesets/virtualmachines#ManagedDiskParameters) aggiunto alla `osDisk` sezione del modello di distribuzione del set di scalabilità di macchine virtuali. Il nuovo set di scalabilità deve essere associato allo stesso bilanciamento del carico/IP originale, in modo che i clienti non sperimentino un'interruzione del servizio durante la migrazione.

2. Quando entrambi i set di scalabilità originali e aggiornati vengono eseguiti side-by-Side, disabilitare le istanze del nodo originale una alla volta in modo che i servizi di sistema (o le repliche dei servizi con stato) eseguano la migrazione al nuovo set di scalabilità.

3. Verificare che il cluster e i nuovi nodi siano integri, quindi rimuovere il set di scalabilità originale e lo stato del nodo per i nodi eliminati.

Questo articolo illustra i passaggi per l'aggiornamento del tipo di nodo primario di un cluster di esempio per l'uso di Managed disks, evitando al tempo stesso i tempi di inattività del cluster (vedere la nota riportata di seguito). Lo stato iniziale del cluster di test di esempio è costituito da un tipo di nodo di [durabilità Silver](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster), supportato da un singolo set di scalabilità con cinque nodi.

> [!NOTE]
> Le limitazioni di un servizio di bilanciamento del carico SKU Basic impediscono l'aggiunta di un set di scalabilità aggiuntivo. È consigliabile usare invece il servizio di bilanciamento del carico SKU standard. Per ulteriori informazioni, vedere [confronto tra i due SKU](/azure/load-balancer/skus).

> [!CAUTION]
> Si verificherà un'interruzione con questa procedura solo se si dispone di dipendenze dal DNS del cluster, ad esempio quando si accede a [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md). La [procedura consigliata per l'architettura per i servizi front-end](/azure/architecture/microservices/design/gateway) consiste nel disporre di un tipo di servizio di [bilanciamento del carico](/azure/architecture/guide/technology-choices/load-balancing-overview) davanti ai tipi di nodo per consentire lo swapping dei nodi senza interruzioni.

Ecco i [modelli e i cmdlet](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade-no-outage) per Azure Resource Manager che verranno usati per completare lo scenario di aggiornamento. Le modifiche apportate al modello verranno descritte in [distribuire un set di scalabilità aggiornato per il tipo di nodo primario](#deploy-an-upgraded-scale-set-for-the-primary-node-type)  riportato di seguito.

## <a name="set-up-the-test-cluster"></a>Configurare il cluster di test

Si configurano il cluster iniziale Service Fabric test. Per prima cosa, [scaricare](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade-no-outage) i modelli di esempio Azure Resource Manager che verranno usati per completare questo scenario.

Successivamente, accedere all'account Azure.

```powershell
# Sign in to your Azure account
Login-AzAccount -SubscriptionId "<subscription ID>"
```

I comandi seguenti consentono di generare un nuovo certificato autofirmato e di distribuire il cluster di test. Se si dispone già di un certificato che si vuole usare, passare a [usare un certificato esistente per distribuire il cluster](#use-an-existing-certificate-to-deploy-the-cluster).

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>Generare un certificato autofirmato e distribuire il cluster

Per prima cosa, assegnare le variabili necessarie per la distribuzione del cluster Service Fabric. Modificare i valori per `resourceGroupName`, `certSubjectName`, `parameterFilePath`e `templateFilePath` per l'account e l'ambiente specifici:

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
> Prima di eseguire il comando per distribuire un nuovo cluster Service Fabric, verificare che nel computer locale esista il percorso `certOutputFolder`.

Successivamente, aprire il [*Initial-1NodeType-UnmanagedDisks.parameters.jsnel*](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Initial-1NodeType-UnmanagedDisks.parameters.json) file e modificare i valori per `clusterName` e `dnsName` in modo che corrispondano ai valori dinamici impostati in PowerShell e salvare le modifiche.

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

L'operazione restituisce l'identificazione personale del certificato, che verrà usata per [connettersi al nuovo cluster](#connect-to-the-new-cluster-and-check-health-status) e controllarne lo stato di integrità. Ignorare la sezione seguente, che rappresenta un approccio alternativo alla distribuzione di cluster.

### <a name="use-an-existing-certificate-to-deploy-the-cluster"></a>Usare un certificato esistente per distribuire il cluster

È anche possibile usare un certificato di Azure Key Vault esistente per distribuire il cluster di test. A tale scopo, è necessario [ottenere riferimenti all'identificazione personale del Key Vault e del](#obtain-your-key-vault-references) certificato.

```powershell
# Key Vault variables
$certUrlValue = "https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
$sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
```

Aprire il [*Initial-1NodeType-UnmanagedDisks.parameters.jsnel*](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Initial-1NodeType-UnmanagedDisks.parameters.json) file e modificare i valori di `clusterName` e `dnsName` in un valore univoco.

Infine, designare il nome di un gruppo di risorse per il cluster e impostare i `templateFilePath` `parameterFilePath` percorsi e dei file *Initial-1NodeType-UnmanagedDisks* :

> [!NOTE]
> Il gruppo di risorse designato deve esistere già e trovarsi nella stessa area del Key Vault.

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

### <a name="connect-to-the-new-cluster-and-check-health-status"></a>Connettersi al nuovo cluster e controllarne lo stato di integrità

Connettersi al cluster e assicurarsi che tutti e cinque i relativi nodi siano integri (sostituendo `clusterName` le `thumb` variabili e per il cluster):

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

A questo punto, è possibile iniziare la procedura di aggiornamento.

## <a name="deploy-an-upgraded-scale-set-for-the-primary-node-type"></a>Distribuire un set di scalabilità aggiornato per il tipo di nodo primario

Per eseguire l'aggiornamento o la *scalabilità verticale*di un tipo di nodo, è necessario distribuire una copia del set di scalabilità di macchine virtuali del tipo di nodo, che è altrimenti identico al set di scalabilità originale (incluso il riferimento allo stesso `nodeTypeRef` , `subnet` , e), con `loadBalancerBackendAddressPools` la differenza che include l'aggiornamento o le modifiche desiderate e la propria subnet separata e il pool di indirizzi NAT in ingresso Poiché si sta aggiornando un tipo di nodo primario, il nuovo set di scalabilità verrà contrassegnato come Primary ( `isPrimary: true` ), proprio come il set di scalabilità originale. Per gli aggiornamenti del tipo di nodo non primario, è sufficiente omettere questo.

Per praticità, le modifiche necessarie sono già state apportate nel [modello](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Upgrade-1NodeType-2ScaleSets-ManagedDisks.json) *upgrade-1NodeType-2ScaleSets-ManagedDisks* e nei file dei [parametri](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json) .

Le sezioni seguenti illustrano in modo dettagliato le modifiche del modello. Se si preferisce, è possibile ignorare la spiegazione e continuare con [il passaggio successivo della procedura di aggiornamento](#obtain-your-key-vault-references).

### <a name="update-the-cluster-template-with-the-upgraded-scale-set"></a>Aggiornare il modello del cluster con il set di scalabilità aggiornato

Di seguito sono riportate le modifiche della sezione per sezione del modello di distribuzione del cluster originale per l'aggiunta di un set di scalabilità aggiornato per il tipo di nodo primario.

#### <a name="parameters"></a>Parametri

Aggiungere i parametri per il nome dell'istanza, il numero e le dimensioni del nuovo set di scalabilità. Si noti che `vmNodeType1Name` è univoco per il nuovo set di scalabilità, mentre i valori di conteggio e dimensione sono identici al set di scalabilità originale.

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

### <a name="variables"></a>Variabili

Nella sezione modello di distribuzione `variables` aggiungere una voce per il pool di indirizzi NAT in ingresso del nuovo set di scalabilità.

**File modello**

```json
"lbNatPoolID1": "[concat(variables('lbID0'),'/inboundNatPools/LoadBalancerBEAddressNatPool1')]", 
```

### <a name="resources"></a>Risorse

Nella sezione Deployment template *Resources* aggiungere il nuovo set di scalabilità di macchine virtuali, tenendo presente quanto segue:

* Il nuovo set di scalabilità fa riferimento allo stesso tipo di nodo dell'originale:

    ```json
    "nodeTypeRef": "[parameters('vmNodeType0Name')]",
    ```

* Il nuovo set di scalabilità fa riferimento allo stesso indirizzo back-end e alla stessa subnet del servizio di bilanciamento del carico, ma usa un pool NAT in ingresso di bilanciamento del carico diverso:

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

* Come il set di scalabilità originale, il nuovo set di scalabilità è contrassegnato come tipo di nodo primario. Quando si aggiornano tipi di nodo non primari, omettere la modifica.

    ```json
    "isPrimary": true,
    ```

* A differenza del set di scalabilità originale, il nuovo set di scalabilità viene aggiornato per l'uso di Managed Disks.

    ```json
    "managedDisk": {
        "storageAccountType": "[parameters('storageAccountType')]"
    }
    ```

Dopo aver implementato tutte le modifiche nei file di modello e di parametri, passare alla sezione successiva per acquisire i riferimenti Key Vault e distribuire gli aggiornamenti al cluster.

### <a name="obtain-your-key-vault-references"></a>Ottenere i riferimenti Key Vault

Per distribuire la configurazione aggiornata, è prima di tutto necessario ottenere diversi riferimenti al certificato del cluster archiviato nel Key Vault. Il modo più semplice per trovare questi valori consiste nell'portale di Azure. Saranno necessari gli elementi seguenti:

* **URL Key Vault del certificato del cluster.** Dal Key Vault in portale di Azure selezionare **certificati**  >  *l'*  >  **identificatore del segreto**del certificato desiderato:

    ```powershell
    $certUrlValue="https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
    ```

* **Identificazione personale del certificato del cluster.** Probabilmente è già presente se è stata effettuata [la connessione al cluster iniziale](#connect-to-the-new-cluster-and-check-health-status) per verificarne lo stato di integrità. Dallo stesso pannello**certificato (Certificates**  >  *your desired certificate*) in portale di Azure, copy **X. 509 SHA-1 identificazione personale (in esadecimale)**:

    ```powershell
    $thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
    ```

* **ID risorsa della Key Vault.** Dal Key Vault in portale di Azure selezionare **Proprietà**  >  **ID risorsa**:

    ```powershell
    $sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
    ```

### <a name="deploy-the-updated-template"></a>Distribuire il modello aggiornato

Modificare `parameterFilePath` e `templateFilePath` in base alle esigenze quindi eseguire il comando seguente:

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

Al termine della distribuzione, controllare di nuovo l'integrità del cluster e assicurarsi che tutti i dieci nodi (cinque nell'originale e cinque sul nuovo set di scalabilità) siano integri.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-seed-nodes-to-the-new-scale-set"></a>Migrare i nodi di inizializzazione al nuovo set di scalabilità

A questo punto è possibile avviare la disabilitazione dei nodi del set di scalabilità originale. Poiché tali nodi diventano disabilitati, i servizi di sistema e i nodi di inizializzazione eseguono la migrazione alle macchine virtuali del nuovo set di scalabilità poiché è anche contrassegnato come tipo di nodo primario.

```powershell
# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

Usare Service Fabric Explorer per monitorare la migrazione dei nodi di inizializzazione al nuovo set di scalabilità e l'avanzamento dei nodi nel set di scalabilità originale dallo stato *Disattivazione* a quello *Disattivato*.

![Service Fabric Explorer che mostra lo stato dei nodi disabilitati](./media/upgrade-managed-disks/service-fabric-explorer-node-status.png)

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

![Service Fabric Explorer che mostra i nodi disabilitati in stato di errore](./media/upgrade-managed-disks/service-fabric-explorer-disabled-nodes-error-state.png)

Rimuovere i nodi obsoleti dal cluster Service Fabric per ripristinare lo stato di integrità del cluster su *OK*.

```powershell
# Remove node states for the deleted scale set
foreach($name in $nodeNames){
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

![Service Fabric Explorer con i nodi inattivi in stato di errore rimossi](./media/upgrade-managed-disks/service-fabric-explorer-healthy-cluster.png)

## <a name="next-steps"></a>Passaggi successivi

In questa procedura dettagliata si è appreso come aggiornare i set di scalabilità di un cluster di Service Fabric per l'uso di Managed disks evitando interruzioni del servizio durante il processo. Per altre informazioni sugli argomenti correlati, vedere le risorse seguenti.

Viene illustrato come:

* [Aumentare le prestazioni di un tipo di nodo primario di un cluster di Service Fabric](service-fabric-scale-up-primary-node-type.md)

* [Convertire un modello di set di scalabilità per usare Managed Disks](../virtual-machine-scale-sets/virtual-machine-scale-sets-convert-template-to-md.md)

* [Rimuovere un tipo di nodo di Service Fabric](service-fabric-how-to-remove-node-type.md)

Vedere anche la pagina relativa alla

* [Esempio: aggiornare i nodi del cluster per l'uso di Azure Managed Disks](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade-no-outage)

* [Considerazioni sul ridimensionamento verticale](service-fabric-best-practices-capacity-scaling.md#vertical-scaling-considerations)
