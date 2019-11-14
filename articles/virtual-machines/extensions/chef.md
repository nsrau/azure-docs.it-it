---
title: Estensione chef per macchine virtuali di Azure
description: Distribuire Chef Client in una macchina virtuale usando l'estensione macchina virtuale Chef.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 09/21/2018
ms.author: akjosh
ms.openlocfilehash: 2b69a17c7f9de62187d9dc99f7c1d5c5b74c25ad
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073195"
---
# <a name="chef-vm-extension-for-linux-and-windows"></a>Estensione macchina virtuale Chef per Linux e Windows

Chef Software offre una piattaforma di automazione DevOps per Linux e Windows che consente la gestione di configurazioni di server fisici e virtuali. L'estensione macchina virtuale Chef è un'estensione che abilita Chef nelle macchine virtuali.

## <a name="prerequisites"></a>prerequisiti

### <a name="operating-system"></a>Sistema operativo

L'estensione macchina virtuale Chef è supportata in tutti i [sistemi operativi supportati dall'estensione](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems) in Azure.

### <a name="internet-connectivity"></a>Connettività Internet

L'estensione macchina virtuale Chef richiede che la macchina virtuale di destinazione sia connessa a Internet per recuperare il payload di Chef Client dalla rete per la distribuzione di contenuti (CDN).  

## <a name="extension-schema"></a>Schema dell'estensione

Il codice JSON riportato di seguito mostra lo schema dell'estensione macchina virtuale Chef. L'estensione richiede come minimo l'URL del server Chef, il nome del client di convalida e la chiave di convalida per il server Chef. Questi valori sono reperibili nel file `knife.rb` nel file starter-kit.zip che viene scaricato quando si installa [Chef Automate](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate) o un [serve Chef](https://downloads.chef.io/chef-server) autonomo. Dato che la chiave di convalida deve essere trattata come dati sensibili, deve essere configurata nell'ambito dell'elemento **protectedSettings** e ciò significa che verrà decrittografata solo nella macchina virtuale di destinazione.

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(variables('vmName'),'/', parameters('chef_vm_extension_type'))]",
  "apiVersion": "2017-12-01",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
  ],
  "properties": {
    "publisher": "Chef.Bootstrap.WindowsAzure",
    "type": "[parameters('chef_vm_extension_type')]",
    "typeHandlerVersion": "1210.12",
    "settings": {
      "bootstrap_options": {
        "chef_server_url": "[parameters('chef_server_url')]",
        "validation_client_name": "[parameters('chef_validation_client_name')]"
      },
      "runlist": "[parameters('chef_runlist')]"
    },
    "protectedSettings": {
      "validation_key": "[parameters('chef_validation_key')]"
    }
  }
}  
```

### <a name="core-property-values"></a>Valori delle proprietà principali

| Nome | Valore/Esempio | Tipo di dati
| ---- | ---- | ----
| apiVersion | `2017-12-01` | string (date) |
| publisher | `Chef.Bootstrap.WindowsAzure` | stringa |
| type | `LinuxChefClient` (Linux), `ChefClient` (Windows) | stringa |
| typeHandlerVersion | `1210.12` | string (double) |

### <a name="settings"></a>Settings

| Nome | Valore/Esempio | Tipo di dati | Obbligatorio?
| ---- | ---- | ---- | ----
| settings/bootstrap_options/chef_server_url | `https://api.chef.io/organizations/myorg` | string (url) | S |
| settings/bootstrap_options/validation_client_name | `myorg-validator` | stringa | S |
| settings/runlist | `recipe[mycookbook::default]` | stringa | S |

### <a name="protected-settings"></a>Impostazioni protette

| Nome | Esempio | Tipo di dati | Obbligatorio?
| ---- | ---- | ---- | ---- |
| protectedSettings/validation_key | `-----BEGIN RSA PRIVATE KEY-----\nKEYDATA\n-----END RSA PRIVATE KEY-----` | stringa | S |

<!--
### Linux-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |

### Windows-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |
-->

## <a name="template-deployment"></a>Distribuzione del modello

Le estensioni macchina virtuale di Azure possono essere distribuite con i modelli di Azure Resource Manager. È possibile usare i modelli per distribuire una o più macchine virtuali, installare il client Chef, connettersi al server Chef e quindi eseguire la configurazione iniziale nel server come definito da [Run-list](https://docs.chef.io/run_lists.html)

Un modello di Gestione risorse di esempio che include l'estensione della macchina virtuale chef è disponibile nella [raccolta di avvio rapido di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/chef-json-parameters-linux-vm).

La configurazione JSON per un'estensione macchina virtuale può essere annidata nella risorsa della macchina virtuale o posizionata nel livello radice o nel livello superiore di un modello JSON di Gestione risorse. Il posizionamento della configurazione JSON influisce sul valore del nome e del tipo di risorsa. Per altre informazioni, vedere [Set name and type for child resources](../../azure-resource-manager/resource-manager-template-child-resource.md) (Impostare il nome e il tipo per le risorse figlio).

## <a name="azure-cli-deployment"></a>Distribuzione dell'interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure può essere usata per distribuire l'estensione macchina virtuale Chef in una macchina virtuale esistente. Sostituire **validation_key** con il contenuto della chiave di convalida (questo file come estensione `.pem`).  Sostituire **validation_client_name**, **chef_server_url** e **run_list** con i valori dal file `knife.rb` nello starter kit.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myExistingVM \
  --name LinuxChefClient \
  --publisher Chef.Bootstrap.WindowsAzure \
  --version 1210.12 --protected-settings '{"validation_key": "<validation_key>"}' \
  --settings '{ "bootstrap_options": { "chef_server_url": "<chef_server_url>", "validation_client_name": "<validation_client_name>" }, "runlist": "<run_list>" }'
```

## <a name="troubleshooting-and-support"></a>Risoluzione dei problemi e supporto

I dati sullo stato delle distribuzioni dell'estensione possono essere recuperati nel portale di Azure e tramite l'interfaccia della riga di comando di Azure. Per visualizzare lo stato di distribuzione delle estensioni per una determinata macchina virtuale, eseguire il comando seguente nell'interfaccia della riga di comando di Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myExistingVM -o table
```

L'output dell'esecuzione dell'estensione viene registrato nel file seguente:

### <a name="linux"></a>Linux

```bash
/var/lib/waagent/Chef.Bootstrap.WindowsAzure.LinuxChefClient
```

### <a name="windows"></a>Windows

```powershell
C:\Packages\Plugins\Chef.Bootstrap.WindowsAzure.ChefClient\
```

### <a name="error-codes-and-their-meanings"></a>Codici di errore e relativi significati

| Codice di errore | Significato | Azione possibile |
| :---: | --- | --- |
| 51 | Questa estensione non è supportata nel sistema operativo della macchina virtuale | |

Altre informazioni sulla risoluzione dei problemi sono disponibili nel [file Leggimi dell'estensione macchina virtuale Chef](https://github.com/chef-partners/azure-chef-extension).

## <a name="next-steps"></a>Passaggi successivi

Per ricevere assistenza in relazione a qualsiasi punto di questo articolo, contattare gli esperti di Azure nei [forum MSDN e Stack Overflow relativi ad Azure](https://azure.microsoft.com/support/forums/). In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Passare al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare Ottenere supporto. Per informazioni sull'uso del supporto di Azure, leggere le [Domande frequenti sul supporto di Azure](https://azure.microsoft.com/support/faq/).
