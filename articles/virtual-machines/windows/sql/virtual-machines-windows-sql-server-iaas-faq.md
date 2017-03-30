---
title: Domande frequenti su SQL Server in Macchine virtuali di Azure| Microsoft Docs
description: Questo articolo offre risposta ad alcune domande frequenti sull&quot;esecuzione di SQL Server in macchine virtuali di Azure.
services: virtual-machines-windows
documentationcenter: 
author: v-shysun
manager: felixwu
editor: 
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: v-shysun
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 498291fbf49e8bc119d93bb2dd4118e62ebdc71c
ms.lasthandoff: 03/18/2017

---
# <a name="frequently-asked-questions-for-sql-server-on-azure-virtual-machines"></a>Domande frequenti su SQL nelle macchine virtuali di Azure
Questo argomento offre risposta ad alcune delle domande più comuni sull'esecuzione di [SQL Server in macchine virtuali di Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="frequently-asked-questions"></a>Domande frequenti
1. **Come si crea una macchina virtuale di Azure con SQL Server?**
   
    La soluzione più semplice consiste nel creare una macchina virtuale che include SQL Server. Per un'esercitazione sulla registrazione in Azure e sulla creazione di una VM di SQL Server dal portale, vedere [Effettuare il provisioning di una macchina virtuale di SQL Server nel portale di Azure](virtual-machines-windows-portal-sql-server-provision.md). È possibile selezionare un'immagine di macchina virtuale che utilizza la licenza di SQL Server con costo al minuto oppure usare un'immagine che consente di trasferire la licenza di SQL Server dell'utente. È anche possibile installare manualmente SQL Server in una VM e usare di nuovo una licenza locale. Se si usa la funzionalità Bring Your Own License, è necessario avere [Mobilità delle licenze tramite Software Assurance in Azure](https://azure.microsoft.com/pricing/license-mobility/).
2. **Qual è la differenza tra VM di SQL Server e servizio Database SQL?**
   
    Dal punto di vista concettuale l'esecuzione di SQL Server in una macchina virtuale di Azure non è diversa dall'esecuzione di SQL Server in un centro dati remoto. Per contro, il servizio [Database SQL](../../../sql-database/sql-database-technical-overview.md) offre una soluzione DaaS (Database-as-a-service). Con Database SQL non si ha accesso ai computer che ospitano i database. Per un confronto completo, vedere [Scegliere un'opzione di SQL Server cloud: database SQL di Azure (PaaS) o SQL Server in VM di Azure (IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md).
3. **Come si esegue la migrazione di un database SQL Server locale nel cloud?**
   
    Creare prima una macchina virtuale di Azure con un'istanza di SQL Server, quindi eseguire la migrazione dei database locali in tale istanza. Per alcune strategie di migrazione dei dati, vedere [Eseguire la migrazione di un database di SQL Server a SQL Server in una macchina virtuale di Azure](virtual-machines-windows-migrate-sql.md).
4. **È possibile modificare le funzionalità installate o installare una seconda istanza di SQL Server nella stessa VM?**
   
    Sì. Il supporto di installazione di SQL Server si trova in una cartella nell'unità **C** . Eseguire **Setup.exe** da tale percorso per aggiungere nuove istanze di SQL Server o per modificare altre funzionalità di SQL Server installate nella macchina virtuale.
5. **Come si esegue l'aggiornamento a una nuova versione o a una nuova edizione di SQL Server in una VM di Azure?**
   
    Attualmente non è disponibile alcun aggiornamento sul posto per SQL Server in esecuzione in una VM di Azure. Creare una nuova macchina virtuale di Azure con la versione o l'edizione di SQL Server voluta, quindi eseguire la migrazione dei database nel nuovo server tramite [tecniche di migrazione dati](virtual-machines-windows-migrate-sql.md) standard.
6. **Come si installa una copia di SQL Server con licenza in una VM di Azure?**
   
    Per eseguire questa operazione è possibile procedere in due modi: È possibile eseguire il provisioning di una delle [immagini di macchine virtuali che supporta le licenze](virtual-machines-windows-sql-server-iaas-overview.md#BYOL). Un'altra possibilità consiste nel copiare il supporto di installazione di SQL Server in una VM Windows Server, quindi installare SQL Server nella VM. Per motivi di licenza, è necessario avere [Mobilità delle licenze tramite Software Assurance in Azure](https://azure.microsoft.com/pricing/license-mobility/).
7. **È possibile modificare una VM per l'uso di una licenza di SQL Server, se è stata creata da una delle immagini della raccolta con pagamento in base al consumo?**

    No. Non è possibile passare dalla licenza con costo al minuto all'utilizzo della propria licenza. Creare una nuova macchina virtuale di Azure usando una delle [immagini BYOL](virtual-machines-windows-sql-server-iaas-overview.md#BYOL) e quindi migrare i database nel nuovo server utilizzando le [tecniche di migrazione dei dati](virtual-machines-windows-migrate-sql.md) standard.

7. **Le istanze del cluster di failover di SQL Server sono supportate nelle macchine virtuali di Azure?**

   Sì. È possibile [creare un cluster di failover di Windows in Windows Server 2016](virtual-machines-windows-portal-sql-create-failover-cluster.md) e usare Spazi di archiviazione diretta (S2D) per l'archiviazione del cluster. In alternativa, è possibile usare soluzioni di clustering o archiviazione di terze parti come descritto in [Disponibilità elevata e ripristino di emergenza per SQL Server nelle macchine virtuali di Azure](virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions).

7. **È necessario pagare la licenza di SQL Server in una VM di Azure se viene utilizzata solo per standby/failover?**
   
    Se si dispone di Software Assurance e si usa la mobilità delle licenze come descritto in [Domande frequenti sulla gestione delle licenze di Macchine virtuali](http://azure.microsoft.com/pricing/licensing-faq/), non è necessario pagare la licenza per un'istanza di SQL Server che funge da replica secondaria passiva in una distribuzione a disponibilità elevata.
    
8. **Come si applicano gli aggiornamenti e i Service Pack a una VM di SQL Server?**
   
    Le macchine virtuali consentono di controllare il computer host e di decidere quindi quando e come applicare gli aggiornamenti. Per il sistema operativo, è possibile applicare manualmente gli aggiornamenti di Windows oppure abilitare un servizio di pianificazione definito [Applicazione automatica delle patch](virtual-machines-windows-sql-automated-patching.md). Applicazione automatica delle patch installa tutti gli aggiornamenti contrassegnati come importanti, inclusi gli aggiornamenti di SQL Server in tale categoria. Gli aggiornamenti facoltativi di SQL Server devono essere installati manualmente.
9. **È possibile impostare configurazioni non visualizzate nella raccolta di macchine virtuali, ad esempio Windows 2008 R2 + SQL Server 2012?**
   
    No. Per le raccolte di macchine virtuali che includono SQL Server è necessario selezionare una delle immagini disponibili.
10. **Come si installa SQL Server Data Tools in una VM di Azure?**
    
     Scaricare e installare SQL Server Data Tools da [Microsoft SQL Server Data Tools - Business Intelligence per Visual Studio 2013 ](https://www.microsoft.com/en-us/download/details.aspx?id=42313).

## <a name="resources"></a>Risorse
Per una panoramica di SQL Server nelle macchine virtuali di Azure, guardare il video [Azure VM is the best platform for SQL Server 2016](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/Azure-VM-is-the-best-platform-for-SQL-Server-2016)(Macchine virtuali di Azure è la piattaforma ottimale per SQL Server 2016). Un'ottima introduzione è anche l'argomento [Panoramica di SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-overview.md).

Altre risorse:

* [Effettuare il provisioning di una macchina virtuale di SQL Server nel portale di Azure](virtual-machines-windows-portal-sql-server-provision.md)
* [Migrazione di un database a SQL Server su una macchina virtuale di Azure](virtual-machines-windows-migrate-sql.md)
* [Disponibilità elevata e ripristino di emergenza di SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-high-availability-dr.md)
* [Performance best practices for SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-performance.md) (Procedure consigliate sulle prestazioni per SQL Server nelle macchine virtuali di Azure)
* [Modelli di applicazione e strategie di sviluppo per SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)


