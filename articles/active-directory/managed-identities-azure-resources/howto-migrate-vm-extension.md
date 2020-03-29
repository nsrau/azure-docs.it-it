---
title: Interrompere l'uso dell'estensione della macchina virtuale dell'identità gestita - Azure ADStop using managed identity VM extension - Azure AD
description: Istruzioni dettagliate per interrompere l'uso dell'estensione della macchina virtuale e iniziare a usare il servizio metadati dell'istanza di Azure (IMDS) per l'autenticazione.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/25/2018
ms.author: markvi
ms.openlocfilehash: 01b8e1dbc290bed86ccfc3c7016e8bd9168e427a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049074"
---
# <a name="how-to-stop-using-the-virtual-machine-managed-identities-extension-and-start-using-the-azure-instance-metadata-service"></a>Come interrompere l'uso dell'estensione delle identità gestite dalla macchina virtuale e iniziare a usare il servizio metadati dell'istanza di AzureHow to stop using the virtual machine managed identities extension and start using the Azure Instance Metadata Service

## <a name="virtual-machine-extension-for-managed-identities"></a>Estensione della macchina virtuale per le identità gestiteVirtual machine extension for managed identities

L'estensione della macchina virtuale per le identità gestite viene usata per richiedere token per un'identità gestita all'interno della macchina virtuale. Il flusso di lavoro è costituito dai passaggi seguenti:The workflow consists of the following steps:

1. In primo luogo, il carico `http://localhost/oauth2/token` di lavoro all'interno della risorsa chiama l'endpoint locale per richiedere un token di accesso.
2. L'estensione della macchina virtuale usa quindi le credenziali per l'identità gestita per richiedere un token di accesso da Azure AD.The virtual machine extension then uses the credentials for the managed identity, to request an access token from Azure AD.The virtual machine extension then uses the credentials for the managed identity, to request an access token from Azure AD.The 
3. The access token is returned to the caller, and can be used to authenticate to services that support Azure AD authentication, like Azure Key Vault or Azure Storage.

A causa di diverse limitazioni descritte nella sezione successiva, l'estensione della macchina virtuale dell'identità gestita è stata deprecata a favore dell'uso dell'endpoint equivalente nel servizio metadati dell'istanza di Azure (IMDS)A due limitations outlined in the next section, the managed identity VM extension has been deprecated in favour of using the equivalent endpoint in the Azure Instance Metadata Service (IMDS)

### <a name="provision-the-extension"></a>Effettuare il provisioning dell'estensione 

Quando si configura una macchina virtuale o un set di scalabilità di macchine virtuali per avere un'identità gestita, è possibile scegliere di eseguire il provisioning delle identità gestite per l'estensione della macchina virtuale delle risorse di Azure usando il parametro nel cmdlet `-Type` [Set-AzVMExtension.When](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) you configure a virtual machine or virtual machine scale set to have a managed identity, you may optionally choose to provision the managed identities for Azure resources VM extension using the parameter on the Set-AzVMExtension cmdlet. È possibile `ManagedIdentityExtensionForWindows` passare `ManagedIdentityExtensionForLinux`uno o , a seconda del tipo `-Name` di macchina virtuale e denominarla utilizzando il parametro . Il parametro `-Settings` specifica la porta usata dall'endpoint del token OAuth per l'acquisizione del token:

```powershell
   $settings = @{ "port" = 50342 }
   Set-AzVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
```

È anche possibile usare il modello di distribuzione di Azure Resource Manager `resources` per eseguire il `ManagedIdentityExtensionForLinux` provisioning dell'estensione della macchina virtuale, aggiungendo il codice JSON seguente alla sezione al modello (usare per gli elementi name e type per la versione Linux).

    ```json
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
        "apiVersion": "2018-06-01",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "properties": {
            "publisher": "Microsoft.ManagedIdentity",
            "type": "ManagedIdentityExtensionForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "port": 50342
            }
        }
    }
    ```
    
    
