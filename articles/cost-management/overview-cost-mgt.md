---
title: Panoramica di Gestione costi di Azure | Microsoft Docs
description: Gestione costi di Azure è una soluzione di gestione dei costi che consente di monitorare e controllare le spese per Azure e di ottimizzare l'uso delle risorse.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/14/2019
ms.topic: overview
ms.service: cost-management
manager: benshy
ms.custom: ''
ms.openlocfilehash: 69f91949347eadcffb3c0d3ff833a40b5e483e24
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58003181"
---
# <a name="what-is-azure-cost-management"></a>Che cos'è la gestione dei costi di Azure?

Gestione costi è il processo che consente di pianificare e controllare in maniera efficace i costi aziendali. Le attività di gestione dei costi vengono eseguite in genere dai team finanziario, di gestione e dai team app. Gestione costi di Azure consente alle organizzazioni di pianificare tenendo presenti i costi. Consente inoltre di analizzare in modo efficace i costi e di intraprendere azioni per ottimizzare la spesa per il cloud. Per altre informazioni su come approcciare la gestione dei costi come organizzazione, consultare l'articolo [Procedure consigliate di Gestione costi di Azure](cost-mgt-best-practices.md).

Anche se correlate, le operazioni di fatturazione e di gestione dei costi sono differenti. La fatturazione è il processo di invio di fatture ai clienti per di merci o servizi e di gestione delle relazioni commerciali.  I team di approvvigionamento e quello finanziario svolgono solitamente le attività di fatturazione.

Gestione costi mostra i costi aziendali e i criteri di utilizzo tramite analisi avanzate. I report in Gestione costi mostrano i costi basati sull'utilizzo da parte dei servizi di Azure e delle offerte di Marketplace di terze parti. I costi sono basati sui prezzi negoziati e considerati negli sconti per la prenotazione e per il Vantaggio Azure Hybrid. Collettivamente, i report mostrano i costi interni ed esterni sull'utilizzo e gli addebiti per Azure Marketplace. Altri addebiti, come gli acquisti di prenotazioni, il supporto e le imposte non vengono ancora visualizzati nel report. I report consentono di comprendere l'uso di risorse e spese e possono suggerire come trovare le anomalie di spesa. È inoltre disponibile analisi predittiva. Gestione costi usa i gruppi di gestione di Azure, i budget e gli elementi consigliati per mostrare chiaramente come sono organizzate le spese e come si potrebbero ridurre i costi.

È possibile usare il portale di Azure o varie API per l'esportazione di automazione per integrare i dati sui costi con i processi e sistemi esterni. Sono inoltre disponibili l'esportazione automatizzata dei dati di fatturazione e report pianificati.

## <a name="plan-and-control-expenses"></a>Pianificare e controllare le spese

Le modalità che Gestione costi usa per pianificare e controllare i costi includono: analisi dei costi, budget, elementi consigliati ed esportazione dei dati di gestione dei costi.

L'analisi dei costi consente di esplorare e analizzare i costi aziendali. È possibile visualizzare i costi aggregati per organizzazione per comprendere dove i costi sono maggiori e identificare le tendenze di spesa. È possibile visualizzare i costi accumulati nel corso del tempo per stimare i trend mensili, trimestrali o addirittura annuali rispetto a un budget.

I budget consentono di pianificare e soddisfare i controlli amministrativi aziendali. Consentono di impedire il superamento delle soglie per costo o i limiti di costo. I budget consentono inoltre di segnalare ad altri utenti le spese per gestire i costi in modo proattivo. Inoltre, aiutano a visualizzare l'avanzamento della spesa nel corso del tempo.

Gli elementi consigliati mostrano come ottimizzare e migliorare l'efficienza identificando le risorse inattive e sottoutilizzate. In alternativa, mostrano le opzioni delle risorse meno costose. Quando si agisce sugli elementi consigliati, si modifica la modalità di uso delle risorse per risparmiare denaro. Per prendere un'iniziativa, visualizzare innanzitutto gli elementi consigliati per l'ottimizzazione dei costi e identificare potenziali inefficienze di utilizzo. Successivamente, agire su un elemento consigliato per modificare l'utilizzo delle risorse di Azure per un'opzione più conveniente. Verificare quindi l'azione per assicurarsi che la modifica apportata abbia avuto esito positivo.

Se si utilizzano sistemi esterni per accedere o per revisionare i dati di gestione dei costi, è possibile esportare facilmente i dati da Azure. È possibile impostare un'esportazione giornaliera pianificata in formato CSV e archiviare i file di dati in archiviazione di Azure. È quindi possibile accedere ai dati dal sistema esterno.

## <a name="consider-cloudyn"></a>Prendere in considerazione Cloudyn

[Cloudyn](overview.md) è un servizio di Azure correlato a Gestione costi. Con Cloudyn, è possibile tenere traccia dell'utilizzo del cloud e delle spese per le risorse di Azure. Supporta anche altri provider di cloud, inclusi AWS e Google. I dashboard report di facile comprensione agevolano l'allocazione dei costi e gli showback/chargeback. Attualmente Gestione costi non supporta showback/chargeback o altri provider di servizi cloud. Tuttavia, Cloudyn è un'opzione che _viene_ supportata. Attualmente Gestione costi, al contrario di Cloudyn, non supporta gli account di Microsoft Cloud Service Provider (CSP). Se si hanno account CSP o se si desidera utilizzare lo showback/chargeback, è possibile usare Cloudyn per gestire i costi.

## <a name="additional-azure-tools"></a>Strumenti aggiuntivi di Azure

Azure offre altri strumenti che non fanno parte del set di funzionalità di Gestione costi di Azure. Tuttavia, svolgono un ruolo importante nel processo di gestione costi. Per altre informazioni riguardo questi strumenti, consultare i collegamenti seguenti.

- [Calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/) - Usare questo strumento per stimare i costi iniziali del cloud.
- [Azure Migrate](../migrate/migrate-overview.md) - Valutare il carico di lavoro corrente del proprio centro dati logico per informazioni dettagliate su ciò che serve da una soluzione di sostituzione di Azure.
- [Azure Advisor](../advisor/advisor-overview.md) - Identificare le macchine virtuali inutilizzate e ricevere consigli sugli acquisti di istanza riservata di Azure.
- [Vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/) - Utilizzare le licenze correnti di Windows Server o SQL Server per permettere alle macchine virtuali in Azure di effettuare un salvataggio.


## <a name="next-steps"></a>Passaggi successivi

Ora che si ha familiarità con Gestione costi, il passaggio successivo serve per iniziare a usare il servizio.

- Iniziare a usare Gestione costi di Azure per eseguire l'[analisi dei costi](quick-acm-cost-analysis.md).
- Per altre informazioni, consultare [Procedure consigliate di Gestione costi di Azure](cost-mgt-best-practices.md).
