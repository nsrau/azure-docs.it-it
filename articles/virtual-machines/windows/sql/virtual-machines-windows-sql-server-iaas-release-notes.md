---
title: Note sulla versione di SQL Server in una macchina virtuale Azure | Microsoft Docs
description: Informazioni sulle nuove funzionalità e i miglioramenti di SQL Server in una macchina virtuale Azure
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2018
ms.author: mathoma
ms.openlocfilehash: 44aee447c7f935cd67ca55902c53c5f6f9eb6fda
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2018
ms.locfileid: "52577088"
---
# <a name="sql-server-on-azure-virtual-machine-release-notes"></a>Note sulla versione di SQL Server in una macchina virtuale Azure

Azure consente di distribuire una macchina virtuale con un'immagine di SQL Server predefinita. Questo articolo elenca le nuove funzionalità e i miglioramenti previsti nell'ultima versione di SQL Server distribuita in una macchina virtuale Azure. 


## <a name="november-2018"></a>Novembre 2018
- **Nuovo provider di risorse SQL**: esiste un nuovo provider di risorse per le macchine virtuali di SQL per una migliore gestione della macchina virtuale. Per altre informazioni sulla registrazione di una macchina virtuale, vedere [Registrare una macchina virtuale SQL legacy con nuovo provider di risorse](virtual-machines-windows-sql-ahb.md#register-legacy-sql-vm-with-new-resource-provider).
- **Cambiare il modello di licenza**: è ora possibile passare dal modello di licenza a pagamento in base all'utilizzo al modello di licenza Bring-Your-Own per la propria macchina virtuale SQL con l'interfaccia della riga di comando di Azure o Powershell. Per altre informazioni, vedere [Come cambiare il livello di licenza per una macchina virtuale SQL](virtual-machines-windows-sql-ahb.md).



## <a name="additional-resources"></a>Risorse aggiuntive

**Macchine virtuali Windows**:

* [Panoramica di SQL Server in una macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-overview.md).
* [Effettuare il provisioning di una macchina virtuale Windows di SQL Server](virtual-machines-windows-portal-sql-server-provision.md)
* [Migrazione di un database a SQL Server su una macchina virtuale di Azure](virtual-machines-windows-migrate-sql.md)
* [Disponibilità elevata e ripristino di emergenza di SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-high-availability-dr.md)
* [Performance best practices for SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-performance.md) (Procedure consigliate sulle prestazioni per SQL Server nelle macchine virtuali di Azure)
* [Modelli di applicazione e strategie di sviluppo per SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Macchine virtuali Linux**:

* [Panoramica di SQL Server in una macchina virtuale Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Effettuare il provisioning di una macchina virtuale Linux con SQL Server](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Domande frequenti (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Documentazione di SQL Server in Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
