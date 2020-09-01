---
title: Convalida dell'immagine della macchina virtuale di Azure-Azure Marketplace
description: Informazioni su come testare e inviare un'offerta di macchina virtuale nel marketplace commerciale.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 08/14/2020
ms.openlocfilehash: fd8f41f88b6184eee15477c460dc9d2e521d25e6
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2020
ms.locfileid: "89144188"
---
# <a name="azure-virtual-machine-image-validation"></a>Convalida dell'immagine della macchina virtuale di Azure

Questo articolo descrive come testare e inviare un'immagine di macchina virtuale nel marketplace commerciale per verificare che soddisfi i requisiti di pubblicazione di Azure Marketplace più recenti.

Prima di inviare l'offerta di macchina virtuale, completare questi passaggi:

- Distribuire una macchina virtuale di Azure usando l'immagine generalizzata.
- Eseguire convalide.

## <a name="deploy-an-azure-vm-using-your-generalized-image"></a>Distribuire una macchina virtuale di Azure usando l'immagine generalizzata

Questa sezione descrive come distribuire un'immagine di disco rigido virtuale (VHD) generalizzata per creare una nuova risorsa di VM di Azure. Per questo processo, verrà usato il modello di Azure Resource Manager fornito e lo script di Azure PowerShell.

### <a name="prepare-an-azure-resource-manager-template"></a>Preparare un modello di Azure Resource Manager

Questa sezione descrive come creare e distribuire un'immagine di macchina virtuale (VM) fornita dall'utente. A tale scopo, è possibile fornire le immagini del sistema operativo e del disco rigido virtuale del disco dati da un disco rigido virtuale distribuito da Azure. Questa procedura distribuisce la macchina virtuale usando il disco rigido virtuale generalizzato.

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Caricare il VHD del sistema operativo generalizzato e i dischi rigidi virtuali del disco dati nell'account di archiviazione di Azure.
3. Nella home page selezionare **Crea una risorsa**, cercare "distribuzione modello" e selezionare **Crea**.
4. Scegliere **Creare un modello personalizzato nell'editor**.

    :::image type="content" source="media/vm/template-deployment.png" alt-text="Mostra la selezione di un modello.":::

