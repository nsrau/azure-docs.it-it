---
title: SQL Server VM on an Azure Dedicated Host
description: Informazioni sui dettagli per l'esecuzione di una macchina virtuale di SQL Server in un host dedicato di Azure.Learn about the details for running a SQL Server VM on an Azure Dedicated Host.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: edb2d3fa670475d9b08fe05494035949181a9240
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834345"
---
# <a name="sql-server-vm-on-an-azure-dedicated-host"></a>SQL Server VM on an Azure Dedicated Host 

Questo articolo descrive in dettaglio le specifiche dell'uso di una macchina virtuale di SQL Server con un host [dedicato di Azure.](/azure/virtual-machines/windows/dedicated-hosts) Ulteriori informazioni sull'host dedicato di Azure sono disponibili nel post di blog [Introducing Azure Dedicated Host](https://azure.microsoft.com/blog/introducing-azure-dedicated-host/). 

## <a name="overview"></a>Panoramica
Host dedicato di Azure è un servizio che fornisce server fisici, in grado di ospitare una o più macchine virtuali, dedicate a una sottoscrizione di [Azure.Azure Dedicated Host](/azure/virtual-machines/windows/dedicated-hosts) is a service that provides physical servers - able to host one or more virtual machines - dedicated to one Azure subscription. Gli host dedicati sono gli stessi server fisici utilizzati nei data center di Microsoft, forniti come risorsa. È possibile eseguire il provisioning di host dedicati all'interno di un'area, di una zona di disponibilità e di un dominio di errore. È quindi possibile inserire le macchine virtuali direttamente negli host di cui è stato eseguito il provisioning, in qualsiasi configurazione soddisfi meglio le proprie esigenze.

## <a name="limitations"></a>Limitazioni

- I set di scalabilità delle macchine virtuali non sono attualmente supportati in host dedicati.
- Sono supportate le seguenti serie di macchine virtuali: DSv3 ed ESv3. 

## <a name="licensing"></a>Gestione delle licenze

È possibile scegliere tra due diverse opzioni di licenza quando si aggiunge la macchina virtuale di SQL Server a un host dedicato di Azure.You can choose between two different licensing options when adding your SQL Server VM to an Azure Dedicated Host. 

  - **Licenza SQL VM:** questa è l'opzione di licenza esistente, in cui si paga per ogni licenza DI macchina virtuale di SQL Server singolarmente. 
  - **Licenze host dedicate:** il nuovo modello di licenza disponibile per l'host dedicato di Azure, in cui le licenze di SQL Server vengono raggruppate e pagate a livello di host. 


Opzioni a livello di host per l'utilizzo di licenze di SQL Server esistenti:Host-level options for using existing SQL Server licenses: 
  - Vantaggio di SQL Server Enterprise Edition Azure Hybrid
    - Disponibile per i clienti con SA o abbonamento.
    - Licenza di tutti i core fisici disponibili e godere di virtualizzazione illimitata (fino al massimo vCPU supportate dall'host).
        - Per altre informazioni sull'applicazione del vantaggio Azure Hybrid all'host dedicato di Azure, vedere le domande frequenti sul vantaggio Azure Hybrid.For more information about applying the Azure Hybrid Benefit to Azure Dedicated host, see the [Azure Hybrid Benefit FAQ](https://azure.microsoft.com/pricing/hybrid-benefit/faq/). 
  - Licenze di SQL Server acquisite prima del 1 ottobre
      - Sql Server Enterprise Edition include opzioni di licenza sia a livello di host che in base alla macchina virtuale. 
      - In SQL Server Standard Edition è disponibile solo l'opzione di licenza per macchina virtuale. 
          - Per informazioni dettagliate, vedere [Le Condizioni del prodotto Microsoft](https://www.microsoft.com/licensing/product-licensing/products). 
  - Se non è selezionata alcuna opzione a livello di host dedicata di SQL Server, è possibile selezionare AHB di SQL Server a livello di singole macchine virtuali, proprio come con le macchine virtuali multi-tenant.



## <a name="provisioning"></a>Provisioning  
Provisioning a SQL Server VM to the dedicated host is no different than any other Azure Virtual Machine. A tale scopo, è possibile usare [Azure PowerShell,](../dedicated-hosts-powershell.md)il [portale di Azure](../dedicated-hosts-portal.md)e [l'interfaccia della](../../linux/dedicated-hosts-cli.md)riga di comando di Azure.

Il processo di aggiunta di una macchina virtuale di SQL Server esistente all'host dedicato richiede tempi di inattività, ma non influirà sui dati e non avrà perdita di dati. Tuttavia, è necessario eseguire il backup di tutti i database, inclusi i database di sistema, prima dello spostamento.

## <a name="virtualization"></a>Virtualizzazione 

Uno dei vantaggi di un host dedicato è la virtualizzazione illimitata. Ad esempio, è possibile avere licenze per 64 vCore, ma è possibile configurare l'host in modo che abbia 128 vCore, in modo da ottenere il doppio dei vCore, ma solo il pagamento per la metà delle licenze di SQL Server. 

Dal momento che è il tuo host, sei idoneo a impostare la virtualizzazione con un rapporto 1:2. 

## <a name="faq"></a>Domande frequenti

**D: Come funziona il vantaggio Azure Hybrid per le licenze di Windows Server/SQL Server nell'host dedicato di Azure?**

R: I clienti possono usare il valore delle licenze di Windows Server e SQL Server esistenti con Software Assurance o licenze di sottoscrizione qualificate per pagare una tariffa ridotta in Host dedicato di Azure usando il vantaggio Azure Hybrid. I clienti di Windows Server Datacenter e SQL Server Enterprise Edition ottengono una virtualizzazione illimitata (distribuire il maggior numero possibile di macchine virtuali Windows Server nell'host in base alla capacità fisica del server sottostante) quando concedono in licenza l'intero host e usare il vantaggio Azure Hybrid.  Tutti i carichi di lavoro di Windows Server e SQL Server in Azure Dedicated Host sono idonei anche per gli aggiornamenti della sicurezza estesa per Windows Server e SQL Server 2008/R2 senza alcun costo aggiuntivo. 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti: 

* [Panoramica di SQL Server in una macchina virtuale WindowsOverview of SQL Server on a Windows VM](virtual-machines-windows-sql-server-iaas-overview.md)
* [Domande frequenti su SQL Server in una macchina virtuale WindowsFAQ for SQL Server on a Windows VM](virtual-machines-windows-sql-server-iaas-faq.md)
* [Indicazioni sui prezzi per SQL Server in una macchina virtuale WindowsPricing guidance for SQL Server on a Windows VM](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Note sulla versione per SQL Server in una macchina virtuale WindowsRelease notes for SQL Server on a Windows VM](virtual-machines-windows-sql-server-iaas-release-notes.md)


