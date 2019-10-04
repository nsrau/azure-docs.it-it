---
title: SQL Server VM in un host dedicato di Azure
description: Informazioni sui dettagli per l'esecuzione di una macchina virtuale SQL Server in un host dedicato di Azure.
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
ms.openlocfilehash: 8b29bbce1511b022def522d46c74b99967a76ea3
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71204523"
---
# <a name="sql-server-vm-on-an-azure-dedicated-host"></a>SQL Server VM in un host dedicato di Azure 

Questo articolo illustra in dettaglio le specifiche dell'uso di una macchina virtuale SQL Server con un [host dedicato di Azure](/azure/virtual-machines/windows/dedicated-hosts). Altre informazioni sull'host dedicato di Azure sono reperibili nel post di Blog relativo all' [Introduzione a host dedicato di Azure](https://azure.microsoft.com/blog/introducing-azure-dedicated-host/). 

## <a name="overview"></a>Panoramica
[Host dedicato di Azure](/azure/virtual-machines/windows/dedicated-hosts) è un servizio che fornisce server fisici in grado di ospitare una o più macchine virtuali, dedicate a una sottoscrizione di Azure. Gli host dedicati sono gli stessi server fisici usati nei data center di Microsoft, forniti come risorsa. È possibile eseguire il provisioning di host dedicati in un'area, una zona di disponibilità e un dominio di errore. Quindi, è possibile inserire le VM direttamente negli host di cui è stato eseguito il provisioning, in base a qualsiasi configurazione più adatta alle proprie esigenze.


[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-common-dedicated-hosts-preview.md)]


## <a name="licensing"></a>Licenze

È possibile scegliere tra due diverse opzioni di licenza quando si aggiunge la macchina virtuale SQL Server a un host dedicato di Azure. 

  - **Licenze VM SQL**: Si tratta dell'opzione di gestione delle licenze esistente, in cui si paga ogni singolo SQL Server licenza VM. 
  - **Licenze host dedicate**: Il nuovo modello di licenza disponibile per l'host dedicato di Azure, in cui SQL Server licenze vengono aggregate e pagate a livello di host. 


Opzioni a livello di host per l'uso di licenze SQL Server esistenti: 
  - SQL Server Enterprise Edition Vantaggio Azure Hybrid
    - Disponibile per i clienti con SA o sottoscrizione.
    - Concedere in licenza tutti i core fisici disponibili e sfruttare la virtualizzazione illimitata (fino al numero massimo di vCPU supportato dall'host).
        - Per altre informazioni sull'applicazione dell'Vantaggio Azure Hybrid all'host dedicato di Azure, vedere le [domande frequenti su vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/faq/). 
  - Licenze SQL Server acquisite prima del 1 ° ottobre
      - SQL Server Enterprise Edition include opzioni di licenza sia a livello di host che per macchina virtuale. 
      - SQL Server Standard Edition è disponibile solo l'opzione di licenza per macchina virtuale. 
          - Per informazioni dettagliate, vedere le condizioni per i [prodotti Microsoft](https://www.microsoft.com/licensing/product-licensing/products). 
  - Se non è selezionata alcuna opzione SQL Server a livello di host dedicato, è possibile selezionare SQL Server vantaggio Azure Hybrid a livello di singole VM, come per le VM multi-tenant.



## <a name="provisioning"></a>Provisioning  
Il provisioning di una macchina virtuale SQL Server nell'host dedicato non è diverso da qualsiasi altra macchina virtuale di Azure. Questa operazione può essere eseguita usando [Azure PowerShell](../dedicated-hosts-powershell.md), l' [portale di Azure](../dedicated-hosts-portal.md)e l'interfaccia della riga di comando di [Azure](../../linux/dedicated-hosts-cli.md).

Il processo di aggiunta di una macchina virtuale SQL Server esistente all'host dedicato richiede tempi di inattività, ma non avrà alcun effetto sulla perdita di dati. Tuttavia, prima dello spostamento è necessario eseguire il backup di tutti i database, inclusi i database di sistema.

## <a name="virtualization"></a>Virtualizzazione 

Uno dei vantaggi di un host dedicato è la virtualizzazione illimitata. Ad esempio, è possibile avere licenze per 64 Vcore, ma è possibile configurare l'host in modo da avere 128 Vcore, in modo da ottenere il doppio dei Vcore ma solo la metà delle licenze SQL Server. 

Poiché è l'host, si è idonei a impostare la virtualizzazione con un rapporto 1:2. 

## <a name="faq"></a>Domande frequenti

**D: Come funziona il Vantaggio Azure Hybrid per le licenze di Windows Server/SQL Server nell'host dedicato di Azure?**

R: I clienti possono usare il valore delle licenze Windows Server e SQL Server esistenti con Software Assurance o licenze di sottoscrizione idonee per pagare una tariffa ridotta nell'host dedicato di Azure usando Vantaggio Azure Hybrid. I clienti di Windows Server Datacenter e SQL Server Enterprise Edition ottengono la virtualizzazione illimitata (distribuire il maggior numero possibile di macchine virtuali Windows Server nell'host soggetto alla capacità fisica del server sottostante) quando vengono concesse in licenza l'intero host e usare Vantaggio Azure Hybrid.  Tutti i carichi di lavoro di Windows Server e SQL Server nell'host dedicato di Azure sono anche idonei per gli aggiornamenti della sicurezza estesa per Windows Server e SQL Server 2008/R2 senza costi aggiuntivi. 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti: 

* [Panoramica di SQL Server in una macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Domande frequenti per SQL Server in una macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Linee guida sui prezzi per SQL Server in una macchina virtuale Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Note sulla versione per SQL Server in una macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


