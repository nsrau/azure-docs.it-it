---
title: Console seriale di Azure | Microsoft Docs
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
ms.openlocfilehash: 65679c900baaf0f98a21e4b1f6b1d350c4b945c4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87074337"
---
# <a name="azure-serial-console"></a>Console seriale di Azure

La console seriale nel portale di Azure fornisce l'accesso a una console basata su testo per istanze di macchine virtuali e set di scalabilità di macchine virtuali in esecuzione su Linux o Windows. Questa connessione seriale viene stabilita con la porta seriale ttyS0 o COM1 della macchina virtuale o del set di scalabilità di macchine virtuali e fornisce un accesso indipendente dallo stato del sistema operativo o della rete. L'accesso alla console seriale è consentito solo mediante il portale di Azure e solo agli utenti che dispongono di un ruolo accesso come Collaboratore o superiore alla macchina virtuale o al set di scalabilità di macchine virtuali.

La console seriale funziona allo stesso modo per istanze di macchine virtuali e set di scalabilità di macchine virtuali. In questo documento, se non diversamente specificato, ogni riferimento a macchine virtuali include in modo implicito le istanze dei set di scalabilità di macchine virtuali.

> [!NOTE]
> La console seriale è disponibile a livello generale nelle aree globali di Azure e in anteprima pubblica in Azure per enti pubblici. Non è ancora disponibile nel cloud Azure Cina.

## <a name="prerequisites-to-access-the-azure-serial-console"></a>Prerequisiti per accedere alla console seriale di Azure
Per accedere alla console seriale nell'istanza della macchina virtuale o del set di scalabilità di macchine virtuali, è necessario:

- Abilitare la diagnostica di avvio per la macchina virtuale
- Un account utente che usi l'autenticazione della password deve esistere all'interno della macchina virtuale. È possibile creare un utente basato su password con la funzione di [reimpostazione della password](../extensions/vmaccess.md#reset-password) dell'estensione di accesso alla macchina virtuale. Selezionare **Reimposta password** nella sezione **Supporto e risoluzione dei problemi**.
- L'account di Azure che accede alla console seriale deve disporre del [ruolo di Collaboratore macchina virtuale](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) sia per la macchina virtuale che per l'account di archiviazione della [diagnostica di avvio](boot-diagnostics.md)

> [!NOTE]
> Le distribuzioni classiche non sono supportate. L'istanza della macchina virtuale o del set di scalabilità di macchine virtuali deve usare il modello di distribuzione Azure Resource Manager.

## <a name="get-started-with-the-serial-console"></a>Introduzione alla console seriale
La console seriale per macchine virtuali e set di scalabilità di macchine virtuali è accessibile solo tramite il portale di Azure:

### <a name="serial-console-for-virtual-machines"></a>Console seriale per macchine virtuali
Per accedere alla console seriale per macchine virtuali è sufficiente fare clic su **Console seriale** nella sezione **Supporto e risoluzione dei problemi** del portale di Azure.
  1. Aprire il [portale di Azure](https://portal.azure.com).

  1. Passare a **Tutte le risorse** e selezionare una macchina virtuale. Viene visualizzata la pagina Panoramica relativa alla macchina virtuale.

  1. Scorrere verso il basso fino alla sezione **Supporto e risoluzione dei problemi** e selezionare **Console seriale**. Viene visualizzato un nuovo riquadro con la console seriale e viene avviata la connessione.

     ![Finestra della console seriale Linux](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)

### <a name="serial-console-for-virtual-machine-scale-sets"></a>Console seriale per i set di scalabilità di macchine virtuali
La console seriale è disponibile per i set di scalabilità di macchine virtuali e accessibile in ogni istanza nel set di scalabilità. Prima di visualizzare il pulsante **Console seriale** è necessario passare alla singola istanza di un set di scalabilità di macchine virtuali. Qualora per il set di scalabilità di macchine virtuali non sia abilitata la diagnostica di avvio, assicurarsi di aggiornare il modello del set di scalabilità di macchine virtuali per abilitare la diagnostica di avvio e quindi aggiornare tutte le istanze al nuovo modello per accedere alla console seriale.
  1. Aprire il [portale di Azure](https://portal.azure.com).

  1. Passare a **Tutte le risorse** e selezionare un set di scalabilità di macchine virtuali. Si aprirà la pagina di panoramica del set di scalabilità di macchine virtuali.

  1. Passare a **Istanze**

  1. Selezionare un'istanza del set di scalabilità di macchine virtuali

  1. Nella sezione **Supporto e risoluzione dei problemi**, selezionare **Console seriale**. Viene visualizzato un nuovo riquadro con la console seriale e viene avviata la connessione.

     ![Console seriale per i set di scalabilità di macchine virtuali Linux](./media/virtual-machines-serial-console/vmss-start-console.gif)


### <a name="tls-12-in-serial-console"></a>TLS 1.2 nella console seriale
La console seriale usa TLS 1.2 end-to-end per proteggere tutte le comunicazioni all'interno del servizio. La console seriale presenta una dipendenza da un account di archiviazione di diagnostica di avvio gestito dall'utente e TLS 1.2 deve essere configurato separatamente per l'account di archiviazione. Le istruzioni complete sono riportate [qui](../../storage/common/transport-layer-security-configure-minimum-version.md).

## <a name="advanced-uses-for-serial-console"></a>Opzioni d'uso avanzate per la console seriale
Oltre all'accesso alla console per la macchina virtuale, è anche possibile usare la console seriale di Azure per le operazioni seguenti:
* Invio di un [comando di richiesta di sistema alla macchina virtuale](./serial-console-nmi-sysrq.md)
* Invio di un [interrupt non mascherabile alla macchina virtuale](./serial-console-nmi-sysrq.md)
* [Riavvio graduale o potenziamento forzato della macchina virtuale](./serial-console-power-options.md)


## <a name="next-steps"></a>Passaggi successivi
La documentazione aggiuntiva della console seriale è disponibile nella barra laterale.
- Per la [console seriale per macchine virtuali Linux](./serial-console-linux.md) sono disponibili altre informazioni.
- Per la [console seriale per macchine virtuali Windows](./serial-console-windows.md) sono disponibili altre informazioni.
