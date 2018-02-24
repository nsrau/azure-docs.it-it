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
ms.date: 12/13/2017
ms.author: jroth
ms.openlocfilehash: 8b556b01aa47aeb3588138dfa61e517c00dc44dc
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="frequently-asked-questions-for-sql-server-on-linux-azure-virtual-machines"></a>Domande frequenti su SQL Server nelle macchine virtuali Linux di Azure

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](sql-server-linux-faq.md)

Questo articolo offre risposte ad alcune delle domande più comuni sull'esecuzione di [SQL Server in macchine virtuali Linux di Azure](sql-server-linux-virtual-machines-overview.md).

> [!NOTE]
> Questo articolo descrive i problemi specifici di SQL Server nelle macchine virtuali Linux. Se si esegue SQL Server in macchine virtuali Windows, vedere le [domande frequenti su Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a id="images"></a> Immagini

1. **Quali immagini della raccolta di macchine virtuali di SQL Server sono disponibili?**

   Azure mantiene le immagini di macchine virtuali per tutte le principali versioni supportate di SQL Server in tutte le edizioni sia per Linux che per Windows. Per altre informazioni, vedere l'elenco completo di [immagini di macchine virtuali Linux](sql-server-linux-virtual-machines-overview.md#create) e [immagini di macchine virtuali Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).

1. **Le immagini della raccolta di macchine virtuali di SQL Server esistenti vengono aggiornate?**

   Ogni due mesi, le immagini di SQL Server nella raccolta di macchine virtuali vengono aggiornate con gli aggiornamenti di Linux e Windows più recenti. Per le immagini Linux si includono gli aggiornamenti di sistema più recenti. Per le immagini Windows, si includono tutti gli aggiornamenti contrassegnati come importanti in Windows Update, inclusi gli aggiornamenti di sicurezza e i Service Pack di SQL Server importanti. Gli aggiornamenti cumulativi di SQL Server vengono gestiti in modo diverso per Linux e Windows. Per Linux, gli aggiornamenti cumulativi di SQL Server sono inclusi nell'aggiornamento. Tuttavia al momento le macchine virtuali Windows non vengono aggiornate con gli aggiornamenti cumulativi di SQL Server o Windows Server.

1. **Quali altri pacchetti di SQL Server correlati vengono installati?**

   Per visualizzare i pacchetti di SQL Server installati per impostazione predefinita nelle VM Linux di SQL Server, vedere [Pacchetti installati](sql-server-linux-virtual-machines-overview.md#packages).

1. **Le immagini di macchine virtuali di SQL Server possono essere rimosse dalla raccolta?**

   Sì. Azure mantiene solo un'immagine per ogni versione e per ogni edizione principale. Ad esempio, quando viene rilasciato un nuovo Service Pack di SQL Server, Azure aggiunge una nuova immagine alla raccolta per quel Service Pack. L'immagine di SQL Server del Service Pack precedente viene immediatamente rimossa dal portale di Azure. Tuttavia è ancora disponibile per il provisioning di PowerShell per i successivi tre mesi. Dopo tre mesi, l'immagine del Service Pack precedente non è più disponibile. Questi criteri di rimozione sono applicabili anche se una versione di SQL Server non è più supportata perché raggiunge la fine del ciclo di vita.

## <a name="creation"></a>Creazione

1. **Come si crea una macchina virtuale Linux di Azure con SQL Server?**

   La soluzione più semplice consiste nel creare una macchina virtuale Linux che include SQL Server. Per un'esercitazione sulla registrazione in Azure e sulla creazione di una VM di SQL dal portale, vedere [Effettuare il provisioning di una macchina virtuale Linux di SQL Server nel portale di Azure](provision-sql-server-linux-virtual-machine.md). È anche possibile installare manualmente in una macchina virtuale un'edizione di SQL Server con licenza gratuita (Developer o Express) o riutilizzando una licenza locale. Se si usa la funzionalità Bring Your Own License, è necessario avere [Mobilità delle licenze tramite Software Assurance in Azure](https://azure.microsoft.com/pricing/license-mobility).

1. **Perché non è possibile effettuare il provisioning di una VM di SQL Server RHEL o SLES con una sottoscrizione di Azure che prevede un limite di spesa?**

   Le macchina virtuali RHEL e SLES richiedono una sottoscrizione senza limite di spesa e un metodo di pagamento verificato (in genere una carta di credito) associato alla sottoscrizione. Se si effettua il provisioning di una VM RHEL o SLES senza rimuovere il limite di spesa, la sottoscrizione verrà disabilitata e tutte le VM e i servizi verranno arrestati. Se si verifica questo problema, per abilitare di nuovo la sottoscrizione, [rimuovere il limite di spesa](https://account.windowsazure.com/subscriptions). I crediti rimanenti verranno ripristinati per il ciclo di fatturazione corrente, ma sulla carta di credito verrà addebitato un sovrapprezzo per l'immagine della VM RHEL o SLES se si sceglie di riavviarla e di continuare a eseguirla.

## <a name="licensing"></a>Licenze

1. **Come si installa una copia di SQL Server con licenza in una VM di Azure?**

   Creare prima di tutto una macchina virtuale con il solo sistema operativo Linux. Eseguire quindi i [passaggi di installazione di SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-setup#platforms) per la distribuzione Linux. Se non si installa una delle edizioni con licenza gratuita di SQL Server, sono necessarie anche una licenza di SQL Server e la [mobilità delle licenze tramite Software Assurance in Azure](https://azure.microsoft.com/pricing/license-mobility/).

1. **Sono disponibili immagini di macchine virtuali Linux BYOL (Bring Your Own License) per SQL Server?**

   Non sono attualmente disponibili immagini di macchine virtuali Linux BYOL per SQL Server. È tuttavia possibile installare manualmente SQL Server in una VM solo Linux, come illustrato nelle domande precedenti.

1. **È possibile modificare una VM per l'uso di una licenza di SQL Server, se è stata creata da una delle immagini della raccolta con pagamento in base al consumo?**

   di serie Non è possibile passare dalla licenza con costo al minuto all'uso della propria licenza. È necessario creare una nuova VM Linux, installare SQL Server ed eseguire la migrazione dei dati. Per altre informazioni sull'opzione Bring Your Own License, vedere la domanda precedente.

## <a name="administration"></a>Administration

1. **È possibile gestire una macchina virtuale Linux di SQL Server con SQL Server Management Studio (SSMS)?**

   Sì, ma lo strumento SSMS è attualmente disponibile solo su Windows. Per usare SSMS con le macchine virtuali Linux di SQL Server è necessario connettersi in remoto da un computer Windows. Su Linux in locale il nuovo strumento [mssql-conf](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf) può eseguire molte attività amministrative. Per visualizzare l'anteprima di uno strumento di gestione del database multi-piattaforma, vedere [SQL Server Operations Studio (anteprima)](https://docs.microsoft.com/sql/sql-operations-studio/what-is).

## <a name="updating-and-patching"></a>Aggiornamento e applicazione di patch

1. **Come si esegue l'aggiornamento a una nuova versione o a una nuova edizione di SQL Server in una VM di Azure?**

   Attualmente non è disponibile alcun aggiornamento sul posto per SQL Server in esecuzione in una VM di Azure. Creare una nuova macchina virtuale di Azure con la versione o l'edizione di SQL Server voluta, quindi eseguire la migrazione dei database nel nuovo server tramite [tecniche di migrazione dati](https://docs.microsoft.com/sql/linux/sql-server-linux-migrate-overview) standard.

## <a name="general"></a>Generale

1. **Le soluzioni di disponibilità elevata di SQL Server sono supportate nelle VM di Azure?**

   Attualmente non è possibile. Sia i gruppi di disponibilità AlwaysOn che il clustering di failover richiedono una soluzione di clustering in Linux, ad esempio Pacemaker. Le distribuzioni Linux supportate per SQL Server non supportano i componenti aggiuntivi con disponibilità elevata nel cloud.

## <a name="resources"></a>Risorse

**Macchine virtuali Linux**:

* [Panoramica di SQL Server in una macchina virtuale Linux](sql-server-linux-virtual-machines-overview.md)
* [Effettuare il provisioning di una macchina virtuale Linux con SQL Server](provision-sql-server-linux-virtual-machine.md)
* [Documentazione di SQL Server in Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)

**Macchine virtuali Windows**:

* [Panoramica di SQL Server in una macchina virtuale Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
* [Effettuare il provisioning di una macchina virtuale Windows di SQL Server](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)
* [Domande frequenti (Windows)](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)