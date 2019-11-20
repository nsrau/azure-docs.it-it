---
title: Interrompi l'uso dell'estensione della macchina virtuale identità gestita-Azure AD
description: Istruzioni dettagliate per arrestare l'uso dell'estensione della macchina virtuale e iniziare a usare il servizio metadati dell'istanza di Azure (IMDS) per l'autenticazione.
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
ms.openlocfilehash: 3440713c287967655678e1cde2c000a6ed28b900
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74183944"
---
# <a name="how-to-stop-using-the-virtual-machine-managed-identities-extension-and-start-using-the-azure-instance-metadata-service"></a>Come interrompere l'uso dell'estensione identità gestite della macchina virtuale e iniziare a usare il servizio metadati dell'istanza di Azure

## <a name="virtual-machine-extension-for-managed-identities"></a>Estensione macchina virtuale per identità gestite

L'estensione della macchina virtuale per le identità gestite viene usata per richiedere token per un'identità gestita all'interno della macchina virtuale. Il flusso di lavoro è costituito dai passaggi seguenti:

1. In primo luogo, il carico di lavoro all'interno della risorsa chiama l'endpoint locale `http://localhost/oauth2/token` per richiedere un token di accesso.
2. L'estensione della macchina virtuale usa quindi le credenziali per l'identità gestita per richiedere un token di accesso da Azure AD. 
3. Il token di accesso viene restituito al chiamante e può essere usato per eseguire l'autenticazione ai servizi che supportano l'autenticazione Azure AD, ad esempio Azure Key Vault o archiviazione di Azure.

A causa di diverse limitazioni descritte nella sezione successiva, l'estensione della macchina virtuale di identità gestita è stata deprecata a favore dell'utilizzo dell'endpoint equivalente nel servizio metadati dell'istanza di Azure (IMDS)

### <a name="provision-the-extension"></a>Eseguire il provisioning dell'estensione 

Quando si configura una macchina virtuale o un set di scalabilità di macchine virtuali in modo che abbia un'identità gestita, è possibile scegliere di eseguire il provisioning delle identità gestite per le risorse di Azure estensione VM usando il parametro `-Type` del cmdlet [set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) . È possibile passare `ManagedIdentityExtensionForWindows` o `ManagedIdentityExtensionForLinux`, a seconda del tipo di macchina virtuale, e denominarlo utilizzando il parametro `-Name`. Il parametro `-Settings` specifica la porta usata dall'endpoint del token OAuth per l'acquisizione del token:

```powershell
   $settings = @{ "port" = 50342 }
   Set-AzVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
```

