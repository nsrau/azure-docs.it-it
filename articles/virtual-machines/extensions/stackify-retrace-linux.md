---
title: Estensione dell'agente Linux di Azure dello Stackify Retrace | Microsoft Docs
description: Distribuire l'agente Linux di Stackify Retrace in una macchina virtuale di Linux.
services: virtual-machines-linux
documentationcenter: ''
author: darinhoward
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/12/2018
ms.author: roiyz
ms.openlocfilehash: ca3232dc3862cffb67d396b3ec2333fdc28dbb65
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706648"
---
# <a name="stackify-retrace-linux-agent-extension"></a>Estensione dell'agente Linux dello Stackify Retrace

## <a name="overview"></a>Panoramica

Stackify fornisce i prodotti che rilevano i dettagli relativi all'applicazione per individuare e risolvere rapidamente i problemi. Per i team di sviluppatori, Retrace è una super potenza dalle prestazioni delle app completamente integrate e multi-ambiente. Combina diversi strumenti di cui ogni team di sviluppatori ha bisogno.

Retrace è il SOLO strumento che offre tutte le seguenti funzionalità per tutti gli ambienti in una singola piattaforma.

* Gestione delle prestazioni delle applicazioni (APM)
* Applicazione e registrazione del server
* Rilevamento degli errori e monitoraggio
* Server, applicazione e metriche personalizzate

**Estensione dell'agente Linux di Stackify**

Questa estensione fornisce un percorso di installazione per l'agente Linux per Retrace. 

## <a name="prerequisites"></a>Prerequisiti

### <a name="operating-system"></a>Sistema operativo 

L'agente di Retrace può essere eseguito in queste distribuzioni di Linux

| Distribuzione | Version |
|---|---|
| Ubuntu | 16.04 LTS, 14.04 LTS, 16.10 e 17.04 |
| Debian | 7.9+, 8.2+ e 9 |
| Red Hat | 6.7+, 7.1+ |
| CentOS | 6.3+, 7.0+ |

### <a name="internet-connectivity"></a>Connettività Internet

Per distribuire l'estensione agente Stackify per Linux, è necessario che la macchina virtuale di destinazione sia connessa a Internet. 

Potrebbe essere necessario modificare la configurazione di rete per consentire le connessioni a Stackify, vedere https://support.stackify.com/hc/en-us/articles/207891903-Adding-Exceptions-to-a-Firewall. 


## <a name="extension-schema"></a>Schema dell'estensione

---

Il codice JSON riportato di seguito mostra lo schema dell'estensione dell'agente di Stackify Retrace. L'estensione richiede `environment` e `activationKey`.

```json
    {
      "type": "extensions",
      "name": "StackifyExtension",
      "apiVersion": "[variables('apiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines',variables('vmName'))]"
      ],
      "properties": {
        "publisher": "Stackify.LinuxAgent.Extension",
        "type": "StackifyLinuxAgentExtension",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "environment": "myEnvironment"
        },
        "protectedSettings": {
          "activationKey": "myActivationKey"
        }
      }
    }      
```

## <a name="template-deployment"></a>Distribuzione del modello 

Le estensioni macchina virtuale di Azure possono essere distribuite con i modelli di Azure Resource Manager. Lo schema JSON indicato nella sezione precedente può essere usato in un modello di Azure Resource Manager per eseguire l'estensione agente Linux di Stackify Retrace durante la distribuzione di un modello di Azure Resource Manager.  

Il codice JSON per un'estensione della macchina virtuale può essere nidificato nella risorsa della macchina virtuale o posizionato nel livello radice o nel livello superiore di un modello JSON di Resource Manager. Il posizionamento di JSON influisce sul valore del nome e tipo di risorsa. Per altre informazioni, vedere Impostare il nome e il tipo per le risorse figlio.

