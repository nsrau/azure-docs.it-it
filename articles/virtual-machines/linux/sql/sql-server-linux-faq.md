---
title: Domande frequenti su SQL Server in macchine virtuali Linux di Azure | Microsoft Docs
description: Questo articolo offre risposta ad alcune domande frequenti sull'esecuzione di SQL Server in macchine virtuali Linux di Azure.
services: virtual-machines-linux
documentationcenter: 
author: rothja
manager: jhubbard
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: troubleshooting
ms.workload: iaas-sql-server
ms.date: 10/05/2017
ms.author: jroth
ms.openlocfilehash: a001ae116e33e0b7be4431b0bc4c8bb319f4e801
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="frequently-asked-questions-for-sql-server-on-linux-azure-virtual-machines"></a>Domande frequenti su SQL Server nelle macchine virtuali Linux di Azure

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](sql-server-linux-faq.md)

Questo argomento offre risposta ad alcune delle domande più comuni sull'esecuzione di [SQL Server in macchine virtuali Linux di Azure](sql-server-linux-virtual-machines-overview.md).

> [!NOTE]
> Questo argomento descrive i problemi specifici di SQL Server in macchine virtuali Linux. Se si esegue SQL Server in macchine virtuali Windows, vedere le [domande frequenti su Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="frequently-asked-questions"></a>Domande frequenti

1. **Come si crea una macchina virtuale Linux di Azure con SQL Server?**

   La soluzione più semplice consiste nel creare una macchina virtuale Linux che include SQL Server. Per un'esercitazione sulla registrazione in Azure e sulla creazione di una VM di SQL dal portale, vedere [Effettuare il provisioning di una macchina virtuale Linux di SQL Server nel portale di Azure](provision-sql-server-linux-virtual-machine.md). È anche possibile installare manualmente in una macchina virtuale un'edizione di SQL Server con licenza gratuita (Developer o Express) o riutilizzando una licenza locale. Se si usa la funzionalità Bring Your Own License, è necessario avere [Mobilità delle licenze tramite Software Assurance in Azure](https://azure.microsoft.com/pricing/license-mobility).

1. **Come si esegue l'aggiornamento a una nuova versione o a una nuova edizione di SQL Server in una VM di Azure?**

   Attualmente non è disponibile alcun aggiornamento sul posto per SQL Server in esecuzione in una VM di Azure. Creare una nuova macchina virtuale di Azure con la versione o l'edizione di SQL Server voluta, quindi eseguire la migrazione dei database nel nuovo server tramite [tecniche di migrazione dati](https://docs.microsoft.com/sql/linux/sql-server-linux-migrate-overview) standard.

1. **Come si installa una copia di SQL Server con licenza in una VM di Azure?**

   Creare prima di tutto una macchina virtuale con il solo sistema operativo Linux. Eseguire quindi i [passaggi di installazione di SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-setup#platforms) per la distribuzione Linux. Se non si installa una delle edizioni con licenza gratuita di SQL Server, sono necessarie anche una licenza di SQL Server e la [mobilità delle licenze tramite Software Assurance in Azure](https://azure.microsoft.com/pricing/license-mobility/).

1. **Sono disponibili immagini di macchine virtuali Linux BYOL (Bring Your Own License) per SQL Server?**

   Non sono attualmente disponibili immagini di macchine virtuali Linux BYOL per SQL Server. È tuttavia possibile installare manualmente SQL Server in una VM solo Linux, come illustrato nelle domande precedenti.

1. **È possibile modificare una VM per l'uso di una licenza di SQL Server, se è stata creata da una delle immagini della raccolta con pagamento in base al consumo?**

   No. Non è possibile passare dalla licenza con costo al minuto all'uso della propria licenza. È necessario creare una nuova VM Linux, installare SQL Server ed eseguire la migrazione dei dati. Per altre informazioni sull'opzione Bring Your Own License, vedere la domanda precedente.

1. **Quali altri pacchetti di SQL Server correlati vengono installati?**

   Per visualizzare i pacchetti di SQL Server installati per impostazione predefinita nelle VM Linux di SQL Server, vedere [Pacchetti installati](sql-server-linux-virtual-machines-overview.md#packages).

1. **Le soluzioni di disponibilità elevata di SQL Server sono supportate nelle VM di Azure?**

   Attualmente non è possibile. Sia i gruppi di disponibilità AlwaysOn che il clustering di failover richiedono una soluzione di clustering in Linux, ad esempio Pacemaker. Le distribuzioni Linux supportate per SQL Server non supportano i componenti aggiuntivi con disponibilità elevata nel cloud.

1. **Perché non è possibile effettuare il provisioning di una VM di SQL Server RHEL o SLES con una sottoscrizione di Azure che prevede un limite di spesa?**

   Le macchina virtuali RHEL e SLES richiedono una sottoscrizione senza limite di spesa e un metodo di pagamento verificato (in genere una carta di credito) associato alla sottoscrizione. Se si effettua il provisioning di una VM RHEL o SLES senza rimuovere il limite di spesa, la sottoscrizione verrà disabilitata e tutte le VM e i servizi verranno arrestati. Se si verifica questo problema, per abilitare di nuovo la sottoscrizione, [rimuovere il limite di spesa](https://account.windowsazure.com/subscriptions). I crediti rimanenti verranno ripristinati per il ciclo di fatturazione corrente, ma sulla carta di credito verrà addebitato un sovrapprezzo per l'immagine della VM RHEL o SLES se si sceglie di riavviarla e di continuare a eseguirla.

## <a name="resources"></a>Risorse

**Macchine virtuali Linux**:

* [Panoramica di SQL Server in una macchina virtuale Linux](sql-server-linux-virtual-machines-overview.md)
* [Effettuare il provisioning di una macchina virtuale Linux con SQL Server](provision-sql-server-linux-virtual-machine.md)
* [Documentazione di SQL Server in Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)

**Macchine virtuali Windows**:

* [Panoramica di SQL Server in una macchina virtuale Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
* [Effettuare il provisioning di una macchina virtuale Windows di SQL Server](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)
* [Domande frequenti (Windows)](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)