È anche possibile usare il modello di distribuzione Azure Resource Manager per eseguire il provisioning dell'estensione della macchina virtuale aggiungendo il codice JSON seguente alla sezione `resources` al modello (usare `ManagedIdentityExtensionForLinux` per gli elementi Name e Type per la versione di Linux).

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
    
    
Se si usano i set di scalabilità di macchine virtuali, è anche possibile eseguire il provisioning delle identità gestite per le risorse di Azure estensione del set di scalabilità di macchine virtuali usando il cmdlet [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) . È possibile passare `ManagedIdentityExtensionForWindows` o `ManagedIdentityExtensionForLinux`, a seconda del tipo di set di scalabilità di macchine virtuali e denominarlo utilizzando il parametro `-Name`. Il parametro `-Settings` specifica la porta usata dall'endpoint del token OAuth per l'acquisizione del token:

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzVmss
   Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```
Per eseguire il provisioning dell'estensione del set di scalabilità di macchine virtuali con il modello di distribuzione Azure Resource Manager, aggiungere il codice JSON seguente alla sezione `extensionpProfile` al modello (utilizzare `ManagedIdentityExtensionForLinux` per gli elementi Name e Type per la versione Linux).

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

Il provisioning dell'estensione della macchina virtuale potrebbe non riuscire a causa di errori di ricerca DNS. In tal caso, riavviare la macchina virtuale e riprovare. 

### <a name="remove-the-extension"></a>Rimuovere l'estensione 
Per rimuovere l'estensione, usare l'opzione `-n ManagedIdentityExtensionForWindows` o `-n ManagedIdentityExtensionForLinux` (a seconda del tipo di macchina virtuale) con [AZ VM Extension Delete](https://docs.microsoft.com/cli/azure/vm/)oppure [AZ vmss Extension Delete](https://docs.microsoft.com/cli/azure/vmss) per i set di scalabilità di macchine virtuali usando l'interfaccia della riga di comando di Azure o `Remove-AzVMExtension` per PowerShell:

```azurecli-interactive
az vm identity --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
```

```azurecli-interactive
az vmss extension delete -n ManagedIdentityExtensionForWindows -g myResourceGroup -vmss-name myVMSS
```

```powershell
Remove-AzVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
```

### <a name="acquire-a-token-using-the-virtual-machine-extension"></a>Acquisire un token usando l'estensione della macchina virtuale

Di seguito è riportata una richiesta di esempio che usa l'endpoint dell'estensione della macchina virtuale identità gestite per le risorse di Azure:

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Elemento | DESCRIZIONE |
| ------- | ----------- |
| `GET` | Verbo HTTP, che indica che si vuole recuperare i dati dall'endpoint. In questo caso, un token di accesso OAuth. | 
| `http://localhost:50342/oauth2/token` | L'endpoint delle identità gestite per risorse di Azure, dove 50342 è la porta predefinita ed è configurabile. |
| `resource` | Parametro della stringa di query, che indica l'URI ID app della risorsa di destinazione. Viene visualizzato anche nell'attestazione `aud` (audience, destinatari) del token emesso. In questo esempio viene richiesto un token per accedere ad Azure Resource Manager, con l'URI di ID app https://management.azure.com/. |
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

