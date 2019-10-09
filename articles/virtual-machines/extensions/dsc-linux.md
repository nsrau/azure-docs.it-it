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
ms.openlocfilehash: e2faf444aa411f0e60f1b5c7b1f811abc2f6b63a
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176680"
---
# <a name="dsc-extension-for-linux-microsoftostcextensionsdscforlinux"></a>Estensione DSC per Linux (Microsoft.OSTCExtensions.DSCForLinux)

Desired State Configuration (DSC) è una piattaforma di gestione che consente di gestire l'infrastruttura IT e di sviluppo con la configurazione come codice.

> ! Si noti che l'estensione DSC per Linux e l' [estensione della macchina virtuale di monitoraggio di Azure per Linux](/azure/virtual-machines/extensions/oms-linux) attualmente presentano un conflitto e non sono supportati in una configurazione side-by-side.  Ciò significa che le due soluzioni non devono essere usate insieme nella stessa VM.

L'estensione DSCForLinux è pubblicata e supportata da Microsoft. L'estensione installa l'agente OMI e DSC nelle macchine virtuali di Azure. L'estensione DSC può eseguire anche le azioni seguenti


- Registrare la macchina virtuale Linux nell'account di Automazione di Azure per il pull delle configurazioni dal servizio Automazione di Azure (ExtensionAction Register)
- Eseguire il push delle configurazioni MOF alla macchina virtuale Linux (ExtensionAction Push)
- Applicare la metaconfigurazione MOF alla macchina virtuale Linux per configurare il server di pull in modo che esegua il pull della configurazione del nodo (ExtensionAction Pull)
- Installare moduli DSC personalizzati nella macchina virtuale Linux (ExtensionAction Install)
- Rimuovere i moduli DSC personalizzati dalla macchina virtuale Linux (ExtensionAction Remove)

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

### <a name="operating-system"></a>Sistema operativo

L'estensione DSC Linux supporta tutte le [distribuzioni Linux approvate in Azure](/azure/virtual-machines/linux/endorsed-distros) tranne:

| Distribuzione | Versione |
|---|---|
| Debian | tutte le versioni |
| Ubuntu| 18,04 |
 
### <a name="internet-connectivity"></a>Connettività Internet

L'estensione DSCForLinux richiede che la macchina virtuale di destinazione sia connessa a Internet. Per l'estensione Register, ad esempio, è necessaria la connettività al servizio di automazione. Per altre azioni, ad esempio Push, Pull o Install, è necessaria la connettività al servizio di archiviazione di Azure/GitHub. Dipende dalle impostazioni specificate dal cliente.

## <a name="extension-schema"></a>Schema dell'estensione

### <a name="11-public-configuration"></a>1.1 Configurazione pubblica

Di seguito sono riportati tutti i parametri della configurazione pubblica supportati:

* `FileUri`: (facoltativo, stringa) URI del file MOF/file meta.MOF/file di risorse personalizzato con estensione zip.
* `ResourceName`: (facoltativo, stringa) nome del modulo di risorse personalizzato
* `ExtensionAction`: (facoltativo, stringa) specifica le azioni compiute da un'estensione. Valori validi: Register, Push, Pull, Install, Remove. Se non specificato, viene considerata l'azione Push per impostazione predefinita.
* `NodeConfigurationName`: (facoltativo, stringa) nome di una configurazione del nodo da applicare.
* `RefreshFrequencyMins`: (facoltativo, int) specifica la frequenza (in minuti) con cui DSC tenta di ottenere la configurazione dal server di pull. 
       Se la configurazione nel server di pull è diversa da quella corrente nel nodo di destinazione, viene copiata nell'archivio di elementi in sospeso e applicata.
* `ConfigurationMode`: (facoltativo, stringa) specifica la modalità di applicazione della configurazione da parte di DSC. I valori validi sono: ApplyOnly, ApplyAndMonitor, ApplyAndAutoCorrect.
* `ConfigurationModeFrequencyMins`: (facoltativo, int) specifica la frequenza (in minuti) con cui DSC verifica che la configurazione si trovi nello stato desiderato.

> [!NOTE]
> Se si usa una versione precedente alla 2.3, il parametro relativo alla modalità equivale a ExtensionAction. La modalità sembra essere un termine di overload. Di conseguenza, per evitare confusione, dalla versione 2.3 in avanti viene usato ExtensionAction. Per garantire la compatibilità con le versioni precedenti, l'estensione li supporta entrambi. 
>

### <a name="12-protected-configuration"></a>1.2 Configurazione protetta

Di seguito sono riportati tutti i parametri della configurazione protetta supportati:

* `StorageAccountName`: (facoltativo, stringa) nome dell'account di archiviazione che contiene il file
* `StorageAccountKey`: (facoltativo, stringa) chiave dell'account di archiviazione che contiene il file
* `RegistrationUrl`: (facoltativo, stringa) URL dell'account di Automazione di Azure
* `RegistrationKey`: (facoltativo, stringa) chiave di accesso dell'account di Automazione di Azure


