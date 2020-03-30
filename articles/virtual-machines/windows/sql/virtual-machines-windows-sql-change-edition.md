---
title: Modifica sul posto dell'edizione di SQL ServerIn-place change of SQL Server edition
description: Informazioni su come modificare l'edizione della macchina virtuale DI SQL Server in Azure.Learn how to change the edition of your SQL Server virtual machine in Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/14/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: bd9622d3e34198402bc45e5d855d05b0c8e35eb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77605453"
---
# <a name="in-place-change-of-sql-server-edition-on-azure-vm"></a>Modifica sul posto dell'edizione di SQL Server nella macchina virtuale di AzureIn-place change of SQL Server edition on Azure VM

Questo articolo descrive come modificare l'edizione di SQL Server in una macchina virtuale Windows in Azure.This article describes how to change the edition of SQL Server on a Windows virtual machine in Azure. 

L'edizione di SQL ServerSQL Server è determinata dal codice Product Key e viene specificata durante il processo di installazione utilizzando il supporto di installazione. L'edizione determina le [funzionalità](/sql/sql-server/editions-and-components-of-sql-server-2017) disponibili nel prodotto SQL ServerSQL Server . È possibile modificare l'edizione di SQL Server con il supporto di installazione e eseguire il downgrade per ridurre i costi o eseguire l'aggiornamento per abilitare altre funzionalità.

Dopo aver modificato internamente l'edizione di SQL Server nella macchina virtuale di SQL Server, è necessario aggiornare la proprietà dell'edizione di SQL Server nel portale di Azure ai fini della fatturazione. 

## <a name="prerequisites"></a>Prerequisiti

Per eseguire una modifica sul posto dell'edizione di SQL ServerSQL Server, è necessario quanto segue: 

