---
title: Gestire SQL Server macchine virtuali in Azure usando il portale di Azure | Microsoft Docs
description: Informazioni su come accedere alla risorsa Macchina virtuale SQL nel portale di Azure per una VM di SQL Server ospitata in Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 6b563e8ca93487a123f97f0bbb86624dc3be2db0
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94556289"
---
# <a name="manage-sql-server-vms-in-azure-by-using-the-azure-portal"></a>Gestire le macchine virtuali di SQL Server in Azure tramite il portale di Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Nella [portale di Azure](https://portal.azure.com)la risorsa [**macchine virtuali SQL**](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) è un servizio di gestione indipendente per gestire SQL Server in macchine virtuali di Azure. È possibile usarlo per visualizzare contemporaneamente tutte le macchine virtuali di SQL Server e modificare le impostazioni dedicate a SQL Server: 

![Risorsa Macchine virtuali SQL](./media/manage-sql-vm-portal/sql-vm-manage.png)


## <a name="remarks"></a>Osservazioni

- Si consiglia di usare la risorsa [**macchine virtuali SQL**](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) per visualizzare e gestire le macchine virtuali SQL Server in Azure. Tuttavia, al momento la risorsa **Macchine virtuali SQL** non supporta la gestione delle macchine virtuali di SQL Server in [fine del supporto](sql-server-2008-extend-end-of-support.md). Per gestire le impostazioni per le macchine virtuali di SQL Server in fine del supporto, usare la [scheda Configurazione SQL Server](#access-the-sql-server-configuration-tab) obsoleta. 
- La risorsa **macchine virtuali SQL** è disponibile solo per SQL Server macchine virtuali [registrate con l'estensione SQL IaaS Agent](sql-agent-extension-manually-register-single-vm.md). 


## <a name="access-the-sql-virtual-machines-resource"></a>Accedere alla risorsa Macchine virtuali SQL
Per accedere alla risorsa **Macchine virtuali SQL** , procedere come segue:

1. Aprire il [portale di Azure](https://portal.azure.com). 
1. Selezionare **Tutti i servizi**. 
1. Immettere **macchine virtuali SQL** nella casella di ricerca.
1. (Facoltativo): Selezionare la stella accanto a **Macchine virtuali SQL** per aggiungere questa opzione al menu **Preferiti**. 
1. Selezionare **Macchine virtuali SQL**. 

   ![Trovare le macchine virtuali di SQL Server in tutti i servizi](./media/manage-sql-vm-portal/sql-vm-search.png)

1. Il portale elenca tutte le macchine virtuali di SQL Server disponibili nella sottoscrizione. Selezionare quella che si vuole gestire per aprire la risorsa **Macchine virtuali SQL**. Utilizzare la casella di ricerca se la macchina virtuale di SQL Server non viene visualizzata. 

   ![Tutte le macchine virtuali di SQL Server disponibili](./media/manage-sql-vm-portal/all-sql-vms.png)

   Quando si seleziona la macchina virtuale di SQL Server, si apre la risorsa **Macchine virtuali SQL** : 


   ![Visualizzare la risorsa macchine virtuali SQL](./media/manage-sql-vm-portal/sql-vm-resource.png)

> [!TIP]
> La risorsa **Macchine virtuali SQL** è destinata a impostazioni di SQL Server dedicate. Selezionare il nome della VM nella casella **Macchina virtuale** per aprire le impostazioni specifiche della VM, ma non esclusive per SQL Server. 

## <a name="access-the-sql-server-configuration-tab"></a>Accedere alla scheda Configurazione di SQL Server
La scheda **Configurazione di SQL Server** è ormai obsoleta. Attualmente, è l'unico metodo per gestire la [fine del supporto](sql-server-2008-extend-end-of-support.md) SQL Server macchine virtuali e SQL Server macchine virtuali che non sono state [registrate con l'estensione SQL IaaS Agent](sql-agent-extension-manually-register-single-vm.md).

Per accedere alla scheda obsoleta **Configurazione di SQL Server** , passare alla risorsa **Macchine virtuali**. Eseguire la procedura descritta di seguito:

1. Aprire il [portale di Azure](https://portal.azure.com). 
1. Selezionare **Tutti i servizi**. 
1. Immettere **macchine virtuali** nella casella di ricerca.
1. (Facoltativo): Selezionare la stella accanto a **Macchine virtuali** per aggiungere questa opzione al menu **Preferiti**. 
1. Selezionare **Macchine virtuali**. 

   ![Ricerca delle macchine virtuali](./media/manage-sql-vm-portal/vm-search.png)

1. Il portale elenca tutte le macchine virtuali nella sottoscrizione. Selezionare quella che si vuole gestire per aprire la risorsa **Macchine virtuali**. Utilizzare la casella di ricerca se la macchina virtuale di SQL Server non viene visualizzata. 
1. Selezionare **Configurazione di SQL Server** nel riquadro **Impostazioni** per gestire la macchina virtuale di SQL Server. 

   ![Configurazione di SQL Server](./media/manage-sql-vm-portal/sql-vm-configuration.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti: 

* [Panoramica di SQL Server in una macchina virtuale Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Domande frequenti su SQL Server in una macchia virtuale Windows](frequently-asked-questions-faq.md)
* [Informazioni sui prezzi di SQL Server in una macchina virtuale Windows](pricing-guidance.md)
* [Note sulla versione di SQL Server in una macchina virtuale Windows](doc-changes-updates-release-notes.md)


