---
title: Pianificazione della capacità per Azure Stack | Microsoft Docs
description: Informazioni sulla pianificazione della capacità per le distribuzioni di Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2019
ms.author: jeffgilb
ms.reviewer: prchint
ms.lastreviewed: 03/29/2019
ms.openlocfilehash: e4678b445dce5b337fb7d51e1b938adb944b4440
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58648725"
---
# <a name="azure-stack-capacity-planning"></a>Azure Stack la pianificazione della capacità
Durante la valutazione di una soluzione di Azure Stack, sono disponibili opzioni di configurazione hardware che hanno un impatto diretto sulla capacità complessiva del Cloud Azure Stack. Questi sono disponibili i classiche della CPU, memoria densità, configurazione dell'archiviazione e complessiva scalabilità soluzione o numero di server. A differenza di una soluzione di virtualizzazione tradizionale, non è applicabile l'aritmetica semplice di questi componenti per determinare la capacità utilizzabile. Il primo motivo è che Azure Stack è progettato per ospitare i componenti di infrastruttura o di gestione all'interno della soluzione stessa. Il secondo motivo è che alcune delle capacità della soluzione è riservato per il supporto di resilienza; l'aggiornamento del software della soluzione in modo da ridurre al minimo l'interruzione dei carichi di lavoro tenant.

> [!IMPORTANT]
> Le informazioni sulla pianificazione della capacità fornita e relativo foglio di calcolo vengono fornite solo come una Guida che consentono di rendere la pianificazione di Azure Stack e le decisioni di configurazione. Non sono progettati per essere utilizzato come soluzione alternativa per il proprio analisi e l'analisi. 

## <a name="compute-and-storage-capacity-planning"></a>Calcolo e pianificazione della capacità di archiviazione
Una soluzione di Azure Stack è creata come un cluster iperconvergente di calcolo, rete e archiviazione. Ciò consente l'uso effettivo o condivisione di tutta la capacità hardware del cluster, definito come unità di scala per Azure Stack, in modo da garantire la disponibilità e scalabilità. Tutto il software dell'infrastruttura è ospitato all'interno di un set di macchine virtuali (VM) e condivide stessi server fisici come macchine virtuali tenant. Tutte le macchine virtuali vengono gestite come tecnologie di clustering di Windows Server dell'unità di scala e le singole istanze di Hyper-V. Questo approccio semplifica l'acquisizione e la gestione di una soluzione di Azure Stack e mette a disposizione per lo spostamento e la scalabilità di tutti i servizi (tenant e dell'infrastruttura) tra l'intero di unità di scala.

La risorsa solo fisica che non ha un provisioning eccessivo in una soluzione di Azure Stack è la memoria del server. Altre risorse, CPU Core, la larghezza di banda di rete e capacità di archiviazione, sarà possibile effettuati per ottimizzare l'uso delle risorse disponibili. Quando si calcola la capacità disponibile per una soluzione, la memoria del server fisico è il collaboratore principale. L'utilizzo di altre risorse è quindi comprendere la percentuale di overprovision che è possibile e qual è accettabile per il carico di lavoro previsto.

Circa 30 VM vengono usate per ospitare l'infrastruttura di Azure Stack e, in totale, usano circa 230 GB di memoria e core virtuali 140. La base logica per questo numero di VM è per soddisfare la separazione di servizio necessari per soddisfare la protezione, scalabilità, i requisiti dell'applicazione di patch e manutenzione. Questa struttura interno del servizio consente l'introduzione di nuovi servizi di infrastruttura futuri non appena vengono sviluppati.

Per supportare l'aggiornamento automatico di tutti i server fisici e i componenti software dell'infrastruttura, o patch e aggiornamento, l'infrastruttura e VM utente posizionamenti non utilizzerà tutte le risorse di memoria dell'unità di scala. Una quantità di memoria totale in tutti i server di un'unità di scala sarà non allocata per il supporto di requisiti di resilienza della soluzione. Ad esempio, quando viene aggiornato l'immagine di Windows Server del server fisico, le macchine virtuali ospitate nel server vengono spostate in un' posizione all'interno dell'unità di scala durante l'aggiornamento di immagini di Windows Server del server. Una volta completato l'aggiornamento, il server viene riavviato e restituito per i carichi di lavoro di manutenzione. L'obiettivo per le patch e l'aggiornamento di una soluzione di Azure Stack è per evitare la necessità di arrestare le VM ospitate. Impegno a raggiungere tale obiettivo, nel minimo della capacità di memoria del server viene allocato per consentire lo spostamento di macchine virtuali all'interno dell'unità di scala. Questa selezione host e lo spostamento viene applicata per le macchine virtuali dell'infrastruttura e le macchine virtuali create per conto dell'utente o del tenant della soluzione Azure Stack. Risultati implementazione finali sono tali che la quantità di memoria riservata per supportare lo spostamento della macchina virtuale necessario può essere molto più di una singola della capacità del server a causa di problemi di selezione host quando le macchine virtuali hanno diversi requisiti di memoria. Un sovraccarico aggiuntivo nella categoria dell'uso della memoria è quello dell'istanza del Server Windows se stesso. L'istanza del sistema operativo di base per ogni server utilizzerà la memoria per il sistema operativo e le relative tabelle di pagina virtuale con la memoria utilizzata da Hyper-V nella gestione di tutte le macchine virtuali ospitate.

La capacità di macchina virtuale è determinata dalla memoria disponibile. I core virtuali per core fisici rapporti fornire come la densità della macchina virtuale cambierà capacità CPU disponibile, a meno che la soluzione viene costruita con un numero elevato di core fisici (viene scelta un'altra CPU). Lo stesso vale della capacità di archiviazione e capacità di archiviazione della cache.

Gli esempi precedenti di densità di VM sono solo a scopo esplicativo. È ulteriore complessità nei calcoli relativi al supporto. È necessario per comprendere a fondo le opzioni di pianificazione della capacità e dalla capacità risulta, disponibile contatto con Microsoft o un partner di soluzioni.

Il resto di questa sezione descrive i requisiti di distribuzione Azure Stack per il calcolo e archiviazione. Usare queste informazioni per ottenere una base di comprendere quali componenti sono necessari e i relativi valori di configurazione minima. Vengono inoltre fornite informazioni aggiuntive per descrivere la configurazione della soluzione per quanto riguarda la capacità disponibile e potenziali limiti del sistema in relazione al tenant la capacità e prestazioni della capacità.

> [!NOTE]
> I requisiti della pianificazione della capacità per la rete sono minimi, perché solo le dimensioni di indirizzo VIP pubblico sono configurabile. Per informazioni su come aggiungere ulteriori indirizzi IP pubblici in Azure Stack, vedere [aggiungere gli indirizzi IP pubblici](azure-stack-add-ips.md).


## <a name="next-steps"></a>Passaggi successivi
[Pianificazione della capacità di calcolo](capacity-planning-compute.md)
