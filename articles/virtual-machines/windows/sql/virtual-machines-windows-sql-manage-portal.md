---
title: Gestire le macchine virtuali di SQL Server in Azure tramite il portale di Azure Documenti Microsoft
description: Informazioni su come accedere alla risorsa macchina virtuale SQL nel portale di Azure per una macchina virtuale di SQL Server ospitata in Azure.Learn how to access the SQL virtual machine resource in the Azure portal for a SQL Server VM hosted on Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 244ad7d079fd7baf25f8079557576c42d25ca785
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243211"
---
# <a name="manage-sql-server-vms-in-azure-by-using-the-azure-portal"></a>Gestire le macchine virtuali di SQL Server in Azure tramite il portale di AzureManage SQL Server VMs in Azure by using the Azure portal

Nel [portale di Azure](https://portal.azure.com)la risorsa **macchine virtuali SQL** è un servizio di gestione indipendente. È possibile utilizzarlo per visualizzare tutte le macchine virtuali di SQL Server contemporaneamente e modificare le impostazioni dedicate a SQL Server:You can use it to view all of your SQL Server VMs simultaneously and modify settings dedicated to SQL Server: 

![Risorsa macchine virtuali SQL](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)


## <a name="remarks"></a>Osservazioni

- È consigliabile usare la risorsa macchine virtuali SQL per visualizzare e gestire le macchine virtuali di SQL Server in Azure.We recommend that you use the **SQL virtual machines** resource to view and manage your SQL Server VMs in Azure. Ma attualmente, la risorsa **macchine virtuali SQL** non supporta la gestione delle macchine virtuali di SQL Server con fine [supporto.](virtual-machines-windows-sql-server-2008-eos-extend-support.md) Per gestire le impostazioni per le macchine virtuali di SQL Server con fine supporto, usare invece la scheda di configurazione di [SQL Server](#access-the-sql-server-configuration-tab) deprecata. 
- La risorsa **macchine virtuali SQL** è disponibile solo per le macchine virtuali di SQL Server registrate con il provider di risorse della macchina [virtuale SQL.](virtual-machines-windows-sql-register-with-resource-provider.md) 


## <a name="access-the-sql-virtual-machines-resource"></a>Accedere alla risorsa Macchine virtuali SQLAccess the SQL virtual machines resource
Per accedere alla risorsa **macchine virtuali SQL,** eseguire le operazioni seguenti:To access the SQL virtual machines resource, do the following:

1. Aprire il [portale di Azure](https://portal.azure.com). 
1. Selezionare **Tutti i servizi**. 
1. Immettere **le macchine virtuali SQL** nella casella di ricerca.
1. (Facoltativo): selezionare la stella accanto a **Macchine virtuali SQL** per aggiungere questa opzione al menu **Preferiti.** 
1. Selezionare **Macchine virtuali SQL**. 

   ![Trovare macchine virtuali di SQL Server in tutti i serviziFind SQL Server virtual machines in all services](media/virtual-machines-windows-sql-manage-portal/sql-vm-search.png)

1. Il portale elenca tutte le macchine virtuali di SQL Server disponibili all'interno della sottoscrizione. Selezionare quella che si desidera gestire per aprire la risorsa **macchine virtuali SQL.** Usare la casella di ricerca se la macchina virtuale di SQL Server non viene visualizzata. 

   ![Tutte le macchine virtuali di SQL Server disponibiliAll available SQL Server VMs](media/virtual-machines-windows-sql-manage-portal/all-sql-vms.png)

   Se si seleziona la macchina virtuale di SQL Server, viene aperta la risorsa **Macchine virtuali SQL:Selecting** your SQL Server VM opens the SQL virtual machines resource: 


   ![Risorsa macchine virtuali SQL](media/virtual-machines-windows-sql-manage-portal/sql-vm-resource.png)

> [!TIP]
> La risorsa **macchine virtuali SQL** è per le impostazioni di SQL Server dedicate. Selezionare il nome della macchina virtuale nella casella **Macchina virtuale** per aprire le impostazioni specifiche della macchina virtuale, ma non esclusive di SQL Server. 

## <a name="access-the-sql-server-configuration-tab"></a>Accedere alla scheda Configurazione di SQL ServerAccess the SQL Server configuration tab
La scheda **Configurazione di SQL Server** è deprecata. A questo punto, è l'unico metodo per gestire le macchine virtuali di SQL Server di [fine supporto](virtual-machines-windows-sql-server-2008-eos-extend-support.md) e le macchine virtuali di SQL Server che non sono state registrate con il provider di risorse della macchina virtuale [SQL.](virtual-machines-windows-sql-register-with-resource-provider.md)

Per accedere alla scheda configurazione di **SQL Server** deprecata, passare alla risorsa **Macchine virtuali.** Eseguire la procedura descritta di seguito:

1. Aprire il [portale di Azure](https://portal.azure.com). 
1. Selezionare **Tutti i servizi**. 
1. Immettere le **macchine virtuali** nella casella di ricerca.
1. (Facoltativo): selezionare la stella accanto a **Macchine virtuali** per aggiungere questa opzione al menu **Preferiti.** 
1. Selezionare **Macchine virtuali**. 

   ![Cercare macchine virtuali](media/virtual-machines-windows-sql-manage-portal/vm-search.png)

1. Il portale elenca tutte le macchine virtuali nella sottoscrizione. Selezionare quella che si desidera gestire per aprire la risorsa **Macchine virtuali.** Usare la casella di ricerca se la macchina virtuale di SQL Server non viene visualizzata. 
1. Selezionare **Configurazione di SQL Server** nel riquadro Impostazioni per gestire la macchina virtuale di SQL Server.Select SQL Server configuration in the **Settings** pane to manage your SQL Server VM. 

   ![Configurazione di SQL Server](media/virtual-machines-windows-sql-manage-portal/sql-vm-configuration.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti: 

* [Panoramica di SQL Server in una macchina virtuale WindowsOverview of SQL Server on a Windows VM](virtual-machines-windows-sql-server-iaas-overview.md)
* [Domande frequenti su SQL Server in una macchina virtuale WindowsFAQ for SQL Server on a Windows VM](virtual-machines-windows-sql-server-iaas-faq.md)
* [Indicazioni sui prezzi per SQL Server in una macchina virtuale WindowsPricing guidance for SQL Server on a Windows VM](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Note sulla versione per SQL Server in una macchina virtuale WindowsRelease notes for SQL Server on a Windows VM](virtual-machines-windows-sql-server-iaas-release-notes.md)


