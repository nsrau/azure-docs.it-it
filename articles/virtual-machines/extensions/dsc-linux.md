---
title: Estensione DSC di Azure per Linux
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
ms.openlocfilehash: b631a370c64522c201f1208819b5a76895d83b09
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457514"
---
# <a name="dsc-extension-for-linux-microsoftostcextensionsdscforlinux"></a>Estensione DSC per Linux (Microsoft. OSTCExtensions. DSCForLinux)

DSC (Desired state Configuration) è una piattaforma di gestione che è possibile usare per gestire l'infrastruttura IT e di sviluppo con la configurazione come codice.

> [!NOTE]
> L'estensione DSC per Linux e l' [estensione della macchina virtuale di monitoraggio di Azure per Linux](/azure/virtual-machines/extensions/oms-linux) attualmente presentano un conflitto e non sono supportati in una configurazione side-by-side. Non usare le due soluzioni insieme nella stessa VM.

L'estensione DSCForLinux è pubblicata e supportata da Microsoft. L'estensione installa l'agente OMI e DSC nelle macchine virtuali di Azure. L'estensione DSC può anche eseguire le azioni seguenti:


- Registrare la VM Linux in un account di automazione di Azure per effettuare il pull delle configurazioni dal servizio di automazione di Azure (Register ExtensionAction).
- Eseguire il push delle configurazioni MOF alla VM Linux (push ExtensionAction).
- Applicare la configurazione meta MOF alla VM Linux per configurare un server di pull per eseguire il pull della configurazione del nodo (pull ExtensionAction).
- Installare moduli DSC personalizzati nella VM Linux (installare ExtensionAction).
- Rimuovere i moduli DSC personalizzati dalla VM Linux (rimuovere ExtensionAction).

 

## <a name="prerequisites"></a>prerequisiti

### <a name="operating-system"></a>Sistema operativo

L'estensione DSC Linux supporta tutte le [distribuzioni Linux approvate in Azure](/azure/virtual-machines/linux/endorsed-distros) tranne:

| Distribuzione | Version |
|---|---|
| Debian | Tutte le versioni |
| Ubuntu| 18,04 |
 
### <a name="internet-connectivity"></a>Connettività Internet

Per l'estensione DSCForLinux è necessario che la macchina virtuale di destinazione sia connessa a Internet. Ad esempio, l'estensione Register richiede la connettività al servizio di automazione. Per altre azioni, ad esempio pull, pull, l'installazione richiede la connettività ad archiviazione di Azure e GitHub. Dipende dalle impostazioni fornite dal cliente.

## <a name="extension-schema"></a>Schema dell'estensione

### <a name="public-configuration"></a>Configurazione pubblica

Di seguito sono riportati tutti i parametri della configurazione pubblica supportati:

* `FileUri`: (facoltativo, stringa) URI del file MOF, del file meta MOF o del file zip della risorsa personalizzata.
* `ResourceName`: (facoltativo, stringa) il nome del modulo della risorsa personalizzata.
* `ExtensionAction`: (facoltativo, stringa) specifica le azioni compiute da un'estensione. I valori validi sono Register, push, pull, install e Remove. Se non è specificato, viene considerata un'azione push per impostazione predefinita.
* `NodeConfigurationName`: (facoltativo, stringa) il nome della configurazione di un nodo da applicare.
* `RefreshFrequencyMins`: (facoltativo, int) specifica la frequenza (in minuti) con cui DSC tenta di ottenere la configurazione dal server di pull. 
       Se la configurazione nel server di pull è diversa da quella corrente nel nodo di destinazione, viene copiata nell'archivio in sospeso e applicata.
* `ConfigurationMode`: (facoltativo, stringa) specifica la modalità di applicazione della configurazione da parte di DSC. I valori validi sono ApplyOnly, ApplyAndMonitor e ApplyAndAutoCorrect.
* `ConfigurationModeFrequencyMins`: (facoltativo, int) specifica la frequenza (in minuti) con cui DSC verifica che la configurazione si trovi nello stato desiderato.

> [!NOTE]
> Se si usa una versione precedente alla 2,3, il parametro mode è uguale a ExtensionAction. La modalità sembra essere un termine di overload. Per evitare confusione, ExtensionAction viene usato dalla versione 2,3 in poi. Per garantire la compatibilità con le versioni precedenti, l'estensione li supporta entrambi. 
>

### <a name="protected-configuration"></a>Configurazione protetta

Di seguito sono riportati tutti i parametri della configurazione protetta supportati:

* `StorageAccountName`: (facoltativo, stringa) il nome dell'account di archiviazione che contiene il file
* `StorageAccountKey`: (facoltativo, stringa) la chiave dell'account di archiviazione che contiene il file
* `RegistrationUrl`: (facoltativo, stringa) l'URL dell'account di automazione di Azure
* `RegistrationKey`: (facoltativo, stringa) la chiave di accesso dell'account di automazione di Azure


