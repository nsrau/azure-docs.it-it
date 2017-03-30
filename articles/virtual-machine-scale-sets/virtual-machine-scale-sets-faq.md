---
title: "Domande frequenti sui set di scalabilità di macchine virtuali di Azure | Microsoft Docs"
description: "Risposte alle domande frequenti sui set di scalabilità di macchine virtuali"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/17/2017
ms.author: negat
ms.custom: na
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 850459a79c723de0eb3249cfe9ea19bc988ca318
ms.lasthandoff: 03/18/2017


---

# <a name="azure-virtual-machine-scale-sets-faq"></a>Domande frequenti sui set di scalabilità di macchine virtuali di Azure

Questo articolo contiene risposte alle domande frequenti sui set di scalabilità.

## <a name="autoscale"></a>Autoscale

### <a name="what-are-best-practices-for-azure-autoscale"></a>Quali sono le procedure consigliate per la scalabilità automatica di Azure?

Sì. Vedere https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-best-practices

### <a name="where-do-i-find-the-metric-names-for-autoscaling-using-host-based-metrics"></a>Dove è possibile trovare i nomi delle metriche per la scalabilità automatica con metriche basate su host?

https://azure.microsoft.com/documentation/articles/monitoring-supported-metrics/

### <a name="are-there-any-examples-of-autoscaling-based-on-a-service-bus-topic-and-queue-length"></a>Sono disponibili eventuali esempi di scalabilità automatica basata sull'argomento o sulla lunghezza della coda del bus di servizio?

Sì. Vedere:

https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/

Per la coda del bus di servizio:

```json
"metricName": "MessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

Per le code di archiviazione:

```json
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

Sostituire questi valori di esempio con gli URI delle risorse appropriati.


### <a name="should-we-autoscale-with-host-based-metrics-or-use-a-diagnostics-extension"></a>È consigliabile la scalabilità automatica con metriche basate su host oppure con un'estensione di diagnostica?

È possibile creare un'impostazione di scalabilità automatica in una macchina virtuale per l'uso di metriche a livello di host o per l'uso di metriche basate sul sistema operativo guest.

Vedere l'elenco di metriche supportate: https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-common-metrics. Di seguito è riportato un esempio completo per i set di scalabilità. In questo caso è stata usata la metrica della CPU a livello di host e una metrica di conteggio dei messaggi:

https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-advanced-autoscale-virtual-machine-scale-sets

### <a name="how-can-i-set-alert-rules-on-a-scale-set"></a>Come è possibile impostare regole di avviso per un set di scalabilità?

È possibile creare avvisi relativi alle metriche per i set di scalabilità tramite PowerShell o l'interfaccia della riga di comando. Vedere:

https://azure.microsoft.com/documentation/articles/insights-powershell-samples/#create-alert-rules

https://azure.microsoft.com/documentation/articles/insights-cli-samples/#work-with-alerts

L'ID risorsa di destinazione del set di scalabilità è simile al seguente: /subscriptions/yoursubscriptionid/resourceGroups/yourresourcegroup/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmssname

È possibile scegliere qualsiasi contatore delle prestazioni delle macchine virtuali come metrica per l'avviso:

https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#compute-metrics-for-windows-vm-v2-as-a-guest-os

https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#compute-metrics-for-linux-vm-v2-as-a-guest-os

### <a name="how-can-i-set-up-autoscale-on-a-scale-set-using-powershell"></a>Come è possibile configurare la scalabilità automatica per un set di scalabilità tramite PowerShell?

Vedere https://msftstack.wordpress.com/2017/03/05/how-to-add-autoscale-to-an-azure-vm-scale-set/




## <a name="certificates"></a>Certificati

### <a name="how-do-you-securely-ship-a-certificate-into-the-vm--is-there-an-example-of-provisioning-a-scale-set-to-run-a-website-where-the-ssl-for-the-website-is-shipped-securely-from-a-certificate-configuration--the-common-certificate-rotation-operation-would-amount-to-a-configuration-update-operation"></a>Come si invia un certificato nella VM in modo sicuro?  È disponibile un esempio di provisioning di un set di scalabilità per l'esecuzione di un sito Web in cui il certificato SSL per il sito stesso viene fornito in modo sicuro da una configurazione del certificato?  L'operazione di rotazione comune del certificato equivale a un'operazione di aggiornamento della configurazione.

È supportata l'installazione di certificati dei clienti direttamente nell'archivio certificati di Windows dall'insieme di credenziali delle chiavi dei clienti stessi.

Nel contesto dei set di scalabilità:

https://msdn.microsoft.com/library/mt589035.aspx

