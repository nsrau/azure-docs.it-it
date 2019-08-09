---
title: Gestire SQL Server VM in Azure usando il portale di Azure | Microsoft Docs
description: Informazioni su come accedere alla risorsa della macchina virtuale SQL nel portale di Azure per una VM SQL Server ospitata in Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 398eea4b968bb77017415e1dc259004c697b8dda
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68846183"
---
# <a name="manage-sql-server-vms-in-azure-by-using-the-azure-portal"></a>Gestire SQL Server VM in Azure usando il portale di Azure

Nella [portale di Azure](https://portal.azure.com)la risorsa **macchine virtuali SQL** è un servizio di gestione indipendente. È possibile usarlo per visualizzare contemporaneamente tutte le macchine virtuali SQL Server e modificare le impostazioni dedicate SQL Server: 

![Risorsa macchine virtuali SQL](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)


## <a name="remarks"></a>Note

- Si consiglia di usare la risorsa **macchine virtuali SQL** per visualizzare e gestire le macchine virtuali SQL Server in Azure. Attualmente, tuttavia, la risorsa **macchine virtuali SQL** non supporta la gestione della [fine del supporto](virtual-machines-windows-sql-server-2008-eos-extend-support.md) SQL Server macchine virtuali. Per gestire le impostazioni per la fine del supporto SQL Server macchine virtuali, usare invece la [scheda di configurazione](#access-the-sql-server-configuration-tab) deprecata SQL Server. 
- La risorsa **macchine virtuali SQL** è disponibile solo per SQL Server macchine virtuali [registrate con il provider di risorse VM SQL](virtual-machines-windows-sql-register-with-resource-provider.md). 


## <a name="access-the-sql-virtual-machines-resource"></a>Accedere alla risorsa macchine virtuali SQL
Per accedere alla risorsa **macchine virtuali SQL** , eseguire le operazioni seguenti:

1. Aprire il [portale di Azure](https://portal.azure.com). 
1. Selezionare **Tutti i servizi**. 
1. Immettere **macchine virtuali SQL** nella casella di ricerca.
1. (Facoltativo): Selezionare la stella accanto a **macchine virtuali SQL** per aggiungere questa opzione al menu **Preferiti** . 
1. Selezionare **macchine virtuali SQL**. 

   ![Trova SQL Server macchine virtuali in tutti i servizi](media/virtual-machines-windows-sql-manage-portal/sql-vm-search.png)

1. Il portale elenca tutte le macchine virtuali SQL Server disponibili nella sottoscrizione. Selezionare quello che si vuole gestire per aprire la risorsa **macchine virtuali SQL** . Utilizzare la casella di ricerca se la macchina virtuale SQL Server non viene visualizzata. 

   ![Tutte le macchine virtuali SQL Server disponibili](media/virtual-machines-windows-sql-manage-portal/all-sql-vms.png)

   Selezionando la macchina virtuale SQL Server si apre la risorsa **macchine virtuali SQL** : 


   ![Risorsa macchine virtuali SQL](media/virtual-machines-windows-sql-manage-portal/sql-vm-resource.png)

> [!TIP]
> La risorsa **macchine virtuali SQL** è destinata a impostazioni di SQL Server dedicate. Selezionare il nome della VM nella casella **macchina virtuale** per aprire le impostazioni specifiche della VM, ma non esclusive per SQL Server. 

## <a name="access-the-sql-server-configuration-tab"></a>Accedere alla scheda configurazione SQL Server
La scheda **configurazione SQL Server** è stata deprecata. Attualmente, è l'unico metodo per gestire la [fine del supporto](virtual-machines-windows-sql-server-2008-eos-extend-support.md) SQL Server macchine virtuali e SQL Server macchine virtuali che non sono state [registrate con il provider di risorse VM SQL](virtual-machines-windows-sql-register-with-resource-provider.md).

Per accedere alla scheda di **configurazione SQL Server** deprecata, passare alla risorsa **macchine virtuali** . Seguire questa procedura:

1. Aprire il [portale di Azure](https://portal.azure.com). 
1. Selezionare **Tutti i servizi**. 
1. Immettere **macchine virtuali** nella casella di ricerca.
1. (Facoltativo): Selezionare la stella accanto a **macchine virtuali** per aggiungere questa opzione al menu **Preferiti** . 
1. Selezionare **Macchine virtuali**. 

   ![Cerca macchine virtuali](media/virtual-machines-windows-sql-manage-portal/vm-search.png)

1. Il portale elenca tutte le macchine virtuali nella sottoscrizione. Selezionare quello che si vuole gestire per aprire la risorsa **macchine virtuali** . Utilizzare la casella di ricerca se la macchina virtuale SQL Server non viene visualizzata. 
1. Selezionare **SQL Server configurazione** nel riquadro **Impostazioni** per gestire la macchina virtuale SQL Server. 

   ![Configurazione di SQL Server](media/virtual-machines-windows-sql-manage-portal/sql-vm-configuration.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere i seguenti articoli: 

* [Panoramica di SQL Server in una macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Domande frequenti per SQL Server in una macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Linee guida sui prezzi per SQL Server in una macchina virtuale Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Note sulla versione per SQL Server in una macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


