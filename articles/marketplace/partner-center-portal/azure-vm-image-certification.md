---
title: Certificazione delle macchine virtuali di Azure-Azure Marketplace
description: Informazioni su come testare e inviare un'offerta di macchina virtuale nel Marketplace commerciale.
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 9bd7e40855f30612b90cf28365c0b1410cd3e3d8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81731134"
---
# <a name="azure-virtual-machine-vm-image-certification"></a>Certificazione immagini macchina virtuale (VM) di Azure

> [!NOTE]
> Stiamo comunicando la gestione delle offerte della macchina virtuale di Azure dal portale Cloud Partner al centro per i partner. Fino a quando non viene eseguita la migrazione delle offerte, continuare a seguire le istruzioni riportate in [creare certificati per Azure Key Vault](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-key-vault-cert) in Portale cloud partner per gestire le offerte.

Questo articolo descrive come testare e inviare un'immagine di macchina virtuale (VM) nel Marketplace commerciale per verificare che soddisfi i requisiti di pubblicazione di Azure Marketplace più recenti.

Completare questi passaggi prima di inviare l'offerta di VM:

1. Creare e distribuire i certificati.
2. Distribuire una macchina virtuale di Azure usando l'immagine generalizzata.
3. Eseguire convalide.

## <a name="create-and-deploy-certificates-for-azure-key-vault"></a>Creare e distribuire certificati per Azure Key Vault

Questa sezione descrive come creare e distribuire i certificati autofirmati necessari per configurare la connettività di Gestione remota Windows (WinRM) a una macchina virtuale ospitata da Azure.

### <a name="create-certificates-for-azure-key-vault"></a>Creare certificati per Azure Key Vault

Questo processo si articola in tre passaggi:

1. Creare il certificato di protezione.
2. Creare il Azure Key Vault per archiviare il certificato.
3. Archiviare i certificati nell'insieme di credenziali delle chiavi.

È possibile usare un gruppo di risorse di Azure nuovo o esistente per questa operazione.

#### <a name="create-the-security-certificate"></a>Creare il certificato di sicurezza

Modificare ed eseguire lo script di Azure PowerShell seguente per creare il file del certificato (con estensione pfx) in una cartella locale. Sostituire i valori per i parametri indicati nella tabella seguente.

| **Parametro** | **Descrizione** |
| --- | --- |
| $certroopath | Cartella locale in cui salvare il file con estensione pfx. |
| $location | Una delle posizioni geografiche standard di Azure. |
| $vmName | Nome della macchina virtuale di Azure pianificata. |
| $certname | Nome del certificato. deve corrispondere al nome di dominio completo della macchina virtuale pianificata. |
| $certpassword | Password per i certificati, deve corrispondere alla password usata per la VM pianificata. |
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
> Mantenere lo stesso Azure PowerShell sessione della console aperta ed eseguita durante questi passaggi per mantenere i valori dei vari parametri.

> [!WARNING]
> Se si salva questo script, salvarlo solo in un percorso sicuro perché contiene informazioni di sicurezza (una password).

#### <a name="create-the-azure-key-vault-to-store-the-certificate"></a>Creare l'insieme di credenziali delle chiavi di Azure per archiviare il certificato

Copiare il contenuto del modello seguente in un file nel computer locale. Nello script di esempio riportato di seguito, questa `C:\certLocation\keyvault.json`risorsa è).

