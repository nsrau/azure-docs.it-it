---
title: Domande frequenti su Azure Confidential computing
description: Risposte alle domande frequenti su Azure Confidential computing.
author: JBCook
ms.topic: troubleshooting
ms.workload: infrastructure
ms.service: virtual-machines
ms.subservice: workloads
ms.date: 4/17/2020
ms.author: jencook
ms.openlocfilehash: e9cb691ef60f612672078a9ef84db904c79cbc87
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82189446"
---
# <a name="frequently-asked-questions-for-azure-confidential-computing"></a>Domande frequenti su Azure Confidential computing

Questo articolo fornisce le risposte ad alcune delle domande più comuni sull'esecuzione di [carichi di lavoro di elaborazione riservati in Azure](overview.md).

Se il problema di Azure non viene risolto in questo articolo, visitare i forum di Azure su [MSDN e stack overflow](https://azure.microsoft.com/support/forums/). È possibile pubblicare il problema in questi forum o pubblicare un post [ @AzureSupport su su Twitter](https://twitter.com/AzureSupport). È anche possibile inviare una richiesta di supporto tecnico di Azure. Per inviare una richiesta di supporto, nella [pagina supporto tecnico di Azure](https://azure.microsoft.com/support/options/)selezionare Ottieni supporto.

## <a name="confidential-computing-virtual-machines"></a>Macchine virtuali di elaborazione riservate<a id="vm-faq"></a>

1. **Come è possibile iniziare a distribuire macchine virtuali della serie DCsv2?**

   Ecco alcuni modi in cui è possibile distribuire una macchina virtuale DCsv2:
   - Uso di un [modello di Azure Resource Manager](../virtual-machines/windows/template-description.md)
   - Dal [portale di Azure](https://portal.azure.com/#create/hub)
   - Nel modello di soluzione del Marketplace [Azure Confidential (macchina virtuale)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview) . Il modello di soluzione Marketplace consente di vincolare un cliente agli scenari supportati (aree, immagini, disponibilità, crittografia del disco). 

1. **Tutte le immagini del sistema operativo funzionano con Azure Confidential computing?**

   No. Le macchine virtuali possono essere distribuite solo in macchine virtuali di seconda generazione. Offriamo supporto di generazione 2 per Ubuntu server 18,04, Ubuntu Server 16,04 e Windows Server 2016 datacenter. Scopri di più sulle macchine virtuali di seconda generazione in [Linux](../virtual-machines/linux/generation-2.md) e [Windows](../virtual-machines/windows/generation-2.md)

1. **Le macchine virtuali DCsv2 sono disabilitate nel portale e non è possibile selezionarne una**

   In base alla bolla delle informazioni accanto alla macchina virtuale, sono disponibili diverse azioni da eseguire:
    -   **UnsupportedGeneration**: modificare la generazione dell'immagine della macchina virtuale in "Gen2".
    -   **NotAvailableForSubscription** : l'area non è ancora disponibile per la sottoscrizione. Selezionare un'area disponibile.
    -   **InsufficientQuota**: [creare una richiesta di supporto per aumentare la quota](../azure-portal/supportability/per-vm-quota-requests.md). Le sottoscrizioni della versione di valutazione gratuita non hanno una quota per le macchine virtuali di calcolo 

1. **Le macchine virtuali DCsv2 non vengono visualizzate quando si tenta di cercarle nel selettore delle dimensioni del portale**

   Assicurarsi di aver selezionato un'area disponibile. Assicurarsi inoltre di selezionare "Cancella tutti i filtri" nel selettore delle dimensioni. 

1. **Qual è la differenza tra le macchine virtuali serie DCsv2 e DC?**

   Le macchine virtuali serie DC vengono eseguite in processori Intel a 6 core precedenti con Intel SGX. Questi hanno meno memoria totale, meno la memoria della pagina enclave (gabbia) e sono disponibili in meno aree. Queste VM sono disponibili solo negli Stati Uniti orientali e l'Europa occidentale sono disponibili in due dimensioni: Standard_DC2s e Standard_DC4s. Non saranno disponibili in GA e potranno essere distribuiti solo nell'istanza del Marketplace relativa alla [macchina virtuale di calcolo riservata della serie DC [Preview]](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.confidentialcompute?tab=Overview)

1. **Le macchine virtuali DCsv2 sono disponibili a livello globale?**

   No, queste macchine virtuali sono disponibili solo in aree selezionate. Controllare la [pagina prodotti per aree](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) per le aree più recenti disponibili. 

1. **Ricerca per categorie installare Open enclave SDK?**
   
   Per istruzioni su come installare OE SDK in un computer in Azure o in locale, seguire le istruzioni [disponibili in Open enclave SDK GitHub](https://github.com/openenclave/openenclave).
     
   È anche possibile passare all'Open enclave SDK GitHub per le istruzioni di installazione specifiche del sistema operativo:
     - [Installare OE SDK in Windows](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Windows.md)
     - [Installare l'SDK di OE in Ubuntu 18,04](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_18.04.md)
     - [Installare l'SDK di OE in Ubuntu 16,04](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_16.04.md)
