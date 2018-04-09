---
title: Diagnostica di avvio per le macchine virtuali Linux in Azure | Microsoft Docs
description: Panoramica delle due funzionalità di debug per le macchine virtuali Linux in Azure
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: Deland-Han
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/19/2018
ms.author: delhan
ms.openlocfilehash: bf8e1b338012898ed3de3f443cf492b6890af796
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="how-to-use-boot-diagnostics-to-troubleshoot-linux-virtual-machines-in-azure"></a>Come usare la diagnostica di avvio per risolvere i problemi relativi alle macchine virtuali Linux in Azure

In Azure ora è disponibile il supporto per due funzionalità di debug: il supporto per l'output della console e per gli screenshot per il modello di distribuzione Resource Manager di Macchine virtuali di Azure. 

Quando si usa l'immagine personale in Azure o anche quando si avvia una delle immagini della piattaforma, una macchina virtuale può passare a uno stato non avviabile per diversi motivi. Queste funzionalità consentono di diagnosticare facilmente gli errori di avvio e di ripristinare le macchine virtuali.

Per le macchine virtuali Linux, è possibile visualizzare facilmente l'output del log della console dal portale:

![Portale di Azure](./media/boot-diagnostics/screenshot1.png)
 
Per le macchine virtuali sia Windows che Linux, Azure consente anche di visualizzare uno screenshot della VM dall'hypervisor:

![Tipi di errore](./media/boot-diagnostics/screenshot2.png)

Entrambe le funzionalità sono supportate per Macchine virtuali di Azure in tutte le aree. Si noti che la visualizzazione degli screenshot e dell'output nell'account di archiviazione può richiedere fino a 10 minuti.

## <a name="common-boot-errors"></a>Errori di avvio comuni

- [Problemi del file system](https://support.microsoft.com/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck) 
- [Problemi del kernel](https://support.microsoft.com/help/4091524/how-recovery-azure-linux-vm-from-kernel-related-boot-related-issues/) 
- [Errori relativi alla tabella del file system](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors)

## <a name="enable-diagnostics-on-a-new-virtual-machine"></a>Abilitare la diagnostica in una nuova macchina virtuale
1. Quando si crea una nuova macchina virtuale dal portale di anteprima, selezionare **Azure Resource Manager** dall'elenco a discesa del modello di distribuzione:
 
    ![Gestione risorse](./media/boot-diagnostics/screenshot3.jpg)

2. Configurare l'opzione Monitoraggio per selezionare l'account di archiviazione in cui si vogliono inserire questi file di diagnostica.
 
    ![Creare una macchina virtuale](./media/boot-diagnostics/screenshot4.jpg)

3. Se si esegue la distribuzione da un modello di Azure Resource Manager, passare alla risorsa macchina virtuale e aggiungere la sezione del profilo di diagnostica. Si ricordi di usare l'intestazione di versione API "2015-06-15".

    ```json
    {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Compute/virtualMachines",
          … 
    ```

4. Il profilo di diagnostica consente di selezionare l'account di archiviazione in cui si vogliono inserire questi log.

    ```json
            "diagnosticsProfile": {
                "bootDiagnostics": {
                "enabled": true,
                "storageUri": "[concat('http://', parameters('newStorageAccountName'), '.blob.core.windows.net')]"
                }
            }
            }
        }
    ```

## <a name="update-an-existing-virtual-machine"></a>Aggiornare una macchina virtuale esistente

Per abilitare la diagnostica dal portale, è anche possibile aggiornare una macchina virtuale esistente dal portale. Selezionare l'opzione Diagnostica di avvio e fare clic su Salva. Riavviare la VM per rendere effettivo l'aggiornamento.

![Aggiornare una VM esistente](./media/boot-diagnostics/screenshot5.png)

## <a name="next-steps"></a>Passaggi successivi

Se durante l'uso della diagnostica di avvio delle macchine virtuali viene visualizzato un messaggio di errore che indica l'impossibilità di ottenere il contenuto del log, vedere [Failed to get contents of the log error in VM Boot Diagnostics](https://support.microsoft.com/help/4094480/failed-to-get-contents-of-the-log-error-in-vm-boot-diagnostics-in-azur) (Errore: Impossibile ottenere il contenuto del log in Diagnostica di avvio della macchina virtuale).