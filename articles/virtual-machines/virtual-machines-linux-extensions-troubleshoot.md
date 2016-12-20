---
title: Risoluzione degli errori delle estensioni della macchina virtuale Linux | Microsoft Docs
description: Informazioni sulla risoluzione degli errori delle estensioni della macchina virtuale di Azure
services: virtual-machines-linux
documentationcenter: 
author: kundanap
manager: timlt
editor: 
tags: top-support-issue,azure-resource-manager
ms.assetid: f05d93f3-42fc-4a09-9798-d92f7929c417
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: support-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: cdb2f59d227b3c39c4ee21bf3f383f223577bf49


---
# <a name="troubleshooting-azure-linux-vm-extension-failures"></a>Risoluzione degli errori delle estensioni della macchina virtuale Linux di Azure
[!INCLUDE [virtual-machines-common-extensions-troubleshoot](../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>Visualizzazione dello stato dell'estensione
I modelli di Azure Resource Manager possono essere eseguiti dall'interfaccia della riga di comando di Azure. Una volta che il modello viene eseguito, è possibile visualizzare lo stato dell'estensione da Esplora risorse di Azure o dagli strumenti da riga di comando.

Di seguito è fornito un esempio:

Interfaccia della riga di comando di Azure:

      azure vm get-instance-view


Di seguito è riportato l'output di esempio:

      Extensions:  {
      "ExtensionType": "Microsoft.Compute.CustomScriptExtension",
      "Name": "myCustomScriptExtension",
      "SubStatuses": [
        {
          "Code": "ComponentStatus/StdOut/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "    Directory: C:\\temp\\n\\n\\nMode                LastWriteTime     Length Name
              \\n----                -------------     ------ ----                              \\n-a---          9/1/2015   2:03 AM         11
              test.txt                          \\n\\n",
                      "Time": null
          },
        {
          "Code": "ComponentStatus/StdErr/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "",
          "Time": null
        }
    }
  ]

## <a name="troubleshooting-extenson-failures"></a>Risoluzione degli errori delle Estensioni:
### <a name="re-running-the-extension-on-the-vm"></a>Eseguire nuovamente l'estensione nella macchina virtuale
Se si eseguono gli script nella macchina virtuale usando l'estensione script personalizzato, è possibile riscontrare in alcuni casi un errore in cui la creazione della macchina virtuale è riuscita, ma lo script ha avuto esito negativo. In queste condizioni, il metodo consigliato per risolvere il problema consiste nel rimuovere l'estensione e eseguire nuovamente il modello.
Nota: In futuro, questa funzionalità potrebbe essere migliorata in modo da eliminare la necessità di disinstallazione dell'estensione.

#### <a name="remove-the-extension-from-azure-cli"></a>Rimuovere l'estensione dall'interfaccia della riga di comando di Azure
      azure vm extension set --resource-group "KPRG1" --vm-name "kundanapdemo" --publisher-name "Microsoft.Compute.CustomScriptExtension" --name "myCustomScriptExtension" --version 1.4 --uninstall

Dove "publsher-name" corrisponde al tipo di estensione dall'output di "azure vm get-instance-view" e nome è il nome della risorsa di estensione dal modello

Una volta rimossa l'estensione, il modello può essere eseguito nuovamente per eseguire gli script nella macchina virtuale.




<!--HONumber=Nov16_HO3-->