```json
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

Modificare ed eseguire lo script di Azure PowerShell seguente per creare una Azure Key Vault e il gruppo di risorse associato. Sostituire i valori per i parametri indicati nella tabella seguente

| **Parametro** | **Descrizione** |
| --- | --- |
| $postfix | Stringa numerica casuale associata agli identificatori di distribuzione. |
| $rgName | Nome del gruppo di risorse di Azure da creare. |
| $location | Una delle posizioni geografiche standard di Azure. |
| $kvTemplateJson | Percorso del file (Key Vault. Json) che contiene Gestione risorse modello per Key Vault. |
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
        $accounts = Get-AzureAccount
        $accountNum = 0
        $accounts.Id | %{ ++$accountNum; Write-Host $accountNum $_}
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
        $id = $accountSelected.Id

        Write-Host "User $id Selected"
        $myobjectId=(Get-AzADUser -Mail $id)[0].Id
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
        $subslist=Get-AzureSubscription
        for($i=1; $i -le $subslist.Length;$i++)
        {
           Write-Host ($i.ToString() +":"+ $subslist[$i-1].SubscriptionName)
        }
        Write-Host "`nPlease pick subscription from above, e.g. 1:" -ForegroundColor DarkYellow
        [int] $selectedsub=Read-Host

        While($selectedsub -lt 1 -or $selectedsub -gt $subslist.Length)
        {
            Write-Host "incorrect input" -ForegroundColor Red
            for($i=1; $i -le $subslist.Length;$i++)
             {
              Write-Host ($i.ToString() +":"+ $subslist[$i-1].SubscriptionName)
             }
            Write-Host "`nPlease pick subscription from above, e.g. 1:" -ForegroundColor DarkYellow
           [int] $selectedsub=Read-Host
        }
        if($selectedsub -ge 1 -and $selectedsub -le $subslist.Length)
        {
        $mysubid=$subslist[$selectedsub-1].SubscriptionId
        $mysubName=$subslist[$selectedsub-1].SubscriptionName
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
     Create-ResourceGroup -rgName $rgName -location $location
     Write-Host "-----------------------------------"

    # Create key vault and configure access
    New-AzResourceGroupDeployment -Name "kvdeploy$postfix" -ResourceGroupName $rgName -TemplateFile $kvTemplateJson -keyVaultName $kvname -tenantId $mytenantId -objectId $myobjectId

    Set-AzKeyVaultAccessPolicy -VaultName $kvname -ObjectId $myobjectId -PermissionsToKeys all -PermissionsToSecrets all

```

#### <a name="store-the-certificates-to-the-key-vault"></a>Archiviare i certificati nell'insieme di credenziali delle chiavi

Archiviare i certificati contenuti nel file con estensione pfx nel nuovo insieme di credenziali delle chiavi usando questo script:

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
            $objAzureKeyVaultSecret=Set-AzureKeyVaultSecret -VaultName $kvname -Name "ISVSecret$postfix" -SecretValue $secret
            echo $objAzureKeyVaultSecret.Id

```

## <a name="deploy-an-azure-vm-using-your-generalized-image"></a>Distribuire una macchina virtuale di Azure usando l'immagine generalizzata

Questa sezione descrive come distribuire un'immagine del disco rigido virtuale generalizzato per creare una nuova risorsa di VM di Azure. Per questo processo, verrà usato il modello di Azure Resource Manager fornito e lo script di Azure PowerShell.

### <a name="prepare-an-azure-resource-manager-template"></a>Preparare un modello di Azure Resource Manager

Copiare il modello di Azure Resource Manager seguente per la distribuzione del disco rigido virtuale in un file locale denominato VHDtoImage. JSON. Lo script successivo richiederà il percorso nel computer locale per l'uso di questo JSON.

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

Modificare questo file per specificare i valori per i parametri seguenti:

| **Parametro** | **Descrizione** |
| --- | --- |
| ResourceGroupName | Nome del gruppo di risorse di Azure esistente. In genere, usare lo stesso RG dell'insieme di credenziali delle chiavi. |
| TemplateFile | Percorso completo del file VHDtoImage. JSON. |
| userStorageAccountName | Nome dell'account di archiviazione. |
| sNameForPublicIP | Nome DNS per l'indirizzo IP pubblico; deve essere minuscolo. |
| subscriptionId | Identificatore della sottoscrizione di Azure. |
| Percorso | Posizione geografica di Azure standard del gruppo di risorse. |
| vmName | Nome della macchina virtuale. |
| vaultName | Nome dell'insieme di credenziali delle chiavi. |
| vaultResourceGroup | Gruppo di risorse dell'insieme di credenziali delle chiavi. |
| certificateUrl | Indirizzo Web (URL) del certificato, inclusa la versione archiviata nell'insieme di credenziali delle chiavi, `https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7`ad esempio:. |
| vhdUrl | Indirizzo Web del disco rigido virtuale. |
| vmSize | Dimensioni dell'istanza di macchina virtuale. |
| publicIPAddressName | Nome dell'indirizzo IP pubblico. |
| virtualNetworkName | Nome della rete virtuale. |
| nicName | Nome della scheda di interfaccia di rete per la rete virtuale. |
| adminUserName | Nome utente dell'account amministratore. |
| adminPassword | Password dell'amministratore. |
|   |   |

### <a name="deploy-an-azure-vm"></a>Distribuire una macchina virtuale di Azure

Copiare e modificare lo script seguente per specificare i valori per `$storageaccount` le `$vhdUrl` variabili e. Eseguirlo per creare una risorsa di macchina virtuale di Azure dal disco rigido virtuale generalizzato esistente.

```PowerShell

# storage account of existing generalized VHD

$storageaccount = "testwinrm11815"

# generalized VHD URL

$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "Standard\_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

# deploying VM with existing VHD

New-AzResourceGroupDeployment -Name"dplisvvm$postfix" -ResourceGroupName"$rgName" -TemplateFile"C:\certLocation\VHDtoImage.json" -userStorageAccountName"$storageaccount" -dnsNameForPublicIP"$vmName" -subscriptionId"$mysubid" -location"$location" -vmName"$vmName" -vaultName"$kvname" -vaultResourceGroup"$rgName" -certificateUrl$objAzureKeyVaultSecret.Id  -vhdUrl"$vhdUrl" -vmSize"Standard\_A2" -publicIPAddressName"myPublicIP1" -virtualNetworkName"myVNET1" -nicName"myNIC1" -adminUserName"isv" -adminPassword$pwd

```

## <a name="run-validations"></a>Esegui convalide

Esistono due modi per eseguire le convalide nell'immagine distribuita:

- Usare lo strumento di test di certificazione per Azure Certified
- Usare l'API self-test

### <a name="download-and-run-the-certification-test-tool"></a>Scaricare ed eseguire lo strumento di test di certificazione

Lo strumento di test di certificazione per Azure Certified viene eseguito in un computer Windows locale, ma verifica una VM Windows o Linux basata su Azure. Certifica che l'immagine della macchina virtuale utente può essere usata con Microsoft Azure e che sono state soddisfatte le linee guida e i requisiti per la preparazione del disco rigido virtuale. L'output dello strumento è un report di compatibilità che verrà caricato nel portale del centro per i partner per richiedere la certificazione della macchina virtuale.

1. Scaricare e installare lo [strumento di test di certificazione per Azure Certified](https://www.microsoft.com/download/details.aspx?id=44299).
2. Aprire lo strumento di certificazione e quindi selezionare **Avvia nuovo test**.
3. Nella schermata **Test Information** (Informazioni test) immettere un valore in **Test Name** (Nome test) per l'esecuzione del test.
4. Selezionare la **piattaforma** per la VM, ovvero Windows Server o Linux. La piattaforma scelta determina le opzioni rimanenti.
5. Se la macchina virtuale usa questo servizio di database, selezionare la casella **di controllo test per database SQL di Azure** .

### <a name="connect-the-certification-tool-to-a-vm-image"></a>Connettere lo strumento di certificazione a un'immagine di macchina virtuale

Lo strumento si connette alle macchine virtuali basate su Windows con [Azure PowerShell](https://docs.microsoft.com/powershell/) e si connette alle macchine virtuali Linux tramite [SSH.NET](https://www.ssh.com/ssh/protocol/).

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>Connettere lo strumento di certificazione a un'immagine di macchina virtuale Linux

1. Selezionare la modalità di **autenticazione SSH** : autenticazione della password o autenticazione del file di chiave.
2. Se si usa l'autenticazione basata su password, immettere i valori per il **nome DNS della VM**, il **nome utente**e la **password**. È anche possibile modificare il numero di **porta SSH** predefinito.

    ![Strumento di test Azure Certified, autenticazione della password dell'immagine di macchina virtuale Linux](media/avm-cert2.png)

3. Se si usa l'autenticazione basata su file di chiavi, immettere i valori nei campi **VM DNS Name** (Nome DNS macchina virtuale), **User name** (Nome utente) e **Private key** (Chiave privata). È anche possibile includere una **passphrase** o modificare il numero di **porta SSH** predefinito.

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**Connettere lo strumento di certificazione a un'immagine di macchina virtuale basata su Windows**

1. Immettere il **nome DNS completo della macchina virtuale** (ad esempio, MyVMName.cloudapp.NET).
2. Immettere i valori per **User Name** (Nome utente) e **Password**.

    ![Strumento di test Azure Certified, autenticazione della password dell'immagine di macchina virtuale basata su Windows](media/avm-cert4.png)

### <a name="run-a-certification-test"></a>Eseguire un test di certificazione

Dopo avere specificato i valori dei parametri per l'immagine di macchina virtuale nello strumento di certificazione, selezionare **Test connessione** per creare una connessione valida alla VM. Dopo che la connessone è verificata, selezionare **Next** (Avanti) per avviare il test. Al termine del test, i risultati del test vengono visualizzati in una tabella. La colonna stato Mostra (pass/fail/Warning) per ogni test. Se uno dei test non riesce, l'immagine _non viene_ certificata. In questo caso, esaminare i requisiti e i messaggi di errore, apportare le modifiche suggerite ed eseguire di nuovo il test.

Al termine del test automatizzato, fornire informazioni aggiuntive sull'immagine della macchina virtuale nelle due schede della schermata del **questionario** , **valutazione generale** e **personalizzazione del kernel**, quindi selezionare **Avanti**.

L'ultima schermata consente di fornire altre informazioni, ad esempio le informazioni di accesso SSH per un'immagine di macchina virtuale Linux e una spiegazione per eventuali valutazioni non riuscite se si cercano eccezioni.

Infine, selezionare **genera report** per scaricare i risultati dei test e i file di log per i test case eseguiti insieme alle risposte al questionario. Salvare i risultati nello stesso contenitore dei VHD.

## <a name="next-step"></a>Passaggio successivo

- [Generare un URI (Uniform Resource Identifier) per ogni disco rigido virtuale](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-get-sas-uri)