```json
        "secrets": [ {
              "sourceVault": {
                      "id": "/subscriptions/{subscriptionid}/resourceGroups/myrg1/providers/Microsoft.KeyVault/vaults/mykeyvault1"
          },
          "vaultCertificates": [ {
                      "certificateUrl": "https://mykeyvault1.vault.azure.net/secrets/{secretname}/{secret-version}",
                  "certificateStore": "certificateStoreName"
          } ]
        } ]
```

Supporta sia Windows che Linux.

### <a name="self-signed-certificate-example"></a>Esempio di certificato autofirmato:

#### <a name="create-a-self-signed-cert-in-a-keyvault"></a>Creare un certificato autofirmato in un insieme di credenziali delle chiavi

Un modo per creare un certificato autofirmato in un insieme di credenziali delle chiavi è usare le istruzioni di questo articolo su Service Fabric: https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/

Comandi di PowerShell:

```powershell
Import-Module "C:\Users\mikhegn\Downloads\Service-Fabric-master\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"

Login-AzureRmAccount

Invoke-AddCertToKeyVault -SubscriptionId <Your SubID> -ResourceGroupName KeyVault -Location westus -VaultName MikhegnVault -CertificateName VMSSCert -Password VmssCert -CreateSelfSignedCertificate -DnsName vmss.mikhegn.azure.com -OutputPath c:\users\mikhegn\desktop\
```

Il comando precedente mette a disposizione l'input per il modello di Resource Manager.

#### <a name="change-resource-manager-template"></a>Modificare il modello di Resource Manager

Aggiungere questa proprietà a "virtualMachineProfile" come parte della risorsa del set di scalabilità:

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
 

### <a name="is-there-a-way-to-specify-an-ssh-key-pair-that-i-want-to-use-for-ssh-authentication-with-a-linux-scale-set-from-a-resource-manager-template"></a>Esiste un modo per specificare una coppia di chiavi SSH da usare per l'autenticazione SSH con un set di scalabilità di Linux da un modello di Resource Manager?  

L'API REST per osProfile è simile al caso della macchina virtuale normale:
 
https://msdn.microsoft.com/library/azure/mt589035.aspx#linuxconfiguration
 
Includere un elemento `osProfile` nel modello come nell'esempio seguente:

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
 
Il blocco JSON viene usato nel modello di avvio rapido seguente:
 
https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json
 
Esaminare anche il profilo del sistema operativo in questo modello:
 
https://github.com/ExchMaster/gadgetron/blob/master/Gadgetron/Templates/grelayhost.json

### <a name="how-do-i-remove-deprecated-certificates"></a>Come si rimuovono i certificati deprecati? 

È necessario rimuovere il certificato precedente dall'elenco di certificati dell'insieme delle credenziali, ma lasciare tutti i certificati che si vogliono mantenere nel computer. Il certificato non verrà rimosso da tutte le VM, né verrà aggiunto alle nuove VM create nel set di scalabilità. Per rimuovere il certificato dalle VM esistenti è necessario scrivere un'estensione di script personalizzata che consente di rimuovere manualmente i certificati dall'archivio certificati.
 
### <a name="how-do-i-take-an-existing-ssh-public-key-and-inject-it-into-the-scale-set-ssh-layer-during-provisioning--i-would-like-to-store-the-ssh-public-key-values-in-azure-key-vault-and-then-utilize-them-in-my-resource-manager-template"></a>Come si inserisce una chiave pubblica SSH esistente nel livello SSH del set di scalabilità durante il provisioning?  L'obiettivo è archiviare i valori della chiave pubblica SSH in Azure Key Vault e quindi usarli nel modello di Resource Manager.

Se alle VM viene fornita solo una chiave SSH pubblica, non è necessario inserire le chiavi pubbliche in Azure Key Vault perché le chiavi pubbliche non sono segrete.
 
Quando si crea una VM Linux è possibile fornire le chiavi pubbliche SSH in testo normale.
Un esempio è disponibile qui:

https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json
 
In particolare:

```json
"linuxConfiguration": {  
          "ssh": {  
            "publicKeys": [ {  
              "path": "path",
              "keyData": "publickey"
            } ]
          }
```
 
Nome elemento linuxConfiguration | Obbligatorio | Tipo | Descrizione
--- | --- | --- | --- |  ---
ssh | No | Raccolta | Specifica la configurazione delle chiavi SSH per un sistema operativo Linux.
path | Sì | String | Specifica il percorso file di Linux nel quale salvare le chiavi o il certificato SSH.
keyData | Sì | String | Specifica una chiave pubblica SSH con codifica Base64.
 