- Una [sottoscrizione di Azure.](https://azure.microsoft.com/free/)
- Una [macchina virtuale di SQL Server in Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) registrata con il provider di risorse della macchina virtuale [SQL.](virtual-machines-windows-sql-register-with-resource-provider.md)
- Supporti di installazione con **l'edizione desiderata** di SQL Server. I clienti che dispongono di [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) possono ottenere il supporto di installazione dal Volume [Licensing Center](https://www.microsoft.com/Licensing/servicecenter/default.aspx). I clienti che non dispongono di Software Assurance possono usare il supporto di installazione da `C:\SQLServerFull`un'immagine della macchina virtuale di SQL Server di Azure Marketplace con l'edizione desiderata (in genere si trova in ). 


## <a name="upgrade-an-edition"></a>Aggiornare un'edizione

> [!WARNING]
> L'aggiornamento dell'edizione di SQL ServerSQL Server riavvierà il servizio per SQL ServerSQL Server, insieme a tutti i servizi associati, ad esempio Analysis Services e R Services. 

Per aggiornare l'edizione di SQL Server, ottenere il supporto di installazione di SQL Server per l'edizione desiderata di SQL Server e quindi eseguire le operazioni seguenti:

1. Aprire Setup.exe dal supporto di installazione di SQL Server. 
1. Vai a **Manutenzione** e scegli l'opzione **Di aggiornamento edizione.** 

   ![Selezione per l'aggiornamento dell'edizione di SQL Server](media/virtual-machines-windows-sql-change-edition/edition-upgrade.png)

1. Selezionare **Avanti** fino a raggiungere la pagina **Pronto per l'aggiornamento dell'edizione** e quindi selezionare **Aggiorna**. La finestra di installazione potrebbe bloccarsi per alcuni minuti mentre la modifica ha effetto. Una pagina **Completa** confermerà che l'aggiornamento dell'edizione è terminato. 

Dopo aver aggiornato l'edizione di SQL Server, modificare la proprietà dell'edizione della macchina virtuale di SQL Server nel portale di Azure.After the SQL Server edition is upgraded, modify the edition property of the SQL Server virtual machine in the Azure portal. In questo modo verranno aggiornati i metadati e la fatturazione associati a questa macchina virtuale.

## <a name="downgrade-an-edition"></a>Eseguire il downgrade di un'edizione

Per eseguire il downgrade dell'edizione di SQL ServerSQL Server, è necessario disinstallare completamente SQL Server e reinstallarla con il supporto di installazione dell'edizione desiderato. 

> [!WARNING]
> La disinstallazione di SQL Serverpotrebbe comportare tempi di inattività aggiuntivi. 

È possibile eseguire il downgrade dell'edizione di SQL Server attenendosi alla seguente procedura:

1. Eseguire il backup di tutti i database, inclusi i database di sistema. 
1. Spostare i database di sistema (master, model e msdb) in una nuova posizione. 
1. Disinstallare completamente SQL Server e tutti i servizi associati. 
1. Riavviare la macchina virtuale. 
1. Installare SQL Server utilizzando il supporto con l'edizione desiderata di SQL Server.
1. Installare i service pack e gli aggiornamenti cumulativi più recenti.  
1. Sostituire i nuovi database di sistema creati durante l'installazione con i database di sistema precedentemente spostati in un percorso diverso. 

Dopo il downgrade dell'edizione di SQL Server, modificare la proprietà dell'edizione della macchina virtuale di SQL Server nel portale di Azure.After the SQL Server edition is downgraded, modify the edition property of the SQL Server virtual machine in the Azure portal. In questo modo verranno aggiornati i metadati e la fatturazione associati a questa macchina virtuale.

## <a name="change-edition-in-portal"></a>Modificare l'edizione nel portaleChange edition in portal 

Dopo aver modificato l'edizione di SQL Server usando il supporto di installazione e aver registrato la macchina virtuale di SQL Server con il provider di risorse della [macchina virtuale SQL,](virtual-machines-windows-sql-register-with-resource-provider.md)è possibile usare il portale di Azure per modificare la proprietà Edition della macchina virtuale di SQL Server per scopi di fatturazione. A questo scopo, attenersi alla procedura seguente: 

1. Accedere al [portale](https://portal.azure.com)di Azure . 
1. Passare alla risorsa della macchina virtuale di SQL Server.Go to your SQL Server virtual machine resource. 
1. In **Impostazioni**selezionare **Configura**. Selezionare quindi l'edizione desiderata di SQL Server dall'elenco a discesa in **Edizione**. 

   ![Modificare i metadati dell'edizione](media/virtual-machines-windows-sql-change-edition/edition-change-in-portal.png)

1. Esaminare l'avviso che indica che è necessario modificare prima l'edizione di SQL Serversql Server e che la proprietà dell'edizione deve corrispondere all'edizione di SQL ServerSQL Server . 
1. Selezionare **Applica** per applicare le modifiche ai metadati dell'edizione. 


## <a name="remarks"></a>Osservazioni

- La proprietà edition per la macchina virtuale di SQL Server deve corrispondere all'edizione dell'istanza di SQL Server installata per tutte le macchine virtuali di SQL Server, inclusi i tipi di licenze con pagamento in base al popup e portare la propria licenza.
- Se si elimina la risorsa VM di SQL Server, si tornerà all'impostazione dell'edizione hardcoded dell'immagine.
- La possibilità di modificare l'edizione è una funzionalità del provider di risorse della macchina virtuale SQL. La distribuzione di un'immagine di Azure Marketplace tramite il portale di Azure registra automaticamente una macchina virtuale di SQL Server con il provider di risorse. Tuttavia, i clienti che si installano automaticamente SQL Server dovranno registrare manualmente la macchina virtuale di [SQL Server.](virtual-machines-windows-sql-register-with-resource-provider.md)
- L'aggiunta di una macchina virtuale di SQL Server a un set di disponibilità richiede la ricreazione della macchina virtuale. Tutte le macchine virtuali aggiunte a un set di disponibilità torneranno all'edizione predefinita e l'edizione dovrà essere modificata di nuovo.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti: 

* [Panoramica di SQL Server in una macchina virtuale WindowsOverview of SQL Server on a Windows VM](virtual-machines-windows-sql-server-iaas-overview.md)
* [Domande frequenti su SQL Server in una macchina virtuale WindowsFAQ for SQL Server on a Windows VM](virtual-machines-windows-sql-server-iaas-faq.md)
* [Indicazioni sui prezzi per SQL Server in una macchina virtuale WindowsPricing guidance for SQL Server on a Windows VM](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Note sulla versione per SQL Server in una macchina virtuale WindowsRelease notes for SQL Server on a Windows VM](virtual-machines-windows-sql-server-iaas-release-notes.md)


