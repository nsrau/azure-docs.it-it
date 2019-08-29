---
title: Console seriale di Azure per Linux | Microsoft Docs
description: Console seriale bidirezionale per macchine virtuali di Azure e set di scalabilità di macchine virtuali.
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/6/2019
ms.author: alsin
ms.openlocfilehash: cc964b1b11a68921c1d5d2b68a7413934565fdc8
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129601"
---
# <a name="azure-serial-console"></a>Console seriale di Azure

La console seriale nell'portale di Azure fornisce l'accesso a una console basata su testo per le macchine virtuali (VM) e le istanze del set di scalabilità di macchine virtuali che eseguono Linux o Windows. Questa connessione seriale si connette alla porta seriale ttyS0 o COM1 della VM o dell'istanza del set di scalabilità di macchine virtuali, fornendo accesso indipendente dalla rete o dallo stato del sistema operativo. È possibile accedere alla console seriale solo usando la portale di Azure ed è consentita solo per gli utenti che hanno un ruolo accesso di collaboratore o superiore alla macchina virtuale o al set di scalabilità di macchine virtuali.

La console seriale funziona allo stesso modo per le macchine virtuali e le istanze del set di scalabilità di macchine virtuali. In questo documento, tutte le menzioni delle macchine virtuali includeranno in modo implicito le istanze del set di scalabilità di macchine virtuali, se non diversamente specificato.

> [!NOTE]
> La console seriale è disponibile a livello generale nelle aree globali di Azure. Non è al momento disponibile nei cloud di Azure per enti pubblici o di Azure Cina.

## <a name="prerequisites-to-access-the-azure-serial-console"></a>Prerequisiti per accedere alla console seriale di Azure
Per accedere alla console seriale nell'istanza della VM o del set di scalabilità di macchine virtuali, è necessario quanto segue:

- È necessario abilitare la diagnostica di avvio per la macchina virtuale
- Un account utente che usa l'autenticazione con password deve esistere all'interno della macchina virtuale. È possibile creare un utente basato su password con la funzione [Reimposta password](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) dell'estensione di accesso alla macchina virtuale. Selezionare **Reimposta password** nella sezione **Supporto e risoluzione dei problemi**.
- L'account di Azure che accede alla console seriale deve avere il [ruolo Collaboratore macchina virtuale](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) per la VM e l'account di archiviazione di [diagnostica di avvio](boot-diagnostics.md)

> [!NOTE]
> - Le distribuzioni classiche non sono supportate. L'istanza della VM o del set di scalabilità di macchine virtuali deve usare il modello di distribuzione Azure Resource Manager.

## <a name="get-started-with-the-serial-console"></a>Introduzione alla console seriale
La console seriale per le macchine virtuali e il set di scalabilità di macchine virtuali è accessibile solo tramite la portale di Azure:

### <a name="serial-console-for-virtual-machines"></a>Console seriale per le macchine virtuali
La console seriale per le macchine virtuali è semplice come fare clic su **console seriale** nella sezione **supporto e risoluzione dei problemi** nel portale di Azure.
  1. Aprire il [portale di Azure](https://portal.azure.com).

  1. Passare a **tutte le risorse** e selezionare una macchina virtuale. Viene visualizzata la pagina Panoramica relativa alla macchina virtuale.

  1. Scorrere verso il basso fino alla sezione **Supporto e risoluzione dei problemi** e selezionare **Console seriale**. Viene visualizzato un nuovo riquadro con la console seriale e viene avviata la connessione.

     ![Finestra console seriale Linux](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)

### <a name="serial-console-for-virtual-machine-scale-sets"></a>Console seriale per i set di scalabilità di macchine virtuali
La console seriale è disponibile per i set di scalabilità di macchine virtuali, accessibile in ogni istanza nel set di scalabilità. Prima di visualizzare il pulsante **console seriale** , sarà necessario passare alla singola istanza di un set di scalabilità di macchine virtuali. Se per il set di scalabilità di macchine virtuali non è abilitata la diagnostica di avvio, assicurarsi di aggiornare il modello del set di scalabilità di macchine virtuali per abilitare la diagnostica di avvio e quindi aggiornare tutte le istanze al nuovo modello per accedere alla console seriale.
  1. Aprire il [portale di Azure](https://portal.azure.com).

  1. Passare a **tutte le risorse** e selezionare un set di scalabilità di macchine virtuali. Verrà visualizzata la pagina Panoramica per il set di scalabilità di macchine virtuali.

  1. Passa a **istanze**

  1. Selezionare un'istanza del set di scalabilità di macchine virtuali

  1. Nella sezione **supporto e risoluzione dei problemi** selezionare **console seriale**. Viene visualizzato un nuovo riquadro con la console seriale e viene avviata la connessione.

     ![Console seriale del set di scalabilità di macchine virtuali Linux](./media/virtual-machines-serial-console/vmss-start-console.gif)

## <a name="advanced-uses-for-serial-console"></a>Usi avanzati per la console seriale
Oltre all'accesso alla console per la macchina virtuale, è anche possibile usare la console seriale di Azure per le operazioni seguenti:
* Invio [di un comando di richiesta di sistema alla macchina virtuale](./serial-console-nmi-sysrq.md)
* Invio [di un interrupt non mascherabile alla macchina virtuale](./serial-console-nmi-sysrq.md)
* Riavvio normale [o alimentazione forzata della macchina virtuale](./serial-console-power-options.md)


## <a name="next-steps"></a>Passaggi successivi
La documentazione aggiuntiva della console seriale è disponibile nella barra laterale.
- Altre informazioni sono disponibili per la [console seriale per le macchine virtuali Linux](./serial-console-linux.md).
- Altre informazioni sono disponibili per la [console seriale per le macchine virtuali Windows](./serial-console-windows.md).