### <a name="when-i-run-update-azurermvmss-after-more-than-one-certificate-from-the-same-keyvault-i-get-the-following-error"></a>Quando si esegue Update-AzureRmVmss, dopo più di un certificato dello stesso insieme di credenziali delle chiavi viene visualizzato l'errore seguente:
 
Update-AzureRmVmss: L'elenco secrets contiene istanze ripetute di /subscriptions/<my-subscription-id>/resourceGroups/internal-rg-dev/providers/Microsoft.KeyVault/vaults/internal-keyvault-dev. Questo scenario non è consentito. Perché non è possibile aggiungere due certificati dello stesso insieme di credenziali delle chiavi?
 
Questo comportamento può verificarsi se si prova ad aggiungere lo stesso insieme di credenziali due volte al posto di un nuovo elemento vaultCertificate per l'elemento sourceVault esistente. Add-AzureRmVmssSecret non funziona correttamente per l'aggiunta di altri segreti.
 
Per aggiungere altri segreti dallo stesso insieme di credenziali delle chiavi è necessario aggiornare l'elenco $vmss.properties.osProfile.secrets[0].vaultCertificates
 
È possibile visualizzare la struttura di input prevista qui: https://msdn.microsoft.com/library/azure/mt589035.aspx
 
È necessario trovare il segreto nell'oggetto del set di scalabilità avente lo stesso insieme di credenziali delle chiavi contenitore. È quindi necessario aggiungere il riferimento al certificato, ovvero l'URL e il nome dell'archivio segreti, all'elenco associato all'insieme di credenziali.

Nota: la rimozione dei certificati dalle VM tramite le API del set di scalabilità non è attualmente supportata.
 
Le nuove VM non avranno il certificato precedente, a differenza delle VM nelle quali questo certificato era già stato distribuito.
 
### <a name="is-there-a-way-to-get-certificates-pushed-to-the-scale-set-without-providing-the-password-when-the-certificate-is-in-secretstore-currently"></a>Esiste un modo per inserire i certificati nel set di scalabilità senza specificare la password quando il certificato si trova attualmente nell'archivio segreti?

Non è necessario impostare le password come hardcoded negli script. È possibile recuperarle in modo dinamico con le autorizzazioni usate dallo script di distribuzione. Se si ha uno script che sposta un certificato dall'archivio segreti all'insieme di credenziali delle chiavi, il comando dell'archivio segreti per l'ottenimento del certificato genera anche la password del file PFX.
 
### <a name="how-does-the-secrets-property-of-virtualmachineprofileosprofile-of-a-scale-set-work-why-do-you-need-sourcevault-when-you-have-to-specify-the-absolute-uri-to-a-certificate-with-certificateurl"></a>Come funziona la proprietà secrets dell'elemento virtualMachineProfile.osProfile di un set di scalabilità? Perché è necessario l'elemento sourceVault quando si deve specificare l'URI assoluto di un certificato con certificateUrl? 

Un riferimento al certificato Win RM deve essere presente nella proprietà secrets del profilo del sistema operativo. 

Lo scopo di indicare l'insieme di credenziali di origine è poter applicare criteri ACL esistenti in CSM. Senza specificare l'insieme di credenziali di origine, gli utenti non autorizzati alla distribuzione e all'accesso per i segreti in un insieme di credenziali delle chiavi potranno eseguire queste operazioni tramite il provider di risorse di calcolo. Gli ACL sono presenti anche per risorse inesistenti.

Se è stato specificato un ID dell'insieme di credenziali di origine errato, ma un URL dell'insieme di credenziali valido, verrà segnalato un errore quando si esegue il polling dell'operazione
 
### <a name="if-i-add-secrets-to-an-existing-scale-set-does-it-inject-them-in-existing-instances-or-only-new-ones"></a>I segreti aggiunti a un set di scalabilità esistente vengono inseriti nelle istanze esistenti o solo in quelle nuove? 

I certificati vengono aggiunti a tutte le macchine virtuali, comprese quelle già esistenti. Se la proprietà upgradePolicy del set di scalabilità è impostata su "Manual", il certificato viene aggiunto alla VM quando si esegue un aggiornamento manuale nella macchina virtuale.
 
### <a name="where-do-certificates-go-for-linux-vms"></a>Dove vengono salvati i certificati per le VM Linux?

Vedere https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/
  
### <a name="how-do-you-add-a-new-vault-certificate-to-a-new-certificate-object"></a>Come si aggiunge un nuovo certificato dell'insieme di credenziali a un nuovo oggetto certificato?

Per aggiungere un certificato dell'insieme di credenziali a un segreto esistente, che deve essere l'unico oggetto segreto, è possibile procedere come indicato nell'esempio di PowerShell seguente:
 
