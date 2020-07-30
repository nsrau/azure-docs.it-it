---
title: Testare una macchina virtuale (VM) distribuita da VHD-Azure Marketplace
description: Informazioni su come testare e inviare un'offerta di macchina virtuale nel marketplace commerciale.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 07/29/2020
ms.openlocfilehash: 36c497a180070358332997649e768999c78935cb
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2020
ms.locfileid: "87433103"
---
# <a name="test-virtual-machine-vm-deployed-from-vhd"></a>Testare una macchina virtuale (VM) distribuita da un disco rigido virtuale

Questo articolo descrive come distribuire e testare una macchina virtuale (VM) di Azure dall'immagine del disco rigido virtuale generalizzato creata nella sezione precedente ([creare un asset tecnico della macchina virtuale di Azure)](create-azure-vm-technical-asset.md) per assicurarsi che l'immagine del disco rigido virtuale soddisfi i requisiti di pubblicazione di Azure Marketplace.

Completare questi passaggi per generare un report di compatibilità che certifichi che l'immagine del disco rigido virtuale può essere usata in Azure Marketplace.

1. Creare e distribuire il certificato necessario per la gestione remota delle macchine virtuali Azure Key Vault.
2. Distribuire una macchina virtuale di Azure dall'immagine del disco rigido virtuale generalizzato creata in [creare un asset tecnico della macchina virtuale di Azure](create-azure-vm-technical-asset.md).
3. Eseguire i test sulla macchina virtuale distribuita per assicurarsi che l'immagine del disco rigido virtuale sia pronta per essere pubblicata e usata per distribuire le macchine virtuali.

## <a name="running-scripts"></a>Esecuzione di script

Questo articolo contiene tre script da eseguire in PowerShell. Il desktop PowerShell funziona meglio, tuttavia, il Azure Cloud Shell può essere usato anche con l'opzione di PowerShell selezionata (in alto a sinistra della finestra).

1. Verificare che PowerShell sia configurato per l'esecuzione di script.

    - Aprire sempre PowerShell con l'opzione **Esegui come amministratore** .
    - Verificare che sia possibile eseguire gli script seguenti: `Set-ExecutionPolicy` e `RemoteSigned` .

