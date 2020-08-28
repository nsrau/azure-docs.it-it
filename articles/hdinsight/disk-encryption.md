---
title: Crittografia doppia per dati inattivi
titleSuffix: Azure HDInsight
description: Questo articolo descrive i due livelli di crittografia disponibili per i dati inattivi nei cluster HDInsight di Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/10/2020
ms.openlocfilehash: a78b56de537cfac0da48814afe9b07d911a61af1
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "89020763"
---
# <a name="azure-hdinsight-double-encryption-for-data-at-rest"></a>Crittografia doppia di Azure HDInsight per dati inattivi

Questo articolo illustra i metodi per la crittografia dei dati inattivi nei cluster HDInsight di Azure. La crittografia dei dati inattivi si riferisce alla crittografia sui dischi gestiti (dischi dati, dischi del sistema operativo e dischi temporanei) collegati alle macchine virtuali del cluster HDInsight. 

Questo documento non indirizza i dati archiviati nell'account di archiviazione di Azure. I cluster possono avere uno o più account di archiviazione di Azure collegati in cui le chiavi di crittografia possono anche essere gestite da Microsoft o gestite dal cliente, ma il servizio di crittografia è diverso. Per altre informazioni sulla crittografia di archiviazione di Azure, vedere [crittografia di archiviazione di Azure per dati](../storage/common/storage-service-encryption.md)inattivi.

## <a name="introduction"></a>Introduzione

In Azure sono disponibili tre ruoli principali del disco gestito: il disco dati, il disco del sistema operativo e il disco temporaneo. Per altre informazioni sui diversi tipi di dischi gestiti, vedere [Introduzione a Managed Disks di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview). 

HDInsight supporta più tipi di crittografia in due livelli diversi:

- Crittografia lato server (SSE): la crittografia SSE viene eseguita dal servizio di archiviazione. In HDInsight, SSE viene usato per crittografare i dischi del sistema operativo e i dischi dati. ed è abilitato per impostazione predefinita. SSE è un servizio di crittografia di livello 1.
- Crittografia nell'host con chiave gestita dalla piattaforma: simile a SSE, questo tipo di crittografia viene eseguito dal servizio di archiviazione. Tuttavia, è solo per i dischi temporanei e non è abilitato per impostazione predefinita. La crittografia nell'host è anche un servizio di crittografia di livello 1.
- Crittografia dei dati inattivi tramite la chiave gestita dal cliente: questo tipo di crittografia può essere usato su dischi dati e temporanei. Non è abilitato per impostazione predefinita e richiede al cliente di fornire la propria chiave tramite Azure Key Vault. La crittografia Rest è un servizio di crittografia di livello 2.

Questi tipi sono riepilogati nella tabella seguente.