```powershell
$newVaultCertificate = New-AzureRmVmssVaultCertificateConfig -CertificateStore MY -CertificateUrl https://sansunallapps1.vault.azure.net:443/secrets/dg-private-enc/55fa0332edc44a84ad655298905f1809
 
$vmss.VirtualMachineProfile.OsProfile.Secrets[0].VaultCertificates.Add($newVaultCertificate)
 
Update-AzureRmVmss -VirtualMachineScaleSet $vmss -ResourceGroup $rg -Name $vmssName
```
 
### <a name="what-happens-to-certificates-if-you-reimage-a-vm"></a>Cosa accade ai certificati se si ricrea l'immagine di una macchina virtuale?

Se si ricrea l'immagine di una macchina virtuale, i certificati non vengono più visualizzati perché l'operazione comporta l'eliminazione dell'intero disco del sistema operativo. 
 
### <a name="what-happens-if-you-delete-a-certificate-from-the-key-vault"></a>Cosa accade se si elimina un certificato dall'insieme di credenziali delle chiavi?

Se viene eliminato il segreto nell'insieme di credenziali delle chiavi e si arrestano, deallocano e successivamente si riavviano tutte le macchine virtuali, si verificherà un errore. Questo errore si verifica perché il provider di risorse di calcolo deve recuperare i segreti dall'insieme di credenziali delle chiavi, ma non riesce a completare l'operazione. In questo scenario è possibile eliminare i certificati dal modello del set di scalabilità. 

Il provider di risorse di calcolo non rende persistenti i segreti dei clienti. Se si arrestano e deallocano tutte le macchine virtuali nel set di scalabilità, la cache viene eliminata. In questo scenario, i segreti vengono recuperati dall'insieme di credenziali delle chiavi.

Questo problema non viene riscontrato in caso di aumento del numero di istanze perché è presente una copia del segreto memorizzata nella cache dell'infrastruttura, almeno nel modello a tenant singolo.
 
### <a name="why-do-we-have-to-specify-the-exact-location-for-the-certificate-url-as-referenced-here-per-httpsazuremicrosoftcomdocumentationarticlesservice-fabric-cluster-security"></a>Perché è necessario specificare il percorso esatto dell'URL del certificato come descritto in: https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/, 
https://<name of the vault>.vault.azure.net:443/secrets/<exact location>?
 
Secondo la documentazione dell'insieme di credenziali delle chiavi, l'API REST per l'ottenimento del segreto deve restituire la versione più recente del segreto, se la versione non è specificata:
 
Metodo | URL
--- | ---
GET | https://mykeyvault.vault.azure.net/secrets/{secret-name}/{secret-version}?api-version={api-version}

Sostituire {secret-name} con il nome e {secret-version} con la versione del segreto da recuperare. La versione del segreto può essere esclusa; in questo caso verrà recuperata la versione corrente.
  
### <a name="why-does-certificate-version-have-to-be-specified-when-using-key-vault"></a>Perché è necessario specificare la versione del certificato quando si usa l'insieme di credenziali delle chiavi?

Il motivo di questo requisito è indicare chiaramente all'utente quale certificato è distribuito nelle macchine virtuali.

Se si crea una macchina virtuale, quindi si aggiorna il segreto nell'insieme di credenziali delle chiavi, il nuovo certificato non verrà scaricato nelle macchine virtuali. Le macchine virtuali faranno tuttavia riferimento al certificato e le nuove VM otterranno il nuovo segreto. Per evitare questa confusione è necessario fare riferimento a una versione esplicita del segreto.

### <a name="my-team-works-with-several-certificates-that-are-distributed-to-us-as-cer-public-keys-what-is-the-recommended-approach-is-for-deployment-of-these-certs-to-a-scale-set"></a>Il team lavora con diversi certificati che vengono distribuiti come chiavi pubbliche CER. Qual è l'approccio consigliato per la distribuzione di questi certificati in un set di scalabilità?

È possibile generare un file PFX contenente solo file CER, con X509ContentType = PFX. Caricare ad esempio il file CER come oggetto x509Certificate2 in C# o PowerShell e chiamare questo metodo: https://msdn.microsoft.com/library/24ww6yzk(v=vs.110).aspx

### <a name="i-do-not-see-an-option-for-users-to-pass-in-certificates-as-base64-strings-that-most-other-resource-providers-provide"></a>Non è presente un'opzione per passare i certificati sotto forma di stringhe Base64 fornite dalla maggior parte degli altri provider di risorse.

