---
title: Console seriale di Azure Documenti Microsoft
description: La console seriale di Azure consente di connettersi alla macchina virtuale quando SSH o RDP non sono disponibili.
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: 779bb88d15ea6c52f4399f17223b89916e22653d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267014"
---
# <a name="azure-serial-console"></a>Console seriale di AzureAzure Serial Console

The Serial Console in the Azure portal provides access to a text-based console for virtual machines (VMs) and virtual machine scale set instances running either Linux or Windows. Questa connessione seriale si connette alla porta seriale ttyS0 o COM1 della macchina virtuale o dell'istanza del set di scalabilità della macchina virtuale, fornendo l'accesso indipendentemente dallo stato della rete o del sistema operativo. È possibile accedere alla console seriale solo tramite il portale di Azure ed è consentita solo per gli utenti che dispongono di un ruolo di accesso Collaboratore o versione successiva al set di scalabilità della macchina virtuale o della macchina virtuale.

La console seriale funziona allo stesso modo per le macchine virtuali e le istanze del set di scalabilità delle macchine virtuali. In questo documento, tutte le menzioni alle macchine virtuali includeranno implicitamente le istanze del set di scalabilità delle macchine virtuali, se non diversamente specificato.

> [!NOTE]
> La console seriale è in genere disponibile nelle aree globali di Azure e nell'anteprima pubblica in Azure per enti pubblici. Non è ancora disponibile nel cloud di Azure China.It is not yet available in the Azure China cloud.

## <a name="prerequisites-to-access-the-azure-serial-console"></a>Prerequisiti per accedere alla console seriale di AzurePrerequisites to access the Azure Serial Console
Per accedere alla Console seriale nell'istanza del set di scalabilità della macchina virtuale o della macchina virtuale, è necessario quanto segue:To access the Serial Console on your VM or virtual machine scale set instance, you will need the following:

- La diagnostica di avvio deve essere abilitata per la macchina virtualeBoot diagnostics must be enabled for the VM
- Un account utente che utilizza l'autenticazione tramite password deve esistere all'interno della macchina virtuale. È possibile creare un utente basato su password con la funzione [di reimpostazione](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) della password dell'estensione di accesso alla macchina virtuale. Selezionare **Reimposta password** nella sezione **Supporto e risoluzione dei problemi**.
- L'account di Azure che accede alla console seriale deve disporre del [ruolo Collaboratore macchina virtuale](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) sia per la macchina virtuale che per l'account di archiviazione di diagnostica di [avvio](boot-diagnostics.md)

> [!NOTE]
> Le distribuzioni classiche non sono supportate. La macchina virtuale o l'istanza del set di scalabilità della macchina virtuale deve usare il modello di distribuzione di Azure Resource Manager.Your VM or virtual machine scale set instance must use the Azure Resource Manager deployment model.

## <a name="get-started-with-the-serial-console"></a>Introduzione alla console seriale
La console seriale per le macchine virtuali e il set di scalabilità delle macchine virtuali è accessibile solo tramite il portale di Azure:The Serial Console for VMs and virtual machine scale set is accessible only through the Azure portal:

### <a name="serial-console-for-virtual-machines"></a>Console seriale per macchine virtuali
La console seriale per le macchine virtuali è semplice come fare clic su **Console seriale** all'interno della sezione **Supporto e risoluzione dei problemi** nel portale di Azure.Serial Console for VMs is as straightforward as clicking on Serial console within the Support - troubleshooting section in the Azure portal.
  1. Aprire il [portale di Azure](https://portal.azure.com).

  1. Passare a **Tutte le risorse** e selezionare una macchina virtuale. Viene visualizzata la pagina Panoramica relativa alla macchina virtuale.

  1. Scorrere verso il basso fino alla sezione **Supporto e risoluzione dei problemi** e selezionare **Console seriale**. Viene visualizzato un nuovo riquadro con la console seriale e viene avviata la connessione.

     ![Finestra della console seriale Linux](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)

### <a name="serial-console-for-virtual-machine-scale-sets"></a>Console seriale per set di scalabilità di macchine virtualiSerial Console for Virtual Machine Scale Sets
La console seriale è disponibile per i set di scalabilità delle macchine virtuali, accessibili in ogni istanza all'interno del set di scalabilità. È necessario passare alla singola istanza di un set di scalabilità di una macchina virtuale prima di visualizzare il pulsante **Console seriale.** Se nel set di scalabilità della macchina virtuale non è abilitata la diagnostica di avvio, assicurarsi di aggiornare il modello di set di scalabilità della macchina virtuale per abilitare la diagnostica di avvio e quindi aggiornare tutte le istanze al nuovo modello per accedere alla console seriale.
  1. Aprire il [portale di Azure](https://portal.azure.com).

  1. Passare a **Tutte le risorse** e selezionare un set di scalabilità di macchine virtuali. Viene visualizzata la pagina di panoramica del set di scalabilità della macchina virtuale.

  1. Passare a **Istanze**

  1. Selezionare un'istanza del set di scalabilità di macchine virtualiSelect a virtual machine scale set instance

  1. Nella sezione **Supporto e risoluzione dei problemi** selezionare Console **seriale**. Viene visualizzato un nuovo riquadro con la console seriale e viene avviata la connessione.

     ![Console seriale del set di scalabilità delle macchine virtuali LinuxLinux virtual machine scale set Serial Console](./media/virtual-machines-serial-console/vmss-start-console.gif)

## <a name="advanced-uses-for-serial-console"></a>Usi avanzati per la console seriale
Oltre all'accesso della console alla macchina virtuale, è anche possibile usare la console seriale di Azure per gli elementi seguenti:From from console access to your VM, you can also use the Azure Serial Console for the following:
* Invio di un comando di richiesta di [sistema alla macchina virtualeSending a system request command to your VM](./serial-console-nmi-sysrq.md)
* Invio di un [interrupt non mascherabile alla macchina virtualeSending a non-maskable interrupt to your VM](./serial-console-nmi-sysrq.md)
* Riavvio con grazia o ciclo di [alimentazione](./serial-console-power-options.md) forzato della macchina virtuale


## <a name="next-steps"></a>Passaggi successivi
Nella barra laterale è disponibile ulteriore la documentazione della console seriale.
- Ulteriori informazioni sono disponibili per [Serial Console per macchine virtuali Linux.](./serial-console-linux.md)
- Ulteriori informazioni sono disponibili per [Serial Console per macchine virtuali Windows.](./serial-console-windows.md)
