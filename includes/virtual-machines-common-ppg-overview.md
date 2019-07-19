---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 06/28/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 9832ae6010f262322328ec22876a35c89e1bedca
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850327"
---
L'inserimento di macchine virtuali in una singola area riduce la distanza fisica tra le istanze. L'inserimento di tali elementi all'interno di una singola zona di disponibilità li rende anche fisicamente più vicini. Tuttavia, man mano che il footprint di Azure cresce, una singola zona di disponibilità può estendersi su più data center fisici, causando una latenza di rete che influisca sull'applicazione. 

Per ottenere le macchine virtuali il più vicino possibile, ottenendo la latenza più bassa possibile, è consigliabile distribuirle all'interno di un gruppo di posizionamento di prossimità.

Un gruppo di posizionamento vicino è un raggruppamento logico usato per assicurarsi che le risorse di calcolo di Azure siano posizionate fisicamente tra loro. I gruppi di posizionamento prossimità sono utili per i carichi di lavoro in cui è necessario un livello di latenza bassa.


- Bassa latenza tra macchine virtuali autonome.
- Bassa latenza tra le macchine virtuali in un singolo set di disponibilità o un set di scalabilità di macchine virtuali. 
- Bassa latenza tra macchine virtuali autonome, VM in più set di disponibilità o più set di scalabilità. È possibile disporre di più risorse di calcolo in un singolo gruppo di posizionamento per riunire un'applicazione a più livelli. 
- Bassa latenza tra più livelli applicazione con diversi tipi di hardware. Ad esempio, l'esecuzione del back-end usando la serie M in un set di disponibilità e il front-end in un'istanza della serie D, in un set di scalabilità, in un singolo gruppo di posizionamento di prossimità.

> [!IMPORTANT]
> I gruppi di posizionamento di prossimità sono attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> I gruppi di posizionamento di prossimità non sono disponibili in queste aree durante l'anteprima: **Giappone orientale**, **Australia orientale** e **India centrale**.


## <a name="best-practices"></a>Procedure consigliate 
- Per la latenza più bassa, usare i gruppi di posizionamento di prossimità insieme alla rete accelerata. Per altre informazioni, vedere [creare una macchina virtuale Linux con rete accelerata](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) o [creare una macchina virtuale Windows con rete accelerata](/azure/virtual-network/create-vm-accelerated-networking-powershell?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Distribuire tutte le dimensioni delle macchine virtuali in un singolo modello di Resource Manager. Per evitare di dover atterrare su hardware che non supporta tutti gli SKU e le dimensioni delle VM necessari, includere tutti i livelli applicazione in un singolo modello in modo che vengano distribuiti tutti nello stesso momento.
- Se si crea uno script per la distribuzione usando PowerShell, l'interfaccia della riga di comando o l'SDK, `OverconstrainedAllocationRequest`è possibile che si verifichi un errore di allocazione. In questo caso, è necessario arrestare/deallocare tutte le macchine virtuali esistenti e modificare la sequenza nello script di distribuzione per iniziare con le dimensioni/SKU della macchina virtuale non riuscite. 
- Quando si riutilizza un gruppo di posizionamento esistente dal quale sono state eliminate le macchine virtuali, attendere il completamento dell'eliminazione prima di aggiungere macchine virtuali.
- Se la latenza è la prima priorità, inserire le macchine virtuali in un gruppo di posizionamento vicino e l'intera soluzione in una zona di disponibilità. Tuttavia, se la resilienza è la priorità più alta, distribuire le istanze tra più zone di disponibilità (un singolo gruppo di posizionamento di prossimità non può estendersi alle zone).