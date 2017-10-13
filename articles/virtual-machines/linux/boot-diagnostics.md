---
title: Diagnostica di avvio per le macchine virtuali Linux in Azure | Microsoft Docs
description: "Panoramica delle due funzionalità di debug per le macchine virtuali Linux in Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: Deland-Han
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: troubleshooting
ms.date: 08/21/2017
ms.author: delhan
ms.openlocfilehash: 79e412bd7523a55fc7d081121af9434520868880
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-boot-diagnostics-to-troubleshoot-linux-virtual-machines-in-azure"></a>Come usare la diagnostica di avvio per risolvere i problemi relativi alle macchine virtuali Linux in Azure

In Azure ora è disponibile il supporto per due funzionalità di debug: il supporto per l'output della console e per gli screenshot per il modello di distribuzione Resource Manager di Macchine virtuali di Azure. 

Quando si usa l'immagine personale in Azure o anche quando si avvia una delle immagini della piattaforma, una macchina virtuale può passare a uno stato non avviabile per diversi motivi. Queste funzionalità consentono di diagnosticare facilmente gli errori di avvio e di ripristinare le macchine virtuali.

Per le macchine virtuali Linux, è possibile visualizzare facilmente l'output del log della console dal portale:

![Portale di Azure](./media/boot-diagnostics/screenshot1.png)
 
Per le macchine virtuali sia Windows che Linux, Azure consente anche di visualizzare uno screenshot della VM dall'hypervisor:

![Errore](./media/boot-diagnostics/screenshot2.png)

Entrambe le funzionalità sono supportate per Macchine virtuali di Azure in tutte le aree. Si noti che la visualizzazione degli screenshot e dell'output nell'account di archiviazione può richiedere fino a 10 minuti.

## <a name="common-boot-errors"></a>Errori di avvio comuni

- [Problemi del file system](https://blogs.msdn.microsoft.com/linuxonazure/2016/09/13/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck-inodes/)
- [Problemi del kernel](https://blogs.msdn.microsoft.com/linuxonazure/2016/10/09/linux-recovery-manually-fixing-non-boot-issues-related-to-kernel-problems/)
- [Errori relativi alla tabella del file system](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/ )

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