L'esempio seguente presuppone che l'estensione Linux di Stackify Retrace sia annidata all'interno della risorsa della macchina virtuale. Quando la risorsa di estensione viene nidificata, JSON viene inserito nell'oggetto "risorse": [] della macchina virtuale.

L'estensione richiede `environment` e `activationKey`.

```json
    {
      "type": "extensions",
      "name": "StackifyExtension",
      "apiVersion": "[variables('apiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines',variables('vmName'))]"
      ],
      "properties": {
        "publisher": "Stackify.LinuxAgent.Extension",
        "type": "StackifyLinuxAgentExtension",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "environment": "myEnvironment"
        },
        "protectedSettings": {
          "activationKey": "myActivationKey"
        }
      }
    }      
```

Quando si posiziona l'estensione JSON nella radice del modello, il nome della risorsa include un riferimento alla macchina virtuale padre e il tipo riflette la configurazione annidata.

```json
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "<parentVmResource>/StackifyExtension",
        "apiVersion": "[variables('apiVersion')]",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "properties": {
            "publisher": "Stackify.LinuxAgent.Extension",
            "type": "StackifyLinuxAgentExtension",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "environment": "myEnvironment"
            },
            "protectedSettings": {
              "activationKey": "myActivationKey"
            }
        }
    }
```


## <a name="powershell-deployment"></a>Distribuzione PowerShell

Il comando `Set-AzVMExtension` consente di distribuire l'estensione macchina virtuale dell'agente Linux di Stackify Retrace a una macchina virtuale esistente. Prima di eseguire il comando, le configurazioni pubbliche e private devono essere archiviate in una tabella hash di PowerShell.

L'estensione richiede `environment` e `activationKey`.

```powershell
$PublicSettings = @{"environment" = "myEnvironment"}
$ProtectedSettings = @{"activationKey" = "myActivationKey"}

Set-AzVMExtension -ExtensionName "Stackify.LinuxAgent.Extension" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Stackify.LinuxAgent.Extension" `
    -ExtensionType "StackifyLinuxAgentExtension" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS `
```

## <a name="azure-cli-deployment"></a>Distribuzione dell'interfaccia della riga di comando di Azure 

Lo strumento dell'interfaccia della riga di comando di Azure consente di distribuire l'estensione macchina virtuale dell'agente Linux di Stackify Retrace a una macchina virtuale esistente.  

L'estensione richiede `environment` e `activationKey`.

```azurecli
az vm extension set --publisher 'Stackify.LinuxAgent.Extension' --version 1.0 --name 'StackifyLinuxAgentExtension' --protected-settings '{"activationKey":"myActivationKey"}' --settings '{"environment":"myEnvironment"}'  --resource-group 'myResourceGroup' --vm-name 'myVmName'
```

## <a name="troubleshoot-and-support"></a>Risoluzione dei problemi e supporto

### <a name="error-codes"></a>Codici di errore

| Codice di errore | Significato | Azione possibile |
| :---: | --- | --- |
| 10 | Errore di installazione | wget è obbligatorio |
| 20 | Errore di installazione | python è obbligatorio |
| 30 | Errore di installazione | sudo è obbligatorio |
| 40 | Errore di installazione | activationKey è obbligatorio |
| 51 | Errore di installazione | Distribuzione del sistema operativo non supportata |
| 60 | Errore di installazione | l'ambiente è obbligatorio |
| 70 | Errore di installazione | Sconosciuto |
| 80 | Errore di abilitazione | L'installazione del servizio non è riuscita |
| 90 | Errore di abilitazione | L'avvio del servizio non è riuscito |
| 100 | Errore di disabilitazione | L'interruzione del servizio non è riuscita |
| 110 | Errore di disabilitazione | La rimozione del servizio non è riuscita |
| 120 | Errore di disinstallazione | L'interruzione del servizio non è riuscita |

Se è necessaria ulteriore assistenza è possibile contattare il supporto di Stackify al sito https://support.stackify.com.