2. [Installare l'interfaccia](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)della riga di comando di Azure

3. Installare Azure PowerShell AZ Module.
    1. **Opzione A**: non è stato ancora installato alcun modulo.
        - `Install-Module -Name Az -AllowClobber -Scope AllUsers`

        Per altre informazioni, vedere [Install Azure PowerShell Module](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-4.2.0).

    2. **Opzione B**: uso corrente del modulo AzureRM.

        - Uninstall-AzureRM
        - Install-module-name AZ-AllowClobber-scope AllUsers
        - Enable-AzureRmAlias-scope CurrentUser

        Per altre informazioni, vedere [Eseguire la migrazione di Azure PowerShell da AzureRM ad Az](https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az?view=azps-4.2.0).

4. Salvare i parametri della sessione.

Gli script in questa sezione usano variabili/parametri di sessione. Se si chiude la sessione, i parametri vengono cancellati. È consigliabile usare una sessione per eseguire tutti gli script per evitare errori del valore del parametro. Se questa operazione non è possibile, sarà necessario reinizializzare i parametri quando si apre una nuova sessione, in particolare per gli script successivi.

## <a name="create-and-deploy-certificates-for-azure-key-vault"></a>Creare e implementare certificati per Azure Key Vault

Questa sezione descrive come creare e distribuire i certificati autofirmati necessari per configurare la connettività di Gestione remota Windows (WinRM) in una macchina virtuale ospitata in Azure.

### <a name="create-certificates-for-azure-key-vault"></a>Creare certificati per Azure Key Vault

Questo processo si articola in tre passaggi:

1. Creare il certificato di protezione.
2. Creare un'istanza di Azure Key Vault per archiviare questo certificato.
3. Archiviare i certificati nell'insieme di credenziali delle chiavi.

È possibile usare un gruppo di risorse di Azure nuovo o esistente per questa operazione.

#### <a name="create-the-security-certificate"></a>Creare il certificato di sicurezza

Eseguire questo script per creare il file del certificato (con estensione pfx) in una cartella locale. Il certificato appartiene alla macchina virtuale di Azure pianificata che verrà distribuita dall'immagine del disco rigido virtuale. Per la macchina virtuale sono necessari un nome, un percorso e una password, come specificato dai parametri dello script. Modificare lo script di **creazione della certificazione** Azure PowerShell seguente per specificare i valori corretti per i parametri di script visualizzati nella tabella.

| **Parametro** | **Descrizione** |
| --- | --- |
| $certroopath | Cartella locale in cui salvare il file con estensione .pfx. |
| $location | Una delle posizioni geografiche standard di Azure. |
| $vmName | Nome della macchina virtuale di Azure pianificata. |
| $certname | Nome del certificato, che deve corrispondere al nome di dominio completo della macchina virtuale pianificata. |
| $certpassword | Password per i certificati, che deve corrispondere alla password usata per la macchina virtuale pianificata. |
| | |

```PowerShell
   # Certification creation script

    # pfx certification stored path
    $certroopath = "C:\certLocation"

    # location of the resource group
    $location = "westus"

    # Azure virtual machine name that we are going to create
    $vmName = "testvm000000906"

    # Certification name - should match with FQDN of Windows Azure creating VM
    $certname = "$vmName.$location.cloudapp.azure.com"

    # Certification password - should be match with password of Windows Azure creating VM
    $certpassword = "SecretPassword@123"

    $cert=New-SelfSignedCertificate -DnsName "$certname" -CertStoreLocation cert:\LocalMachine\My
    $pwd = ConvertTo-SecureString -String $certpassword -Force -AsPlainText
    $certwithThumb="cert:\localMachine\my\"+$cert.Thumbprint
    $filepath="$certroopath\$certname.pfx"
    Export-PfxCertificate -cert $certwithThumb -FilePath $filepath -Password $pwd
    Remove-Item -Path $certwithThumb

```

> [!TIP]
> Mantenere la stessa sessione di console di Azure PowerShell aperta e in esecuzione durante questa procedura, in modo che i valori dei vari parametri vengano mantenuti.

> [!WARNING]
> Se si salva questo script, salvarlo solo in una posizione sicura perché contiene informazioni di sicurezza (password).

#### <a name="create-the-azure-key-vault-to-store-the-certificate"></a>Creare un'istanza di Azure Key Vault per archiviare questo certificato

Copiare il contenuto del modello seguente in un file nel computer locale. Nello script di esempio riportato di seguito questa risorsa è `C:\certLocation\keyvault.json`.

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "defaultValue":"isvkv0001",
      "metadata": {
        "description": "Name of the Vault"
      }
    },
    "tenantId": {
      "type": "string",
      "defaultValue":"72f988bf-86f1-41af-91ab-2d7cd011db47",
      "metadata": {
        "description": "Tenant Id of the subscription. Get using Get-AzureSubscription cmdlet or Get Subscription API"
      }
    },
    "objectId": {
      "type": "string",
      "defaultValue":"d55739bf-d5d6-4ce0-be1c-49ade53c4315",
      "metadata": {
        "description": "Object Id of the AD user. Get using Get-AzureADUser or Get-AzureADServicePrincipal cmdlets"
      }
    },
    "keysPermissions": {
      "type": "array",
      "defaultValue": ["all"],
      "metadata": {
        "description": "Permissions to keys in the vault. Valid values are: all, create, import, update, get, list, delete, backup, restore, encrypt, decrypt, wrapkey, unwrapkey, sign, and verify."
      }
    },
    "secretsPermissions": {
      "type": "array",
      "defaultValue": ["all"],
      "metadata": {
        "description": "Permissions to secrets in the vault. Valid values are: all, get, set, list, and delete."
      }
    },
    "skuName": {
      "type": "string",
      "defaultValue": "Standard",
      "allowedValues": [
        "Standard",
        "Premium"
      ],
      "metadata": {
        "description": "SKU for the vault"
      }
    },
    "enableVaultForDeployment": {
      "type": "bool",
      "defaultValue": true,
      "allowedValues": [
        true,
        false
      ],
      "metadata": {
        "description": "Specifies if the vault is enabled for a VM deployment"
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "name": "[parameters('keyVaultName')]",
      "apiVersion": "2015-06-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "enabledForDeployment": "[parameters('enableVaultForDeployment')]",
        "tenantId": "[parameters('tenantId')]",
        "accessPolicies": [
          {
            "tenantId": "[parameters('tenantId')]",
            "objectId": "[parameters('objectId')]",
            "permissions": {
              "keys": "[parameters('keysPermissions')]",
              "secrets": "[parameters('secretsPermissions')]"
            }
          }
        ],
        "sku": {
          "name": "[parameters('skuName')]",
          "family": "A"
        }
      }
    }
  ]
}

