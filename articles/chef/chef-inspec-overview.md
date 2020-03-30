---
title: Usare InSpec per l'automazione della conformità dell'infrastruttura di Azure
description: Informazioni su come usare InSpec per rilevare problemi nelle distribuzioni di Azure
keywords: azure, chef, devops, macchine virtuali, panoramica, automazione, inspec
ms.date: 03/19/2019
ms.topic: article
ms.openlocfilehash: 2531277eb1aa6048c93240031652e09582409e56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74158237"
---
# <a name="use-inspec-for-compliance-automation-of-your-azure-infrastructure"></a>Usare InSpec per l'automazione della conformità dell'infrastruttura di Azure

[InSpec](https://www.chef.io/inspec/) è il linguaggio open source di Chef per descrivere le regole di sicurezza e conformità che può essere condiviso tra gli ingegneri software, le operazioni e i tecnici di sicurezza. InSpec confronta lo stato reale dell'infrastruttura con lo stato desiderato, definito in codice InSpec di facile scrittura e lettura. InSpec rileva le violazioni e visualizza i risultati in un report, ma consente anche all'utente di assumere il controllo della correzione.

È possibile usare InSpec per convalidare lo stato delle risorse e dei gruppi di risorse in una sottoscrizione, incluse le macchine virtuali, le configurazioni di rete, le impostazioni di Azure Active Directory e altro ancora.

Questo articolo descrive i vantaggi dell'uso di InSpec per semplificare la sicurezza e la conformità in Azure.

## <a name="make-compliance-easy-to-understand-and-assess"></a>Semplificare la comprensione e la valutazione della conformità

La documentazione di conformità scritta in fogli di calcolo o documenti di Word lascia i requisiti aperti all'interpretazione. Con InSpec i requisiti operativi vengono trasformati in codice eseguibile, provvisto di versione e leggibile dall'utente. Il codice elimina le divergenze sugli elementi da valutare a favore di test tangibili con finalità chiare.

## <a name="detect-fleet-wide-issues-and-prioritize-their-remediation"></a>Rilevare i problemi estesi e assegnare priorità alla loro correzione

La modalità di rilevamento senza agenti di InSpec consente di valutare rapidamente e su larga scala il livello di esposizione. I metadati predefiniti per i punteggi di impatto/gravità consentono di determinare le aree sulle quali concentrare le correzioni. È anche possibile scrivere rapidamente regole in risposta a nuove vulnerabilità o normative e implementarle immediatamente.

## <a name="audit-azure-virtual-machines-with-policy-guest-configuration"></a>Controllare le macchine virtuali di Azure con la configurazione guest dei criteriAudit Azure virtual machines with Policy Guest Configuration

Azure supporta direttamente l'uso delle definizioni Chef InSpec per controllare le macchine virtuali di Azure tramite la configurazione Guest dei criteri di [Azure.](/azure/governance/policy/concepts/guest-configuration) Guest Configuration evaluates a Linux virtual machine to a provided Chef InSpec definition and reports compliance back through Azure Policy. I risultati di questi controlli vengono segnalati anche tramite i log di Monitoraggio di Azure.The results of these audits are also reported through Azure Monitor logs; l'abilitazione di avvisi e altri scenari di automazione.

## <a name="satisfy-audits"></a>Soddisfare i controlli

InSpec consente di rispondere alle richieste di controllo in qualsiasi momento e non solo a intervalli predeterminati, ad esempio con frequenza trimestrale o annuale. Con l'esecuzione di test InSpec in modo continuo si inizia un ciclo di controllo conoscendo nel dettaglio la cronologia e lo stato di conformità, evitando così risultati inattesi derivanti dalle conclusioni di un revisore.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"] 
> [Provare InSpec in Azure Cloud Shell](https://shell.azure.com)