È possibile estrarre l'URL con versione più recente all'interno di un modello di Resource Manager per emulare questo comportamento. È possibile includere la proprietà JSON seguente nel modello di Resource Manager:

```json 
"certificateUrl": "[reference(resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults/secrets', parameters('vaultName'), parameters('secretName')), '2015-06-01').secretUriWithVersion]"
```
 
### <a name="do-we-have-to-wrap-certs-in-json-objects-in-keyvaults"></a>È necessario eseguire il wrapping dei certificati in oggetti JSON negli insiemi di credenziali delle chiavi?

Si tratta di un requisito del set di scalabilità o della VM. È anche supportato il tipo di contenuto application/x-pkcs12. Per istruzioni, vedere http://www.rahulpnath.com/blog/pfx-certificate-in-azure-key-vault/
 
Non sono attualmente supportati i file CER, che dovranno essere esportati nei contenitori PFX.





## <a name="compliance"></a>Conformità

### <a name="are-scale-sets-pci-compliant"></a>I set di scalabilità sono conformi allo standard PCI?

I set di scalabilità rappresentano un livello API thin basato sul provider di risorse di calcolo, facente parte dell'area "Piattaforma di calcolo" nella struttura dei servizi di Azure.

Dal punto di vista della conformità, i set di scalabilità sono quindi una parte fondamentale della piattaforma di calcolo di Azure. Condividono quindi gli stessi team, strumenti, processi, metodologia di distribuzione, controlli di sicurezza, JIT, monitoraggio, avvisi e così via del provider di risorse di calcolo (CRP).  I set di scalabilità sono conformi allo standard PCI perché il provider di risorse di calcolo fa parte di un'attestazione PCI DSS corrente:

Per altre informazioni, vedere: [https://www.microsoft.com/TrustCenter/Compliance/PCI](https://www.microsoft.com/TrustCenter/Compliance/PCI).






## <a name="extensions"></a>Estensioni

### <a name="how-do-you-delete-a-scale-set-extension"></a>Come si elimina l'estensione di un set di scalabilità?

Di seguito è illustrato un esempio con PowerShell:

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" 

$vmss=Remove-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "extensionName"

Update-AzureRmVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" -VirtualMacineScaleSet $vmss
```
 
L'elemento extensionName è disponibile in `$vmss`.
   
### <a name="is-there-a-scale-set-template-example-that-integrates-with-oms"></a>È disponibile un esempio di modello di set di scalabilità che si integra con OMS?

Vedere il secondo esempio qui:

https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/ServiceFabric
   
### <a name="extensions-seem-to-run-in-parallel-on-scale-sets-causing-my-custom-script-extension-to-fail-what-can-i-do-to-fix-this-behavior"></a>Le estensioni sembrano essere eseguite in parallelo nei set di scalabilità, provocando un errore nell'estensione di script personalizzata. Come si può risolvere questo problema?

Vedere https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/ 
 
 
### <a name="how-do-i-reset-the-password-for-scale-set-vms"></a>Come si reimposta la password nelle VM dei set di scalabilità?

Usare le estensioni di accesso alle VM

Di seguito è illustrato un esempio con PowerShell:

```powershell
$vmssName = "myvmss"
$vmssResourceGroup = "myvmssrg"
$publicConfig = @{"UserName" = "newuser"}
$privateConfig = @{"Password" = "********"}
 
$extName = "VMAccessAgent"
$publisher = "Microsoft.Compute"
$vmss = Get-AzureRmVmss -ResourceGroupName $vmssResourceGroup -VMScaleSetName $vmssName
$vmss = Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name $extName -Publisher $publisher -Setting $publicConfig -ProtectedSetting $privateConfig -Type $extName -TypeHandlerVersion "2.0" -AutoUpgradeMinorVersion $true
Update-AzureRmVmss -ResourceGroupName $vmssResourceGroup -Name $vmssName VirtualMachineScaleSet $vmss
```
 
 
### <a name="how-do-i-add-an-extension-to-all-vms-in-my-scale-set"></a>Come si aggiunge un'estensione a tutte le macchine virtuali del set di scalabilità?

- Se i criteri impostati prevedono l'aggiornamento automatico, ridistribuendo il modello con le nuove proprietà di estensione verranno aggiornate tutte le macchine virtuali.
- Se i criteri impostati prevedono l'aggiornamento manuale è necessario aggiornare l'estensione, quindi eseguire un'operazione manualUpdate in tutte le istanze.
  
### <a name="if-the-extensions-associated-with-an-existing-scale-set-are-updated-would-they-affect-already-existing-vms-that-is-would-the-vms-show-up-as-not-matching-the-scale-set-model-or-would-they-be-ignored-when-an-existing-machine-is-service-healed--reimaged--etc-would-the-scripts-that-are-currently-configured-on-the-scale-set-be-executed-or-would-the-ones-that-were-configured-when-the-machine-was-first-created-be-used"></a>L'aggiornamento delle estensioni associate a un set di scalabilità esistente interessa le VM già esistenti? In altre parole, le macchine virtuali saranno considerate non corrispondenti al modello del set di scalabilità oppure verranno ignorate? Quando si eseguono il ripristino del servizio, la ricreazione dell'immagine oppure operazioni simili per una macchina virtuale esistente, vengono eseguiti gli script attualmente configurati nel set di scalabilità oppure quelli configurati durante la creazione iniziale della macchina?

- Se viene aggiornata la definizione di estensione nel modello del set di scalabilità, le VM verranno aggiornate se la proprietà upgradePolicy è stata impostata su "Automatic", mentre verranno contrassegnate come non corrispondenti al modello se la proprietà upgradePolicy è impostata su "Manual". 

- Il ripristino del servizio di una VM esistente sarà considerato un riavvio e le estensioni non verranno eseguite di nuovo. La ricreazione dell'immagine è assimilabile alla sostituzione del disco del sistema operativo con l'immagine di origine. Verranno eseguite le specializzazioni del modello più recente, ad esempio le estensioni.
 
### <a name="how-do-i-get-a-scale-set-to-join-an-ad-domain"></a>Come si aggiunge un set di scalabilità a un dominio AD?

È possibile definire un'estensione come questa usando JsonADDomainExtension, ad esempio:
```json
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "joindomain",
                                "properties": {
                                    "publisher": "Microsoft.Compute",
                                    "type": "JsonADDomainExtension",
                                    "typeHandlerVersion": "1.0",
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
 
### <a name="my-scale-set-extension-is-trying-to-install-something-that-requires-a-reboot-for-instance-commandtoexecute-powershellexe--executionpolicy-unrestricted-install-windowsfeature-name-fs-resource-manager-includemanagementtools"></a>L'estensione del set di scalabilità prova a eseguire un'installazione che richiede il riavvio, ad esempio "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted Install-WindowsFeature –Name FS-Resource-Manager –IncludeManagementTools"

È possibile usare l'estensione DSC. Se il sistema operativo è 2012 R2, Azure esegue il pull del programma di installazione WMF5.0, riavvia e prosegue con la configurazione. 
 
### <a name="how-can-i-enable-antimalware-on-my-scale-set"></a>Come si abilita l'antimalware nel set di scalabilità?

Di seguito è illustrato un esempio di PowerShell:

```powershell
$rgname = 'autolap'
$vmssname = 'autolapbr'
$location = 'eastus'
 
# retrieve the most recent version number of the extension
$allVersions= (Get-AzureRmVMExtensionImage -Location $location -PublisherName "Microsoft.Azure.Security" -Type "IaaSAntimalware").Version
$versionString = $allVersions[($allVersions.count)-1].Split(".")[0] + "." + $allVersions[($allVersions.count)-1].Split(".")[1]
 
$VMSS = Get-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname
echo $VMSS
Add-AzureRmVmssExtension -VirtualMachineScaleSet $VMSS -Name "IaaSAntimalware" -Publisher "Microsoft.Azure.Security" -Type "IaaSAntimalware" -TypeHandlerVersion $versionString
Update-AzureRmVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $VMSS 
```

### <a name="i-need-to-execute-a-custom-script-hosted-on-a-private-storage-account-i-have-no-problems-when-the-storage-is-public-but-when-i-try-to-use-a-shared-access-signaturesas-it-fails-with-the-error-missing-mandatory-parameters-for-valid-shared-access-signature-i-know-that-linksas-works-fine-from-my-local-browser"></a>È necessario eseguire uno script personalizzato ospitato in un account di archiviazione privata. Non si verificano problemi quando la risorsa di archiviazione è pubblica, ma se si prova a usare la firma di accesso condiviso viene visualizzato l'errore: "Parametri obbligatori mancanti per la firma di accesso condiviso valida". Nel browser locale, il collegamento e la firma di accesso condiviso funzionano correttamente.

Per questo scenario è necessario configurare impostazioni protette con la chiave e il nome dell'account di archiviazione. Vedere https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/#template-example-for-a-windows-vm-with-protected-settings







## <a name="networking"></a>Rete
 
### <a name="how-do-i-do-vip-swap-for-scale-sets-in-the-same-subscription-and-same-region"></a>Come si esegue lo scambio di indirizzi VIP per i set di scalabilità che si trovano nella stessa sottoscrizione e nella stessa area?

Vedere: https://msftstack.wordpress.com/2017/02/24/vip-swap-blue-green-deployment-in-azure-resource-manager/ 
 
  
### <a name="what-is-the-resourceguid-property-on-a-nic-for"></a>A cosa serve la proprietà resourceGuid in una scheda di interfaccia di rete?

Si tratta di un ID univoco. I livelli inferiori registreranno questo ID in futuro. 
 
### <a name="how-do-i-specify-a-range-of-private-ip-addresses-for-static-private-ip-address-allocation"></a>Come è possibile specificare un intervallo di indirizzi IP privati per l'allocazione di indirizzi IP privati statici?

Gli indirizzi IP vengono selezionati da una subnet specificata. 

Il metodo di allocazione degli indirizzi IP del set di scalabilità è sempre "Dinamico". Questo non significa tuttavia che gli indirizzi IP possono cambiare, ma semplicemente che non viene specificato l'indirizzo IP nella richiesta PUT. In altre parole viene specificato il set statico tramite la subnet. 
    
### <a name="how-do-i-deploy-a-scale-set-into-an-existing-vnet"></a>Come si distribuisce un set di scalabilità in una rete virtuale esistente? 

Vedere https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-existing-vnet 

### <a name="how-do-i-add-a-scale-sets-first-vms-ip-address-to-the-output-of-a-template"></a>Come aggiungere un indirizzo IP della prima macchina virtuale del set di scalabilità all'output di un modello?

Vedere: http://stackoverflow.com/questions/42790392/arm-get-vmsss-private-ips



## <a name="scale"></a>Scalabilità

### <a name="why-would-you-ever-create-a-scale-set-with-fewer-than-2-vms"></a>Perché creare un set di scalabilità con meno di 2 macchine virtuali?

Un motivo può essere l'uso delle proprietà elastiche di un set di scalabilità. È ad esempio possibile distribuire un set di scalabilità con zero macchine virtuali per definire l'infrastruttura senza sostenere i costi operativi delle macchine virtuali. Per distribuire le macchine virtuali, aumentare la "capacità" del set di scalabilità fino al numero di istanze di produzione.

Un altro motivo è quando si esegue un'operazione con il set di scalabilità per la quale la disponibilità non è importante come nell'uso di un set di disponibilità con macchine virtuali discrete. I set di scalabilità consentono di usare unità di calcolo indifferenziate fungibili. Questa uniformità è un elemento di differenziazione chiave tra set di scalabilità e set di disponibilità. Per molti carichi di lavoro senza stato, le singole unità non sono importanti ed è possibile ridurle fino a una sola unità di calcolo se il carico di lavoro diminuisce, per poi aumentarle di nuovo quando il carico di lavoro aumenta.

### <a name="how-do-you-change-the-number-of-vms-in-a-scale-set"></a>Come si modifica il numero di VM in un set di scalabilità?

Vedere: https://msftstack.wordpress.com/2016/05/13/change-the-instance-count-of-an-azure-vm-scale-set/

### <a name="how-can-you-define-custom-alerts-for-when-certain-thresholds-are-reached"></a>Come è possibile definire avvisi personalizzati per il raggiungimento di determinate soglie?

La gestione degli avvisi consente una certa flessibilità, ad esempio è possibile definire webhook personalizzati come in questo esempio di un modello di Resource Manager:
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
                     "notifications": [{
                               "operation": "Scale",
                              "email": {
                                     "sendToSubscriptionAdministrator": true,
                                     "sendToSubscriptionCoAdministrators": true,
                                     "customEmails": [
                                        "youremail@address.com"
                                     ]},
                              "webhooks": [{
                                    "serviceUri": "https://events.pagerduty.com/integration/0b75b57246814149b4d87fa6e1273687/enqueue",
                                    "properties": {
                                        "key1": "custommetric",
                                        "key2": "scalevmss"
                                    }
                                    }
                              ]}],
```

In questo esempio viene inviato un avviso a Pagerduty al raggiungimento di una soglia.



## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="how-do-i-enable-boot-diagnostics"></a>Come si abilita la diagnostica di avvio?

Creare un account di archiviazione e inserire questo blocco JSON nell'elemento virtualMachineProfile del set di scalabilità, quindi aggiornare il set:
```json
      "diagnosticsProfile": {
        "bootDiagnostics": {
          "enabled": true,
          "storageUri": "http://yourstorageaccount.blob.core.windows.net"
        }
      }
```

Quando viene creata una nuova macchina virtuale, l'oggetto InstanceView della VM visualizza i dettagli dello screenshot e così via. Ad esempio:
 
```json
"bootDiagnostics": {
    "consoleScreenshotBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.screenshot.bmp",
    "serialConsoleLogBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.serialconsole.log"
  }
```

 

## <a name="updates"></a>Aggiornamenti

### <a name="how-to-i-update-my-scale-set-to-a-new-image-and-manage-patching"></a>Come è possibile aggiornare il set di scalabilità con una nuova immagine e gestire l'applicazione di patch?

Vedere: https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set

### <a name="can-you-use-the-reimage-operation-to-reset-a-vm-without-changing-the-image-that-is-reset-a-vm-to-factory-settings-rather-than-to-a-new-image"></a>È possibile usare l'operazione di ricreazione dell'immagine per ripristinare una macchina virtuale senza modificare l'immagine, ovvero ripristinare le impostazioni predefinite di una VM invece di usare una nuova immagine?

Sì. Vedere: https://docs.microsoft.com/rest/api/virtualmachinescalesets/manage-all-vms-in-a-set

Se tuttavia il set di scalabilità fa riferimento a un'immagine di piattaforma con versione "latest", la macchina virtuale può essere aggiornata a un'immagine del sistema operativo successiva quando si esegue la ricreazione dell'immagine.







## <a name="vm-properties"></a>Proprietà della macchina virtuale

### <a name="how-do-i-get-property-information-for-each-vm-without-having-to-make-multiple-calls-for-example-getting-the-fault-domain-for-each-vm-in-my-100-scale-set"></a>Come si possono ottenere informazioni sulle proprietà di ogni macchina virtuale senza dover eseguire più chiamate? Come si ottiene ad esempio il dominio di errore di ogni macchina virtuale in un set di scalabilità da 100?

È possibile chiamare ListVMInstanceViews eseguendo un comando `GET` dell'API REST nell'URI risorsa seguente:

`/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.Compute/virtualMachineScaleSets/<scaleset_name>/virtualMachines?$expand=instanceView&$select=instanceView`

### <a name="are-there-ways-to-pass-different-extension-arguments-to-different-vms-in-a-scale-set"></a>È possibile passare argomenti di estensione diversi a macchine virtuali differenti in un set di scalabilità?

No, ma le estensioni possono funzionare in base alle proprietà univoche della macchina virtuale sulla quale sono in esecuzione, ad esempio il nome della macchina. Le estensioni possono anche eseguire query sui metadati dell'istanza su http://169.254.169.254 per ottenere altre informazioni.

### <a name="why-are-there-gaps-between-my-scale-set-vm-machine-names-and-vm-ids-for-example-0-1-3"></a>Perché sono presenti gap tra i nomi delle macchine virtuali del set di scalabilità e gli ID, ad esempio 0, 1, 3?

Sono presenti gap perché la proprietà overprovision del set di scalabilità è impostata sul valore predefinito true. Con il provisioning eccessivo impostato su true vengono create più macchine virtuali rispetto a quelle richieste e le macchine virtuali aggiuntive vengono successivamente eliminate. Si ottiene una maggiore affidabilità di distribuzione a scapito della contiguità di denominazioni e regole NAT. È possibile impostare questa proprietà su false; per i set di scalabilità di piccole dimensioni non fa molta differenza in termini di affidabilità di distribuzione.

### <a name="what-is-the-difference-between-deleting-a-vm-in-a-scale-set-vs-deallocating-the-vm-when-should-i-choose-one-over-the-other"></a>Qual è la differenza tra l'eliminazione di una VM in un set di scalabilità e la deallocazione della VM? Quando scegliere l'una o l'altra?

La differenza principale è che la deallocazione non elimina i dischi rigidi virtuali, quindi esistono costi di archiviazione associati all'arresto e alla deallocazione. Le ragioni per usare una piuttosto che l'altra includono:

- Non si vogliono più sostenere i costi dei servizi di calcolo, mantenendo tuttavia lo stato dei dischi delle VM.
- Si vuole avviare un set di macchine virtuali più rapidamente rispetto all'aumento del numero di istanze di un set di scalabilità.
  - In relazione a questo scenario è stato creato un motore di ridimensionamento automatico proprio e si vuole ottenere una scalabilità end-to-end più rapida.
  - È presente un set di scalabilità distribuito non uniformemente tra domini di errore e domini di aggiornamento, a causa dell'eliminazione selettiva di VM o dell'eliminazione delle VM dopo il provisioning eccessivo. Con l'arresto, la deallocazione e il successivo avvio eseguiti nel set di scalabilità sarà possibile distribuire uniformemente le VM tra domini di errore e domini di aggiornamento.








 
   