```

Modificare ed eseguire lo script di Azure PowerShell seguente per creare un'istanza di Azure Key Vault e il gruppo di risorse associato. Sostituire i valori per i parametri indicati nella tabella seguente

| **Parametro** | **Descrizione** |
| --- | --- |
| $postfix | Stringa numerica casuale allegata agli identificatori della distribuzione. |
| $rgName | Nome del gruppo di risorse di Azure da creare. |
| $location | Una delle posizioni geografiche standard di Azure. |
| $kvTemplateJson | Percorso del file (keyvault.json) contenente il modello di Resource Manager per l'insieme di credenziali delle chiavi. |
| $kvname | Nome del nuovo insieme di credenziali delle chiavi.|
|   |   |

```PowerShell
# Creating Key vault in resource group

    # "Random" number for deployment identifiers
    $postfix = "0101048"

    # Resource group name
    $rgName = "TestRG$postfix"

    # Location of Resource Group
    $location = "westus"

    # Key vault template location
    $kvTemplateJson = "C:\certLocation\keyvault.json"

    # Key vault name
    $kvname = "isvkv$postfix"

    # code snippet to get the Azure user object ID
    try
       {
        $accounts = Get-AzContext
        $accountNum = 0
        $accounts.Account | %{ ++$accountNum; Write-Host $accountNum $_}
        Write-Host "`nPlease select User, e.g. 1:" -ForegroundColor DarkYellow
        [Int] $accountChoice = Read-Host

        While($accountChoice -lt 1 -or $accountChoice -gt $accounts.Length)
        {
            Write-Host "incorrect input" -ForegroundColor Red
            Write-Host "`nPlease select User, e.g. 1:" -ForegroundColor DarkYellow
            [Int] $accountChoice = Read-Host
        }

        $accountSelected = $accounts[$accountChoice-1]
        echo $accountSelected
        $id = $accountSelected.Account

        Write-Host "User $id Selected"
        $myobjectId=(Get-AzADUser -Mail $id).Id
      }
      catch
      {
      Write-Host $_.Exception.Message
      Break
      }

    # code snippet to get Azure User Tenant Id
      # SELECT Subscriptions
        #**************************************
        try
        {
        $subslist=Get-AzSubscription
        for($i=1; $i -le $subslist.Length;$i++)
        {
           Write-Host ($i.ToString() +":"+ $subslist[$i-1].Name)
        }
        Write-Host "`nPlease pick subscription from above, e.g. 1:" -ForegroundColor DarkYellow
        [int] $selectedsub=Read-Host

        While($selectedsub -lt 1 -or $selectedsub -gt $subslist.Length)
        {
            Write-Host "incorrect input" -ForegroundColor Red
            for($i=1; $i -le $subslist.Length;$i++)
             {
              Write-Host ($i.ToString() +":"+ $subslist[$i-1].Name)
             }
            Write-Host "`nPlease pick subscription from above, e.g. 1:" -ForegroundColor DarkYellow
           [int] $selectedsub=Read-Host
        }
        if($selectedsub -ge 1 -and $selectedsub -le $subslist.Length)
        {
        $mysubid=$subslist[$selectedsub-1].Id
        $mysubName=$subslist[$selectedsub-1].Name
        $mytenantId=$subslist[$selectedsub-1].TenantId
        Write-Host "$mysubName selected"
        }
        }
        catch
        {
        Write-Host $_.Exception.Message
        Break
        }

    # Create a resource group
     Write-Host "Creating Resource Group $rgName"
     az group create --name $rgName --location $location
     Write-Host "-----------------------------------"

    # Create key vault and configure access
    New-AzResourceGroupDeployment -Name "kvdeploy$postfix" -ResourceGroupName $rgName -TemplateFile $kvTemplateJson -keyVaultName $kvname -tenantId $mytenantId -objectId $myobjectId

    Set-AzKeyVaultAccessPolicy -VaultName $kvname -ObjectId $myobjectId -PermissionsToKeys Decrypt,Encrypt,UnwrapKey,WrapKey,Verify,Sign,Get,List,Update,Create,Import,Delete,Backup,Restore,Recover,Purge -PermissionsToSecrets Get,List,Set,Delete,Backup,Restore,Recover,Purge

