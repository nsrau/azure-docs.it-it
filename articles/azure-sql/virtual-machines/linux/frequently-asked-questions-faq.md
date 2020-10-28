---
title: Domande frequenti sulle macchine virtuali SQL Server in Linux | Microsoft Docs
description: Questo articolo fornisce le risposte alle domande frequenti sull'esecuzione di SQL Server in Linux macchine virtuali.
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: troubleshooting
ms.workload: iaas-sql-server
ms.date: 12/13/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: ad991974df30060e552d21a44d5796cd2ba165e2
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792549"
---
# <a name="frequently-asked-questions-for-sql-server-on-linux-virtual-machines"></a>Domande frequenti per SQL Server in Linux macchine virtuali
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](../windows/frequently-asked-questions-faq.md)
> * [Linux](frequently-asked-questions-faq.md)

Questo articolo fornisce le risposte ad alcune delle domande più comuni sull'esecuzione di [SQL Server in Linux macchine virtuali](sql-server-on-linux-vm-what-is-iaas-overview.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="images"></a><a id="images"></a> Immagini

1. **Quali immagini della raccolta di macchine virtuali di SQL Server sono disponibili?**

   Azure gestisce le immagini di macchine virtuali (VM) per tutte le versioni principali supportate di SQL Server in tutte le edizioni sia per Linux che per Windows. Per altre informazioni, vedere l'elenco completo di [immagini di macchine virtuali Linux](sql-server-on-linux-vm-what-is-iaas-overview.md#create) e [immagini di macchine virtuali Windows](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo).

1. **Le immagini della raccolta di macchine virtuali di SQL Server esistenti vengono aggiornate?**

   Ogni due mesi, le immagini di SQL Server nella raccolta di macchine virtuali vengono aggiornate con gli aggiornamenti di Linux e Windows più recenti. Per le immagini Linux si includono gli aggiornamenti di sistema più recenti. Per le immagini di Windows, sono inclusi tutti gli aggiornamenti contrassegnati come importanti in Windows Update, inclusi importanti SQL Server aggiornamenti della protezione e Service Pack. Gli aggiornamenti cumulativi di SQL Server vengono gestiti in modo diverso per Linux e Windows. Per Linux, gli aggiornamenti cumulativi di SQL Server sono inclusi nell'aggiornamento. Tuttavia al momento le macchine virtuali Windows non vengono aggiornate con gli aggiornamenti cumulativi di SQL Server o Windows Server.