## <a name="scenarios"></a>Scenari

### <a name="register-an-azure-automation-account"></a>Registrare un account di automazione di Azure
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

### <a name="apply-an-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>Applicare un file di configurazione MOF (in un account di archiviazione di Azure) alla macchina virtuale

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


### <a name="apply-an-mof-configuration-file-in-public-storage-to-the-vm"></a>Applicare un file di configurazione MOF (nell'archivio pubblico) alla macchina virtuale

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

### <a name="apply-a-meta-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>Applicare un file di configurazione meta MOF (in un account di archiviazione di Azure) alla macchina virtuale

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

### <a name="install-a-custom-resource-module-a-zip-file-in-an-azure-storage-account-to-the-vm"></a>Installare un modulo di risorse personalizzato (un file zip in un account di archiviazione di Azure) nella macchina virtuale
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

### <a name="install-a-custom-resource-module-a-zip-file-in-public-storage-to-the-vm"></a>Installare un modulo di risorse personalizzato (un file zip nell'archivio pubblico) nella macchina virtuale
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

Le estensioni macchina virtuale di Azure possono essere distribuite con i modelli di Azure Resource Manager. I modelli sono ideali quando si distribuiscono una o più macchine virtuali che richiedono la configurazione post-distribuzione, ad esempio l'onboarding in automazione di Azure. 

Il modello di Resource Manager di esempio è [201-dsc-linux-azure-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-azure-storage-on-ubuntu) e [201-dsc-linux-public-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-public-storage-on-ubuntu).

Per ulteriori informazioni sul modello di Azure Resource Manager, vedere [authoring Azure Resource Manager Templates](../../azure-resource-manager/resource-group-authoring-templates.md).


## <a name="azure-cli-deployment"></a>Distribuzione dell'interfaccia della riga di comando di Azure

### <a name="use-azure-cliazure-cli"></a>Usare [interfaccia della riga di comando di Azure] [Azure-CLI]
Prima di distribuire l'estensione DSCForLinux, configurare il `public.json` e `protected.json` in base ai diversi scenari descritti nella sezione 3.

#### <a name="classic"></a>Classico
La modalità di distribuzione classica è detta anche modalità di gestione dei servizi di Azure. Per passare a questa modalità, eseguire:
```
$ azure config mode asm
```

È possibile distribuire l'estensione DSCForLinux eseguendo:
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

È possibile distribuire l'estensione DSCForLinux eseguendo:
```
$ azure vm extension set <resource-group> <vm-name> \
DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```
> [!NOTE]
> In modalità Azure Resource Manager `azure vm extension list` non è disponibile per il momento.
>

### <a name="use-azure-powershellazure-powershell"></a>Usare [Azure PowerShell] [Azure-PowerShell]

#### <a name="classic"></a>Classico

Per accedere all'account Azure in modalità di gestione dei servizi di Azure, è possibile eseguire:

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

È possibile accedere al proprio account Azure in modalità Azure Resource Manager eseguendo:

```powershell>
Login-AzAccount
```

Per altre informazioni su come usare Azure PowerShell con Azure Resource Manager, vedere [gestire le risorse di Azure usando Azure PowerShell](../../azure-resource-manager/manage-resources-powershell.md).

È possibile distribuire l'estensione DSCForLinux eseguendo:

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

I dati sullo stato delle distribuzioni dell'estensione possono essere recuperati dal portale di Azure e usando l'interfaccia della riga di comando di Azure. Per visualizzare lo stato di distribuzione delle estensioni per una determinata macchina virtuale, eseguire il comando seguente usando l'interfaccia della riga di comando di Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

L'output dell'esecuzione dell'estensione viene registrato nel file seguente:

```
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Codice di errore: 51 rappresenta una distribuzione non supportata o un'azione di estensione non supportata.
In alcuni casi, l'estensione di Linux DSC non riesce a installare OMI quando nel computer esiste già una versione più recente di OMI. [Risposta di errore: (000003)Downgrade not allowed] (Downgrade non consentito)



### <a name="support"></a>Supporto

Per ulteriori informazioni in qualsiasi punto di questo articolo, contattare gli esperti di Azure nei [Forum MSDN Azure e stack overflow](https://azure.microsoft.com/support/community/). In alternativa, è possibile archiviare un evento imprevisto del supporto tecnico di Azure. Accedere al [sito del supporto tecnico di Azure](https://azure.microsoft.com/support/options/)e selezionare **ottenere supporto**. Per informazioni sull'uso del Supporto tecnico di Azure, leggere le [Domande frequenti sul supporto tecnico di Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle estensioni, vedere [Estensioni e funzionalità della macchina virtuale per Linux](features-linux.md).