```

#### <a name="store-the-certificates-to-the-key-vault"></a>Archiviare i certificati nell'insieme di credenziali delle chiavi

Archiviare i certificati contenuti nel file con estensione .pfx nel nuovo insieme di credenziali delle chiavi tramite questo script:

```PowerShell
 $fileName =$certroopath+"\$certname"+".pfx"

     $fileContentBytes = get-content $fileName -Encoding Byte
     $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

            $jsonObject = @"
    {
    "data": "$filecontentencoded",
    "dataType" :"pfx",
    "password": "$certpassword"
    }
"@
            echo $certpassword
            $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
            $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
            $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
            $objAzureKeyVaultSecret=Set-AzKeyVaultSecret -VaultName $kvname -Name "ISVSecret$postfix" -SecretValue $secret
            echo $objAzureKeyVaultSecret.Id

```

## <a name="deploy-an-azure-vm-from-your-generalized-vhd-image"></a>Distribuire una macchina virtuale di Azure dall'immagine del disco rigido virtuale generalizzato

Questa sezione descrive come distribuire un'immagine del disco rigido virtuale generalizzata per creare una nuova risorsa di macchina virtuale di Azure. Per questo processo, verranno usati il modello di Azure Resource Manager e lo script di Azure PowerShell forniti.

### <a name="prepare-an-azure-resource-manager-template"></a>Preparare un modello di Azure Resource Manager

Copiare uno dei seguenti modelli di Azure Resource Manager per la distribuzione VHD (per Windows o Linux) in un file locale denominato VHDtoImage.jssu. Lo script successivo richiederà il percorso nel computer locale per l'uso di questo file .json.

#### <a name="for-windows-based-vms"></a>Per le macchine virtuali basate su Windows

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "userStorageAccountName": {
            "type": "string"
        },
        "userStorageContainerName": {
            "type": "string",
            "defaultValue": "vhds"
        },
        "dnsNameForPublicIP": {
            "type": "string"
        },
        "adminUserName": {
            "defaultValue": "isv",
            "type": "string"
        },
        "adminPassword": {
            "type": "securestring",
            "defaultValue": "Password@123"
        },
        "osType": {
            "type": "string",
            "defaultValue": "windows",
            "allowedValues": [
                "windows",
                "linux"
            ]
        },
        "subscriptionId": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "vmSize": {
            "type": "string"
        },
        "publicIPAddressName": {
            "type": "string"
        },
        "vmName": {
            "type": "string"
        },
        "virtualNetworkName": {
            "type": "string"
        },
        "nicName": {
            "type": "string"
        },
        "vaultName": {
            "type": "string",
            "metadata": {
                "description": "Name of the KeyVault"
            }
        },
        "vaultResourceGroup": {
            "type": "string",
            "metadata": {
                "description": "Resource Group of the KeyVault"
            }
        },
        "certificateUrl": {
            "type": "string",
            "metadata": {
                "description": "Url of the certificate with version in KeyVault e.g. https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7"
            }
        },
        "vhdUrl": {
            "type": "string",
            "metadata": {
                "description": "VHD Url..."
            }
        }
    },
        "variables": {
            "addressPrefix": "10.0.0.0/16",
            "subnet1Name": "Subnet-1",
            "subnet2Name": "Subnet-2",
            "subnet1Prefix": "10.0.0.0/24",
            "subnet2Prefix": "10.0.1.0/24",
            "publicIPAddressType": "Dynamic",
            "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
            "subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
            "osDiskVhdName": "[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
        },
        "resources": [
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/publicIPAddresses",
                "name": "[parameters('publicIPAddressName')]",
                "location": "[parameters('location')]",
                "properties": {
                    "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                    "dnsSettings": {
                        "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                    }
                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/virtualNetworks",
                "name": "[parameters('virtualNetworkName')]",
                "location": "[parameters('location')]",
                "properties": {
                    "addressSpace": {
                        "addressPrefixes": [
                            "[variables('addressPrefix')]"
                        ]
                    },
                    "subnets": [
                        {
                            "name": "[variables('subnet1Name')]",
                            "properties": {
                                "addressPrefix": "[variables('subnet1Prefix')]"
                            }
                        },
                        {
                            "name": "[variables('subnet2Name')]",
                            "properties": {
                                "addressPrefix": "[variables('subnet2Prefix')]"
                            }
                        }
                    ]
                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/networkInterfaces",
                "name": "[parameters('nicName')]",
                "location": "[parameters('location')]",
                "dependsOn": [
                    "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
                    "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
                ],
                "properties": {
                    "ipConfigurations": [
                        {
                            "name": "ipconfig1",
                            "properties": {
                                "privateIPAllocationMethod": "Dynamic",
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                                },
                                "subnet": {
                                    "id": "[variables('subnet1Ref')]"
                                }
                            }
                        }
                    ]
                }
            },
            {
                "apiVersion": "2015-06-15",
                "type": "Microsoft.Compute/virtualMachines",
                "name": "[parameters('vmName')]",
                "location": "[parameters('location')]",
                "dependsOn": [
                    "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
                ],
                "properties": {
                    "hardwareProfile": {
                        "vmSize": "[parameters('vmSize')]"
                    },
                    "osProfile": {
                        "computername": "[parameters('vmName')]",
                        "adminUsername": "[parameters('adminUsername')]",
                        "adminPassword": "[parameters('adminPassword')]",
                        "secrets": [
                            {
                                "sourceVault": {
                                    "id": "[resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
                                },
                                "vaultCertificates": [
                                    {
                                        "certificateUrl": "[parameters('certificateUrl')]",
                                        "certificateStore": "My"
                                    }
                                ]
                            }
                        ],
                        "windowsConfiguration": {
                            "provisionVMAgent": "true",
                            "winRM": {
                                "listeners": [
                                    {
                                        "protocol": "http"
                                    },
                                    {
                                        "protocol": "https",
                                        "certificateUrl": "[parameters('certificateUrl')]"
                                    }
                                ]
                            },
                            "enableAutomaticUpdates": "true"
                        }
                    },
                    "storageProfile": {
                        "osDisk": {
                            "name": "[concat(parameters('vmName'),'-osDisk')]",
                            "osType": "[parameters('osType')]",
                            "caching": "ReadWrite",
                            "image": {
                                "uri": "[parameters('vhdUrl')]"
                            },
                            "vhd": {
                                "uri": "[variables('osDiskVhdName')]"
                            },
                            "createOption": "FromImage"
                        }
                    },
                    "networkProfile": {
                        "networkInterfaces": [
                            {
                                "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                            }
                        ]
                    },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": true,
                        "storageUri": "[concat('http://', parameters('userStorageAccountName'), '.blob.core.windows.net')]"
                    }
                }
                }
            }
        ]
    }

```