|Tipo di cluster |Disco del sistema operativo (disco gestito) |Disco dati (disco gestito) |Disco dati temporanei (unità SSD locale) |
|---|---|---|---|
|Kafka, HBase con scritture accelerate|Layer1: [crittografia SSE](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#encryption) per impostazione predefinita|Layer1: [crittografia SSE](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#encryption) per impostazione predefinita, Layer2: crittografia inattiva facoltativa con CMK|Layer1: crittografia facoltativa nell'host con PMK, Layer2: crittografia aggiuntiva inattiva con CMK|
|Tutti gli altri cluster (Spark, Interactive, Hadoop, HBase senza scritture accelerate)|Layer1: [crittografia SSE](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#encryption) per impostazione predefinita|N/D|Layer1: crittografia facoltativa nell'host con PMK, Layer2: crittografia aggiuntiva inattiva con CMK|

## <a name="encryption-at-rest-using-customer-managed-keys"></a>Crittografia dei componenti inattivi con chiavi gestite dal cliente

La crittografia a chiave gestita dal cliente è un processo che viene gestito in un unico passaggio durante la creazione del cluster senza costi aggiuntivi. È sufficiente autorizzare un'identità gestita con Azure Key Vault e aggiungere la chiave di crittografia durante la creazione del cluster.

Sia i dischi dati che i dischi temporanei in ogni nodo del cluster vengono crittografati con una chiave di crittografia dei dati simmetrica. La chiave DEK viene protetta con la chiave di crittografia della chiave dall'insieme di credenziali delle chiavi. I processi di crittografia e decrittografia vengono gestiti interamente da Azure HDInsight.

Per i dischi del sistema operativo collegati alle VM del cluster è disponibile un solo livello di crittografia (PMK). Si consiglia ai clienti di evitare di copiare dati sensibili nei dischi del sistema operativo se è necessaria una crittografia CMK per i rispettivi scenari.

Se il firewall dell'insieme di credenziali delle chiavi è abilitato nell'insieme di credenziali delle chiavi in cui è archiviata la chiave di crittografia del disco, gli indirizzi IP del provider di risorse di HDInsight per l'area in cui verrà distribuito il cluster devono essere aggiunti alla configurazione del firewall di Key Vault. Questa operazione è necessaria perché HDInsight non è un servizio di Azure Key Vault attendibile.

È possibile usare il portale di Azure o l'interfaccia della riga di comando di Azure per ruotare in modo sicuro le chiavi nell'insieme di credenziali delle chiavi. Quando si ruota un tasto, il cluster HDInsight inizia a usare la nuova chiave in pochi minuti. Abilitare le funzionalità di protezione delle chiavi di [eliminazione](../key-vault/general/soft-delete-overview.md) temporanea per la protezione da scenari ransomware e da eliminazioni accidentali. Gli insiemi di credenziali delle chiavi senza questa funzionalità di protezione non sono supportati.

### <a name="get-started-with-customer-managed-keys"></a>Introduzione alle chiavi gestite dal cliente

Per creare un cluster HDInsight abilitato per la chiave gestita dal cliente, seguire questa procedura:

1. Creare identità gestite per le risorse di Azure
1. Creare un Azure Key Vault
1. Creare la chiave
1. Crea criteri di accesso
1. Creare un cluster HDInsight con la chiave gestita dal cliente abilitata
1. Rotazione della chiave di crittografia

Ogni passaggio è illustrato in dettaglio in una delle sezioni seguenti.

### <a name="create-managed-identities-for-azure-resources"></a>Creare identità gestite per le risorse di Azure

Creare un'identità gestita assegnata dall'utente per l'autenticazione Key Vault.

Per i passaggi specifici, vedere [creare un'identità gestita assegnata dall'utente](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) . Per altre informazioni sul funzionamento delle identità gestite in Azure HDInsight, vedere [identità gestite in Azure HDInsight](hdinsight-managed-identities.md). Assicurarsi di salvare l'ID risorsa dell'identità gestita da usare quando lo si aggiungerà ai criteri di accesso di Key Vault.

### <a name="create-azure-key-vault"></a>Creare un Azure Key Vault

Creare un insieme di credenziali delle chiavi. Per i passaggi specifici, vedere [creare Azure Key Vault](../key-vault/secrets/quick-create-portal.md) .

In HDInsight è supportato solo Azure Key Vault. Se si ha un proprio insieme di credenziali delle chiavi, è possibile importare le chiavi in Azure Key Vault. Tenere presente che l'insieme di credenziali delle chiavi deve avere l' **eliminazione** temporanea abilitata. Per altre informazioni sull'importazione delle chiavi esistenti, vedere [Informazioni su chiavi, segreti e certificati](../key-vault/about-keys-secrets-and-certificates.md).

### <a name="create-key"></a>Creare la chiave

1. Dal nuovo insieme di credenziali delle chiavi passare a **Impostazioni**  >  **chiavi**  >  **+ genera/importa**.

    ![Generare una nuova chiave in Azure Key Vault](./media/disk-encryption/create-new-key.png "Generare una nuova chiave in Azure Key Vault")

1. Specificare un nome e quindi selezionare **Crea**. Mantenere il **tipo di chiave** predefinito di **RSA**.

    ![genera il nome della chiave](./media/disk-encryption/create-key.png "Genera nome chiave")

1. Quando si torna alla pagina **chiavi** , selezionare la chiave creata.

    ![Elenco chiavi Key Vault](./media/disk-encryption/key-vault-key-list.png)

1. Selezionare la versione per aprire la pagina della **versione della chiave** . Quando si usa una chiave personalizzata per la crittografia del cluster HDInsight, è necessario specificare l'URI della chiave. Copiare l'**identificatore di chiave** e salvarlo in qualunque posizione fino a quando non si è pronti per creare il cluster.

    ![ottenere l'identificatore di chiave](./media/disk-encryption/get-key-identifier.png)

### <a name="create-access-policy"></a>Crea criteri di accesso

1. Dal nuovo insieme di credenziali delle chiavi passare a **Impostazioni**criteri di  >  **accesso**e  >  **Aggiungi criteri di accesso**.

    ![Creare il nuovo criterio di accesso di Azure Key Vault](./media/disk-encryption/key-vault-access-policy.png)

1. Nella pagina **Aggiungi criteri di accesso** specificare le informazioni seguenti:

    |Proprietà |Descrizione|
    |---|---|
    |Autorizzazioni chiave|Selezionare **Get**, **Unwrap Key**e **Wrap Key**.|
    |Autorizzazioni segrete|Selezionare **Get**, **set**ed **Delete**.|
    |Selezionare un'entità|Selezionare l'identità gestita assegnata dall'utente creata in precedenza.|

    ![Impostare Selezionare un'entità per il criterio di accesso di Azure Key Vault](./media/disk-encryption/azure-portal-add-access-policy.png)

1. Selezionare **Aggiungi**.

1. Selezionare **Salva**.

    ![Salva i criteri di accesso Azure Key Vault](./media/disk-encryption/add-key-vault-access-policy-save.png)

### <a name="create-cluster-with-customer-managed-key-disk-encryption"></a>Creare un cluster con crittografia del disco della chiave gestita dal cliente

È ora possibile creare un nuovo cluster HDInsight. Le chiavi gestite dal cliente possono essere applicate solo ai nuovi cluster durante la creazione del cluster. Non è possibile rimuovere la crittografia dai cluster di chiavi gestite dal cliente e le chiavi gestite dal cliente non possono essere aggiunte ai cluster esistenti.

#### <a name="using-the-azure-portal"></a>Uso del portale di Azure

Durante la creazione del cluster, specificare l' **identificatore di chiave**completo, inclusa la versione della chiave. Ad esempio: `https://contoso-kv.vault.azure.net/keys/myClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. È anche necessario assegnare l'identità gestita al cluster e fornire l'URI della chiave.

![Crea nuovo cluster](./media/disk-encryption/create-cluster-portal.png)

#### <a name="using-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure

L'esempio seguente illustra come usare l'interfaccia della riga di comando di Azure per creare un nuovo cluster di Apache Spark con la crittografia del disco abilitata. Per altre informazioni, vedere l'interfaccia della riga di comando di [Azure AZ HDInsight create](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create).

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \
-p "HttpPassword1234!" --workernode-data-disks-per-node 2 \
--storage-account MyStorageAccount \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--assign-identity MyMSI
```

#### <a name="using-azure-resource-manager-templates"></a>Uso di modelli di Azure Resource Manager

Nell'esempio seguente viene illustrato come utilizzare un modello di Azure Resource Manager per creare un nuovo cluster Apache Spark con la crittografia del disco abilitata. Per altre informazioni, vedere [che cosa sono i modelli ARM?](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview).

Questo esempio USA PowerShell per chiamare il modello.

```powershell
$templateFile = "azuredeploy.json"
$ResourceGroupName = "MyResourceGroup"
$clusterName = "MyCluster"
$password = ConvertTo-SecureString 'HttpPassword1234!' -AsPlainText -Force
$diskEncryptionVaultUri = "https://MyKeyVault.vault.azure.net"
$diskEncryptionKeyName = "SparkClusterKey"
$diskEncryptionKeyVersion = "00000000000000000000000000000000"
$managedIdentityName = "MyMSI"

New-AzResourceGroupDeployment `
  -Name mySpark `
  -TemplateFile $templateFile `
  -ResourceGroupName $ResourceGroupName `
  -clusterName $clusterName `
  -clusterLoginPassword $password `
` -sshPassword $password `
  -diskEncryptionVaultUri $diskEncryptionVaultUri `
  -diskEncryptionKeyName $diskEncryptionKeyName `
  -diskEncryptionKeyVersion $diskEncryptionKeyVersion `
  -managedIdentityName $managedIdentityName
```

Il contenuto del modello di gestione delle risorse `azuredeploy.json` :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "0.9.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "metadata": {
        "description": "The name of the HDInsight cluster to create."
      }
    },
    "clusterLoginUserName": {
      "type": "string",
      "defaultValue": "admin",
      "metadata": {
        "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
      }
    },
    "clusterLoginPassword": {
      "type": "securestring",
      "metadata": {
        "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "The location where all azure resources will be deployed."
      }
    },
    "sshUserName": {
      "type": "string",
      "defaultValue": "sshuser",
      "metadata": {
        "description": "These credentials can be used to remotely access the cluster."
      }
    },
    "sshPassword": {
      "type": "securestring",
      "metadata": {
        "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
      }
    },
    "headNodeSize": {
      "type": "string",
      "defaultValue": "Standard_D12_v2",
      "metadata": {
        "description": "The VM size of the head nodes."
      }
    },
    "workerNodeSize": {
      "type": "string",
      "defaultValue": "Standard_D13_v2",
      "metadata": {
        "description": "The VM size of the worker nodes."
      }
    },
    "diskEncryptionVaultUri": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault DNSname."
      }
    },
    "diskEncryptionKeyName": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault key name."
      }
    },
    "diskEncryptionKeyVersion": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault key version for the selected key."
      }
    },
    "managedIdentityName": {
      "type": "string",
      "metadata": {
        "description": "The user-assigned managed identity."
      }
    }
  },
  "variables": {
    "defaultStorageAccount": {
      "name": "[uniqueString(resourceGroup().id)]",
      "type": "Standard_LRS"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('defaultStorageAccount').name]",
      "location": "[parameters('location')]",
      "apiVersion": "2019-06-01",
      "sku": {
        "name": "[variables('defaultStorageAccount').type]"
      },
      "kind": "Storage",
      "properties": {}
    },
    {
      "apiVersion": "2018-06-01-preview",
      "name": "[parameters('clusterName')]",
      "type": "Microsoft.HDInsight/clusters",
      "location": "[parameters('location')]",
      "properties": {
        "clusterVersion": "3.6",
        "osType": "Linux",
        "tier": "standard",
        "clusterDefinition": {
          "kind": "spark",
          "componentVersion": {
            "Spark": "2.3"
          },
          "configurations": {
            "gateway": {
              "restAuthCredential.isEnabled": true,
              "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
              "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
            }
          }
        },
        "storageProfile": {
          "storageaccounts": [
            {
              "name": "[replace(replace(reference(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccount').name), '2019-06-01').primaryEndpoints.blob,'https://',''),'/','')]",
              "isDefault": true,
              "container": "[parameters('clusterName')]",
              "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccount').name), '2019-06-01').keys[0].value]"
            }
          ]
        },
        "computeProfile": {
          "roles": [
            {
              "name": "headnode",
              "minInstanceCount": 1,
              "targetInstanceCount": 2,
              "hardwareProfile": {
                "vmSize": "[parameters('headNodeSize')]"
              },
              "osProfile": {
                "linuxOperatingSystemProfile": {
                  "username": "[parameters('sshUserName')]",
                  "password": "[parameters('sshPassword')]"
                },
              },
            },
            {
              "name": "workernode",
              "targetInstanceCount": 1,
              "hardwareProfile": {
                "vmSize": "[parameters('workerNodeSize')]"
              },
              "osProfile": {
                "linuxOperatingSystemProfile": {
                  "username": "[parameters('sshUserName')]",
                  "password": "[parameters('sshPassword')]"
                },
              },
            }
          ]
        },
        "minSupportedTlsVersion": "1.2",
        "diskEncryptionProperties": {
          "vaultUri": "[parameters('diskEncryptionVaultUri')]",
          "keyName": "[parameters('diskEncryptionKeyName')]",
          "keyVersion": "[parameters('diskEncryptionKeyVersion')]",
          "msiResourceId": "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('managedIdentityName'))]"
        }
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('managedIdentityName'))]": {}
        }
      }
    }
  ]
}
```

### <a name="rotating-the-encryption-key"></a>Rotazione della chiave di crittografia

Potrebbero esserci scenari in cui potrebbe essere necessario modificare le chiavi di crittografia usate dal cluster HDInsight dopo che è stato creato. Questo può essere facilmente tramite il portale. Per questa operazione, il cluster deve avere accesso sia alla chiave corrente che alla nuova chiave prevista. in caso contrario, l'operazione di rotazione del tasto avrà esito negativo.

#### <a name="using-the-azure-portal"></a>Uso del portale di Azure

Per ruotare la chiave, è necessario l'URI dell'insieme di credenziali delle chiavi di base. Al termine, passare alla sezione proprietà del cluster HDInsight nel portale e fare clic su **cambia chiave** in **URL chiave di crittografia del disco**. Immettere l'URL della nuova chiave e inviare per ruotare la chiave.

![ruotare la chiave di crittografia del disco](./media/disk-encryption/change-key.png)

#### <a name="using-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure

Nell'esempio seguente viene illustrato come ruotare la chiave di crittografia del disco per un cluster HDInsight esistente. Per altre informazioni, vedere l'interfaccia della riga di comando di [Azure AZ HDInsight rotate-Disk-Encryption-Key](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-rotate-disk-encryption-key).

```azurecli
az hdinsight rotate-disk-encryption-key \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--name MyCluster \
--resource-group MyResourceGroup
```

## <a name="faq-for-customer-managed-key-encryption"></a>Domande frequenti sulla crittografia della chiave gestita dal cliente

**In che modo il cluster HDInsight accede all'insieme di credenziali delle chiavi?**

HDInsight accede all'istanza di Azure Key Vault usando l'identità gestita associata al cluster HDInsight. È possibile creare questa identità gestita prima o durante la creazione del cluster. È anche necessario concedere all'identità gestita l'accesso all'insieme di credenziali delle chiavi in cui viene archiviata la chiave.

**Questa funzionalità è disponibile per tutti i cluster in HDInsight?**

La crittografia della chiave gestita dal cliente è disponibile per tutti i tipi di cluster eccetto Spark 2,1 e 2,2.

**È possibile usare più chiavi per crittografare dischi o cartelle differenti?**

No, tutti i dischi gestiti e i dischi delle risorse vengono crittografati con la stessa chiave.

**Cosa accade se il cluster perde l'accesso all'insieme di credenziali delle chiavi o alla chiave?**

Se il cluster perde l'accesso alla chiave, gli avvisi vengono visualizzati nel portale di Apache Ambari. In questo stato, l'operazione di **modifica della chiave** avrà esito negativo. Una volta ripristinato l'accesso alla chiave, gli avvisi di Ambari verranno abbandonati e le operazioni come la rotazione delle chiavi possono essere eseguite correttamente.

![avviso di Ambari di accesso alla chiave](./media/disk-encryption/ambari-alert.png)

**Come è possibile recuperare il cluster se le chiavi vengono eliminate?**

Poiché sono supportate solo le chiavi abilitate per l'eliminazione temporanea, se le chiavi vengono ripristinate nell'insieme di credenziali delle chiavi, il cluster dovrebbe riottenere l'accesso alle chiavi. Per recuperare una chiave di Azure Key Vault, vedere [Undo-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) o [AZ-Key Vault-Key-Recover](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-recover).


**Se si aumenta la scalabilità verticale di un cluster, i nuovi nodi supporteranno le chiavi gestite dal cliente in modo uniforme?**

Sì. Il cluster deve avere accesso alla chiave nell'insieme di credenziali delle chiavi durante il passaggio a un piano superiore. La stessa chiave viene usata per crittografare i dischi gestiti e i dischi di risorse nel cluster.

**Le chiavi gestite dal cliente sono disponibili nella mia posizione?**

Le chiavi gestite dal cliente di HDInsight sono disponibili in tutti i cloud pubblici e nei cloud nazionali.

## <a name="encryption-at-host-using-platform-managed-keys"></a>Crittografia nell'host con chiavi gestite dalla piattaforma

### <a name="enable-in-the-azure-portal"></a>Abilitazione nel portale di Azure

La crittografia nell'host può essere abilitata durante la creazione del cluster nel portale di Azure.

> [!Note]
> Quando è abilitata la crittografia in host, non è possibile aggiungere applicazioni al cluster HDInsight da Azure Marketplace.

:::image type="content" source="media/disk-encryption/encryption-at-host.png" alt-text="Abilitare la crittografia nell'host.":::

Questa opzione Abilita la [crittografia in host](../virtual-machines/linux/disks-enable-host-based-encryption-portal.md) per i dischi dati temporanei delle VM HDINSIGHT usando PMK. La crittografia nell'host è [supportata solo in determinati SKU di macchine virtuali in aree limitate](../virtual-machines/linux/disks-enable-host-based-encryption-portal.md) e HDInsight supporta la [configurazione e gli SKU del nodo seguenti](./hdinsight-supported-node-configuration.md).

Per informazioni sulle dimensioni corrette della macchina virtuale per il cluster HDInsight, vedere [selezione delle dimensioni appropriate per il cluster Azure HDInsight](hdinsight-selecting-vm-size.md). Lo SKU di VM predefinito per il nodo Zookeeper quando la crittografia nell'host è abilitato sarà DS2V2.

### <a name="enable-using-powershell"></a>Abilitare con PowerShell

Il frammento di codice seguente mostra come creare un nuovo cluster Azure HDInsight con la crittografia in host abilitata tramite PowerShell. Usa il parametro `-EncryptionAtHost $true` per abilitare la funzionalità.

```powershell
$storageAccountResourceGroupName = "Group"
$storageAccountName = "yourstorageacct001"
$storageAccountKey = Get-AzStorageAccountKey `
    -ResourceGroupName $storageAccountResourceGroupName `
    -Name $storageAccountName | %{ $_.Key1 }
