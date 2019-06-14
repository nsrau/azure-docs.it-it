---
title: Gestire macchine virtuali di SQL Server in Azure usando il portale di Azure | Microsoft Docs
description: Informazioni su come accedere alla risorsa di macchina virtuale SQL nel portale di Azure per una VM di SQL Server ospitato in Azure.
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
ms.openlocfilehash: e2a807bbd6baeb2f14a6d36f5d98a28d48725449
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082716"
---
# <a name="manage-sql-server-vms-in-azure-using-the-azure-portal"></a>Gestire macchine virtuali di SQL Server in Azure usando il portale di Azure

È un nuovo punto di accesso per gestire VM di SQL Server in Azure usando il [portale di Azure](https://portal.azure.com). 

Il **macchine virtuali SQL** risorsa ora è un servizio di gestione indipendente che consente di visualizzare contemporaneamente tutte le VM di SQL Server e modificare le impostazioni dedicate a SQL Server: 

![Risorse delle macchine virtuali SQL](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)


## <a name="remarks"></a>Note

- Il **macchine virtuali SQL** risorsa è il metodo consigliato per visualizzare e gestire le macchine virtuali di SQL Server. Tuttavia, attualmente, il **macchine virtuali SQL** risorsa non supporta la gestione dei [fine del supporto (May)](virtual-machines-windows-sql-server-2008-eos-extend-support.md) VM di SQL Server. Per gestire le impostazioni per le VM di SQL Server EOS, utilizzare deprecate [della scheda di configurazione di SQL Server](#access-sql-server-configuration-tab) invece. 
- Il **macchine virtuali SQL** risorsa è disponibile solo per le VM di SQL Server che hanno [registrata con il provider di risorse di VM di SQL](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-the-sql-vm-resource-provider). 


## <a name="access-sql-virtual-machine-resource"></a>Risorsa della macchina virtuale l'accesso SQL
Per accedere alla risorsa di macchine virtuali SQL, eseguire le operazioni seguenti:

1. Aprire il [portale di Azure](https://portal.azure.com). 
1. Selezionare **Tutti i servizi**. 
1. Tipo `SQL virtual machines` nella casella di ricerca.
1. (Facoltativo): Selezionare la stella accanto a **macchine virtuali SQL** per aggiungere questa opzione di menu Preferiti. 
1. Selezionare **macchine virtuali SQL**. 

   ![Individuare le macchine virtuali di VM di SQL in tutti i servizi](media/virtual-machines-windows-sql-manage-portal/sql-vm-search.png)

1. Questo Elenca tutte le VM di SQL Server disponibile all'interno della sottoscrizione. Selezionare quella che si desidera gestire per avviare il **macchine virtuali SQL** risorsa. Usare la casella di ricerca se la macchina virtuale di SQL Server non è immediatamente evidente. 

![Tutte le VM di SQL disponibili](media/virtual-machines-windows-sql-manage-portal/all-sql-vms.png)

Verrà aperto selezionando la macchina virtuale di SQL Server la **macchine virtuali SQL** risorsa: 


![Risorse delle macchine virtuali SQL](media/virtual-machines-windows-sql-manage-portal/sql-vm-resource.png)

  > [!TIP]
  > Il **macchine virtuali SQL** risorse sia per le impostazioni di SQL Server dedicate. Selezionare il nome della macchina virtuale nel **macchina virtuale** campo per passare a impostazioni che sono specifiche per la macchina virtuale, ma non esclusivo a SQL Server. 

## <a name="access-sql-server-configuration-tab"></a>Scheda di configurazione di accesso SQL Server
La scheda di configurazione di SQL Server è stata deprecata. A questo punto, è l'unico metodo per gestire [fine del supporto (May)](virtual-machines-windows-sql-server-2008-eos-extend-support.md) macchine virtuali SQL Server e macchine virtuali di SQL Server che non sono state [registrata con il provider di risorse di VM di SQL](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-the-sql-vm-resource-provider).

Per accedere alla scheda Configurazione deprecata di SQL server, è necessario passare per il **macchine virtuali** risorsa. A tale scopo, seguire questa procedura:

1. Aprire il [portale di Azure](https://portal.azure.com). 
1. Selezionare **Tutti i servizi**. 
1. Tipo `virtual machines` nella casella di ricerca.
1. (Facoltativo): Selezionare la stella accanto a **macchine virtuali** per aggiungere questa opzione di menu Preferiti. 
1. Selezionare **Macchine virtuali**. 

   ![Ricerca per le macchine virtuali](media/virtual-machines-windows-sql-manage-portal/vm-search.png)

1. Verranno elencati tutte le macchine virtuali nella sottoscrizione. Selezionare quella che si desidera gestire per avviare il **macchina virtuale** risorsa. Usare la casella di ricerca se la macchina virtuale di SQL Server non è immediatamente evidente. 
1. Selezionare **configurazione di SQL Server** nel **impostazioni** riquadro per gestire il Server SQL. 

![Configurazione di SQL Server](media/virtual-machines-windows-sql-manage-portal/sql-vm-configuration.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti: 

* [Panoramica di SQL Server in una macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server sulle domande frequenti di una macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Guida ai prezzi per le VM di SQL Server in Azure).
* [SQL Server sulle note sulla versione della macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