#### <a name="for-linux-based-vms"></a>Per macchine virtuali basate su Linux

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "userStorageAccountName": {
            "type": "string"
        },
        "userStorageContainerName": {
            "type": "string",
            "defaultValue": "vhds"
        },
        "dnsNameForPublicIP": {
            "type": "string"
        },
        "adminUserName": {
            "defaultValue": "isv",
            "type": "string"
        },
        "adminPassword": {
            "type": "securestring",
            "defaultValue": "Password@123"
        },
        "osType": {
            "type": "string",
            "defaultValue": "linux",
            "allowedValues": [
                "windows",
                "linux"
            ]
        },
        "subscriptionId": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "vmSize": {
            "type": "string"
        },
        "publicIPAddressName": {
            "type": "string"
        },
        "vmName": {
            "type": "string"
        },
        "virtualNetworkName": {
            "type": "string"
        },
        "nicName": {
            "type": "string"
        },
        "vaultName": {
            "type": "string",
            "metadata": {
                "description": "Name of the KeyVault"
            }
        },
        "vaultResourceGroup": {
            "type": "string",
            "metadata": {
                "description": "Resource Group of the KeyVault"
            }
        },
        "certificateUrl": {
            "type": "string",
            "metadata": {
                "description": "Url of the certificate with version in KeyVault e.g. https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7"
            }
        },
        "vhdUrl": {
            "type": "string",
            "metadata": {
                "description": "VHD Url..."
            }
        }
    },
        "variables": {
            "addressPrefix": "10.0.0.0/16",
            "subnet1Name": "Subnet-1",
            "subnet2Name": "Subnet-2",
            "subnet1Prefix": "10.0.0.0/24",
            "subnet2Prefix": "10.0.1.0/24",
            "publicIPAddressType": "Dynamic",
            "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
            "subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
            "osDiskVhdName": "[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
        },
        "resources": [
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/publicIPAddresses",
                "name": "[parameters('publicIPAddressName')]",
                "location": "[parameters('location')]",
                "properties": {
                    "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                    "dnsSettings": {
                        "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                    }
                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/virtualNetworks",
                "name": "[parameters('virtualNetworkName')]",
                "location": "[parameters('location')]",
                "properties": {
                    "addressSpace": {
                        "addressPrefixes": [
                            "[variables('addressPrefix')]"
                        ]
                    },
                    "subnets": [
                        {
                            "name": "[variables('subnet1Name')]",
                            "properties": {
                                "addressPrefix": "[variables('subnet1Prefix')]"
                            }
                        },
                        {
                            "name": "[variables('subnet2Name')]",
                            "properties": {
                                "addressPrefix": "[variables('subnet2Prefix')]"
                            }
                        }
                    ]
                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/networkInterfaces",
                "name": "[parameters('nicName')]",
                "location": "[parameters('location')]",
                "dependsOn": [
                    "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
                    "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
                ],
                "properties": {
                    "ipConfigurations": [
                        {
                            "name": "ipconfig1",
                            "properties": {
                                "privateIPAllocationMethod": "Dynamic",
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                                },
                                "subnet": {
                                    "id": "[variables('subnet1Ref')]"
                                }
                            }
                        }
                    ]
                }
            },
            {
                "apiVersion": "2015-06-15",
                "type": "Microsoft.Compute/virtualMachines",
                "name": "[parameters('vmName')]",
                "location": "[parameters('location')]",
                "dependsOn": [
                    "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
                ],
                "properties": {
                    "hardwareProfile": {
                        "vmSize": "[parameters('vmSize')]"
                    },
                    "osProfile": {
                        "computername": "[parameters('vmName')]",
                        "adminUsername": "[parameters('adminUsername')]",
                        "adminPassword": "[parameters('adminPassword')]",
                        "secrets": [
                            {
                                "sourceVault": {
                                    "id": "[resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
                                },
                                "vaultCertificates": [
                                    {
                                        "certificateUrl": "[parameters('certificateUrl')]",
                                        "certificateStore": "My"
                                    }
                                ]
                            }
                        ],
                        "windowsConfiguration": {
                            "provisionVMAgent": "true",
                            "winRM": {
                                "listeners": [
                                    {
                                        "protocol": "http"
                                    },
                                    {
                                        "protocol": "https",
                                        "certificateUrl": "[parameters('certificateUrl')]"
                                    }
                                ]
                            },
                            "enableAutomaticUpdates": "true"
                        }
                    },
                    "storageProfile": {
                        "osDisk": {
                            "name": "[concat(parameters('vmName'),'-osDisk')]",
                            "osType": "[parameters('osType')]",
                            "caching": "ReadWrite",
                            "image": {
                                "uri": "[parameters('vhdUrl')]"
                            },
                            "vhd": {
                                "uri": "[variables('osDiskVhdName')]"
                            },
                            "createOption": "FromImage"
                        }
                    },
                    "networkProfile": {
                        "networkInterfaces": [
                            {
                                "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                            }
                        ]
                    },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": false,
                        "storageUri": "[concat('http://', parameters('userStorageAccountName'), '.blob.core.windows.net')]"
                    }
                }
                }
            }
        ]
    }