5. Incollare il modello JSON seguente nell'editor e selezionare **Salva**.

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "userStorageAccountName": {
            "type": "String"
        },
        "userStorageContainerName": {
            "defaultValue": "vhds",
            "type": "String"
        },
        "dnsNameForPublicIP": {
            "type": "String"
        },
        "adminUserName": {
            "defaultValue": "isv",
            "type": "String"
        },
        "adminPassword": {
            "defaultValue": "",
            "type": "SecureString"
        },
        "osType": {
            "defaultValue": "windows",
            "allowedValues": [
                "windows",
                "linux"
            ],
            "type": "String"
        },
        "subscriptionId": {
            "type": "String"
        },
        "location": {
            "type": "String"
        },
        "vmSize": {
            "type": "String"
        },
        "publicIPAddressName": {
            "type": "String"
        },
        "vmName": {
            "type": "String"
        },
        "virtualNetworkName": {
            "type": "String"
        },
        "nicName": {
            "type": "String"
        },
        "vhdUrl": {
            "type": "String",
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
        "hostDNSNameScriptArgument": "[concat(parameters('dnsNameForPublicIP'),'.',parameters('location'),'.cloudapp.azure.com')]",
        "osDiskVhdName": "[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
    },
    "resources": [
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2015-06-15",
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
            "type": "Microsoft.Network/virtualNetworks",
            "apiVersion": "2015-06-15",
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
            "type": "Microsoft.Network/networkInterfaces",
            "apiVersion": "2015-06-15",
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
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2015-06-15",
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
                    "adminPassword": "[parameters('adminPassword')]"
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
                }
            }
        },
        {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "apiVersion": "2015-06-15",
            "name": "[concat(parameters('vmName'),'/WinRMCustomScriptExtension')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
            ],
            "properties": {
                "publisher": "Microsoft.Compute",
                "type": "CustomScriptExtension",
                "typeHandlerVersion": "1.4",
                "settings": {
                    "fileUris": [
                        "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/ConfigureWinRM.ps1",
                        "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/makecert.exe",
                        "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/winrmconf.cmd"
                    ],
                    "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -file ConfigureWinRM.ps1 ',variables('hostDNSNameScriptArgument'))]"
                }
            }
        }
    ]
}
```

<br>

6. Specificare i valori dei parametri per le pagine delle proprietà di Distribuzione personalizzata.

| ResourceGroupName | Nome del gruppo di risorse di Azure esistente. In genere si usa lo stesso gruppo di replica dell'insieme di credenziali delle chiavi. |
| --- | --- |
| TemplateFile | Percorso completo del file VHDtoImage.json. |
| userStorageAccountName | Nome dell'account di archiviazione. |
| dnsNameForPublicIP | Nome DNS per l'indirizzo IP pubblico; deve essere minuscolo. |
| subscriptionId | Identificatore della sottoscrizione di Azure. |
| Location | Posizione geografica standard di Azure del gruppo di risorse. |
| vmName | Nome della macchina virtuale. |
| vhdUrl | Indirizzo Web del disco rigido virtuale. |
| vmSize | Dimensione dell'istanza di macchina virtuale. |
| publicIPAddressName | Nome dell'indirizzo IP pubblico. |
| virtualNetworkName | Nome della rete virtuale. |
| nicName | Nome dell'interfaccia di rete per la rete virtuale. |
| adminUserName | Nome utente dell'account amministratore. |
| adminPassword | Password amministratore. |
|

7. Dopo aver immesso questi valori, selezionare **Acquisto**.

Azure inizierà la distribuzione. Crea una nuova macchina virtuale con il disco rigido virtuale non gestito specificato nel percorso di account di archiviazione indicato. È possibile monitorare lo stato nel portale di Azure selezionando **Macchine virtuali** nel lato sinistro del portale. Dopo che la macchina virtuale è stata creata, lo stato verrà modificato da Avvio in corso a In esecuzione.

#### <a name="for-generation-2-vm-deployment-use-this-template"></a>Per la distribuzione di macchine virtuali di seconda generazione, usare questo modello:

```JSON
{
   "$schema":"https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
   "contentVersion":"1.0.0.0",
   "parameters":{
      "userStorageAccountName":{
         "type":"String"
      },
      "userStorageContainerName":{
         "type":"String",
         "defaultValue":"vhds-86350-720-f4efbbb2-611b-4cd7-ad1e-afdgfuadfluad"
      },
      "dnsNameForPublicIP":{
         "type":"String"
      },
      "adminUserName":{
         "defaultValue":"isv",
         "type":"String"
      },
      "adminPassword":{
         "type":"securestring",
         "defaultValue":"Certcare@86350"
      },
      "osType":{
         "type":"string",
         "defaultValue":"linux",
         "allowedValues":[
            "windows",
            "linux"
         ]
      },
      "subscriptionId":{
         "type":"string"
      },
      "location":{
         "type":"string"
      },
      "vmSize":{
         "type":"string"
      },
      "publicIPAddressName":{
         "type":"string"
      },
      "vmName":{
         "type":"string"
      },
      "vNetNewOrExisting":{
         "defaultValue":"existing",
         "allowedValues":[
            "new",
            "existing"
         ],
         "type":"String",
         "metadata":{
            "description":"Specify whether to create a new or existing virtual network for the VM."
         }
      },
      "virtualNetworkName":{
         "type":"String",
         "defaultValue":""
      },
      "SubnetName":{
         "defaultValue":"subnet-5",
         "type":"String"
      },
      "SubnetPrefix":{
         "defaultValue":"10.0.5.0/24",
         "type":"String"
      },
      "nicName":{
         "type":"string"
      },
      "vhdUrl":{
         "type":"string",
         "metadata":{
            "description":"VHD Url..."
         }
      },
      "Datadisk1":{
         "type":"string",
         "metadata":{
            "description":"datadisk1 Url..."
         }
      },
      "Datadisk2":{
         "type":"string",
         "metadata":{
            "description":"datadisk2 Url..."
         }
      },
      "Datadisk3":{
         "type":"string",
         "metadata":{
            "description":"datadisk2 Url..."
         }
      }
   },
   "variables":{
      "addressPrefix":"10.0.0.0/16",
      "subnet1Name":"[parameters('SubnetName')]",
      "subnet1Prefix":"[parameters('SubnetPrefix')]",
      "publicIPAddressType":"Static",
      "vnetID":"[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
      "subnet1Ref":"[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
      "osDiskVhdName":"[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]",
      "dataDiskVhdName":"[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'datadisk')]",
      "imageName":"[concat(parameters('vmName'), '-image')]"
   },
   "resources":[
      {
         "apiVersion":"2015-05-01-preview",
         "type":"Microsoft.Network/publicIPAddresses",
         "name":"[parameters('publicIPAddressName')]",
         "location":"[parameters('location')]",
         "properties":{
            "publicIPAllocationMethod":"[variables('publicIPAddressType')]",
            "dnsSettings":{
               "domainNameLabel":"[parameters('dnsNameForPublicIP')]"
            }
         }
      },
      {
         "type":"Microsoft.Compute/images",
         "apiVersion":"2019-12-01",
         "name":"[variables('imageName')]",
         "location":"[parameters('location')]",
         "properties":{
            "storageProfile":{
               "osDisk":{
                  "osType":"[parameters('osType')]",
                  "osState":"Generalized",
                  "blobUri":"[parameters('vhdUrl')]",
                  "storageAccountType":"Standard_LRS"
               },
               "dataDisks":[
                  {
                     "lun":0,
                     "blobUri":"[parameters('Datadisk1')]",
                     "storageAccountType":"Standard_LRS"
                  },
                  {
                     "lun":1,
                     "blobUri":"[parameters('Datadisk2')]",
                     "storageAccountType":"Standard_LRS"
                  },
                  {
                     "lun":2,
                     "blobUri":"[parameters('Datadisk3')]",
                     "storageAccountType":"Standard_LRS"
                  }
               ]
            },
            "hyperVGeneration":"V2"
         }
      },
      {
         "apiVersion":"2015-05-01-preview",
         "type":"Microsoft.Network/virtualNetworks",
         "name":"[parameters('virtualNetworkName')]",
         "location":"[parameters('location')]",
         "properties":{
            "addressSpace":{
               "addressPrefixes":[
                  "[variables('addressPrefix')]"
               ]
            },
            "subnets":[
               {
                  "name":"[variables('subnet1Name')]",
                  "properties":{
                     "addressPrefix":"[variables('subnet1Prefix')]"
                  }
               }
            ]
         },
         "condition":"[equals(parameters('vNetNewOrExisting'), 'new')]"
      },
      {
         "apiVersion":"2016-09-01",
         "type":"Microsoft.Network/networkInterfaces",
         "name":"[parameters('nicName')]",
         "location":"[parameters('location')]",
         "dependsOn":[
            "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
            "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
         ],
         "properties":{
            "ipConfigurations":[
               {
                  "name":"ipconfig1",
                  "properties":{
                     "privateIPAllocationMethod":"Dynamic",
                     "publicIPAddress":{
                        "id":"[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                     },
                     "subnet":{
                        "id":"[variables('subnet1Ref')]"
                     }
                  }
               }
            ]
         }
      },
      {
         "apiVersion":"2019-12-01",
         "type":"Microsoft.Compute/virtualMachines",
         "name":"[parameters('vmName')]",
         "location":"[parameters('location')]",
         "dependsOn":[
            "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]",
            "[variables('imageName')]"
         ],
         "properties":{
            "hardwareProfile":{
               "vmSize":"[parameters('vmSize')]"
            },
            "osProfile":{
               "computername":"[parameters('vmName')]",
               "adminUsername":"[parameters('adminUsername')]",
               "adminPassword":"[parameters('adminPassword')]"
            },
            "storageProfile":{
               "imageReference":{
                  "id":"[resourceId('Microsoft.Compute/images', variables('imageName'))]"
               },
               "dataDisks":[
                  {
                     "name":"[concat(parameters('vmName'),'_DataDisk0')]",
                     "lun":0,
                     "createOption":"FromImage",
                     "managedDisk":{
                        "storageAccountType":"Standard_LRS"
                     }
                  },
                  {
                     "name":"[concat(parameters('vmName'),'_DataDisk1')]",
                     "lun":1,
                     "createOption":"FromImage",
                     "managedDisk":{
                        "storageAccountType":"Standard_LRS"
                     }
                  },
                  {
                     "name":"[concat(parameters('vmName'),'_DataDisk2')]",
                     "lun":2,
                     "createOption":"FromImage",
                     "managedDisk":{
                        "storageAccountType":"Standard_LRS"
                     }
                  }
               ],
               "osDisk":{
                  "name":"[concat(parameters('vmName'),'-OSDisk')]",
                  "createOption":"FromImage",
                  "managedDisk":{
                     "storageAccountType":"Standard_LRS"
                  }
               }
            },
            "networkProfile":{
               "networkInterfaces":[
                  {
                     "id":"[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                  }
               ]
            }
         }
      }
   ]
}
```

### <a name="deploy-an-azure-vm-using-powershell"></a>Distribuire una macchina virtuale di Azure con PowerShell

Copiare e modificare lo script seguente per specificare i valori per le variabili `$storageaccount` e `$vhdUrl`. Eseguirlo per creare una risorsa di macchina virtuale di Azure dal disco rigido virtuale generalizzato esistente.

```PowerShell
# storage account of existing generalized VHD

$storageaccount = "testwinrm11815" # generalized VHD URL
$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl
$objAzureKeyVaultSecret.Id -vhdUrl "$vhdUrl" -vmSize "Standard\_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

# deploying VM with existing VHD

New-AzResourceGroupDeployment -Name"dplisvvm$postfix" -ResourceGroupName"$rgName" -TemplateFile"C:\certLocation\VHDtoImage.json" - userStorageAccountName"$storageaccount" -dnsNameForPublicIP"$vmName" -subscriptionId"$mysubid" -location"$location" - vmName"$vmName" -vaultName"$kvname" -vaultResourceGroup"$rgName" -certificateUrl$objAzureKeyVaultSecret.Id -vhdUrl"$vhdUrl" - vmSize"Standard\_A2" -publicIPAddressName"myPublicIP1" -virtualNetworkName"myVNET1" -nicName"myNIC1" -adminUserName"isv" - adminPassword$pwd
```

## <a name="run-validations"></a>Eseguire convalide

Esistono due modi per eseguire le convalide nell'immagine distribuita.

### <a name="use-certification-test-tool-for-azure-certified"></a>Usare lo strumento di test della certificazione per Azure Certified

#### <a name="download-and-run-the-certification-test-tool"></a>Scaricare ed eseguire lo strumento di test di certificazione

Lo strumento di test di certificazione per Azure Certified viene eseguito in un computer Windows locale, ma esegue il test di una macchina virtuale Linux o Windows basata su Azure. Lo strumento verifica che l'immagine di macchina virtuale dell'utente possa essere usata con Microsoft Azure e che siano stati soddisfatti i requisiti e le linee guida per la preparazione del disco rigido virtuale.

1. Scaricare e installare lo [strumento di test di certificazione per Azure Certified](https://www.microsoft.com/download/details.aspx?id=44299).
2. Aprire lo strumento di certificazione, quindi selezionare **Avvia nuovo test**.
3. Nella schermata Test Information (Informazioni test) immettere un valore in **Test Name** (Nome test) per l'esecuzione del test.
4. Selezionare la piattaforma per la VM, ovvero **Windows Server** o **Linux**. La piattaforma scelta determina le opzioni rimanenti.
5. Se la macchina virtuale usa questo servizio di database, selezionare la casella di controllo **Test for Azure SQL Database** (Test per il database SQL di Azure).

#### <a name="connect-the-certification-tool-to-a-vm-image"></a>Connettere lo strumento di certificazione a un'immagine di macchina virtuale

1. Selezionare la modalità Autenticazione SSH: Autenticazione della password o Autenticazione del file di chiave.
2. Se si usa l'autenticazione basata su password, immettere i valori per il **nome DNS della VM**, il **nome utente**e la **password**. Facoltativamente, è possibile modificare il valore predefinito per la Porta SSH.

    :::image type="content" source="media/vm/azure-vm-cert-2.png" alt-text="Mostra la selezione delle informazioni di test della macchina virtuale.":::

3. Se si usa l'autenticazione basata su file di chiavi, immettere i valori nei campi VM DNS Name (Nome DNS macchina virtuale), User name (Nome utente) e Private key (Chiave privata). È anche possibile specificare un valore per Passphrase o modificare il numero predefinito per il campo Porta SSH.
4. Immettere il nome DNS completo della macchina virtuale, ad esempio MyVMName.Cloudapp.net.
5. Immettere **nome utente** e **password**.

    :::image type="content" source="media/vm/azure-vm-cert-4.png" alt-text="Mostra la selezione del nome utente e della password della macchina virtuale.":::

6. Selezionare **Avanti**.

#### <a name="run-a-certification-test"></a>Eseguire un test di certificazione

Dopo avere specificato i valori dei parametri per l'immagine di macchina virtuale nello strumento di certificazione, selezionare Test connessione per creare una connessione valida alla VM. Dopo che la connessone è verificata, selezionare **Next** (Avanti) per avviare il test. Al termine del test, i risultati vengono visualizzati in una tabella. La colonna Stato mostra (Pass/Fail/Warning) per ogni test. Se uno dei test non riesce, l'immagine non viene certificata. In questo caso, esaminare i requisiti e i messaggi di errore, apportare le modifiche indicate ed eseguire nuovamente il test.

Al termine del test automatizzato, fornire informazioni aggiuntive sull'immagine della macchina virtuale nelle due schede della schermata Questionario, Valutazione generale e Personalizzazione kernel, quindi selezionare Avanti.

L'ultima schermata consente di fornire altre informazioni, ad esempio le informazioni di accesso SSH per un'immagine di macchina virtuale Linux e una spiegazione per eventuali valutazioni non riuscite se si cercano eccezioni.

Selezionare infine Genera report per scaricare i risultati del test e i file di log per i casi di test eseguiti, oltre alle risposte al questionario. Salvare i risultati nello stesso contenitore dei VHD.

## <a name="how-to-use-powershell-to-consume-the-self-test-api"></a>Come usare PowerShell per utilizzare l'API self-test

### <a name="on-linux-os"></a>Nel sistema operativo Linux

Chiamare l'API in PowerShell:

1. Usare il comando Invoke-WebRequest per chiamare l'API.
2. Il metodo è POST e il tipo di contenuto è JSON, come illustrato nella schermata e nell'esempio di codice seguenti.
3. Specificare i parametri del corpo in formato JSON.

Nell'esempio seguente viene illustrata una chiamata a PowerShell per l'API:

```POWERSHELL
$accesstoken = “token”
$headers = New-Object “System.Collections.Generic.Dictionary[[String],[String]]”
$headers.Add(“Authorization”, “Bearer $accesstoken”)
$DNSName = “\&lt;\&lt;Machine DNS Name\&gt;\&gt;”
$UserName = “\&lt;\&lt;User ID\&gt;\&gt;”
$Password = “\&lt;\&lt;Password\&gt;\&gt;”
$OS = “Linux”
$PortNo = “22”
$CompanyName = “ABCD”
$AppID = “\&lt;\&lt;Application ID\&gt;\&gt;”
$TenantId = “\&lt;\&lt;Tenant ID\&gt;\&gt;”

$body =
@{
DNSName = $DNSName
UserName = $UserName
Password = $Password
OS = $OS
PortNo = $PortNo
CompanyName = $CompanyName
AppID = $AppID
TenantId = $TenantId
}| ConvertTo-Json

$body

$uri = “URL”

$res = (Invoke-WebRequest -Method “Post” -Uri $uri -Body $body -ContentType “application/json” -Headers $headers).Content
```

<br>Di seguito è riportato un esempio di chiamata dell'API in PowerShell:

[![Esempio di schermata per chiamare l'API in PowerShell.](media/vm/call-api-in-powershell.png)](media/vm/call-api-in-powershell.png#lightbox)

<br>Usando l'esempio precedente, è possibile recuperare il file JSON e analizzarlo in modo da ottenere i dettagli seguenti:

```PowerShell
$resVar=$res|ConvertFrom-Json

$actualresult =$resVar.Response |ConvertFrom-Json

Write-Host”OSName: $($actualresult.OSName)”Write-Host”OSVersion: $($actualresult.OSVersion)”Write-Host”Overall Test Result: $($actualresult.TestResult)”For ($i=0; $i -lt$actualresult.Tests.Length; $i++){ Write-Host”TestID: $($actualresult.Tests[$i].TestID)”Write-Host”TestCaseName: $($actualresult.Tests[$i].TestCaseName)”Write-Host”Description: $($actualresult.Tests[$i].Description)”Write-Host”Result: $($actualresult.Tests[$i].Result)”Write-Host”ActualValue: $($actualresult.Tests[$i].ActualValue)”}
```

<br>Questa schermata di esempio, che mostra `$res.Content` i dettagli dei risultati del test in formato JSON:

[![Esempio di schermata per chiamare l'API in PowerShell con i dettagli dei risultati del test.](media/vm/call-api-in-powershell-details.png)](media/vm/call-api-in-powershell-details.png#lightbox)

<br>Di seguito è riportato un esempio di risultati dei test JSON visualizzati in un visualizzatore JSON online, ad esempio l' [abbellimento del codice](https://codebeautify.org/jsonviewer) o il [Visualizzatore JSON](https://jsonformatter.org/json-viewer).

![Altri risultati dei test in un visualizzatore JSON online.](media/vm/test-results-json-viewer-1.png)

### <a name="on-windows-os"></a>Nel sistema operativo Windows

Chiamare l'API in PowerShell:

1. Usare il comando Invoke-WebRequest per chiamare l'API.
2. Il metodo è post e il tipo di contenuto è JSON, come illustrato nell'esempio di codice e nella schermata di esempio seguenti.
3. Creare i parametri del corpo in formato JSON.

Questo esempio di codice mostra una chiamata a PowerShell per l'API:

```PowerShell
$accesstoken = “Get token for your Client AAD App”$headers = New-Object”System.Collections.Generic.Dictionary[[String],[String]]”$headers.Add(“Authorization”, “Bearer $accesstoken”)$Body = @{ “DNSName” = “XXXX.westus.cloudapp.azure.com”“UserName” = “XXX”“Password” = “XXX@123456”“OS” = “Windows”“PortNo” = “5986”“CompanyName” = “ABCD” “AppID” = “XXXX-XXXX-XXXX” “TenantId” = “XXXX-XXXX-XXXX” } | ConvertTo-Json$res = Invoke-WebRequest -Method”Post” -Uri$uri -Body$Body -ContentType”application/json” –Headers $headers;$Content = $res | ConvertFrom-Json
```

In queste schermate di esempio viene illustrato l'esempio per chiamare l'API in PowerShell:

**Con la chiave SSH**:

 :::image type="content" source="media/vm/call-api-with-ssh-key.png" alt-text="Chiamata dell'API in PowerShell con una chiave SSH.":::

**Con password**:

 :::image type="content" source="media/vm/call-api-with-password.png" alt-text="Chiamata dell'API in PowerShell con una password.":::

<br>Usando l'esempio precedente, è possibile recuperare il file JSON e analizzarlo in modo da ottenere i dettagli seguenti:

```PowerShell
$resVar=$res|ConvertFrom-Json

$actualresult =$resVar.Response |ConvertFrom-Json

Write-Host”OSName: $($actualresult.OSName)”Write-Host”OSVersion: $($actualresult.OSVersion)”Write-Host”Overall Test Result: $($actualresult.TestResult)”For ($i=0; $i -lt$actualresult.Tests.Length; $i++){ Write-Host”TestID: $($actualresult.Tests[$i].TestID)”Write-Host”TestCaseName: $($actualresult.Tests[$i].TestCaseName)”Write-Host”Description: $($actualresult.Tests[$i].Description)”Write-Host”Result: $($actualresult.Tests[$i].Result)”Write-Host”ActualValue: $($actualresult.Tests[$i].ActualValue)”}
```

<br>Questa schermata, che mostra `$res.Content` , Mostra i dettagli dei risultati del test in formato JSON:

 :::image type="content" source="media/vm/test-results-json-format.png" alt-text="Dettagli dei risultati del test in formato JSON.":::

<br>Di seguito è riportato un esempio di risultati dei test visualizzati in un visualizzatore JSON online, ad esempio l' [abbellimento del codice](https://codebeautify.org/jsonviewer) o il [Visualizzatore JSON](https://jsonformatter.org/json-viewer).

![Risultati dei test in un visualizzatore JSON online.](media/vm/test-results-json-viewer-2.png)

## <a name="how-to-use-curl-to-consume-the-self-test-api-on-linux-os"></a>Come usare CURL per usare l'API self-test in un sistema operativo Linux

Chiamare l'API in CURL:

1. Usare il comando curl per chiamare l'API.
2. Il metodo è POST e il tipo di contenuto è JSON, come illustrato nel frammento di codice seguente.

```JSON
CURL POST -H “Content-Type:application/json”

-H “Authorization: Bearer XXXXXX-Token-XXXXXXXX”

[https://isvapp.azure-api.net/selftest-vm](https://isvapp.azure-api.net/selftest-vm)

-d ‘{ “DNSName”:”XXXX.westus.cloudapp.azure.com”, “UserName”:”XXX”, “Password”:”XXXX@123456”, “OS”:”Linux”, “PortNo”:”22”, “CompanyName”:”ABCD”, “AppId”:”XXXX-XXXX-XXXX”, “TenantId “XXXX-XXXX-XXXX”}’
```

<br>Di seguito è riportato un esempio di uso di CURL per chiamare l'API:

![Esempio di uso di CURL per chiamare l'API.](media/vm/use-curl-call-api.png)

<br>Ecco i risultati JSON della chiamata CURL:

![Risultati JSON dalla chiamata CURL.](media/vm/test-results-json-viewer-3.png)

## <a name="next-step"></a>Passaggio successivo

- Leggere [problemi e correzioni comuni dell'URI SAS](common-sas-uri-issues.md).
