---
title: "Domande frequenti sui set di scalabilità di macchine virtuali di Azure | Microsoft Docs"
description: "Risposte alle domande frequenti sui set di scalabilità di macchine virtuali."
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: negat
ms.custom: na
ms.openlocfilehash: 52be84b73e70a02c43ef71917dc272060d82b42d
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/20/2017
---
# <a name="azure-virtual-machine-scale-sets-faqs"></a>Domande frequenti sui set di scalabilità di macchine virtuali di Azure

Risposte alle domande frequenti sui set di scalabilità di macchine virtuali in Azure.

## <a name="autoscale"></a>Autoscale

### <a name="what-are-best-practices-for-azure-autoscale"></a>Quali sono le procedure consigliate per la scalabilità automatica di Azure?

Per informazioni sulle procedure consigliate per la scalabilità automatica, vedere [Procedure consigliate per la scalabilità automatica delle macchine virtuali](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-best-practices).

### <a name="where-do-i-find-metric-names-for-autoscaling-that-uses-host-based-metrics"></a>Dove è possibile trovare i nomi delle metriche per la scalabilità automatica che usa metriche basate su host?

Per informazioni sui nomi delle metriche per la scalabilità automatica che usa metriche basate su host, vedere [Metriche supportate con Monitoraggio di Azure](https://azure.microsoft.com/documentation/articles/monitoring-supported-metrics/).

### <a name="are-there-any-examples-of-autoscaling-based-on-an-azure-service-bus-topic-and-queue-length"></a>Sono disponibili eventuali esempi di scalabilità automatica basata sull'argomento o sulla lunghezza della coda del bus di servizio di Azure?

Sì. Per esempi di scalabilità automatica basata sull'argomento o sulla lunghezza della coda del bus di servizio di Azure, vedere [Metriche comuni per la scalabilità automatica di Monitoraggio di Azure](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/).

Per una coda del bus di servizio, usare il codice JSON seguente:

```json
"metricName": "MessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

Per una coda di archiviazione, usare il codice JSON seguente:

```json
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

Sostituire i valori dell'esempio con gli URI (Uniform Resource Identifier) della risorsa specifica.


### <a name="should-i-autoscale-by-using-host-based-metrics-or-a-diagnostics-extension"></a>È consigliabile applicare la scalabilità automatica usando metriche basate su host oppure un'estensione di diagnostica?

È possibile creare un'impostazione di scalabilità automatica in una macchina virtuale per l'uso di metriche a livello di host o per l'uso di metriche basate sul sistema operativo guest.

Per un elenco delle metriche supportate, vedere [Metriche comuni per la scalabilità automatica di Monitoraggio di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-common-metrics). 

Per un esempio completo per i set di scalabilità di macchine virtuali, vedere [Configurazione di scalabilità automatica avanzata con modelli di Resource Manager per set di scalabilità di macchine virtuali](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-advanced-autoscale-virtual-machine-scale-sets). 

L'esempio usa la metrica relativa alla CPU a livello di host e una metrica di conteggio dei messaggi.



### <a name="how-do-i-set-alert-rules-on-a-virtual-machine-scale-set"></a>Come si impostano le regole di avviso in un set di scalabilità di macchine virtuali?

È possibile creare avvisi sulle metriche per i set di scalabilità di macchine virtuali tramite PowerShell o l'interfaccia della riga di comando di Azure. Per ulteriori informazioni, vedere [esempi di monitoraggio di Azure PowerShell](https://azure.microsoft.com/documentation/articles/insights-powershell-samples/#create-alert-rules) e [esempi CLI di Azure monitoraggio multipiattaforma](https://azure.microsoft.com/documentation/articles/insights-cli-samples/#work-with-alerts).

Il valore TargetResourceId del set di scalabilità di macchine virtuali ha un aspetto simile al seguente: 

/subscriptions/yoursubscriptionid/resourceGroups/yourresourcegroup/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmssname

È possibile scegliere qualsiasi contatore delle prestazioni delle macchine virtuali come metrica per cui impostare un avviso. Per altre informazioni, vedere [Metriche del sistema operativo guest per le VM Windows basate su Resource Manager](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#guest-os-metrics-resource-manager-based-windows-vms) e [Metriche del sistema operativo guest per le VM Linux](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#guest-os-metrics-linux-vms) nell'articolo [Metriche comuni per la scalabilità automatica di Monitoraggio di Azure](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/).

### <a name="how-do-i-set-up-autoscale-on-a-virtual-machine-scale-set-by-using-powershell"></a>Come si configura la scalabilità automatica in un set di scalabilità di macchine virtuali tramite PowerShell?

Per configurare la scalabilità automatica in un set di scalabilità di macchine virtuali tramite PowerShell, vedere il post di blog [How to add autoscale to an Azure virtual machine scale set](https://msftstack.wordpress.com/2017/03/05/how-to-add-autoscale-to-an-azure-vm-scale-set/) (Come aggiungere la scalabilità automatica in un set di scalabilità di macchine virtuali di Azure).




## <a name="certificates"></a>Certificati

### <a name="how-do-i-securely-ship-a-certificate-to-the-vm-how-do-i-provision-a-virtual-machine-scale-set-to-run-a-website-where-the-ssl-for-the-website-is-shipped-securely-from-a-certificate-configuration-the-common-certificate-rotation-operation-would-be-almost-the-same-as-a-configuration-update-operation-do-you-have-an-example-of-how-to-do-this"></a>Come si invia un certificato alla VM in modo sicuro? Come si effettua il provisioning di un set di scalabilità di macchine virtuali per eseguire un sito Web in cui il certificato SSL per il sito Web viene inviato in modo sicuro da una configurazione del certificato? L'operazione di rotazione del certificato comune è quasi uguale a un'operazione di aggiornamento della configurazione. È disponibile un esempio per questa operazione? 

Per inviare in modo sicuro un certificato alla macchina virtuale, è possibile installare un certificato cliente direttamente nell'archivio certificati Windows dall'insieme di credenziali delle chiavi del cliente.

Usare il codice JSON seguente:

```json
"secrets": [
    {
        "sourceVault": {
            "id": "/subscriptions/{subscriptionid}/resourceGroups/myrg1/providers/Microsoft.KeyVault/vaults/mykeyvault1"
        },
        "vaultCertificates": [
            {
                "certificateUrl": "https://mykeyvault1.vault.azure.net/secrets/{secretname}/{secret-version}",
                "certificateStore": "certificateStoreName"
            }
        ]
    }
]
```

Il codice supporta Windows e Linux.

Per altre informazioni, vedere [Create or update a virtual machine scale set](https://msdn.microsoft.com/library/mt589035.aspx) (Creare o aggiornare un set di scalabilità di macchine virtuali).


### <a name="example-of-self-signed-certificate"></a>Esempio di certificato autofirmato

1.  Creare un certificato autofirmato in un insieme di credenziali delle chiavi.

    Usare i comandi di PowerShell seguenti:

    ```powershell
    Import-Module "C:\Users\mikhegn\Downloads\Service-Fabric-master\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"

    Login-AzureRmAccount

    Invoke-AddCertToKeyVault -SubscriptionId <Your SubID> -ResourceGroupName KeyVault -Location westus -VaultName MikhegnVault -CertificateName VMSSCert -Password VmssCert -CreateSelfSignedCertificate -DnsName vmss.mikhegn.azure.com -OutputPath c:\users\mikhegn\desktop\
    ```

    Questo comando fornisce l'input per il modello di Azure Resource Manager.

    Per un esempio della creazione di un certificato autofirmato in un insieme di credenziali delle chiavi, vedere [Scenari di sicurezza di un cluster di Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/).

2.  Modificare il modello di Resource Manager.

    Aggiungere questa proprietà a **virtualMachineProfile**, come parte della risorsa del set di scalabilità di macchine virtuali:

    ```json 
    "osProfile": {
        "computerNamePrefix": "[variables('namingInfix')]",
        "adminUsername": "[parameters('adminUsername')]",
        "adminPassword": "[parameters('adminPassword')]",
        "secrets": [
            {
                "sourceVault": {
                    "id": "[resourceId('KeyVault', 'Microsoft.KeyVault/vaults', 'MikhegnVault')]"
                },
                "vaultCertificates": [
                    {
                        "certificateUrl": "https://mikhegnvault.vault.azure.net:443/secrets/VMSSCert/20709ca8faee4abb84bc6f4611b088a4",
                        "certificateStore": "My"
                    }
                ]
            }
        ]
    }
    ```
  

### <a name="can-i-specify-an-ssh-key-pair-to-use-for-ssh-authentication-with-a-linux-virtual-machine-scale-set-from-a-resource-manager-template"></a>È possibile specificare una coppia di chiavi SSH da usare per l'autenticazione SSH con un set di scalabilità di macchine virtuali Linux da un modello di Resource Manager?  

Sì. L'API REST per **osProfile** è simile all'API REST delle macchine virtuali standard. 

Includere **osProfile** nel modello:

```json 
"osProfile": {
    "computerName": "[variables('vmName')]",
    "adminUsername": "[parameters('adminUserName')]",
    "linuxConfiguration": {
        "disablePasswordAuthentication": "true",
        "ssh": {
            "publicKeys": [
                {
                    "path": "[variables('sshKeyPath')]",
                    "keyData": "[parameters('sshKeyData')]"
                }
            ]
        }
    }
}
```
 
Questo blocco JSON viene utilizzato [il modello di avvio rapido di GitHub 101-vm-sshkey](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json).
 
Profilo del sistema operativo viene utilizzato anche [il modello di avvio rapido di GitHub grelayhost.json](https://github.com/ExchMaster/gadgetron/blob/master/Gadgetron/Templates/grelayhost.json).

Per altre informazioni, vedere [Create or update a virtual machine scale set](https://msdn.microsoft.com/library/azure/mt589035.aspx#linuxconfiguration) (Creare o aggiornare un set di scalabilità di macchine virtuali).
  

### <a name="how-do-i-remove-deprecated-certificates"></a>Come si rimuovono i certificati deprecati? 

Per rimuovere i certificati deprecati, rimuovere il certificato precedente dall'elenco di certificati dell'insieme di credenziali. Lasciare nell'elenco tutti i certificati da conservare nel computer. Questa operazione non rimuove il certificato da tutte le macchine virtuali. Non aggiunge inoltre il certificato alle nuove macchine virtuali create nel set di scalabilità di macchine virtuali. 

Per rimuovere il certificato dalle macchine virtuali esistenti, scrivere un'estensione personalizzata dello script per rimuovere manualmente i certificati dall'archivio certificati.
 
### <a name="how-do-i-inject-an-existing-ssh-public-key-into-the-virtual-machine-scale-set-ssh-layer-during-provisioning-i-want-to-store-the-ssh-public-key-values-in-azure-key-vault-and-then-use-them-in-my-resource-manager-template"></a>Come si inserisce una chiave pubblica SSH esistente nel livello SSH del set di scalabilità di macchine virtuali durante il provisioning? Si vuole archiviare i valori della chiave pubblica SSH in Azure Key Vault e quindi usarli nel modello di Resource Manager.

Se si fornisce alle macchine virtuali solo una chiave SSH pubblica, non è necessario inserire le chiavi pubbliche in Key Vault. Le chiavi pubbliche non sono segrete.
 
Quando si crea una VM Linux è possibile fornire le chiavi pubbliche SSH in testo normale:

```json
"linuxConfiguration": {
    "ssh": {
        "publicKeys": [
            {
                "path": "path",
                "keyData": "publickey"
            }
        ]
    }
```
 
Nome dell'elemento linuxConfiguration | Obbligatoria | type | DESCRIZIONE
--- | --- | --- | --- |  ---
ssh | No  | Raccolta | Specifica la configurazione delle chiavi SSH per un sistema operativo Linux
path | Sì | string | Specifica il percorso del file Linux in cui devono essere salvate le chiavi SSH o il certificato
keyData | Sì | string | Specifica una chiave pubblica SSH con codifica Base64

Per un esempio, vedere [il modello di avvio rapido di GitHub 101-vm-sshkey](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json).

 
### <a name="when-i-run-update-azurermvmss-after-adding-more-than-one-certificate-from-the-same-key-vault-i-see-the-following-message"></a>Quando si esegue `Update-AzureRmVmss` dopo avere aggiunto più di un certificato dallo stesso insieme di credenziali delle chiavi, viene visualizzato il messaggio seguente:
 
>Update-AzureRmVmss: L'elenco secrets contiene istanze ripetute di /subscriptions/<my-subscription-id>/resourceGroups/internal-rg-dev/providers/Microsoft.KeyVault/vaults/internal-keyvault-dev. Questo scenario non è consentito.
 
Questo problema si può verificare se si prova ad aggiungere di nuovo lo stesso insieme di credenziali invece di usare un nuovo certificato dell'insieme di credenziali per l'insieme di credenziali di origine esistente. Il comando `Add-AzureRmVmssSecret` non funziona correttamente se si aggiungono altri segreti.
 
Per aggiungere altri segreti dallo stesso insieme di credenziali delle chiavi, aggiornare l'elenco $vmss.properties.osProfile.secrets[0].vaultCertificates.
 
Per la struttura di input prevista, vedere [Create or update a virtual machine set](https://msdn.microsoft.com/library/azure/mt589035.aspx) (Creare o aggiornare un set di macchine virtuali).
 
Trovare il segreto nell'oggetto del set di scalabilità di macchine virtuali presente nell'insieme di credenziali delle chiavi. Aggiungere quindi il riferimento al certificato, ovvero l'URL e il nome dell'archivio di certificati, all'elenco associato all'insieme di credenziali.

> [!NOTE] 
> Non è attualmente possibile rimuovere i certificati dalle macchine virtuali usando l'API del set di scalabilità di macchine virtuali.
>

Le nuove macchine virtuali non includeranno il certificato precedente. Le VM che includono il certificato e che sono già distribuite avranno tuttavia il certificato precedente.
 
### <a name="can-i-push-certificates-to-the-virtual-machine-scale-set-without-providing-the-password-when-the-certificate-is-in-the-secret-store"></a>È possibile inserire i certificati nel set di scalabilità di macchine virtuali senza specificare la password quando il certificato si trova nell'archivio di segreti?

Non è necessario usare password hardcoded negli script. È possibile recuperare in modo dinamico le password con le autorizzazioni usate per eseguire lo script di distribuzione. Se è presente uno script che sposta un certificato dall'insieme di credenziali delle chiavi dell'archivio di segreti, il comando `get certificate` dell'archivio di segreti restituisce la password del file con estensione pfx.
 
### <a name="how-does-the-secrets-property-of-virtualmachineprofileosprofile-for-a-virtual-machine-scale-set-work-why-do-i-need-the-sourcevault-value-when-i-have-to-specify-the-absolute-uri-for-a-certificate-by-using-the-certificateurl-property"></a>Come funziona la proprietà Secrets di virtualMachineProfile.osProfile per un set di scalabilità di macchine virtuali? Perché è necessario il valore sourceVault quando occorre specificare l'URI assoluto per un certificato usando la proprietà certificateUrl? 

Un riferimento al certificato Windows Remote Management (WinRM) deve essere presente nella proprietà Secrets del profilo del sistema operativo. 

L'indicazione dell'insieme di credenziali di origine consente di applicare i criteri dell'elenco di controllo di accesso esistenti nel modello di Servizi cloud di Azure di un utente. Se l'insieme di credenziali di origine non è specificato, gli utenti che non sono autorizzati a distribuire o ad accedere ai segreti in un insieme di credenziali delle chiavi potrebbero eseguire l'accesso tramite provider di risorse di calcolo. Gli elenchi di controllo di accesso sono presenti anche per risorse inesistenti.

Se si specifica un ID errato per l'insieme di credenziali di origine ma un URL valido per l'insieme di credenziali delle chiavi, viene segnalato un errore quando si esegue il polling dell'operazione.
 
### <a name="if-i-add-secrets-to-an-existing-virtual-machine-scale-set-are-the-secrets-injected-into-existing-vms-or-only-into-new-ones"></a>Se si aggiungono segreti a un set di scalabilità di macchine virtuali, i segreti vengono inseriti nelle VM esistenti o solo nelle nuove macchine virtuali? 

I certificati vengono aggiunti a tutte le macchine virtuali, anche quelli già esistenti. Se la proprietà upgradePolicy del set di scalabilità di macchine virtuali è impostata su **manual**, il certificato viene aggiunto alla VM quando si esegue un aggiornamento manuale nella macchina virtuale.
 
### <a name="where-do-i-put-certificates-for-linux-vms"></a>Dove vengono inseriti i certificati per le macchine virtuali Linux?

Per informazioni su come distribuire i certificati per le macchine virtuali Linux, vedere [Deploy certificates to VMs from a customer-managed key vault](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) (Distribuire certificati nelle VM da un insieme di credenziali delle chiavi gestito dai clienti).
  
### <a name="how-do-i-add-a-new-vault-certificate-to-a-new-certificate-object"></a>Come si aggiunge un nuovo certificato dell'insieme di credenziali a un nuovo oggetto certificato?

Per aggiungere un certificato dell'insieme di credenziali a un segreto esistente, vedere l'esempio di PowerShell seguente. Usare solo un oggetto segreto.
 
```powershell
$newVaultCertificate = New-AzureRmVmssVaultCertificateConfig -CertificateStore MY -CertificateUrl https://sansunallapps1.vault.azure.net:443/secrets/dg-private-enc/55fa0332edc44a84ad655298905f1809
 
$vmss.VirtualMachineProfile.OsProfile.Secrets[0].VaultCertificates.Add($newVaultCertificate)
 
Update-AzureRmVmss -VirtualMachineScaleSet $vmss -ResourceGroup $rg -Name $vmssName
```
 
### <a name="what-happens-to-certificates-if-you-reimage-a-vm"></a>Cosa accade ai certificati se si ricrea l'immagine di una macchina virtuale?

Se si ricrea l'immagine di una macchina virtuale, i certificati vengono eliminati. La ricreazione dell'immagine elimina l'intero disco del sistema operativo. 
 
### <a name="what-happens-if-you-delete-a-certificate-from-the-key-vault"></a>Cosa accade se si elimina un certificato dall'insieme di credenziali delle chiavi?

Se la chiave privata viene eliminata dall'insieme di credenziali chiave e quindi si esegue `stop deallocate` per tutte le macchine virtuali e riavviarle, si verifica un errore. L'errore si verifica perché il provider di risorse di calcolo deve recuperare i segreti dall'insieme di credenziali delle chiavi ma non riesce. In questo scenario è possibile eliminare i certificati dal modello del set di scalabilità di macchine virtuali. 

Il provider di risorse di calcolo non rende persistenti i segreti dei clienti. Se si esegue `stop deallocate` per tutte le macchine virtuali nel set di scalabilità di macchine virtuali, la cache viene eliminata. In questo scenario, i segreti vengono recuperati dall'insieme di credenziali delle chiavi.

Questo problema non si verifica durante l'aumento del numero di istanze, perché è disponibile una copia del segreto memorizzata nella cache in Azure Service Fabric nel modello di tenant a infrastruttura singola.
 
### <a name="why-do-i-have-to-specify-the-exact-location-for-the-certificate-url-httpsname-of-the-vaultvaultazurenet443secretsexact-location-as-indicated-in-service-fabric-cluster-security-scenarioshttpsazuremicrosoftcomdocumentationarticlesservice-fabric-cluster-security"></a>Per quale motivo è necessario specificare la posizione esatta per l'URL di certificato (https://<name of the vault>.vault.azure.net:443/secrets/<exact location>), come indicato in [Scenari di sicurezza di un cluster di Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/)?
 
La documentazione di Azure Key Vault indica che l'API REST di recupero del segreto deve restituire la versione più recente del segreto nel caso in cui la versione non sia specificata.
 
Metodo | URL
--- | ---
GET | https://mykeyvault.vault.azure.net/secrets/{secret-name}/{secret-version}?api-version={api-version}

Sostituire {*secret-name*} con il nome e {*secret-version*} con la versione del segreto da recuperare. È possibile che la versione del segreto venga esclusa. In questo caso viene recuperata la versione corrente.
  
### <a name="why-do-i-have-to-specify-the-certificate-version-when-i-use-key-vault"></a>Per quale motivo è necessario specificare la versione del certificato quando si usa Key Vault?

Il requisito di Key Vault che richiede di specificare la versione del specificato consente di indicare chiaramente all'utente il certificato che viene distribuito nelle rispettive macchine virtuali.

Se si crea una macchina virtuale e quindi si aggiorna il segreto nell'insieme di credenziali delle chiavi, il nuovo certificato non viene scaricato nelle VM. Le macchine virtuali fanno tuttavia riferimento al certificato e le nuove VM ottengono il nuovo segreto. Per evitare questo problema, è necessario fare riferimento a una versione del segreto.

### <a name="my-team-works-with-several-certificates-that-are-distributed-to-us-as-cer-public-keys-what-is-the-recommended-approach-for-deploying-these-certificates-to-a-virtual-machine-scale-set"></a>Il team lavora con diversi certificati che vengono distribuiti come chiavi pubbliche CER. Qual è l'approccio consigliato per la distribuzione dei certificati nel set di scalabilità di macchine virtuali?

Per distribuire le chiavi pubbliche con estensione cer in un set di scalabilità di macchine virtuali, è possibile generare un file con estensione pfx che contiene solo file con estensione cer. A questo scopo, usare `X509ContentType = Pfx`. Caricare ad esempio il file con estensione cer come oggetto x509Certificate2 in C# o PowerShell, quindi chiamare il metodo. 

Per altre informazioni, vedere [Metodo X509Certificate.Export (X509ContentType, String)](https://msdn.microsoft.com/library/24ww6yzk(v=vs.110.aspx)).

### <a name="i-do-not-see-an-option-for-users-to-pass-in-certificates-as-base64-strings-most-other-resource-providers-have-this-option"></a>Non è presente un'opzione per passare i certificati sotto forma di stringhe Base64, disponibile nella maggior parte degli altri provider di risorse.

Per emulare il passaggio di un certificato come stringa Base64, è possibile estrarre l'URL con versione più recente in un modello di Resource Manager. Includere la proprietà JSON seguente nel modello di Resource Manager:

```json 
"certificateUrl": "[reference(resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults/secrets', parameters('vaultName'), parameters('secretName')), '2015-06-01').secretUriWithVersion]"
```
 
### <a name="do-i-have-to-wrap-certificates-in-json-objects-in-key-vaults"></a>È necessario eseguire il wrapping dei certificati in oggetti JSON negli insiemi di credenziali delle chiavi?

Nei set di scalabilità di macchine virtuali e nelle macchine virtuali è necessario eseguire il wrapping dei certificati in oggetti JSON. 

È anche supportato il tipo di contenuto application/x-pkcs12. Per istruzioni sull'uso di application/x-pkcs12, vedere [PFX certificates in Azure Key Vault](http://www.rahulpnath.com/blog/pfx-certificate-in-azure-key-vault/) (Certificati PFX in Azure Key Vault).
 
Non sono attualmente supportati i file con estensione cer. Per usare i file con estensione cer, esportarli in contenitori PFX.



## <a name="compliance-and-security"></a>Conformità e sicurezza

### <a name="are-virtual-machine-scale-sets-pci-compliant"></a>I set di scalabilità di macchine virtuali sono compatibili con PCI?

I set di scalabilità di macchine virtuali sono un sottile livello API disponibile sopra il provider di risorse di calcolo. Entrambi i componenti sono parte della piattaforma di calcolo nell'albero dei servizi di Azure.

Dal punto di vista della conformità, i set di scalabilità di macchine virtuali sono una parte essenziale della piattaforma di calcolo di Azure. Condividono un team, strumenti, processi, metodologia di distribuzione, controlli di sicurezza, compilazione Just-In-Time (JIT), monitoraggio, avvisi e così via con il provider di risorse di calcolo. I set di scalabilità di macchine virtuali sono compatibili con PCI (Payment Card Industry) perché il provider di risorse di calcolo è parte dell'attestazione PCI Data Security Standard (DSS) corrente.

Per altre informazioni, vedere il [Centro protezione Microsoft](https://www.microsoft.com/TrustCenter/Compliance/PCI).

### <a name="does-azure-managed-service-identityhttpsdocsmicrosoftcomazureactive-directorymsi-overview-work-with-virtual-machine-scale-sets"></a>Does [identità del servizio gestito di Azure](https://docs.microsoft.com/azure/active-directory/msi-overview) funzionano con il set di scalabilità di macchine virtuali?

Sì. È possibile visualizzare alcuni modelli MSI di esempio nei modelli di avvio rapido di Azure. Linux: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-msi-linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-msi-linux). Windows: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-msi-windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-msi-windows).


## <a name="extensions"></a>Estensioni

### <a name="how-do-i-delete-a-virtual-machine-scale-set-extension"></a>Come si elimina un'estensione del set di scalabilità di macchine virtuali?

Per eliminare un'estensione del set di scalabilità di macchine virtuali, usare l'esempio di PowerShell seguente:

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" 

$vmss=Remove-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "extensionName"

Update-AzureRmVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" -VirtualMacineScaleSet $vmss
```
 
È possibile trovare il valore extensionName in `$vmss`.
   
### <a name="is-there-a-virtual-machine-scale-set-template-example-that-integrates-with-operations-management-suite"></a>È disponibile un esempio di modello di set di scalabilità di macchine virtuali che si integra con Operations Management Suite?

Per un esempio di modello di set di scalabilità di macchine virtuali che si integra con Operations Management Suite, vedere il secondo esempio in [Deploy an Azure Service Fabric cluster and enable monitoring by using Log Analytics](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/ServiceFabric) (Distribuire un cluster di Azure Service Fabric e abilitare il monitoraggio usando Log Analytics).
   
### <a name="extensions-seem-to-run-in-parallel-on-virtual-machine-scale-sets-this-causes-my-custom-script-extension-to-fail-what-can-i-do-to-fix-this"></a>Le estensioni vengono eseguite apparentemente in parallelo nei set di scalabilità di macchine virtuali. Ciò provoca un errore dell'estensione di script personalizzata. Come si può risolvere questo problema?

Per informazioni sulla sequenziazione di estensioni nei set di scalabilità di macchine virtuali, vedere [Extension sequencing in Azure virtual machine scale sets](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/) (Sequenziazione delle estensioni nei set di scalabilità di macchine virtuali di Azure).
 
 
### <a name="how-do-i-reset-the-password-for-vms-in-my-virtual-machine-scale-set"></a>Come si reimposta la password per le VM nel set di scalabilità di macchine virtuali?

Esistono due modi per modificare la password per le macchine virtuali nel set di scalabilità.

- Modificare direttamente il modello di set di scalabilità di macchina virtuale. Disponibile con di calcolo API 2017-12-01 e versioni successive.

    Aggiornare le credenziali di amministratore direttamente nel modello di set di scalabilità (ad esempio tramite Esplora risorse di Azure PowerShell o CLI). Al termine il set di scalabilità aggiornata, tutte le nuove macchine virtuali sono le nuove credenziali. Macchine virtuali esistenti sono solo le nuove credenziali se si viene ricreata l'immagine. 

- Reimpostare la password utilizzando le estensioni di accesso della macchina virtuale.

    Usare l'esempio di PowerShell seguente:
    
    ```powershell
    $vmssName = "myvmss"
    $vmssResourceGroup = "myvmssrg"
    $publicConfig = @{"UserName" = "newuser"}
    $privateConfig = @{"Password" = "********"}
     
    $extName = "VMAccessAgent"
    $publisher = "Microsoft.Compute"
    $vmss = Get-AzureRmVmss -ResourceGroupName $vmssResourceGroup -VMScaleSetName $vmssName
    $vmss = Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name $extName -Publisher $publisher -Setting $publicConfig -ProtectedSetting $privateConfig -Type $extName -TypeHandlerVersion "2.0" -AutoUpgradeMinorVersion $true
    Update-AzureRmVmss -ResourceGroupName $vmssResourceGroup -Name $vmssName -VirtualMachineScaleSet $vmss
    ```


### <a name="how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set"></a>Come si aggiunge un'estensione a tutte le macchine virtuali del set di scalabilità di macchine virtuali?

Se i criteri impostati prevedono l'aggiornamento **automatico**, ridistribuendo il modello con le nuove proprietà di estensione verranno aggiornate tutte le macchine virtuali.

Se i criteri di aggiornamento sono impostati su **manuale**, aggiornare prima di tutto l'estensione e quindi aggiornare manualmente tutte le istanze nelle macchine virtuali.

  
### <a name="if-the-extensions-associated-with-an-existing-virtual-machine-scale-set-are-updated-are-existing-vms-affected-that-is-will-the-vms-not-match-the-virtual-machine-scale-set-model-or-are-they-ignored-when-an-existing-machine-is-service-healed-or-reimaged-are-the-scripts-that-are-currently-configured-on-the-virtual-machine-scale-set-executed-or-are-the-scripts-that-were-configured-when-the-vm-was-first-created-used"></a>Se le estensioni associate a un set di scalabilità di macchine virtuali esistente vengono aggiornate, l'operazione influisce sulle VM esistenti, ovvero, le VM *non* corrisponderanno al modello di set di scalabilità di macchine virtuali? O verranno ignorate? Quando una macchina virtuale esistente viene sottoposta a ripristino del servizio o ricreazione dell'immagine, gli script attualmente configurati nel set di scalabilità di macchine virtuali vengono eseguiti o vengono usati gli script configurati durante la creazione iniziale della macchina virtuale?

Se la definizione dell'estensione nel modello di set di scalabilità di macchine virtuali viene aggiornata e la proprietà upgradePolicy è impostata su **automatic**, le macchine virtuali vengono aggiornate. Se la proprietà upgradePolicy è impostata su **manual**, le estensioni vengono contrassegnate come non corrispondenti al modello. 

Il ripristino del servizio di una VM esistente viene considerato un riavvio e le estensioni non vengono eseguite di nuovo. Se viene ricreata l'immagine, tale operazione è simile alla sostituzione dell'unità del sistema operativo con l'immagine di origine. Vengono eseguite eventuali specializzazioni dal modello più recente, ad esempio le estensioni.
 
### <a name="how-do-i-join-a-virtual-machine-scale-set-to-an-azure-ad-domain"></a>Come si aggiunge un set di scalabilità di macchine virtuali a un dominio di Azure AD?

Per aggiungere un set di scalabilità di macchine virtuali a un dominio di Azure Active Directory (Azure AD), è possibile definire un'estensione. 

Per definire un'estensione, usare la proprietà JsonADDomainExtension:

```json
"extensionProfile": {
    "extensions": [
        {
            "name": "joindomain",
            "properties": {
                "publisher": "Microsoft.Compute",
                "type": "JsonADDomainExtension",
                "typeHandlerVersion": "1.3",
                "settings": {
                    "Name": "[parameters('domainName')]",
                    "OUPath": "[variables('ouPath')]",
                    "User": "[variables('domainAndUsername')]",
                    "Restart": "true",
                    "Options": "[variables('domainJoinOptions')]"
                },
                "protectedsettings": {
                    "Password": "[parameters('domainJoinPassword')]"
                }
            }
        }
    ]
}
```
 
### <a name="my-virtual-machine-scale-set-extension-is-trying-to-install-something-that-requires-a-reboot-for-example-commandtoexecute-powershellexe--executionpolicy-unrestricted-install-windowsfeature-name-fs-resource-manager-includemanagementtools"></a>L'estensione del set di scalabilità di macchine virtuali sta provando a installare un programma che richiede un riavvio. Ad esempio, "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted Install-WindowsFeature –Name FS-Resource-Manager –IncludeManagementTools"

Se l'estensione del set di scalabilità di macchine virtuali prova a installare un programma che richiede un riavvio, è possibile usare l'estensione Automation DSC (Desired State Configuration) per Azure. Se il sistema operativo è Windows Server 2012 R2, Azure esegue il pull del programma di installazione di Windows Management Framework (WMF) 5.0, esegue il riavvio e quindi prosegue con la configurazione. 
 
### <a name="how-do-i-turn-on-antimalware-in-my-virtual-machine-scale-set"></a>Come si attiva l'antimalware nel set di scalabilità di macchine virtuali?

Per attivare l'antimalware nel set di scalabilità di macchine virtuali, usare l'esempio di PowerShell seguente:

```powershell
$rgname = 'autolap'
$vmssname = 'autolapbr'
$location = 'eastus'
 
# Retrieve the most recent version number of the extension.
$allVersions= (Get-AzureRmVMExtensionImage -Location $location -PublisherName "Microsoft.Azure.Security" -Type "IaaSAntimalware").Version
$versionString = $allVersions[($allVersions.count)-1].Split(".")[0] + "." + $allVersions[($allVersions.count)-1].Split(".")[1]
 
$VMSS = Get-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname
echo $VMSS
Add-AzureRmVmssExtension -VirtualMachineScaleSet $VMSS -Name "IaaSAntimalware" -Publisher "Microsoft.Azure.Security" -Type "IaaSAntimalware" -TypeHandlerVersion $versionString
Update-AzureRmVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $VMSS 
```

### <a name="i-need-to-execute-a-custom-script-thats-hosted-in-a-private-storage-account-the-script-runs-successfully-when-the-storage-is-public-but-when-i-try-to-use-a-shared-access-signature-sas-it-fails-this-message-is-displayed-missing-mandatory-parameters-for-valid-shared-access-signature-linksas-works-fine-from-my-local-browser"></a>È necessario eseguire uno script personalizzato ospitato in un account di archiviazione privato. Lo script viene eseguito correttamente quando la risorsa di archiviazione è pubblica, ma si verifica un errore quando si prova a usare una firma di accesso condiviso. Viene visualizzato questo messaggio: "Parametri obbligatori mancanti per la firma di accesso condiviso valida". Il collegamento e la firma di accesso condiviso funzionano correttamente.

Per eseguire uno script personalizzato ospitato in un account di archiviazione provato, configurare le impostazioni protette con la chiave e il nome dell'account di archiviazione. Per altre informazioni, vedere [Estensione Script personalizzato per Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/#template-example-for-a-windows-vm-with-protected-settings).


## <a name="networking"></a>Rete
 
### <a name="is-it-possible-to-assign-a-network-security-group-nsg-to-a-scale-set-so-that-it-applies-to-all-the-vm-nics-in-the-set"></a>È possibile assegnare un gruppo di sicurezza di rete (gruppo) a un set di scalabilità, in modo che venga applicata a tutte le schede NIC VM nel set di?

Sì. È possibile applicare un gruppo di sicurezza di rete direttamente a un set di scalabilità facendovi riferimento nella sezione networkInterfaceConfigurations del profilo di rete. Esempio:

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
            "name": "nic1",
            "properties": {
                "primary": "true",
                "ipConfigurations": [
                    {
                        "name": "ip1",
                        "properties": {
                            "subnet": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                            }
                "loadBalancerInboundNatPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                                }
                            ],
                            "loadBalancerBackendAddressPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                                 }
                            ]
                        }
                    }
                ],
                "networkSecurityGroup": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('nsgName'))]"
                }
            }
        }
    ]
}
```

### <a name="how-do-i-do-a-vip-swap-for-virtual-machine-scale-sets-in-the-same-subscription-and-same-region"></a>Come si esegue lo scambio di indirizzi VIP per i set di scalabilità di macchine virtuali che si trovano nella stessa sottoscrizione e nella stessa area?

Se si hanno due set di scalabilità di macchine virtuali con front-end di Azure Load Balancer, che sono nella stessa sottoscrizione e area, è possibile deallocare gli indirizzi IP pubblici da uno e assegnarli all'altro. Per un esempio, vedere [VIP Swap: Blue-green deployment in Azure Resource Manager](https://msftstack.wordpress.com/2017/02/24/vip-swap-blue-green-deployment-in-azure-resource-manager/) (Scambio di indirizzi VIP: distribuzione di tipo "blu-verde" in Azure Resource Manager). Ciò implica un ritardo anche se le risorse vengono deallocate/allocate a livello di rete. Un'opzione più rapida consiste nell'usare il Gateway applicazione di Azure con due pool di back-end e una regola di routing. In alternativa, è possibile ospitare l'applicazione con [Servizio app di Azure](https://azure.microsoft.com/services/app-service/) che offre il supporto per commutare rapidamente gli slot di gestione temporanea in slot di produzione.
 
### <a name="how-do-i-specify-a-range-of-private-ip-addresses-to-use-for-static-private-ip-address-allocation"></a>Come è possibile specificare un intervallo di indirizzi IP privati per l'allocazione di indirizzi IP privati statici?

Gli indirizzi IP vengono selezionati da una subnet specificata. 

Il metodo di allocazione degli indirizzi IP del set di scalabilità di macchine virtuali è sempre "dinamico", ma ciò non significa che è possibile modificare questi indirizzi IP. In questo caso, per "dinamico" si intende solo che non è necessario specificare l'indirizzo IP in una richiesta PUT. Specificare il set statico usando la subnet. 
    
### <a name="how-do-i-deploy-a-virtual-machine-scale-set-to-an-existing-azure-virtual-network"></a>Come si distribuisce un set di scalabilità di macchine virtuali in una rete virtuale di Azure esistente? 

Per distribuire un set di scalabilità di macchine virtuali in una rete virtuale di Azure esistente, vedere [Deploy a virtual machine scale set to an existing virtual network](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-existing-vnet) (Distribuire un set di scalabilità di macchine virtuali in una rete virtuale esistente). 

### <a name="how-do-i-add-the-ip-address-of-the-first-vm-in-a-virtual-machine-scale-set-to-the-output-of-a-template"></a>Come si aggiunge l'indirizzo IP della prima VM di un set di scalabilità di macchine virtuali all'output di un modello?

Per aggiungere l'indirizzo IP della macchina virtuale prima di un set all'output di un modello di scalabilità macchina virtuale, vedere [Gestione risorse di Azure: scalabilità della macchina virtuale Get Imposta indirizzi IP privati](http://stackoverflow.com/questions/42790392/arm-get-vmsss-private-ips).

### <a name="can-i-use-scale-sets-with-accelerated-networking"></a>È possibile usare i set di scalabilità con la rete accelerata?

Sì. Per usare la rete accelerata, impostare enableAcceleratedNetworking su true nelle impostazioni networkInterfaceConfigurations del set di scalabilità. Ad esempio
```json
"networkProfile": {
    "networkInterfaceConfigurations": [
    {
        "name": "niconfig1",
        "properties": {
        "primary": true,
        "enableAcceleratedNetworking" : true,
        "ipConfigurations": [
                ]
            }
            }
        ]
        }
    }
    ]
}
```

### <a name="how-can-i-configure-the-dns-servers-used-by-a-scale-set"></a>Come è possibile configurare i server DNS usati da un set di scalabilità?

Per creare un set con una configurazione DNS personalizzata di scalabilità macchina virtuale, aggiungere un pacchetto JSON impostazioni DNS per la sezione di configurazioni di set di scalabilità. Esempio:
```json
    "dnsSettings":{
        "dnsServers":["10.0.0.6", "10.0.0.5"]
    }
```

### <a name="how-can-i-configure-a-scale-set-to-assign-a-public-ip-address-to-each-vm"></a>Come è possibile configurare un set di scalabilità per assegnare un indirizzo IP pubblico a ogni VM?

Per creare un set di scalabilità della macchina virtuale che assegna un indirizzo IP pubblico a ogni macchina virtuale, assicurarsi che la versione dell'API della risorsa Microsoft.Compute/virtualMAchineScaleSets sia 2017-03-30 e aggiungere un _publicipaddressconfiguration_ JSON sezione di configurazione IP del set di pacchetto per la scala. Esempio:

```json
    "publicipaddressconfiguration": {
        "name": "pub1",
        "properties": {
        "idleTimeoutInMinutes": 15
        }
    }
```

### <a name="can-i-configure-a-scale-set-to-work-with-multiple-application-gateways"></a>È possibile configurare un set di scalabilità da usare con più gateway applicazione?

Sì. È possibile aggiungere ID risorsa per più pool di indirizzi di back-end del gateway applicazione all'elenco _applicationGatewayBackendAddressPools_ nella sezione _ipConfigurations_ del profilo della rete del set di scalabilità.

## <a name="scale"></a>Scalabilità

### <a name="in-what-case-would-i-create-a-virtual-machine-scale-set-with-fewer-than-two-vms"></a>In quale caso è consigliabile creare un set di scalabilità di macchine virtuali con meno di due macchine virtuali?

Un motivo per la creazione di un set di scalabilità di macchine virtuali con meno di due VM può essere relativo all'uso delle proprietà elastiche di un set di scalabilità di macchine virtuali. È ad esempio possibile distribuire un set di scalabilità di macchine virtuali con zero VM per definire l'infrastruttura senza sostenere i costi operativi delle macchine virtuali. Per distribuire le macchine virtuali, aumentare la "capacità" del set di scalabilità di macchine virtuali fino al numero di istanze di produzione.

È possibile creare un set di scalabilità di macchine virtuali con meno di due VM anche se si è interessati principalmente all'uso di un set di disponibilità con VM discrete, invece che alla disponibilità. I set di scalabilità di macchine virtuali consentono di usare unità di calcolo non differenziate e fungibili. Questa uniformità è un elemento di differenziazione chiave tra set di scalabilità di macchine virtuali e set di disponibilità. Molti carichi di lavoro senza stato non tengono traccia delle singole unità. In caso di riduzione del carico di lavoro, è possibile passare a una sola unità di calcolo e quindi aumentare il numero di unità con l'aumento del carico di lavoro.

### <a name="how-do-i-change-the-number-of-vms-in-a-virtual-machine-scale-set"></a>Come si cambia il numero di VM in un set di scalabilità di macchine virtuali?

Per modificare il numero di macchine virtuali in una scala di macchine virtuali impostata nel portale di Azure, dalla macchina virtuale scala sezione delle proprietà del set di fare clic su pannello "Scalabilità" e utilizza la barra di scorrimento. Per altre modalità di modifica del numero di istanze, vedere [Change the instance count of a virtual machine scale set](https://msftstack.wordpress.com/2016/05/13/change-the-instance-count-of-an-azure-vm-scale-set/) (Cambiare il numero di istanze di un set di scalabilità di macchine virtuali).

### <a name="how-do-i-define-custom-alerts-for-when-certain-thresholds-are-reached"></a>Come è possibile definire avvisi personalizzati per il raggiungimento di determinate soglie?

La gestione degli avvisi per le soglie specificate offre una certa flessibilità. È ad esempio possibile definire webhook personalizzati. L'esempio di webhook seguente è tratto da un modello di Resource Manager:

```json
{
    "type": "Microsoft.Insights/autoscaleSettings",
    "apiVersion": "[variables('insightsApi')]",
    "name": "autoscale",
    "location": "[parameters('resourceLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]"
    ],
    "properties": {
        "name": "autoscale",
        "targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/',  resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
        "enabled": true,
        "notifications": [
            {
                "operation": "Scale",
                "email": {
                    "sendToSubscriptionAdministrator": true,
                    "sendToSubscriptionCoAdministrators": true,
                    "customEmails": [
                        "youremail@address.com"
                    ]
                },
                "webhooks": [
                    {
                        "serviceUri": "https://events.pagerduty.com/integration/0b75b57246814149b4d87fa6e1273687/enqueue",
                        "properties": {
                            "key1": "custommetric",
                            "key2": "scalevmss"
                        }
                    }
                ]
            }
        ],
```

In questo esempio viene inviato un avviso a Pagerduty.com al raggiungimento di una soglia.



## <a name="patching-and-operations"></a>Applicazione di patch e operazioni

### <a name="how-do-i-create-a-scale-set-in-an-existing-resource-group"></a>Come si crea un set di scalabilità in un gruppo di risorse esistente?

La creazione di set di scalabilità in un gruppo di risorse esistente non è ancora possibile dal portale di Azure, ma è possibile specificare un gruppo di risorse esistente quando si distribuisce un set di scalabilità da un modello di Azure Resource Manager. È anche possibile specificare un gruppo di risorse esistente durante la creazione di un set di scalabilità usando Azure PowerShell o l'interfaccia della riga di comando.

### <a name="can-we-move-a-scale-set-to-another-resource-group"></a>È possibile spostare un set di scalabilità in un altro gruppo di risorse?

Sì, è possibile spostare le risorse di un set di scalabilità in una nuova sottoscrizione o in un nuovo gruppo di risorse.

### <a name="how-to-i-update-my-virtual-machine-scale-set-to-a-new-image-how-do-i-manage-patching"></a>Come si aggiorna il set di scalabilità di macchine virtuali a una nuova immagine? Come si gestisce l'applicazione di patch?

Per aggiornare il set di scalabilità di macchine virtuali a una nuova immagine e gestire l'applicazione di patch, vedere [Aggiornare un set di scalabilità di macchine virtuali](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set).

### <a name="can-i-use-the-reimage-operation-to-reset-a-vm-without-changing-the-image-that-is-i-want-reset-a-vm-to-factory-settings-rather-than-to-a-new-image"></a>È possibile usare l'operazione di ricreazione dell'immagine per ripristinare una macchina virtuale senza modificare l'immagine, ovvero ripristinare le impostazioni predefinite di una VM invece di usare una nuova immagine?

Sì, è possibile usare l'operazione di ricreazione dell'immagine per ripristinare una macchina virtuale senza modificare l'immagine. Se tuttavia il set di scalabilità di macchine virtuali fa riferimento a un'immagine di piattaforma con `version = latest`, la macchina virtuale può essere aggiornata a un'immagine del sistema operativo successiva quando si chiama `reimage`.

Per altre informazioni, vedere [Manage all VMs in a virtual machine scale set](https://docs.microsoft.com/rest/api/virtualmachinescalesets/manage-all-vms-in-a-set) (Gestire tutte le VM in un set di scalabilità di macchine virtuali).

### <a name="is-it-possible-to-integrate-scale-sets-with-azure-oms-operations-management-suite"></a>È possibile integrare i set di scalabilità con Azure OMS (Operations Management Suite)?

Sì, è possibile installando l'estensione OMS nelle macchine virtuali dei set di scalabilità. Di seguito è riportato un esempio dell'interfaccia della riga di comando di Azure:
```
az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group Team-03 --vmss-name nt01 --settings "{'workspaceId': '<your workspace ID here>'}" --protected-settings "{'workspaceKey': '<your workspace key here'}"
```
È possibile trovare l'ID e la chiave dell'area di lavoro necessari nel portale OMS. Nella pagina Panoramica fare clic sul riquadro Impostazioni. Fare clic sula scheda Origini connesse nella parte superiore.

Nota: se la scala impostata _upgradePolicy_ è impostato su manuale, è necessario applicare l'estensione a tutte le macchine virtuali nel set di mediante la chiamata di aggiornamento su di essi. Nell'interfaccia della riga di comando è necessario chiamare _az vmss update-instances_.

## <a name="troubleshooting"></a>risoluzione dei problemi

### <a name="how-do-i-turn-on-boot-diagnostics"></a>Come si attiva la diagnostica di avvio?

Per attivare la diagnostica di avvio, creare prima di tutto un account di archiviazione. Inserire quindi il blocco JSON nella proprietà **virtualMachineProfile** set di scalabilità di macchine virtuali, quindi aggiornare il JSON:

```json
"diagnosticsProfile": {
    "bootDiagnostics": {
        "enabled": true,
        "storageUri": "http://yourstorageaccount.blob.core.windows.net"
    }
}
```

Quando viene creata una nuova VM, la proprietà InstanceView della VM mostra i dettagli per lo screenshot e così via. Ad esempio:
 
```json
"bootDiagnostics": {
    "consoleScreenshotBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.screenshot.bmp",
    "serialConsoleLogBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.serialconsole.log"
  }
```


## <a name="virtual-machine-properties"></a>Proprietà della macchina virtuale

### <a name="how-do-i-get-property-information-for-each-vm-without-making-multiple-calls-for-example-how-would-i-get-the-fault-domain-for-each-of-the-100-vms-in-my-virtual-machine-scale-set"></a>Come si possono ottenere informazioni sulle proprietà di ogni macchina virtuale senza dover eseguire più chiamate? Come si ottiene ad esempio il dominio di errore per ognuna delle 100 VM del set di scalabilità di macchine virtuali?

Per ottenere informazioni sulle proprietà per ogni macchina virtuale senza eseguire più chiamate, è possibile chiamare `ListVMInstanceViews` tramite un'operazione `GET` dell'API REST nell'URI di risorsa seguente:

/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.Compute/virtualMachineScaleSets/<scaleset_name>/virtualMachines?$expand=instanceView&$select=instanceView

### <a name="can-i-pass-different-extension-arguments-to-different-vms-in-a-virtual-machine-scale-set"></a>È possibile passare argomenti di estensione diversi a diverse VM in un set di scalabilità di macchine virtuali?

No, non è possibile passare argomenti di estensione diversi a diverse VM in un set di scalabilità di macchine virtuali. Le estensioni possono tuttavia funzionare in base alle proprietà univoche della macchina virtuale sulla quale sono in esecuzione, ad esempio il nome della macchina. Le estensioni possono anche eseguire query sui metadati delle istanze in http://169.254.169.254 per ottenere altre informazioni sulla VM.

### <a name="why-are-there-gaps-between-my-virtual-machine-scale-set-vm-machine-names-and-vm-ids-for-example-0-1-3"></a>Perché sono presenti gap tra i nomi delle macchine virtuali del set di scalabilità di macchine virtuali e gli ID, ad esempio 0, 1, 3?

Sono presenti gap tra i nomi delle macchine virtuali del set di scalabilità di macchine virtuali e gli ID delle VM perché la proprietà **overprovision** del set di scalabilità di macchine virtuali è impostata sul valore predefinito **true**. Se la proprietà overprovision è impostata su **true**, viene creato un numero di VM superiore al necessario. Le VM aggiuntive vengono quindi eliminate. In questo caso si ottiene una maggiore affidabilità per la distribuzione, a scapito tuttavia delle regole di contiguità di denominazione e di NAT (Network Address Translation). 

È possibile impostare questa proprietà su **false**. Per i set di scalabilità di macchine virtuali di piccole dimensioni ciò non influisce significativamente sull'affidabilità della distribuzione.

### <a name="what-is-the-difference-between-deleting-a-vm-in-a-virtual-machine-scale-set-and-deallocating-the-vm-when-should-i-choose-one-over-the-other"></a>Qual è la differenza tra l'eliminazione di una VM in un set di scalabilità di macchine virtuali e la deallocazione della VM? Quando scegliere l'una o l'altra?

La differenza principale tra l'eliminazione di una VM in un set di scalabilità di macchine virtuali e la deallocazione della VM consiste nel fatto che `deallocate` non elimina i dischi rigidi virtuali. L'esecuzione di `stop deallocate` comporta costi di archiviazione. È possibile usare una delle due opzioni per i motivi seguenti:

- Non si vogliono più sostenere i costi dei servizi di calcolo, mantenendo tuttavia lo stato dei dischi delle VM.
- Si vuole avviare un set di VM con una velocità maggiore rispetto all'aumento del numero di istanze di un set di scalabilità di macchine virtuali.
  - In relazione a questo scenario è stato creato un motore di ridimensionamento automatico proprio e si vuole ottenere una scalabilità end-to-end più rapida.
- È presente un set di scalabilità di macchine virtuali distribuito in modo non uniforme nei domini di errore o nei domini di aggiornamento. Questo problema può essere stato causato dall'eliminazione selettiva di VM o dall'eliminazione di VM dopo l'overprovisioning. L'esecuzione di `stop deallocate` seguito da `start` nel set di scalabilità di macchine virtuali consente di distribuire uniformemente le VM nei domini di errore o nei domini di aggiornamento.