```

Copiare e modificare lo script seguente per specificare i valori per questi parametri:

| **Parametro** | **Descrizione** |
| --- | --- |
| ResourceGroupName | Nome del gruppo di risorse di Azure esistente. In genere si usa lo stesso gruppo di replica dell'insieme di credenziali delle chiavi. |
| TemplateFile | Percorso completo del file VHDtoImage.json. |
| userStorageAccountName | Nome dell'account di archiviazione. |
| sNameForPublicIP | Nome DNS per l'indirizzo IP pubblico; deve essere minuscolo. |
| subscriptionId | Identificatore della sottoscrizione di Azure. |
| Location | Posizione geografica standard di Azure del gruppo di risorse. |
| vmName | Nome della macchina virtuale. |
| vaultName | Nome dell'insieme di credenziali delle chiavi. |
| vaultResourceGroup | Gruppo di risorse dell'insieme di credenziali delle chiavi. |
| certificateUrl | Indirizzo Web (URL) del certificato, inclusa la versione archiviata nell'insieme di credenziali delle chiavi, ad esempio: `https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7`. |
| vhdUrl | Indirizzo Web del disco rigido virtuale. |
| vmSize | Dimensione dell'istanza di macchina virtuale. |
| publicIPAddressName | Nome dell'indirizzo IP pubblico. |
| virtualNetworkName | Nome della rete virtuale. |
| nicName | Nome dell'interfaccia di rete per la rete virtuale. |
| adminUserName | Nome utente dell'account amministratore. |
| adminPassword | Password amministratore. |
|   |   |

### <a name="deploy-an-azure-vm"></a>Distribuire una macchina virtuale di Azure

Copiare e modificare lo script seguente per specificare i valori per le variabili `$storageaccount` e `$vhdUrl`. Eseguirlo per creare una risorsa di macchina virtuale di Azure dal disco rigido virtuale generalizzato esistente.

```PowerShell

