---
title: Eseguire script personalizzati nelle macchine virtuali Linux in Azure | Microsoft Docs
description: "Automatizzare le attività di configurazione delle macchine virtuali Linux usando l'estensione per script personalizzati"
services: virtual-machines-linux
documentationcenter: 
author: danielsollondon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: cf17ab2b-8d7e-4078-b6df-955c6d5071c2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: danis
ms.openlocfilehash: 53adef0f512c54e036a981dbaa0d08453db6b194
ms.sourcegitcommit: a0d2423f1f277516ab2a15fe26afbc3db2f66e33
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2018
---
# <a name="use-the-azure-custom-script-extension-with-linux-virtual-machines"></a>Usare l'estensione per script personalizzati di Azure con macchine virtuali Linux
L'estensione per script personalizzati scarica ed esegue script sulle macchine virtuali di Azure. Questa estensione è utile per la configurazione post-distribuzione, l'installazione di software o altre attività di configurazione o gestione. È possibile scaricare gli script da Archiviazione di Azure, o da un altro percorso Internet accessibile, oppure è possibile fornirli al runtime dell'estensione. 

L'estensione per script personalizzati è integrabile con i modelli di Azure Resource Manager. È anche possibile eseguirla tramite l'interfaccia della riga di comando di Azure, PowerShell, il portale di Azure o l'API REST di Macchine virtuali di Azure.

Questo articolo descrive come usare l'estensione per script personalizzati dall'interfaccia della riga di comando di Azure ed eseguire l'estensione usando un modello di Azure Resource Manager. Sono inoltre illustrate le procedure di risoluzione dei problemi per i sistemi Linux.

## <a name="extension-configuration"></a>Configurazione dell'estensione
La configurazione dell'estensione per script personalizzati specifica informazioni come il percorso dello script e il comando da eseguire. È possibile archiviare queste informazioni in file di configurazione, specificarle sulla riga di comando o definirle in un modello di Azure Resource Manager. 

I dati sensibili possono essere archiviati in una configurazione protetta, che viene crittografata e decrittografata solo all'interno della macchina virtuale. La configurazione protetta è utile quando il comando di esecuzione include segreti, ad esempio una password.

### <a name="public-configuration"></a>Configurazione pubblica
Di seguito è illustrato lo schema per la configurazione pubblica.

>[!NOTE]
>Questi nomi di proprietà fanno distinzione tra maiuscole e minuscole. Per evitare problemi nella distribuzione, usare i nomi come indicato di seguito.

* **commandToExecute** (obbligatoria, stringa): script del punto di ingresso da eseguire.
* **fileUris** (facoltativa, matrice di stringhe): URL relativi ai file da scaricare.
* **timestamp** (facoltativa, numero intero): timestamp dello script. Modificare il valore di questo campo solo se si vuole attivare un'altra esecuzione dello script.

```json
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>"
}
```

### <a name="protected-configuration"></a>Configurazione protetta
Di seguito è illustrato lo schema per la configurazione protetta.

>[!NOTE]
>Questi nomi di proprietà fanno distinzione tra maiuscole e minuscole. Per evitare problemi nella distribuzione, usare i nomi come indicato di seguito.

* **commandToExecute** (facoltativa, stringa): script del punto di ingresso da eseguire. Usare questo campo se il comando contiene segreti, ad esempio password.
* **storageAccountName** (facoltativa, stringa): nome dell'account di archiviazione. Se si specificano credenziali di archiviazione, tutti gli URI di file devono essere URL relativi a BLOB di Azure.
* **storageAccountKey** (facoltativa, stringa): chiave di accesso dell'account di archiviazione.

```json
{
  "commandToExecute": "<command-to-execute>",
  "storageAccountName": "<storage-account-name>",
  "storageAccountKey": "<storage-account-key>"
}
```

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
Quando si usa l'interfaccia della riga di comando di Azure per eseguire l'estensione per script personalizzati, creare uno o più file di configurazione, che devono contenere almeno l'URI del file e il comando di esecuzione dello script.

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json
```

Facoltativamente, è possibile specificare le impostazioni nel comando come stringa in formato JSON. Ciò consente di specificare la configurazione durante l'esecuzione e senza un file di configurazione separato.

```azurecli
az vm extension set '
  --resource-group exttest `
  --vm-name exttest `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Esempi di interfaccia della riga di comando di Azure

#### <a name="public-configuration-with-script-file"></a>Configurazione pubblica con file di script

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],
  "commandToExecute": "./config-music.sh"
}
```

Comando dell'interfaccia della riga di comando di Azure:

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json
```

#### <a name="public-configuration-with-no-script-file"></a>Configurazione pubblica senza file di script

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Comando dell'interfaccia della riga di comando di Azure:

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json
```

#### <a name="public-and-protected-configuration-files"></a>File di configurazione pubblica e protetta

Si usa un file di configurazione pubblica per specificare l'URI del file di script e un file di configurazione protetta per specificare il comando da eseguire.

File di configurazione pubblica:

```json
{
  "fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"]
}
```

File di configurazione protetta:  

```json
{
  "commandToExecute": "./hello.sh <password>"
}
```

Comando dell'interfaccia della riga di comando di Azure:

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json --protected-settings ./protected-config.json
```

## <a name="resource-manager-template"></a>Modello di Resource Manager
È possibile eseguire l'estensione per script personalizzati di Azure durante la fase di distribuzione della macchina virtuale usando un modello di Resource Manager. A tale scopo, aggiungere una risorsa JSON formattata correttamente al modello di distribuzione.

### <a name="resource-manager-examples"></a>Esempi di Resource Manager

#### <a name="public-configuration"></a>Configurazione pubblica

```json
{
    "name": "scriptextensiondemo",
    "type": "extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "2015-06-15",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('scriptextensiondemoName'))]"
    ],
    "tags": {
        "displayName": "scriptextensiondemo"
    },
    "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
      "settings": {
        "fileUris": [
          "https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"
        ],
        "commandToExecute": "sh hello.sh"
      }
    }
}
```

#### <a name="execution-command-in-protected-configuration"></a>Comando di esecuzione nella configurazione protetta

```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"
      ]              
    },
    "protectedSettings": {
      "commandToExecute": "sh hello.sh <password>"
    }
  }
}
```

Per un esempio completo, vedere la [demo .NET Music Store](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

## <a name="troubleshooting"></a>Risoluzione dei problemi
Quando viene eseguita l'estensione per script personalizzati, lo script viene creato o scaricato in una directory simile all'esempio seguente. Anche l'output del comando viene salvato in questa directory, nei file `stdout` e `stderr`.

```bash
/var/lib/waagent/custom-script/download/0/
```

L'estensione per script di Azure genera un log che è possibile trovare in questo percorso:

```bash
/var/log/azure/custom-script/handler.log
```

Lo stato dell'esecuzione dell'estensione per script personalizzati può essere recuperato anche tramite l'interfaccia della riga di comando di Azure:

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

L'output ha un aspetto simile al testo seguente:

```azurecli
info:    Executing command vm extension get
+ Looking up the VM "scripttst001"
data:    Publisher                   Name                                      Version  State
data:    --------------------------  ----------------------------------------  -------  ---------
data:    Microsoft.Azure.Extensions  CustomScript                              2.0      Succeeded
data:    Microsoft.OSTCExtensions    Microsoft.Insights.VMDiagnosticsSettings  2.3      Succeeded
info:    vm extension get command OK
```

## <a name="next-steps"></a>Passaggi successivi
Per informazioni su altre estensioni per script da usare con macchine virtuali, vedere [Panoramica sulle estensioni per script di Azure per Linux](extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