## <a name="scenarios"></a>Scenari

### <a name="register-to-azure-automation-account"></a>Registrarsi nell'account di Automazione di Azure
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

### <a name="apply-a-mof-configuration-file-in-azure-storage-account-to-the-vm"></a>Applicare un file di configurazione MOF (nell'account di Archiviazione di Azure) alla macchina virtuale

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


### <a name="apply-a-mof-configuration-file-in-public-storage-to-the-vm"></a>Applicare un file di configurazione MOF (in un archivio pubblico) alla macchina virtuale

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

### <a name="apply-a-meta-mof-configuration-file-in-azure-storage-account-to-the-vm"></a>Applicare un file di metaconfigurazione MOF (nell'account di Archiviazione di Azure) alla macchina virtuale

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

### <a name="install-a-custom-resource-module-zip-file-in-azure-storage-account-to-the-vm"></a>Installare un modulo di risorse personalizzato (file con estensione zip nell'account di Archiviazione di Azure) nella macchina virtuale
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

### <a name="install-a-custom-resource-module-zip-file-in-public-storage-to-the-vm"></a>Installare un modulo di risorse personalizzato (file con estensione zip in un archivio pubblico) nella macchina virtuale
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

Le estensioni macchina virtuale di Azure possono essere distribuite con i modelli di Azure Resource Manager. I modelli rappresentano la scelta migliore quando si distribuiscono una o più macchine virtuali per cui è necessaria una configurazione post-distribuzione, ad esempio l'onboarding in Automazione di Azure. 

Il modello di Resource Manager di esempio è [201-dsc-linux-azure-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-azure-storage-on-ubuntu) e [201-dsc-linux-public-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-public-storage-on-ubuntu).

Per altri dettagli sul modello di Azure Resource Manager, vedere [Creazione di modelli di Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md).


## <a name="azure-cli-deployment"></a>Distribuzione dell'interfaccia della riga di comando di Azure

### <a name="21-using-azure-cliazure-cli"></a>2.1. Uso dell'[**interfaccia della riga di comando di Azure**][azure-cli]
Prima di distribuire l'estensione DSCForLinux è necessario configurare i file `public.json` e `protected.json` in base ai diversi scenari descritti nella sezione 3.

#### <a name="211-classic"></a>2.1.1. Classico
La modalità classica viene anche denominata modalità Gestione dei servizi di Azure. Per passare a questa modalità, eseguire:
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

#### <a name="212-resource-manager"></a>2.1.2. Gestione risorse
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
> Nella modalità Azure Resource Manager, `azure vm extension list` non è al momento disponibile.
>

### <a name="22-using-azure-powershellazure-powershell"></a>2.2. Uso di [**Azure PowerShell**][azure-powershell]

#### <a name="221-classic"></a>2.2.1 Classica

È possibile accedere al proprio account Azure (modalità Gestione dei servizi di Azure) eseguendo:

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

È necessario modificare il contenuto di $privateConfig e $publicConfig in base ai diversi scenari descritti nella sezione precedente 
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

#### <a name="222resource-manager"></a>2.2.2.Resource Manager

È possibile accedere al proprio account Azure (modalità Azure Resource Manager) eseguendo:

```powershell>
Login-AzAccount
```

Fare clic [**QUI**](../../azure-resource-manager/manage-resources-powershell.md) per altre informazioni sull'uso di Azure PowerShell con Azure Resource Manager.

È possibile distribuire l'estensione DSCForLinux eseguendo:

```powershell>
$rgName = '<resource-group-name>'
$vmName = '<vm-name>'
$location = '< location>'
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

È necessario modificare il contenuto di $privateConfig e $publicConfig in base ai diversi scenari descritti nella sezione precedente 
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

### <a name="troubleshoot"></a>Risolvere problemi

I dati sullo stato delle distribuzioni dell'estensione possono essere recuperati nel portale di Azure e tramite l'interfaccia della riga di comando di Azure. Per visualizzare lo stato di distribuzione delle estensioni per una determinata VM, eseguire il comando seguente nell'interfaccia della riga di comando di Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

L'output dell'esecuzione dell'estensione viene registrato nel file seguente:

```
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Codice errore: 51 rappresenta una distribuzione non supportata o un'azione dell'estensione non supportata.
In alcuni casi, l'estensione DSC per Linux non riesce a installare OMI quando nel computer è già presente una versione più recente di OMI. [risposta di errore: (000003)Downgrade not allowed (Downgrade non consentito)]



### <a name="support"></a>Supporto

Per ricevere assistenza in relazione a qualsiasi punto di questo articolo, contattare gli esperti di Azure nei [forum MSDN e Stack Overflow relativi ad Azure](https://azure.microsoft.com/support/community/). In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare l'opzione desiderata per ottenere supporto. Per informazioni sull'uso del supporto di Azure, leggere le [Domande frequenti sul supporto di Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle estensioni, vedere [Estensioni e funzionalità delle macchine virtuali per Linux](features-linux.md).