# storage account of existing generalized VHD

$storageaccount = "testwinrm11815"

# generalized VHD URL
$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

# Full pathname to the file VHDtoImage.json. inserted these highlighted lines
$templateFile = "$certroopath\VHDtoImage.json"

# Size of the virtual machine instance.
$vmSize = "Standard_D2s_v3"

# Name of the public IP address.
$publicIPAddressName = "myPublicIP1"

# Name of the virtual network
$virtualNetworkName = "myVNET1"

# Name of the network interface card for the virtual network
$nicName = "myNIC1"

# Username of the administrator account
$adminUserName = "isv"

# The OS of the virtual machine
$osType = "windows"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile $templateFile -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "$vmSize" -publicIPAddressName "$publicIPAddressName" -virtualNetworkName "$virtualNetworkName" -nicName "$nicName" -adminUserName "$adminUserName" -adminPassword $pwd -osType "$osType""

# deploying VM with existing VHD
New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile $templateFile -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id -vhdUrl "$vhdUrl" -vmSize "$vmSize" -publicIPAddressName "$publicIPAddressName" -virtualNetworkName "$virtualNetworkName" -nicName "$nicName" -adminUserName "$adminUserName" -adminPassword $pwd -osType "$osType"

```

## <a name="run-tests-on-the-deployed-vm"></a>Eseguire test sulla macchina virtuale distribuita

### <a name="download-and-run-the-certification-test-tool"></a>Scaricare ed eseguire lo strumento di test di certificazione

Lo strumento di test di certificazione per Azure Certified è uno strumento di test automatico eseguito in un computer Windows locale, ma verifica una VM Windows o Linux basata su Azure. Lo strumento verifica che l'immagine di macchina virtuale dell'utente possa essere usata con Microsoft Azure e che siano stati soddisfatti i requisiti e le linee guida per la preparazione del disco rigido virtuale. Questo strumento garantisce che la macchina virtuale sia pronta per la pubblicazione in base ai requisiti di Azure Marketplace. "

1. Scaricare e installare lo [strumento di test di certificazione per Azure Certified](https://www.microsoft.com/download/details.aspx?id=44299).
2. Aprire lo strumento di certificazione, quindi selezionare **Avvia nuovo test**.
3. Nella schermata **Test Information** (Informazioni test) immettere un valore in **Test Name** (Nome test) per l'esecuzione del test.
4. Selezionare un valore per il campo **Piattaforma** per la macchina virtuale, scegliendo tra Windows Server o Linux. La piattaforma scelta determina le opzioni rimanenti.
5. Se la macchina virtuale usa questo servizio di database, selezionare la casella di controllo **Test for Azure SQL Database** (Test per il database SQL di Azure).

### <a name="connect-the-certification-tool-to-a-vm-image"></a>Connettere lo strumento di certificazione a un'immagine di macchina virtuale

Lo strumento si connette alle macchine virtuali basate su Windows con [Azure PowerShell](https://docs.microsoft.com/powershell/) e alle macchine virtuali Linux tramite [SSH.Net](https://www.ssh.com/ssh/protocol/). Scegliere una delle due opzioni seguenti, ovvero Linux o Windows.

#### <a name="option-1-connect-the-certification-tool-to-a-linux-vm-image"></a>Opzione 1: connettere lo strumento di certificazione a un'immagine di macchina virtuale Linux

1. Selezionare la modalità **Autenticazione SSH**: Autenticazione della password o Autenticazione del file di chiave.
2. Se si usa l'autenticazione basata su password, immettere i valori nei campi **VM DNS Name** (Nome DNS macchina virtuale), **Nome utente** e **Password**. Facoltativamente, è possibile modificare il valore predefinito per la **Porta SSH**.

    ![Strumento di test certificato per Azure, autenticazione della password dell'immagine di macchina virtuale Linux](media/avm-cert2.png)

3. Se si usa l'autenticazione basata su file di chiavi, immettere i valori nei campi **VM DNS Name** (Nome DNS macchina virtuale), **User name** (Nome utente) e **Private key** (Chiave privata). È anche possibile specificare un valore per **Passphrase** o modificare il numero predefinito per il campo **Porta SSH**.

#### <a name="option-2-connect-the-certification-tool-to-a-windows-based-vm-image"></a>Opzione 2: connettere lo strumento di certificazione a un'immagine di macchina virtuale basata su Windows

1. Immettere il **nome DNS completo della macchina virtuale**, ad esempio MyVMName.Cloudapp.net.
2. Immettere i valori per **User Name** (Nome utente) e **Password**.

    ![Strumento di test certificato per Azure, autenticazione della password dell'immagine di macchina virtuale basata su Windows](media/avm-cert4.png)

### <a name="run-a-certification-test"></a>Eseguire un test di certificazione

Dopo aver specificato i valori dei parametri per l'immagine di macchina virtuale nello strumento di certificazione, selezionare **Test Connection** (Connessione di prova) per creare una connessione valida per la macchina virtuale. Dopo che la connessone è verificata, selezionare **Next** (Avanti) per avviare il test. Al termine del test, i risultati del test vengono visualizzati in una tabella. La colonna Stato mostra (Pass/Fail/Warning) per ogni test. Se uno dei test non riesce, l'immagine _non viene_ certificata. In questo caso, esaminare i requisiti e i messaggi di errore, apportare le modifiche indicate ed eseguire nuovamente il test.

Al termine del test automatizzato, fornire informazioni aggiuntive sull'immagine della macchina virtuale nelle due schede della schermata **Questionario**, **Valutazione generale** e **Personalizzazione kernel**, quindi selezionare **Avanti**.

L'ultima schermata consente di indicare informazioni aggiuntive, ad esempio informazioni di accesso SSH per un'immagine di macchina virtuale Linux e una spiegazione per eventuali valutazioni non superate se si cercano le eccezioni.

Selezionare infine **Genera report** per scaricare i risultati del test e i file di log per i casi di test eseguiti, oltre alle risposte al questionario. Salvare i risultati nello stesso contenitore dei VHD.

## <a name="next-step"></a>Passaggio successivo

- [Problemi comuni degli URI di firma di accesso condiviso e relative correzioni](common-sas-uri-issues.md)
