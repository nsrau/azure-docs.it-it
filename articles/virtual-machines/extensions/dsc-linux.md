---
title: Azure DSC extension for Linux
description: Installa pacchetti OMI e DSC che consentono la configurazione di una macchina virtuale Linux di Azure tramite Desired State Configuration.
services: virtual-machines-linux
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: robreed
ms.openlocfilehash: 2f04b5ecb2019a77bbb38e97c3869cc0a9447955
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250621"
---
# <a name="dsc-extension-for-linux-microsoftostcextensionsdscforlinux"></a>Estensione DSC per Linux (Microsoft.OSTCExtensions.DSCForLinux)

DSC (Desired State Configuration) è una piattaforma di gestione che è possibile utilizzare per gestire l'infrastruttura IT e di sviluppo con la configurazione come codice.

> [!NOTE]
> L'estensione DSC per Linux e [l'estensione](/azure/virtual-machines/extensions/oms-linux) della macchina virtuale di Monitoraggio di Azure per Linux presenta attualmente un conflitto e non è supportata in una configurazione side-by-side. Non usare le due soluzioni insieme nella stessa macchina virtuale.

L'estensione DSCForLinux è pubblicata e supportata da Microsoft. L'estensione installa l'agente OMI e DSC nelle macchine virtuali di Azure. L'estensione DSC può anche eseguire le azioni seguenti:The DSC extension can also do the following actions:


- Registrare la macchina virtuale Linux in un account di Automazione di Azure per eseguire il pull delle configurazioni dal servizio di automazione di Azure (Registra ExtensionAction).
- Eseguire il push delle configurazioni MOF nella macchina virtuale Linux (Push ExtensionAction).
- Applicare la configurazione meta MOF alla macchina virtuale Linux per configurare un server di pull per il pull della configurazione del nodo (Pull ExtensionAction).
- Installare moduli DSC personalizzati nella macchina virtuale Linux (Installare ExtensionAction).
- Rimuovere i moduli DSC personalizzati dalla macchina virtuale Linux (Rimuovi ExtensionAction).

 

## <a name="prerequisites"></a>Prerequisiti

### <a name="operating-system"></a>Sistema operativo

L'estensione DSC Linux supporta tutte le [distribuzioni Linux approvate in Azure](/azure/virtual-machines/linux/endorsed-distros) tranne:

| Distribuzione | Versione |
|---|---|
| Debian | Tutte le versioni |
| Ubuntu| 18,04 |
 
### <a name="internet-connectivity"></a>Connettività Internet

L'estensione DSCForLinux richiede che la macchina virtuale di destinazione sia connessa a Internet. Ad esempio, l'estensione Register richiede la connettività al servizio di automazione. Per altre azioni, ad esempio Pull, Pull, Install richiede la connettività ad Archiviazione di Azure e GitHub.For other actions such as Pull, Pull, Install requires connectivity to Azure Storage and GitHub. Dipende dalle impostazioni fornite dal cliente.

## <a name="extension-schema"></a>Schema dell'estensione

### <a name="public-configuration"></a>Configurazione pubblica

Di seguito sono riportati tutti i parametri della configurazione pubblica supportati:

* `FileUri`: (facoltativo, stringa) L'uri del file MOF, del file MOF o del file zip di risorsa personalizzato.
* `ResourceName`: (facoltativo, stringa) Il nome del modulo di risorsa personalizzato.
* `ExtensionAction`: (facoltativo, stringa) specifica le azioni compiute da un'estensione. I valori validi sono Register, Push, Pull, Install e Remove.Valid values are Register, Push, Pull, Install, and Remove. Se non specificato, viene considerata un'azione push per impostazione predefinita.
* `NodeConfigurationName`: (facoltativo, stringa) Il nome della configurazione di un nodo da applicare.
* `RefreshFrequencyMins`: (opzionale, int) Specifica la frequenza (in minuti) con cui DSC tenta di ottenere la configurazione dal server di pull. 
       Se la configurazione nel server di pull è diversa da quella corrente nel nodo di destinazione, viene copiata nell'archivio in sospeso e applicata.
* `ConfigurationMode`: (facoltativo, stringa) specifica la modalità di applicazione della configurazione da parte di DSC. I valori validi sono ApplyOnly, ApplyAndMonitor e ApplyAndAutoCorrect.Valid values are ApplyOnly, ApplyAndMonitor, and ApplyAndAutoCorrect.
* `ConfigurationModeFrequencyMins`: (facoltativo, int) specifica la frequenza (in minuti) con cui DSC verifica che la configurazione si trovi nello stato desiderato.

> [!NOTE]
> Se si utilizza una versione precedente alla 2.3, il parametro mode è lo stesso di ExtensionAction.If you use a version earlier than 2.3, the mode parameter is the same as ExtensionAction. La modalità sembra essere un termine sovraccarico. Per evitare confusione, ExtensionAction viene utilizzato dalla versione 2.3 in poi. Per garantire la compatibilità con le versioni precedenti, l'estensione li supporta entrambi. 
>

### <a name="protected-configuration"></a>Configurazione protetta

