---
title: Eseguire una VM di SQL Server in un host dedicato di Azure
description: Informazioni su come eseguire una VM di SQL Server in un host dedicato di Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 140d95aeb7baf4b342044bc178d108239236229b
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92677665"
---
# <a name="run-sql-server-vm-on-an-azure-dedicated-host"></a>Eseguire una VM di SQL Server in un host dedicato di Azure 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Questo articolo illustra in dettaglio le specifiche dell'uso di una VM di SQL Server con un [host dedicato di Azure](/azure/virtual-machines/windows/dedicated-hosts). Altre informazioni sull'host dedicato di Azure sono disponibili nel post di blog [Introduzione all'host dedicato di Azure](https://azure.microsoft.com/blog/introducing-azure-dedicated-host/). 

## <a name="overview"></a>Panoramica
L'[host dedicato di Azure](/azure/virtual-machines/windows/dedicated-hosts) è un servizio che offre server fisici, in grado di ospitare una o più macchine virtuali, dedicati a una sottoscrizione di Azure. Gli host dedicati sono gli stessi server fisici usati nei data center di Microsoft, offerti come risorsa. È possibile effettuare il provisioning di host dedicati all'interno di un'area, di una zona di disponibilità e di un dominio di errore. In seguito è possibile inserire le VM direttamente negli host con provisioning, nella configurazione più adatta alle proprie esigenze.

## <a name="limitations"></a>Limitazioni

- Non tutte le serie di macchine virtuali sono supportate negli host dedicati e la disponibilità delle serie di VM varia in base all'area. Per altre informazioni, vedere [Panoramica degli host dedicati di Azure](../../../virtual-machines/dedicated-hosts.md).

## <a name="licensing"></a>Gestione delle licenze

È possibile scegliere tra due diverse opzioni di licenza quando si inserisce la VM di SQL Server in un host dedicato di Azure. 

  - **SQL VM licensing** (Licenza di VM di SQL): si tratta dell'opzione di licenza esistente, in cui si paga ogni licenza di VM di SQL Server singolarmente. 
  - **Dedicated host licensing** (Licenza di host dedicato): si tratta del nuovo modello di licenza disponibile per l'host dedicato di Azure, in cui le licenze di SQL Server vengono aggregate e pagate a livello di host. 


Opzioni a livello di host per l'uso di licenze di SQL Server esistenti: 
  - Vantaggio Azure Hybrid di SQL Server Enterprise Edition
    - Disponibile per i clienti con Software Assurance o sottoscrizione.
    - Concede in licenza tutti i core fisici disponibili e consente di sfruttare la virtualizzazione illimitata, fino al numero massimo di vCPU supportato dall'host.
        - Per altre informazioni sull'applicazione di Vantaggio Azure Hybrid per l'host dedicato di Azure, vedere [Domande frequenti su Vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/faq/). 
  - Licenze di SQL Server acquisite prima del 1 ottobre
      - SQL Server Enterprise Edition include opzioni di licenza sia a livello di host che per VM. 
      - In SQL Server Standard Edition è disponibile solo l'opzione di licenza per VM. 
          - Per altre informazioni, vedere [Termini e condizioni per i prodotti Microsoft](https://www.microsoft.com/licensing/product-licensing/products). 
  - Se non è selezionata alcuna opzione per host dedicato di SQL Server, è possibile selezionare Vantaggio Azure Hybrid per SQL Server a livello di singole VM, come si farebbe con le VM multi-tenant.



## <a name="provisioning"></a>Provisioning  
Il provisioning di una VM di SQL Server nell'host dedicato non è diverso da qualsiasi altra macchina virtuale di Azure. È possibile eseguirlo usando[Azure PowerShell](../../../virtual-machines/windows/dedicated-hosts-powershell.md), il [portale di Azure](../../../virtual-machines/windows/dedicated-hosts-portal.md) e l'[interfaccia della riga di comando di Azure](../../../virtual-machines/linux/dedicated-hosts-cli.md).

Il processo di aggiunta di una VM di SQL Server esistente all'host dedicato richiede tempi di inattività, ma non influisce sui dati e non implica perdite di dati. Tuttavia, prima dello spostamento è necessario eseguire il backup di tutti i database, inclusi i database di sistema.

## <a name="virtualization"></a>Virtualizzazione 

Uno dei vantaggi di un host dedicato è la virtualizzazione illimitata. Ad esempio, è possibile avere licenze per 64 vCore, ma configurare l'host in modo che abbia 128 vCore, ottenendo così il doppio dei vCore con solo la metà delle licenze di SQL Server. 

Poiché l'host è dedicato, è possibile impostare la virtualizzazione con un rapporto 1:2. 

## <a name="faq"></a>Domande frequenti

**D: Come funziona Vantaggio Azure Hybrid per le licenze di Windows Server/SQL Server nell'host dedicato di Azure?**

A: I clienti possono usare il valore delle licenze Windows Server e SQL Server esistenti con Software Assurance o licenze di sottoscrizione idonee per pagare una tariffa ridotta nell'host dedicato di Azure usando Vantaggio Azure Hybrid. I clienti di Windows Server Datacenter e SQL Server Enterprise Edition ottengono una virtualizzazione illimitata, ovvero la possibilità di distribuire il maggior numero possibile di macchine virtuali Windows Server nell'host in base alla capacità fisica del server sottostante, quando acquistano la licenza per l'intero host e usano Vantaggio Azure Hybrid.  Tutti i carichi di lavoro di Windows Server e SQL Server nell'host dedicato di Azure sono anche idonei per gli aggiornamenti della sicurezza estesa per Windows Server e SQL Server 2008/R2 senza costi aggiuntivi. 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti: 

* [Panoramica di SQL Server in una macchina virtuale Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Domande frequenti su SQL Server in una VM di Windows](frequently-asked-questions-faq.md)
* [Informazioni sui prezzi di SQL Server in una VM di Windows](pricing-guidance.md)
* [Note sulla versione di SQL Server in una VM di Windows](doc-changes-updates-release-notes.md)


