---
title: Interrompere l'uso dell'estensione di VM identità gestita e iniziare a usare l'endpoint di servizio metadati dell'istanza Azure
description: Eseguire un'istruzione per istruzioni dettagliate per interrompere l'uso dell'estensione VM e iniziare a usare il servizio metadati di Azure istanza (IMDS) per l'autenticazione.
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
ms.openlocfilehash: 5b3c6c99b05320ee53c3ff49f5c299650c32e939
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60440825"
---
# <a name="how-to-stop-using-the-virtual-machine-managed-identities-extension-and-start-using-the-azure-instance-metadata-service"></a>Come interrompere l'uso della macchina virtuale gestiti estensione delle identità e iniziare a usare servizio metadati dell'istanza di Azure

## <a name="virtual-machine-extension-for-managed-identities"></a>Estensione macchina virtuale per identità gestita

L'estensione macchina virtuale per identità gestita viene usata per richiedere token per un'identità gestita all'interno della macchina virtuale. Il flusso di lavoro prevede i passaggi seguenti:

1. Prima di tutto il carico di lavoro all'interno della risorsa chiama l'endpoint locale `http://localhost/oauth2/token` per richiedere un token di accesso.
2. L'estensione macchina virtuale utilizza quindi le credenziali per l'identità gestita, per richiedere un token di accesso da Azure AD.. 
3. Il token di accesso viene restituito al chiamante e può essere utilizzato per l'autenticazione ai servizi che supportano l'autenticazione di Azure AD, ad esempio Azure Key Vault o archiviazione di Azure.

A causa di alcune limitazioni descritte nella sezione successiva, l'estensione di VM identità gestita è stata deprecata a favore dell'uso dell'endpoint equivalente in servizio metadati di Azure istanza (IMDS)

### <a name="provision-the-extension"></a>Effettuare il provisioning dell'estensione 

Quando si configura una macchina virtuale o la scalabilità di macchine virtuali in modo che un'identità gestita, potrebbe facoltativo scegliere, facoltativamente, è possibile scegliere di effettuare il provisioning delle identità gestite per estensione di macchina virtuale di risorse di Azure usando il `-Type` parametro la [ Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) cmdlet. È possibile passare `ManagedIdentityExtensionForWindows` oppure `ManagedIdentityExtensionForLinux`, a seconda del tipo di macchina virtuale e denominarla usando il `-Name` parametro. Il parametro `-Settings` specifica la porta usata dall'endpoint del token OAuth per l'acquisizione del token:

```powershell
   $settings = @{ "port" = 50342 }
   Set-AzVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
```

È anche possibile usare il modello di distribuzione Azure Resource Manager per effettuare il provisioning l'estensione macchina virtuale, aggiungendo il codice JSON seguente per il `resources` sezione il modello (usare `ManagedIdentityExtensionForLinux` per gli elementi nome e il tipo per la versione di Linux).

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
    
    
Se si lavora con set di scalabilità di macchine virtuali, si può effettuare il provisioning di identità gestite per set di scalabilità di macchine virtuali di Azure alle risorse di estensione usando il [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) cmdlet. È possibile passare `ManagedIdentityExtensionForWindows` oppure `ManagedIdentityExtensionForLinux`, a seconda del tipo di scalabilità di macchine virtuali impostato e assegnare un nome mediante il `-Name` parametro. Il parametro `-Settings` specifica la porta usata dall'endpoint del token OAuth per l'acquisizione del token:

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzVmss
   Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```
Effettuare il provisioning di scalabilità di macchine virtuali impostare l'estensione con il modello di distribuzione Azure Resource Manager, aggiungere il codice JSON seguente per il `extensionpProfile` sezione del modello (usare `ManagedIdentityExtensionForLinux` per gli elementi nome e il tipo per la versione di Linux).

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

Il provisioning dell'estensione macchina virtuale potrebbe non riuscire a causa di errori di ricerca DNS. In questo caso, riavviare la macchina virtuale e ripetere l'operazione. 

### <a name="remove-the-extension"></a>Rimuovere l'estensione 
Per rimuovere l'estensione, usare `-n ManagedIdentityExtensionForWindows` oppure `-n ManagedIdentityExtensionForLinux` switch (a seconda del tipo di macchina virtuale) con [Elimina estensione macchina virtuale di az](https://docs.microsoft.com/cli/azure/vm/), o [delete di estensione az vmss](https://docs.microsoft.com/cli/azure/vmss) per scalabilità di macchine virtuali Imposta tramite la CLI di Azure, o `Remove-AzVMExtension` per Powershell:

```azurecli-interactive
az vm identity --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
```

```azurecli-interactive
az vmss extension delete -n ManagedIdentityExtensionForWindows -g myResourceGroup -vmss-name myVMSS
```

```powershell
Remove-AzVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
```

### <a name="acquire-a-token-using-the-virtual-machine-extension"></a>Acquisire un token usando l'estensione macchina virtuale

Di seguito è riportata una richiesta di esempio usando l'identità gestite per le risorse Endpoint dell'estensione macchina virtuale di Azure:

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


### <a name="troubleshoot-the-virtual-machine-extension"></a>Problemi relativi all'estensione di macchina virtuale 

#### <a name="restart-the-virtual-machine-extension-after-a-failure"></a>Riavviare l'estensione macchina virtuale dopo un errore

In Windows e alcune versioni di Linux, se si arresta l'estensione, è possibile usare il cmdlet seguente per riavviarla manualmente:

```powershell
Set-AzVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Dove: 
- Nome dell'estensione e il tipo per Windows è: `ManagedIdentityExtensionForWindows`
- Nome dell'estensione e il tipo per Linux è: `ManagedIdentityExtensionForLinux`

#### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>Lo "Script di automazione" restituisce un errore se si tenta di esportare lo schema per l'estensione relativa alle identità gestite per le risorse di Azure

Quando le identità gestite per le risorse di Azure è abilitata in una macchina virtuale, viene visualizzato l'errore seguente quando si prova a usare la funzionalità "Script di automazione" per la macchina virtuale o il gruppo di risorse:

![Errore di esportazione dello script di automazione relativo alle identità gestite per le risorse di Azure](./media/howto-migrate-vm-extension/automation-script-export-error.png)

Le identità gestite per l'estensione macchina virtuale risorse di Azure non supporta attualmente la possibilità di esportare lo schema in un modello di gruppo di risorse. Di conseguenza, il modello generato non mostra i parametri di configurazione per abilitare le identità gestite per le risorse di Azure sulla risorsa. Queste sezioni possono essere aggiunti manualmente seguendo gli esempi nella [Configura gestiti per le risorse di Azure nella macchina virtuale di Azure usando un modello di identità](qs-configure-template-windows-vm.md).

Quando la funzionalità di esportazione dello schema sarà disponibile per le identità gestite per l'estensione di macchina virtuale risorse di Azure (pianificato per la deprecazione in gennaio 2019), verrà elencata [esportazione di gruppi di risorse contenenti estensioni macchina virtuale ](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

## <a name="limitations-of-the-virtual-machine-extension"></a>Limitazioni dell'estensione macchina virtuale 

Esistono alcune limitazioni principali per l'uso dell'estensione macchina virtuale. 

 * La limitazione più grave consiste nel fatto che le credenziali utilizzate per richiedere i token vengono archiviate nella macchina virtuale. Violazioni correttamente la macchina virtuale da un utente malintenzionato può sottrarre le credenziali. 
 * Inoltre, l'estensione macchina virtuale non è ancora supportato da varie distribuzioni Linux, con un notevole costo per modificare, compilare e testare l'estensione in ognuna di queste distribuzioni di sviluppo. Attualmente sono supportate solo le distribuzioni di Linux seguenti: 
    * CoreOS Stable
    * CentOS 7.1 
    * Red Hat 7.2 
    * Ubuntu 15.04 
    * Ubuntu 16.04
 * È un impatto sulle prestazioni per la distribuzione di macchine virtuali con identità gestite, come l'estensione macchina virtuale deve anche eseguire il provisioning. 
 * Infine, l'estensione macchina virtuale può solo supportare 32 assegnata dall'utente gestito identità usate per ogni macchina virtuale. 

## <a name="azure-instance-metadata-service"></a>Servizio metadati dell'istanza di Azure

Il [servizio metadati dell'istanza di Azure (IMDS)](/azure/virtual-machines/windows/instance-metadata-service) è un endpoint REST che fornisce informazioni sull'esecuzione di istanze di macchina virtuale che possono essere utilizzate per gestire e configurare le macchine virtuali. L'endpoint è disponibile presso un indirizzo IP non instradabile noto (`169.254.169.254`) che è accessibile solo dall'interno della macchina virtuale.

Esistono diversi vantaggi rispetto all'utilizzo di servizio metadati dell'istanza di Azure per richiedere token. 

1. Il servizio è esterno alla macchina virtuale, pertanto le credenziali utilizzate da identità gestite non sono più presenti nella macchina virtuale. Al contrario, sono ospitati e protetti nel computer host di macchina virtuale di Azure.   
2. Tutti i sistemi operativi Windows e Linux supportati nell'ambiente IaaS di Azure può usare le identità gestite.
3. La distribuzione è più veloce e semplice, poiché l'estensione macchina virtuale non deve più eseguire il provisioning.
4. Con il servizio metadati dell'istanza dell'endpoint, fino a 1000 identità gestita assegnata dall'utente possono essere assegnati a una singola macchina virtuale.
5. Si verifica alcuna variazione significativa per le richieste usando IMDS invece di quelle che usano l'estensione macchina virtuale, pertanto è piuttosto semplice alla porta tramite distribuzioni esistenti che usano attualmente l'estensione macchina virtuale.

Per questi motivi, il servizio Azure IMDS è il modo site di richiesta di token, dopo che l'estensione macchina virtuale è stato deprecato. 


## <a name="next-steps"></a>Fasi successive

* [Come usare le identità gestito per le risorse di Azure in una macchina virtuale di Azure per acquisire un token di accesso](how-to-use-vm-token.md)
* [Servizio metadati dell'istanza di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)