Di seguito sono riportati tutti i parametri della configurazione protetta supportati:

* `StorageAccountName`: (facoltativo, stringa) Il nome dell'account di archiviazione che contiene il file
* `StorageAccountKey`: (facoltativo, stringa) La chiave dell'account di archiviazione che contiene il file
* `RegistrationUrl`: (facoltativo, stringa) L'URL dell'account di Automazione di Azure
* `RegistrationKey`: (facoltativo, stringa) Chiave di accesso dell'account di Automazione di Azure


## <a name="scenarios"></a>Scenari

### <a name="register-an-azure-automation-account"></a>Registrare un account di Automazione di AzureRegister an Azure Automation account
protected.json
```json
{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}
```
public.json
```json
{
  "ExtensionAction" : "Register",
  "NodeConfigurationName" : "<node-configuration-name>",
  "RefreshFrequencyMins" : "<value>",
  "ConfigurationMode" : "<ApplyAndMonitor | ApplyAndAutoCorrect | ApplyOnly>",
  "ConfigurationModeFrequencyMins" : "<value>"
}
```

Formato PowerShell
```powershell
$privateConfig = '{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}'

$publicConfig = '{
  "ExtensionAction" : "Register",
  "NodeConfigurationName": "<node-configuration-name>",
  "RefreshFrequencyMins": "<value>",
  "ConfigurationMode": "<ApplyAndMonitor | ApplyAndAutoCorrect | ApplyOnly>",
  "ConfigurationModeFrequencyMins": "<value>"
}'
```

### <a name="apply-an-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>Applicare un file di configurazione MOF (in un account di archiviazione di Azure) alla macchina virtualeApply an MOF configuration file (in an Azure storage account) to the VM

protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.json
```json
{
  "FileUri": "<mof-file-uri>",
  "ExtensionAction": "Push"
}
```

Formato PowerShell
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "FileUri": "<mof-file-uri>",
  "ExtensionAction": "Push"
}'
```


### <a name="apply-an-mof-configuration-file-in-public-storage-to-the-vm"></a>Applicare un file di configurazione MOF (nell'archiviazione pubblica) alla macchina virtualeApply an MOF configuration file (in public storage) to the VM

public.json
```json
{
  "FileUri": "<mof-file-uri>"
}
```

Formato PowerShell
```powershell
$publicConfig = '{
  "FileUri": "<mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>Applicare un file di configurazione Meta MOF (in un account di archiviazione di Azure) alla macchina virtualeApply a meta MOF configuration file (in an Azure storage account) to the VM

protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.json
```json
{
  "ExtensionAction": "Pull",
  "FileUri": "<meta-mof-file-uri>"
}
```

Formato PowerShell
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "ExtensionAction": "Pull",
  "FileUri": "<meta-mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-public-storage-to-the-vm"></a>Applicare un file di metaconfigurazione MOF (in un archivio pubblico) alla macchina virtuale
public.json
```json
{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}
```
Formato PowerShell
```powershell
$publicConfig = '{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}'
```

### <a name="install-a-custom-resource-module-a-zip-file-in-an-azure-storage-account-to-the-vm"></a>Installare un modulo di risorse personalizzato (un file zip in un account di archiviazione di Azure) nella macchina virtualeInstall a custom resource module (a zip file in an Azure storage account) to the VM
protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```
public.json
```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```

Formato PowerShell
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}'
```

### <a name="install-a-custom-resource-module-a-zip-file-in-public-storage-to-the-vm"></a>Installare un modulo di risorse personalizzato (un file zip nell'archivio pubblico) nella macchina virtualeInstall a custom resource module (a zip file in public storage) to the VM
public.json
```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```
Formato PowerShell
```powershell
$publicConfig = '{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}'
```

### <a name="remove-a-custom-resource-module-from-the-vm"></a>Rimuovere un modulo di risorse personalizzato dalla macchina virtuale
public.json
```json
{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}
```
Formato PowerShell
```powershell
$publicConfig = '{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}'
```

## <a name="template-deployment"></a>Distribuzione del modello

Le estensioni macchina virtuale di Azure possono essere distribuite con i modelli di Azure Resource Manager. I modelli sono ideali quando si distribuiscono una o più macchine virtuali che richiedono la configurazione post-distribuzione, ad esempio l'onboarding in Automazione di Azure.Templates are ideal when you deploy one or more virtual machines that require post-deployment configuration, such as onboarding to Azure Automation. 

Il modello di Resource Manager di esempio è [201-dsc-linux-azure-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-azure-storage-on-ubuntu) e [201-dsc-linux-public-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-public-storage-on-ubuntu).

Per altre informazioni sul modello di Azure Resource Manager, vedere Creazione di modelli di Azure Resource Manager.For more information about the Azure Resource Manager template, see [Authoring Azure Resource Manager templates](../../azure-resource-manager/templates/template-syntax.md).


## <a name="azure-cli-deployment"></a>Distribuzione dell'interfaccia della riga di comando di Azure

### <a name="use-azure-cliazure-cli"></a>Usare [Azure CLI][azure-cli]
Prima di distribuire l'estensione DSCForLinux, configurare e `public.json` `protected.json` in base ai diversi scenari nella sezione 3.