1. **Quali altri pacchetti di SQL Server correlati vengono installati?**

   Per visualizzare i pacchetti di SQL Server installati per impostazione predefinita nelle macchine virtuali SQL Server in Linux, vedere [pacchetti installati](sql-server-on-linux-vm-what-is-iaas-overview.md#packages).

1. **Le immagini di macchine virtuali di SQL Server possono essere rimosse dalla raccolta?**

   Sì. Azure mantiene solo un'immagine per ogni versione e per ogni edizione principale. Ad esempio, quando viene rilasciato un nuovo Service Pack di SQL Server, Azure aggiunge una nuova immagine alla raccolta per quel Service Pack. L'immagine di SQL Server del Service Pack precedente viene immediatamente rimossa dal portale di Azure. Tuttavia è ancora disponibile per il provisioning di PowerShell per i successivi tre mesi. Dopo tre mesi, l'immagine del Service Pack precedente non è più disponibile. Questi criteri di rimozione sono applicabili anche se una versione di SQL Server non è più supportata perché raggiunge la fine del ciclo di vita.

## <a name="creation"></a>Creazione

1. **Ricerca per categorie creare una macchina virtuale Linux con SQL Server?**

   La soluzione più semplice consiste nel creare una macchina virtuale Linux che includa SQL Server. Per un'esercitazione sull'iscrizione ad Azure e sulla creazione di una VM SQL Server dal portale, vedere effettuare il [provisioning di una macchina virtuale Linux che esegue SQL Server nel portale di Azure](sql-vm-create-portal-quickstart.md). È anche possibile installare manualmente in una macchina virtuale un'edizione di SQL Server con licenza gratuita (Developer o Express) o riutilizzando una licenza locale. Se si usa la funzionalità Bring Your Own License, è necessario avere [Mobilità delle licenze tramite Software Assurance in Azure](https://azure.microsoft.com/pricing/license-mobility).

1. **Perché non è possibile effettuare il provisioning di una VM di SQL Server RHEL o SLES con una sottoscrizione di Azure che prevede un limite di spesa?**

   Le macchina virtuali RHEL e SLES richiedono una sottoscrizione senza limite di spesa e un metodo di pagamento verificato (in genere una carta di credito) associato alla sottoscrizione. Se si effettua il provisioning di una VM RHEL o SLES senza rimuovere il limite di spesa, la sottoscrizione verrà disabilitata e tutte le VM e i servizi verranno arrestati. Se si verifica questo problema, per abilitare di nuovo la sottoscrizione, [rimuovere il limite di spesa](https://account.windowsazure.com/subscriptions). I crediti rimanenti verranno ripristinati per il ciclo di fatturazione corrente, ma sulla carta di credito verrà addebitato un sovrapprezzo per l'immagine della VM RHEL o SLES se si sceglie di riavviarla e di continuare a eseguirla.

## <a name="licensing"></a>Gestione delle licenze

1. **Come si installa una copia di SQL Server con licenza in una VM di Azure?**

   Creare prima di tutto una macchina virtuale con il solo sistema operativo Linux. Eseguire quindi i [passaggi di installazione di SQL Server](/sql/linux/sql-server-linux-setup#platforms) per la distribuzione Linux. Se non si installa una delle edizioni con licenza gratuita di SQL Server, sono necessarie anche una licenza di SQL Server e la [mobilità delle licenze tramite Software Assurance in Azure](https://azure.microsoft.com/pricing/license-mobility/).

1. **Sono disponibili immagini di macchine virtuali Linux BYOL (Bring Your Own License) per SQL Server?**

   Non sono attualmente disponibili immagini di macchine virtuali Linux BYOL per SQL Server. È tuttavia possibile installare manualmente SQL Server in una VM solo Linux, come illustrato nelle domande precedenti.

1. **È possibile modificare una VM per l'uso di una licenza di SQL Server, se è stata creata da una delle immagini della raccolta con pagamento in base al consumo?**

   No. Non è possibile sostituire una licenza con costo al secondo con una propria licenza. È necessario creare una nuova VM Linux, installare SQL Server ed eseguire la migrazione dei dati. Per altre informazioni sull'opzione Bring Your Own License, vedere la domanda precedente.

## <a name="administration"></a>Amministrazione

1. **È possibile gestire una macchina virtuale Linux che esegue SQL Server con SQL Server Management Studio (SSMS)?**

   Sì, ma lo strumento SSMS è attualmente disponibile solo su Windows. È necessario connettersi in remoto da un computer Windows per usare SSMS con macchine virtuali Linux che eseguono SQL Server. Su Linux in locale il nuovo strumento [mssql-conf](/sql/linux/sql-server-linux-configure-mssql-conf) può eseguire molte attività amministrative. Per uno strumento di gestione database multipiattaforma, vedere [Azure Data Studio](/sql/azure-data-studio/what-is).

1. **Posso rimuovere completamente SQL Server da una macchina virtuale di SQL Server?**

   Sì, ma continueranno a essere addebitati i costi per la VM di SQL Server, come descritto in [Pricing guidance for SQL Server Azure VMs](../windows/pricing-guidance.md?toc=%2fazure%2fvirtual-machines%2flinux%2fsql%2ftoc.json) (Guida ai prezzi per le VM di SQL Server in Azure). Se SQL Server non è più necessario, è possibile distribuire una nuova macchina virtuale ed eseguire la migrazione di dati e applicazioni alla nuova macchina virtuale. Sarà quindi possibile rimuovere la macchina virtuale di SQL Server.

## <a name="updating-and-patching"></a>Aggiornamento e applicazione di patch

1. **Come si esegue l'aggiornamento a una nuova versione o a una nuova edizione di SQL Server in una VM di Azure?**

   Attualmente non è disponibile alcun aggiornamento sul posto per SQL Server in esecuzione in una VM di Azure. Creare una nuova macchina virtuale di Azure con la versione o l'edizione di SQL Server voluta, quindi eseguire la migrazione dei database nel nuovo server tramite [tecniche di migrazione dati](/sql/linux/sql-server-linux-migrate-overview) standard.

## <a name="general"></a>Generale

1. **Sono SQL Server soluzioni a disponibilità elevata supportate nelle macchine virtuali di Azure?**

   Attualmente non è possibile. Gruppi di disponibilità Always On e il clustering di failover richiedono entrambi una soluzione di clustering in Linux, ad esempio pacemaker. Le distribuzioni di Linux supportate per SQL Server non supportano i componenti aggiuntivi a disponibilità elevata nel cloud.

## <a name="resources"></a>Risorse

**Macchine virtuali Linux** :

* [Panoramica di SQL Server in una macchina virtuale Linux](sql-server-on-linux-vm-what-is-iaas-overview.md)
* [Effettuare il provisioning di SQL Server in una VM Linux](sql-vm-create-portal-quickstart.md)
* [Documentazione di SQL Server in Linux](/sql/linux/sql-server-linux-overview)

**Macchine virtuali Windows** :

* [Panoramica di SQL Server in una macchina virtuale Windows](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Effettuare il provisioning di SQL Server in una macchina virtuale Windows](../windows/sql-vm-create-portal-quickstart.md)
* [Domande frequenti (Windows)](../windows/frequently-asked-questions-faq.md)