| Elemento | DESCRIZIONE |
| ------- | ----------- |
| `access_token` | Token di accesso richiesto. Quando si chiama un'API REST protetta, il token è incorporato nel campo di intestazione della richiesta `Authorization` come token di connessione, in modo da consentire all'API di autenticare il chiamante. | 
| `refresh_token` | Non usata dalle identità gestite per risorse di Azure. |
| `expires_in` | Numero di secondi per cui il token di accesso continua a essere valido, prima della scadenza, dal momento del rilascio. L'ora del rilascio è indicata nell'attestazione `iat` del token. |
| `expires_on` | Intervallo di tempo in cui il token di accesso scade. La data è rappresentata come numero di secondi da "1970-01-01T0:0:0Z UTC" (corrisponde all'attestazione `exp` del token). |
| `not_before` | Intervallo di tempo in cui il token di accesso è valido e può essere accettato. La data è rappresentata come numero di secondi da "1970-01-01T0:0:0Z UTC" (corrisponde all'attestazione `nbf` del token). |
| `resource` | Risorsa per cui è stato richiesto il token di accesso, che corrisponde al parametro della stringa di query `resource` della richiesta. |
| `token_type` | Tipo di token, ovvero un token di accesso di connessione, che indica che la risorsa può concedere l'accesso al titolare del token. |


### <a name="troubleshoot-the-virtual-machine-extension"></a>Risolvere i problemi relativi all'estensione della macchina virtuale 

#### <a name="restart-the-virtual-machine-extension-after-a-failure"></a>Riavvia l'estensione della macchina virtuale dopo un errore

In Windows e alcune versioni di Linux, se si arresta l'estensione, è possibile usare il cmdlet seguente per riavviarla manualmente:

```powershell
Set-AzVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Dove: 
- Il nome e il tipo dell'estensione per Windows sono: `ManagedIdentityExtensionForWindows`
- Il nome e il tipo dell'estensione per Linux sono: `ManagedIdentityExtensionForLinux`

#### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>Lo "Script di automazione" restituisce un errore se si tenta di esportare lo schema per l'estensione relativa alle identità gestite per le risorse di Azure

Quando le identità gestite per le risorse di Azure sono abilitate in una macchina virtuale, viene visualizzato l'errore seguente quando si tenta di usare la funzionalità "script di automazione" per la macchina virtuale o il relativo gruppo di risorse:

![Errore di esportazione dello script di automazione relativo alle identità gestite per le risorse di Azure](./media/howto-migrate-vm-extension/automation-script-export-error.png)

L'estensione della macchina virtuale identità gestite per le risorse di Azure non supporta attualmente la possibilità di esportare lo schema in un modello di gruppo di risorse. Di conseguenza, il modello generato non include i parametri di configurazione per abilitare le identità gestite per le risorse di Azure sulla risorsa. Queste sezioni possono essere aggiunte manualmente seguendo gli esempi in [configurare le identità gestite per le risorse di Azure in una macchina virtuale di Azure usando un modello](qs-configure-template-windows-vm.md).

Quando la funzionalità di esportazione dello schema diventa disponibile per l'estensione della macchina virtuale identità gestite per le risorse di Azure (pianificata per la deprecazione nel 2019 gennaio), verrà elencata in [esportazione di gruppi di risorse contenenti estensioni VM](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

## <a name="limitations-of-the-virtual-machine-extension"></a>Limitazioni dell'estensione della macchina virtuale 

L'uso dell'estensione della macchina virtuale presenta diverse limitazioni principali. 

 * Il limite più grave è il fatto che le credenziali utilizzate per richiedere i token sono archiviate nella macchina virtuale. Un utente malintenzionato che viola correttamente la macchina virtuale può sottrarre le credenziali. 
 * Inoltre, l'estensione della macchina virtuale non è ancora supportata da diverse distribuzioni Linux, con un notevole costo di sviluppo per modificare, compilare e testare l'estensione in ognuna di queste distribuzioni. Attualmente sono supportate solo le distribuzioni Linux seguenti: 
    * CoreOS Stable
    * CentOS 7.1 
    * Red Hat 7.2 
    * Ubuntu 15.04 
    * Ubuntu 16.04
 * La distribuzione di macchine virtuali con identità gestite comporta un effetto sulle prestazioni, in quanto è necessario effettuare il provisioning anche dell'estensione della macchina virtuale. 
 * Infine, l'estensione della macchina virtuale può supportare solo la presenza di 32 identità gestite assegnate dall'utente per ogni macchina virtuale. 

## <a name="azure-instance-metadata-service"></a>Servizio metadati dell'istanza di Azure

Il [servizio metadati dell'istanza di Azure (IMDS)](/azure/virtual-machines/windows/instance-metadata-service) è un endpoint REST che fornisce informazioni sull'esecuzione di istanze di macchine virtuali che possono essere usate per gestire e configurare le macchine virtuali. L'endpoint è disponibile a un indirizzo IP non instradabile noto (`169.254.169.254`) a cui è possibile accedere solo dall'interno della macchina virtuale.

L'uso di Azure IMDS per richiedere i token presenta diversi vantaggi. 

1. Il servizio è esterno alla macchina virtuale, pertanto le credenziali usate dalle identità gestite non sono più presenti nella macchina virtuale. Sono invece ospitati e protetti nel computer host della macchina virtuale di Azure.   
2. Tutti i sistemi operativi Windows e Linux supportati in Azure IaaS possono usare identità gestite.
3. La distribuzione è più rapida e semplice, perché non è più necessario effettuare il provisioning dell'estensione della macchina virtuale.
4. Con l'endpoint IMDS, è possibile assegnare fino a 1000 identità gestite assegnate dall'utente a una singola macchina virtuale.
5. Non sono state apportate modifiche significative alle richieste usando IMDS anziché quelle che usano l'estensione della macchina virtuale, pertanto è piuttosto semplice eseguire il Port over delle distribuzioni esistenti che attualmente usano l'estensione della macchina virtuale.

Per questi motivi, il servizio Azure IMDS sarà il metodo di richiesta di token, dopo che l'estensione della macchina virtuale è stata deprecata. 


## <a name="next-steps"></a>Passaggi successivi

* [Come usare le identità gestite per le risorse di Azure in una macchina virtuale di Azure per acquisire un token di accesso](how-to-use-vm-token.md)
* [Servizio metadati dell'istanza di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)