$storageContainer = "container002"
# Cluster configuration info
$location = "East US 2"
$clusterResourceGroupName = "Group"
$clusterName = "your-hadoop-002"
$clusterCreds = Get-Credential
# If the cluster's resource group doesn't exist yet, run:
# New-AzResourceGroup -Name $clusterResourceGroupName -Location $location
# Create the cluster
New-AzHDInsightCluster `
    -ClusterType Hadoop `
    -ClusterSizeInNodes 4 `
    -ResourceGroupName $clusterResourceGroupName `
    -ClusterName $clusterName `
    -HttpCredential $clusterCreds `
    -Location $location `
    -DefaultStorageAccountName "$storageAccountName.blob.core.contoso.net" `
    -DefaultStorageAccountKey $storageAccountKey `
    -DefaultStorageContainer $storageContainer `
    -SshCredential $clusterCreds `
    -EncryptionAtHost $true `
```

### <a name="enable-using-azure-cli"></a>Abilitare tramite l'interfaccia della riga di comando di Azure

Il frammento di codice seguente illustra come creare un nuovo cluster Azure HDInsight con la crittografia in host abilitata, usando l'interfaccia della riga di comando di Azure. Usa il parametro `--encryption-at-host true` per abilitare la funzionalità.

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \\
-p "HttpPassword1234!" \\
--storage-account MyStorageAccount --encryption-at-host true
```

## <a name="next-steps"></a>Passaggi successivi

* Per ulteriori informazioni su Azure Key Vault, vedere [che cos'è Azure Key Vault](../key-vault/general/overview.md).
* [Panoramica della sicurezza aziendale in Azure HDInsight](./domain-joined/hdinsight-security-overview.md).