#### <a name="classic"></a>Classico

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

La modalità di distribuzione classica è detta anche modalità di gestione dei servizi di Azure.The classic deployment mode is also called Azure Service Management mode. Per passare a questa modalità, eseguire:
```
$ azure config mode asm
```

È possibile distribuire l'estensione DSCForLinux eseguendo:You can deploy the DSCForLinux extension by running:
```
$ azure vm extension set <vm-name> DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```

Per conoscere la versione più recente dell'estensione, eseguire:
```
$ azure vm extension list
```

#### <a name="resource-manager"></a>Gestione risorse
È possibile passare alla modalità Azure Resource Manager eseguendo:
```
$ azure config mode arm
```

È possibile distribuire l'estensione DSCForLinux eseguendo:You can deploy the DSCForLinux extension by running:
```
$ azure vm extension set <resource-group> <vm-name> \
DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```
> [!NOTE]
> In modalità Azure `azure vm extension list` Resource Manager non è disponibile per il momento.
>

### <a name="use-azure-powershellazure-powershell"></a>Usare [Azure PowerShell][azure-powershell]

#### <a name="classic"></a>Classico

È possibile accedere all'account azure in modalità di gestione dei servizi di Azure eseguendo:You can sign in to your Azure account in Azure Service Management mode by running:

```powershell>
Add-AzureAccount
```

E distribuire l'estensione DSCForLinux eseguendo:

```powershell>
$vmname = '<vm-name>'
$vm = Get-AzureVM -ServiceName $vmname -Name $vmname
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Modificare il contenuto di $privateConfig e $publicConfig in base a diversi scenari nella sezione precedente.
```
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'
```

```
$publicConfig = '{
  "ExtensionAction": "Push",
  "FileUri": "<mof-file-uri>"
}'
```

```
Set-AzureVMExtension -ExtensionName $extensionName -VM $vm -Publisher $publisher `
  -Version $version -PrivateConfiguration $privateConfig `
  -PublicConfiguration $publicConfig | Update-AzureVM
```

#### <a name="resource-manager"></a>Gestione risorse

È possibile accedere all'account Azure in modalità Azure Resource Manager eseguendo:You can sign in to your Azure account in Azure Resource Manager mode by running:

```powershell>
Login-AzAccount
```

Per altre informazioni su come usare Azure PowerShell con Azure Resource Manager, vedere Gestire le risorse di Azure tramite Azure PowerShell.To learn more about how to use Azure PowerShell with Azure Resource Manager, see [Manage Azure resources by using Azure PowerShell.](../../azure-resource-manager/management/manage-resources-powershell.md)

È possibile distribuire l'estensione DSCForLinux eseguendo:You can deploy the DSCForLinux extension by running:

```powershell>
$rgName = '<resource-group-name>'
$vmName = '<vm-name>'
$location = '< location>'
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Modificare il contenuto di $privateConfig e $publicConfig in base a diversi scenari nella sezione precedente.
```
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'
```

```
$publicConfig = '{
  "ExtensionAction": "Push",
  "FileUri": "<mof-file-uri>"
}'
```

```
Set-AzVMExtension -ResourceGroupName $rgName -VMName $vmName -Location $location `
  -Name $extensionName -Publisher $publisher -ExtensionType $extensionName `
  -TypeHandlerVersion $version -SettingString $publicConfig -ProtectedSettingString $privateConfig
```

## <a name="troubleshoot-and-support"></a>Risoluzione dei problemi e supporto

### <a name="troubleshoot"></a>Risolvere i problemi

I dati sullo stato delle distribuzioni di estensioni possono essere recuperati dal portale di Azure e tramite l'interfaccia della riga di comando di Azure.Data about the state of extension deployments can be retrieved from the Azure portal and by using the Azure CLI. Per visualizzare lo stato di distribuzione delle estensioni per una determinata macchina virtuale, eseguire il comando seguente usando l'interfaccia della riga di comando di Azure.To see the deployment state of extensions for a given VM, run the following command by using the Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

L'output dell'esecuzione dell'estensione viene registrato nel file seguente:

```
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Codice di errore: 51 rappresenta la distribuzione non supportata o l'azione di estensione non supportata.
In alcuni casi, l'estensione DSC Linux non riesce a installare OMI quando esiste già una versione superiore di OMI nella macchina. [Risposta di errore: (000003)Downgrade not allowed] (Downgrade non consentito)



### <a name="support"></a>Supporto

Per altre informazioni in qualsiasi momento di questo articolo, contattare gli esperti di Azure nei [forum MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/community/). In alternativa, è possibile archiviare un evento imprevisto di supporto di Azure.Alternatively, you can file an Azure Support incident. Passare al [sito del supporto](https://azure.microsoft.com/support/options/)di Azure e selezionare Ottieni **supporto**. Per informazioni sull'uso del supporto di Azure, leggere le [domande frequenti sul supporto di Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle estensioni, vedere [Estensioni e funzionalità delle macchine virtuali per Linux](features-linux.md).
