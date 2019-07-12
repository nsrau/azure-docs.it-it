---
title: Come eseguire un aggiornamento sul posto dell'edizione di SQL Server in VM di Azure | Microsoft Docs
description: Informazioni su come modificare l'edizione della VM di SQL Server in Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/26/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 05d2170fe9e6055179bf49d803d4739ddc05be89
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607546"
---
# <a name="how-to-perform-an-in-place-upgrade-of-sql-server-edition-on-azure-vm"></a>Come eseguire un aggiornamento sul posto dell'edizione di SQL Server in VM di Azure

Questo articolo descrive come modificare l'edizione di SQL Server per un Server SQL esistente in una macchina virtuale Windows in Azure. 

L'edizione di SQL Server è determinato dal codice product key e viene specificato con il processo di installazione. L'edizione determina cosa [funzionalità](/sql/sql-server/editions-and-components-of-sql-server-2017) sono disponibili all'interno del prodotto SQL Server. È possibile modificare l'edizione di SQL Server con il supporto di installazione e di downgrade per ridurre i costi o l'aggiornamento per abilitare altre funzionalità.

Se è stato aggiornato l'edizione di SQL Server usando i supporti di installazione dopo la registrazione con il provider di risorse di VM di SQL, quindi per aggiornare Azure fatturazione in conseguenza deve impostare la proprietà di edizione di SQL Server della risorsa VM di SQL come indicato di seguito:

1. Accedere al [portale di Azure](https://portal.azure.com). 
1. Passare alla risorsa di macchina virtuale di SQL Server. 
1. Sotto **le impostazioni**, selezionare **Configure** e quindi selezionare l'edizione desiderata di SQL Server dall'elenco a discesa sotto **edizione**. 

   ![Metadati di Change edition](media/virtual-machines-windows-sql-change-edition/edition-change-in-portal.png)

1. Esaminare l'avviso che informa che è necessario modificare prima edizione di SQL Server e tale proprietà edition deve corrispondere l'edizione di SQL Server. 
1. Selezionare **applica** per applicare le modifiche di metadati edition. 


## <a name="prerequisites"></a>Prerequisiti

Per effettuare una modifica sul posto dell'edizione di SQL Server, è necessario quanto segue: 

- Una [sottoscrizione di Azure](https://azure.microsoft.com/free/).
- Un Windows [VM di SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) registrata con il [provider di risorse di VM di SQL](virtual-machines-windows-sql-register-with-resource-provider.md).
- Configurare i file multimediali con l'edizione desiderata di SQL Server. I clienti che hanno [assurance software](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) possono ottenere i supporti di installazione dalle [Volume Licensing Center](https://www.microsoft.com/Licensing/servicecenter/default.aspx). I clienti non dispongono di software assurance possa usare i supporti di installazione da un'immagine di VM di SQL Server marketplace con loro edizione desiderato.


## <a name="upgrade-edition"></a>Aggiornamento edizione

  > [!WARNING]
  > - **L'aggiornamento dell'edizione di SQL Server verrà riavviato il servizio per SQL Server, nonché eventuali servizi associati, ad esempio Analysis Services e R Services.** 

Per aggiornare l'edizione di SQL Server, ottenere i supporti di installazione di SQL Server per l'edizione desiderata di SQL Server e quindi eseguire le operazioni seguenti:

1. Avviare Setup.exe dai supporti di installazione di SQL Server. 
1. Passare a **manutenzione** e scegliere il **aggiornamento edizione** opzione. 

   ![Aggiornamento edizione di SQL Server](media/virtual-machines-windows-sql-change-edition/edition-upgrade.png)

1. Selezionare **successivo** fino a raggiungere il **pronto per l'aggiornamento edizione** pagina e quindi selezionare **aggiornamento**. Finestra di configurazione potrebbe bloccarsi per alcuni minuti durante la modifica è stata applicata, e quindi verrà visualizzato un **completa** pagina per confermare che l'aggiornamento dell'edizione è stata completata. 

Dopo l'aggiornamento di edizione di SQL Server, è necessario modificare la proprietà "Edition" della macchina virtuale Sql nel portale di Azure come illustrato in precedenza; verranno aggiornati i metadati e fatturazione associato a questa macchina virtuale.

## <a name="downgrade-edition"></a>Edizione di downgrade

  > [!WARNING]
  > - **Il downgrade dell'edizione di SQL Server è necessario disinstallare completamente SQL Server, che possono essere soggette a tempo di inattività aggiuntivo**. 


Per effettuare il downgrade dell'edizione di SQL Server, è necessario disinstallare SQL Server completamente e reinstallarlo nuovamente con i supporti di installazione Edizione desiderato. 

È possibile effettuare il downgrade dell'edizione di SQL Server seguendo questa procedura:

1. Eseguire il backup tutti i database, compresi i database di sistema. 
1. Spostare i database di sistema (master, model e msdb) in una nuova posizione. 
1. Disinstallare completamente tutti i relativi servizi e SQL Server. 
1. Riavviare la macchina virtuale. 
1. Installare SQL Server usando il supporto con l'edizione desiderata di SQL Server.
1. Installare il service pack più recenti e gli aggiornamenti cumulativi.  
1. Sostituire i nuovo database di sistema che sono stati creati durante l'installazione con i database di sistema in precedenza è stato spostato in un percorso diverso. 

Una volta che viene eseguito il downgrade edizione di SQL Server, è necessario modificare la proprietà 'Edizione' della macchina virtuale SQL nel portale di Azure come illustrato in precedenza; verranno aggiornati i metadati e fatturazione associato a questa macchina virtuale.

## <a name="remarks"></a>Note

 - La proprietà edition per la macchina virtuale di SQL Server deve corrispondere l'edizione di SQL Server installata nella macchina virtuale per tutte le macchine virtuali SQL compresi i tipi di licenze PAYG e BYOL.
 - Se si elimina la risorsa di macchina virtuale SQL Server, tornerà all'impostazione a livello di codice edizione dell'immagine.
  - La possibilità di modificare l'edizione è una funzionalità del provider di risorse di VM di SQL. Distribuire automaticamente un'immagine del marketplace tramite il portale di Azure registra una VM SQL Server con il provider di risorse. Tuttavia, i clienti che sono self-installazione di SQL Server saranno necessario manualmente [registrare le VM di SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md).
- Aggiunta di una macchina virtuale di SQL Server a un set di disponibilità, è necessario ricreare la macchina virtuale. Come tali, eventuali macchine virtuali aggiunte a un livello set, torno all'edizione predefinita e l'edizione dovranno essere modificati nuovamente.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere i seguenti articoli: 

* [Panoramica di SQL Server in una macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server sulle domande frequenti di una macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Guida ai prezzi per le VM di SQL Server in Azure).
* [SQL Server sulle note sulla versione della macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


