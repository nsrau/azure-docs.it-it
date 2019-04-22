---
title: Creare un certificato di Azure Key Vault | Microsoft Docs
description: Questo articolo illustra come registrare una macchina virtuale da un disco rigido virtuale distribuito in Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/29/2018
ms.author: pbutlerm
ms.openlocfilehash: a25418f30225184424011527def468d0d3909563
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59045697"
---
# <a name="create-certificates-for-azure-key-vault"></a>Creare certificati per Azure Key Vault

Questo articolo illustra come effettuare il provisioning dei certificati autofirmati richiesti per stabilire una connettività di Gestione remota Windows (WinRM) a una macchina virtuale (VM) ospitata in Azure. Questo processo si articola in tre passaggi:

1.  Creare il certificato di protezione. 
2.  Creare l'insieme di credenziali delle chiavi per archiviare questo certificato. 
3.  Archiviare i certificati in questo insieme di credenziali delle chiavi. 

È possibile usare un gruppo di risorse di Azure nuovo o esistente per questa operazione.  Il primo approccio viene usato nella spiegazione seguente.



[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="create-the-certificate"></a>Creare il certificato

Modificare ed eseguire lo script di Azure PowerShell seguente per creare il file di certificato (con estensione pfx) in una cartella locale.  Sarà necessario sostituire i valori per i parametri seguenti:

|  **Parametro**        |   **Descrizione**                                                               |
|  -------------        |   ---------------                                                               |
| `$certroopath` | Cartella locale in cui salvare il file con estensione pfx  |
| `$location`    | Una delle posizioni geografiche standard di Azure  |
| `$vmName`      | Nome della macchina virtuale di Azure pianificata   |
| `$certname`    | Nome del certificato, che deve corrispondere al nome di dominio completo della macchina virtuale pianificata  |
| `$certpassword` | Password per i certificati, che devono corrispondere alla password usata per la macchina virtuale pianificata  |
|  |  |

```powershell
    # Certification creation script 
    
    # pfx certification stored path
    $certroopath = "C:\certLocation"
    
    #location of the resource group
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
> Mantenere la stessa sessione di console di PowerShell attiva durante questa procedura in modo che i valori dei vari parametri vengano mantenuti.

> [!WARNING]
> Se si salva questo script, si consiglia di archiviarlo solo in una posizione sicura perché contiene informazioni di sicurezza (password).


## <a name="create-the-key-vault"></a>Creare l'insieme di credenziali delle chiavi

Copiare il contenuto del [modello di distribuzione dell'insieme di credenziali delle chiavi](./cpp-key-vault-deploy-template.md) in un file nel computer locale. Nello script di esempio riportato di seguito questa risorsa è `C:\certLocation\keyvault.json`.  Modificare ed eseguire lo script di Azure PowerShell seguente per creare un'istanza di Azure Key Vault e il gruppo di risorse associato.  Sarà necessario sostituire i valori per i parametri seguenti:

|  **Parametro**        |   **Descrizione**                                                               |
|  -------------        |   ---------------                                                               |
| `$postfix`            | Stringa numerica arbitraria accodata agli identificatori della distribuzione                     |
| `$rgName`             | Nome del gruppo di risorse di Azure da creare                                        |
|  `$location`          | Una delle posizioni geografiche standard di Azure                                  |
| `$kvTemplateJson`     | Percorso del file (keyvault.json) contenente il modello di Resource Manager per l'insieme di credenziali delle chiavi |
| `$kvname`             | Nome del nuovo insieme di credenziali delle chiavi                                                       |
|  |  |

```powershell
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

## <a name="store-the-certificate"></a>Archiviare il certificato

È ora possibile archiviare i certificati contenuti nel file con estensione pfx nel nuovo insieme di credenziali delle chiavi eseguendo lo script seguente. 

```powershell
    #push certificate to key vault secret

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
            $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText –Force
            $objAzureKeyVaultSecret=Set-AzureKeyVaultSecret -VaultName $kvname -Name "ISVSecret$postfix" -SecretValue $secret
            echo $objAzureKeyVaultSecret.Id 
    
```


## <a name="next-steps"></a>Passaggi successivi

Nel passaggio successivo si [distribuirà una macchina virtuale dall'immagine di macchina virtuale degli utenti](./cpp-deploy-vm-user-image.md).
