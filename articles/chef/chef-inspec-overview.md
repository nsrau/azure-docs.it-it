---
title: Usare InSpec per l'automazione della conformità dell'infrastruttura di Azure
description: Informazioni su come usare InSpec per rilevare problemi nelle distribuzioni di Azure
keywords: azure, chef, devops, macchine virtuali, panoramica, automazione, inspec
ms.date: 03/19/2019
ms.topic: article
ms.openlocfilehash: 2531277eb1aa6048c93240031652e09582409e56
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158237"
---
# <a name="use-inspec-for-compliance-automation-of-your-azure-infrastructure"></a>Usare InSpec per l'automazione della conformità dell'infrastruttura di Azure

[InSpec](https://www.chef.io/inspec/) è il linguaggio open source di Chef per descrivere le regole di sicurezza e conformità che può essere condiviso tra gli ingegneri software, le operazioni e i tecnici di sicurezza. InSpec confronta lo stato reale dell'infrastruttura con lo stato desiderato, definito in codice InSpec di facile scrittura e lettura. InSpec rileva le violazioni e visualizza i risultati in un report, ma consente anche all'utente di assumere il controllo della correzione.

È possibile usare INSPEC per convalidare lo stato delle risorse e dei gruppi di risorse in una sottoscrizione, tra cui macchine virtuali, configurazioni di rete, Azure Active Directory impostazioni e altro ancora.

Questo articolo descrive i vantaggi dell'uso di InSpec per semplificare la sicurezza e la conformità in Azure.

## <a name="make-compliance-easy-to-understand-and-assess"></a>Semplificare la comprensione e la valutazione della conformità

La documentazione di conformità scritta in fogli di calcolo o documenti di Word lascia i requisiti aperti all'interpretazione. Con InSpec i requisiti operativi vengono trasformati in codice eseguibile, provvisto di versione e leggibile dall'utente. Il codice elimina le divergenze sugli elementi da valutare a favore di test tangibili con finalità chiare.

## <a name="detect-fleet-wide-issues-and-prioritize-their-remediation"></a>Rilevare i problemi estesi e assegnare priorità alla loro correzione

La modalità di rilevamento senza agenti di InSpec consente di valutare rapidamente e su larga scala il livello di esposizione. I metadati predefiniti per i punteggi di impatto/gravità consentono di determinare le aree sulle quali concentrare le correzioni. È anche possibile scrivere rapidamente regole in risposta a nuove vulnerabilità o normative e implementarle immediatamente.

## <a name="audit-azure-virtual-machines-with-policy-guest-configuration"></a>Controllare le macchine virtuali di Azure con la configurazione Guest dei criteri

Azure supporta direttamente l'uso di definizioni di chef INSPEC per controllare le macchine virtuali di Azure tramite la [configurazione Guest di criteri di Azure](/azure/governance/policy/concepts/guest-configuration). La configurazione Guest valuta una macchina virtuale Linux a una definizione di chef INSPEC e segnala la conformità tramite criteri di Azure. I risultati di questi controlli vengono segnalati anche tramite i log di monitoraggio di Azure. Abilitazione di avvisi e altri scenari di automazione.

## <a name="satisfy-audits"></a>Soddisfare i controlli

InSpec consente di rispondere alle richieste di controllo in qualsiasi momento e non solo a intervalli predeterminati, ad esempio con frequenza trimestrale o annuale. Con l'esecuzione di test InSpec in modo continuo si inizia un ciclo di controllo conoscendo nel dettaglio la cronologia e lo stato di conformità, evitando così risultati inattesi derivanti dalle conclusioni di un revisore.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"] 
> [Provare gli INSPEC nella Azure Cloud Shell](https://shell.azure.com)