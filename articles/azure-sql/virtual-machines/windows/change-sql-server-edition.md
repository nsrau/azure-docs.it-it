---
title: Modifica sul posto dell'edizione di SQL Server
description: Informazioni su come modificare l'edizione del SQL Server macchina virtuale in Azure per eseguire il downgrade per ridurre i costi o l'aggiornamento per abilitare altre funzionalità.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/14/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 997e867798922975757a588ef50248f0d09a96e0
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92789846"
---
# <a name="in-place-change-of-sql-server-edition-on-azure-vm"></a>Modifica sul posto dell'edizione di SQL Server in una macchina virtuale di Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Questo articolo descrive come modificare l'edizione di SQL Server in una macchina virtuale Windows in Azure. 

L'edizione di SQL Server è determinata dal codice Product Key e viene specificata durante il processo di installazione usando il supporto di installazione. L'edizione stabilisce quali [funzionalità](/sql/sql-server/editions-and-components-of-sql-server-2017) sono disponibili nel prodotto SQL Server. È possibile modificare l'edizione di SQL Server con il supporto di installazione ed effettuare il downgrade per ridurre i costi o l'aggiornamento per abilitare altre funzionalità.

Dopo aver modificato internamente l'edizione di SQL Server nella VM di SQL Server, è necessario aggiornare la proprietà edition di SQL Server nel portale di Azure ai fini della fatturazione. 

## <a name="prerequisites"></a>Prerequisiti

Per eseguire una modifica sul posto dell'edizione di SQL Server, è necessario quanto segue: 

- Una [sottoscrizione di Azure](https://azure.microsoft.com/free/).
- Una [VM di SQL Server in Windows](./create-sql-vm-portal.md) registrata con il [provider di risorse per VM SQL](sql-vm-resource-provider-register.md).
- Configurare i supporti con un' **edizione specifica** di SQL Server. I clienti che usano [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) possono ottenere il supporto di installazione dal [Centro per i contratti multilicenza](https://www.microsoft.com/Licensing/servicecenter/default.aspx). I clienti che non hanno Software Assurance possono usare il supporto di configurazione da un'immagine della VM di SQL Server di Azure Marketplace con un'edizione specifica (in genere si trova in `C:\SQLServerFull`). 


## <a name="upgrade-an-edition"></a>Aggiornare un'edizione

> [!WARNING]
> Con l'aggiornamento dell'edizione di SQL Server il servizio viene riavviato per SQL Server, insieme a tutti i servizi associati, ad esempio Analysis Services e R Services. 

Per aggiornare l'edizione di SQL Server, ottenere i supporti di installazione di SQL Server per un'edizione specifica di SQL Server, quindi eseguire le operazioni seguenti:

1. Aprire Setup.exe dal supporto di installazione di SQL Server. 
1. Passare a **Manutenzione** e scegliere l'opzione **Aggiornamento edizione** . 

   ![Selezione per l'aggiornamento dell'edizione di SQL Server](./media/change-sql-server-edition/edition-upgrade.png)

1. Selezionare **Avanti** finché non si raggiunge la pagina **Aggiornamento dell'edizione** e quindi selezionare **Aggiornamento** . La finestra di installazione potrebbe non rispondere per alcuni minuti mentre viene applicata la modifica. La pagina **Completato** conferma che l'aggiornamento dell'edizione è terminato. 

Dopo l'aggiornamento dell'edizione di SQL Server, modificare la proprietà edition della macchina virtuale di SQL Server nel portale di Azure. In questo modo i metadati e la fatturazione associati alla macchina virtuale vengono aggiornati.

## <a name="downgrade-an-edition"></a>Effettuare il downgrade di un'edizione

Per effettuare il downgrade dell'edizione di SQL Server, è necessario disinstallare completamente SQL Server e reinstallarlo nuovamente con i relativi supporti di installazione dell'edizione. 

> [!WARNING]
> La disinstallazione di SQL Server può comportare tempi di inattività aggiuntivi. 

È possibile effettuare il downgrade dell'edizione di SQL Server attenendosi alla procedura seguente:

1. Eseguire il backup di tutti i database, inclusi i database di sistema. 
1. Spostare i database di sistema (master, model e msdb) in una nuova posizione. 
1. Disinstallare completamente SQL Server e tutti i servizi associati. 
1. Riavviare la macchina virtuale. 
1. Installare SQL Server usando i supporti con un'edizione specifica di SQL Server.
1. Installare i Service Pack e gli aggiornamenti cumulativi più recenti.  
1. Sostituire i nuovi database di sistema creati durante l'installazione con i database di sistema spostati in precedenza in un'altra posizione. 

Dopo il downgrade dell'edizione di SQL Server, modificare la proprietà edition della macchina virtuale di SQL Server nel portale di Azure. In questo modo i metadati e la fatturazione associati alla macchina virtuale vengono aggiornati.

## <a name="change-edition-in-portal"></a>Modificare l'edizione nel portale 

Dopo aver modificato l'edizione di SQL Server usando il supporto di installazione e aver registrato la VM di SQL Server con il [provider di risorse per VM SQL](sql-vm-resource-provider-register.md), è possibile usare il portale di Azure per modificare la proprietà edition della VM di SQL Server ai fini della fatturazione. A questo scopo, attenersi alla procedura seguente: 

1. Accedere al [portale di Azure](https://portal.azure.com). 
1. Passare alla propria risorsa di macchina virtuale di SQL Server. 
1. In **Impostazioni** selezionare **Configura** . Selezionare quindi l'edizione di SQL Server dall'elenco a discesa sotto **Edizione** . 

   ![Modificare i metadati dell'edizione](./media/change-sql-server-edition/edition-change-in-portal.png)

1. Esaminare l'avviso che indica che per prima cosa è necessario modificare l'edizione di SQL Server e che la proprietà edition deve corrispondere all'edizione di SQL Server. 
1. Selezionare **Applica** per applicare le modifiche ai metadati dell'edizione. 


## <a name="remarks"></a>Osservazioni

- La proprietà edition della VM di SQL Server deve corrispondere all'edizione dell'istanza di SQL Server installata per tutte le macchine virtuali di SQL Server, inclusi i tipi di licenza con pagamento in base al consumo e Bring Your Own License.
- Se si elimina la risorsa VM di SQL Server, sarà necessario ripristinare l'impostazione dell'edizione hardcoded dell'immagine.
- La possibilità di modificare l'edizione è una funzionalità del provider di risorse per VM SQL. La distribuzione di un'immagine di Azure Marketplace attraverso il portale di Azure registra automaticamente una macchina virtuale di SQL Server con il provider di risorse. Tuttavia, i clienti che eseguono l'installazione automatica di SQL Server dovranno [registrare manualmente la loro VM di SQL Server](sql-vm-resource-provider-register.md).
- Per aggiungere una VM di SQL Server a un set di disponibilità, è necessario ricrearla. Tutte le VM aggiunte a un set di disponibilità verranno riportate all'edizione predefinita e l'edizione dovrà essere di nuovo modificata.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti: 

* [Panoramica di SQL Server in una macchina virtuale Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Domande frequenti su SQL Server in una macchina virtuale Windows](frequently-asked-questions-faq.md)
* [Informazioni sui prezzi di SQL Server in una macchina virtuale Windows](pricing-guidance.md)
* [Note sulla versione di SQL Server in una macchina virtuale Windows](doc-changes-updates-release-notes.md)