Se si usa set di scalabilità di macchine virtuali, è anche possibile eseguire il provisioning delle identità gestite per l'estensione del set di scalabilità della macchina virtuale delle risorse di Azure usando il cmdlet [Add-AzVmssExtension.If](/powershell/module/az.compute/add-azvmssextension) you're working with virtual machine scale sets, you can also provision the managed identities for Azure resources virtual machine scale set extension using the Add-AzVmssExtension cmdlet. È possibile `ManagedIdentityExtensionForWindows` passare `ManagedIdentityExtensionForLinux`uno o , a seconda del tipo di `-Name` set di scalabilità della macchina virtuale e denominarlo utilizzando il parametro . Il parametro `-Settings` specifica la porta usata dall'endpoint del token OAuth per l'acquisizione del token:

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzVmss
   Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```
Per eseguire il provisioning dell'estensione del set di scalabilità della `extensionpProfile` macchina virtuale con `ManagedIdentityExtensionForLinux` il modello di distribuzione di Azure Resource Manager, aggiungere il codice JSON seguente alla sezione al modello (usare per gli elementi name e type per la versione Linux).

    ```json
    "extensionProfile": {
        "extensions": [
            {
                "name": "ManagedIdentityWindowsExtension",
                "properties": {
                    "publisher": "Microsoft.ManagedIdentity",
                    "type": "ManagedIdentityExtensionForWindows",
                    "typeHandlerVersion": "1.0",
                    "autoUpgradeMinorVersion": true,
                    "settings": {
                        "port": 50342
                    },
                    "protectedSettings": {}
                }
            }
    ```

Il provisioning dell'estensione della macchina virtuale potrebbe non riuscire a causa di errori di ricerca DNS. In questo caso, riavviare la macchina virtuale e riprovare. 

### <a name="remove-the-extension"></a>Rimuovere l'estensione 
Per rimuovere l'estensione, usare `-n ManagedIdentityExtensionForWindows` o `-n ManagedIdentityExtensionForLinux` passare (a seconda del tipo di macchina virtuale) con az vm extension delete o az vmss extension delete per i set di scalabilità di macchine virtuali usando l'interfaccia della riga di comando di Azure o per Powershell:To remove the extension, use or switch (depending on the type of virtual machine) with az vm extension [delete](https://docs.microsoft.com/cli/azure/vm/), or az [vmss extension delete](https://docs.microsoft.com/cli/azure/vmss) for virtual machine scale sets using Azure CLI, or `Remove-AzVMExtension` for Powershell:

```azurecli-interactive
az vm identity --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
```

```azurecli-interactive
az vmss extension delete -n ManagedIdentityExtensionForWindows -g myResourceGroup -vmss-name myVMSS
```

```powershell
Remove-AzVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
```

### <a name="acquire-a-token-using-the-virtual-machine-extension"></a>Acquisire un token usando l'estensione della macchina virtualeAcquire a token using the virtual machine extension

The following is a sample request using the managed identities for Azure resources VM Extension Endpoint:

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Elemento | Descrizione |
| ------- | ----------- |
| `GET` | Verbo HTTP, che indica che si vuole recuperare i dati dall'endpoint. In questo caso, un token di accesso OAuth. | 
| `http://localhost:50342/oauth2/token` | L'endpoint delle identità gestite per risorse di Azure, dove 50342 è la porta predefinita ed è configurabile. |
| `resource` | Parametro della stringa di query, che indica l'URI ID app della risorsa di destinazione. Viene visualizzato anche nell'attestazione `aud` (audience, destinatari) del token emesso. In questo esempio viene richiesto un token per accedere ad Azure Resource Manager, con l'URI di ID app `https://management.azure.com/`. |
| `Metadata` | Un campo di intestazione della richiesta HTTP, richiesto dalle identità gestite per risorse di Azure come mitigazione degli attacchi SSRF (Server Side Request Forgery). Questo valore deve essere impostato su "true", usando tutte lettere minuscole.|
| `object_id` | (Facoltativo) Un parametro di stringa di query che indichi il valore object_id dell'identità gestita per cui si desidera il token. Obbligatorio, se la macchina virtuale dispone di più identità gestite assegnate dall'utente.|
| `client_id` | (Facoltativo) Un parametro di stringa di query che indichi il valore client_id dell'identità gestita per cui si desidera il token. Obbligatorio, se la macchina virtuale dispone di più identità gestite assegnate dall'utente.|


Risposta di esempio:

```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "refresh_token": "",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

| Elemento | Descrizione |
| ------- | ----------- |
| `access_token` | Token di accesso richiesto. Quando si chiama un'API REST protetta, il token è incorporato nel campo di intestazione della richiesta `Authorization` come token di connessione, in modo da consentire all'API di autenticare il chiamante. | 
| `refresh_token` | Non usata dalle identità gestite per risorse di Azure. |
| `expires_in` | Numero di secondi per cui il token di accesso continua a essere valido, prima della scadenza, dal momento del rilascio. L'ora del rilascio è indicata nell'attestazione `iat` del token. |
| `expires_on` | Intervallo di tempo in cui il token di accesso scade. La data è rappresentata come numero di secondi da "1970-01-01T0:0:0Z UTC" (corrisponde all'attestazione `exp` del token). |
| `not_before` | Intervallo di tempo in cui il token di accesso è valido e può essere accettato. La data è rappresentata come numero di secondi da "1970-01-01T0:0:0Z UTC" (corrisponde all'attestazione `nbf` del token). |
| `resource` | Risorsa per cui è stato richiesto il token di accesso, che corrisponde al parametro della stringa di query `resource` della richiesta. |
| `token_type` | Tipo di token, ovvero un token di accesso di connessione, che indica che la risorsa può concedere l'accesso al titolare del token. |


### <a name="troubleshoot-the-virtual-machine-extension"></a>Risolvere i problemi relativi all'estensione della macchina virtualeTroubleshoot the virtual machine extension 

#### <a name="restart-the-virtual-machine-extension-after-a-failure"></a>Riavviare l'estensione della macchina virtuale dopo un erroreRestart the virtual machine extension after a failure

In Windows e alcune versioni di Linux, se si arresta l'estensione, è possibile usare il cmdlet seguente per riavviarla manualmente:

```powershell
Set-AzVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Dove: 
- Il nome e il tipo di estensione per Windows sono:`ManagedIdentityExtensionForWindows`
- Il nome e il tipo di estensione per Linux sono:`ManagedIdentityExtensionForLinux`

#### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>Lo "Script di automazione" restituisce un errore se si tenta di esportare lo schema per l'estensione relativa alle identità gestite per le risorse di Azure

Quando le identità gestite per le risorse di Azure sono abilitate in una macchina virtuale, viene visualizzato l'errore seguente quando si tenta di usare la funzionalità "Script di automazione" per la macchina virtuale o il relativo gruppo di risorse:When managed identities for Azure resources is enabled on a virtual machine, the following error is shown when attempting to use the "Automation script" feature for the virtual machine, or its resource group:

![Errore di esportazione dello script di automazione relativo alle identità gestite per le risorse di Azure](./media/howto-migrate-vm-extension/automation-script-export-error.png)

L'estensione della macchina virtuale delle identità gestite per le risorse di Azure non supporta attualmente la possibilità di esportare lo schema in un modello di gruppo di risorse. Di conseguenza, il modello generato non mostra i parametri di configurazione per abilitare le identità gestite per le risorse di Azure sulla risorsa. Queste sezioni possono essere aggiunte manualmente seguendo gli esempi in [Configurare le identità gestite per le risorse](qs-configure-template-windows-vm.md)di Azure in una macchina virtuale di Azure usando un modello.

Quando la funzionalità di esportazione dello schema diventa disponibile per le identità gestite per l'estensione della macchina virtuale delle risorse di Azure (pianificata per la deprecazione nel gennaio 2019), verrà elencata in Esportazione di gruppi di [risorse contenenti estensioni di macchine virtuali](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

## <a name="limitations-of-the-virtual-machine-extension"></a>Limitazioni dell'estensione della macchina virtuale 

L'utilizzo dell'estensione della macchina virtuale è dovuto a diverse limitazioni principali. 

 * La limitazione più grave è il fatto che le credenziali utilizzate per richiedere i token vengono archiviate nella macchina virtuale. Un utente malintenzionato che viola correttamente la macchina virtuale può esfiltrare le credenziali. 
 * Inoltre, l'estensione della macchina virtuale non è ancora supportata da diverse distribuzioni Linux, con un costo di sviluppo enorme per modificare, compilare e testare l'estensione su ciascuna di queste distribuzioni. Attualmente sono supportate solo le seguenti distribuzioni Linux: 
    * CoreOS Stable
    * CentOS 7.1 
    * Red Hat 7.2 
    * Ubuntu 15.04 
    * Ubuntu 16.04
 * La distribuzione di macchine virtuali con identità gestite ha un impatto sulle prestazioni, poiché è necessario eseguire il provisioning dell'estensione della macchina virtuale. 
 * Infine, l'estensione della macchina virtuale può supportare solo la presenza di 32 identità gestite assegnate dall'utente per macchina virtuale. 

## <a name="azure-instance-metadata-service"></a>Servizio metadati dell'istanza di Azure

Il [servizio metadati dell'istanza di Azure (IMDS)](/azure/virtual-machines/windows/instance-metadata-service) è un endpoint REST che fornisce informazioni sull'esecuzione di istanze di macchine virtuali che possono essere usate per gestire e configurare le macchine virtuali. L'endpoint è disponibile in un noto indirizzo`169.254.169.254`IP non instradabile ( ) a cui è possibile accedere solo dall'interno della macchina virtuale.

L'uso di Azure IMDS per richiedere i token presenta diversi vantaggi. 

1. Il servizio è esterno alla macchina virtuale, pertanto le credenziali utilizzate dalle identità gestite non sono più presenti nella macchina virtuale. Al contrario, sono ospitati e protetti nella macchina host della macchina virtuale di Azure.Instead, they are hosted and secured on the host machine of the Azure virtual machine.   
2. Tutti i sistemi operativi Windows e Linux supportati in Azure IaaS possono usare le identità gestite.
3. La distribuzione è più veloce e semplice, poiché non è più necessario eseguire il provisioning dell'estensione della macchina virtuale.
4. Con l'endpoint IMDS, è possibile assegnare fino a 1000 identità gestite assegnate dall'utente a una singola macchina virtuale.
5. Non vi è alcuna modifica significativa alle richieste che utilizzano IMDS rispetto a quelli che utilizzano l'estensione della macchina virtuale, pertanto è abbastanza semplice eseguire il porting su distribuzioni esistenti che attualmente utilizzano l'estensione della macchina virtuale.

Per questi motivi, il servizio IMDS di Azure sarà il modo de facto per richiedere i token, una volta deprecata l'estensione della macchina virtuale. 


## <a name="next-steps"></a>Passaggi successivi

* [Come usare le identità gestite per le risorse di Azure in una macchina virtuale di Azure per acquisire un token di accessoHow to use managed identities for Azure resources on an Azure virtual machine to acquire an access token](how-to-use-vm-token.md)
* [Servizio metadati istanza di AzureAzure Instance Metadata Service](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)
