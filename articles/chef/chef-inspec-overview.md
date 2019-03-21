---
title: Usare InSpec per l'automazione della conformità dell'infrastruttura di Azure
description: Informazioni su come usare InSpec per rilevare problemi nelle distribuzioni di Azure
keywords: azure, chef, devops, macchine virtuali, panoramica, automazione, inspec
ms.service: virtual-machines-linux
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 03/19/2019
ms.topic: article
ms.openlocfilehash: bdfa30b48c79a8910d503bb9e54a42c30e5adba6
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58293924"
---
# <a name="use-inspec-for-compliance-automation-of-your-azure-infrastructure"></a>Usare InSpec per l'automazione della conformità dell'infrastruttura di Azure

[InSpec](https://www.chef.io/inspec/) è il linguaggio open source di Chef per descrivere le regole di sicurezza e conformità che può essere condiviso tra gli ingegneri software, le operazioni e i tecnici di sicurezza. InSpec confronta lo stato reale dell'infrastruttura con lo stato desiderato, definito in codice InSpec di facile scrittura e lettura. InSpec rileva le violazioni e visualizza i risultati in un report, ma consente anche all'utente di assumere il controllo della correzione.

InSpec è possibile usare per convalidare lo stato delle risorse e gruppi di risorse in una sottoscrizione, tra cui macchine virtuali, le configurazioni di rete, le impostazioni di Azure Active Directory e altro ancora.

Questo articolo descrive i vantaggi dell'uso di InSpec per semplificare la sicurezza e la conformità in Azure.

## <a name="make-compliance-easy-to-understand-and-assess"></a>Semplificare la comprensione e la valutazione della conformità

Documentazione sulla conformità scritte in fogli di calcolo o in documenti di Word lascia requisiti aperta all'interpretazione. Con InSpec i requisiti operativi vengono trasformati in codice eseguibile, provvisto di versione e leggibile dall'utente. Il codice elimina le divergenze sugli elementi da valutare a favore di test tangibili con finalità chiare.

## <a name="detect-fleet-wide-issues-and-prioritize-their-remediation"></a>Rilevare i problemi estesi e assegnare priorità alla loro correzione

La modalità di rilevamento senza agenti di InSpec consente di valutare rapidamente e su larga scala il livello di esposizione. I metadati predefiniti per i punteggi di impatto/gravità consentono di determinare le aree sulle quali concentrare le correzioni. È anche possibile scrivere rapidamente regole in risposta a nuove vulnerabilità o normative e implementarle immediatamente.

## <a name="audit-azure-virtual-machines-with-policy-guest-configuration"></a>Controllare le macchine virtuali di Azure con configurazione dei criteri di Guest

Azure supporta direttamente l'uso di Chef InSpec definizioni per controllare le macchine virtuali di Azure attraverso [Guest configurazione dei criteri di Azure](/azure/governance/policy/concepts/guest-configuration). Configurazione guest valuta una macchina virtuale a una definizione di Chef InSpec specificata e i report di conformità tramite criteri di Azure. I risultati di questi controlli vengono segnalati anche tramite i log di monitoraggio di Azure; Abilitazione degli avvisi e altri scenari di automazione.

## <a name="satisfy-audits"></a>Soddisfare i controlli

InSpec consente di rispondere alle richieste di controllo in qualsiasi momento e non solo a intervalli predeterminati, ad esempio con frequenza trimestrale o annuale. Con l'esecuzione di test InSpec in modo continuo si inizia un ciclo di controllo conoscendo nel dettaglio la cronologia e lo stato di conformità, evitando così risultati inattesi derivanti dalle conclusioni di un revisore.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"] 
> [Provare a InSpec in Azure Cloud Shell](https://shell.azure.com)