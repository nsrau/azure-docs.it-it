---
title: Usare InSpec per l'automazione della conformità dell'infrastruttura di Azure
description: Informazioni su come usare InSpec per rilevare problemi nelle distribuzioni di Azure
keywords: azure, chef, devops, macchine virtuali, panoramica, automazione, inspec
ms.service: virtual-machines-linux
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: a5de2ca04a193f97a2a65a043f744abb8e0ea758
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359222"
---
# <a name="use-inspec-for-compliance-automation-of-your-azure-infrastructure"></a>Usare InSpec per l'automazione della conformità dell'infrastruttura di Azure
[InSpec](https://www.chef.io/inspec/) è il linguaggio open source di Chef per descrivere le regole di sicurezza e conformità che può essere condiviso tra gli ingegneri software, le operazioni e i tecnici di sicurezza. InSpec confronta lo stato reale dell'infrastruttura con lo stato desiderato, definito in codice InSpec di facile scrittura e lettura. InSpec rileva le violazioni e visualizza i risultati in un report, ma consente anche all'utente di assumere il controllo della correzione.

È possibile usare InSpec per convalidare lo stato delle risorse e dei gruppi di risorse all'interno di una sottoscrizione, tra cui macchine virtuali, configurazioni di rete, impostazioni di Azure Active Directory e altro ancora.

Questo articolo descrive i vantaggi dell'uso di InSpec per semplificare la sicurezza e la conformità in Azure.

## <a name="make-compliance-easy-to-understand-and-assess"></a>Semplificare la comprensione e la valutazione della conformità
I requisiti contenuti nella documentazione sulla conformità scritta in fogli di calcolo o in documenti Word sono soggetti a interpretazioni diverse. Con InSpec i requisiti operativi vengono trasformati in codice eseguibile, provvisto di versione e leggibile dall'utente. Il codice elimina le divergenze sugli elementi da valutare a favore di test tangibili con finalità chiare.

## <a name="detect-fleet-wide-issues-and-prioritize-their-remediation"></a>Rilevare i problemi estesi e assegnare priorità alla loro correzione
La modalità di rilevamento senza agenti di InSpec consente di valutare rapidamente e su larga scala il livello di esposizione. I metadati predefiniti per i punteggi di impatto/gravità consentono di determinare le aree sulle quali concentrare le correzioni. È anche possibile scrivere rapidamente regole in risposta a nuove vulnerabilità o normative e implementarle immediatamente.

## <a name="satisfy-audits"></a>Soddisfare i controlli
InSpec consente di rispondere alle richieste di controllo in qualsiasi momento e non solo a intervalli predeterminati, ad esempio con frequenza trimestrale o annuale. Con l'esecuzione di test InSpec in modo continuo si inizia un ciclo di controllo conoscendo nel dettaglio la cronologia e lo stato di conformità, evitando così risultati inattesi derivanti dalle conclusioni di un revisore.

## <a name="next-steps"></a>Passaggi successivi

* Provare InSpec in Azure Cloud Shell [![Avvia Cloud Shell](https://shell.azure.com/images/launchcloudshell.png "Avvia Cloud Shell")](https://shell.azure.com)
