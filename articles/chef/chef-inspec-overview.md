---
title: Usare InSpec per l'automazione della conformità dell'infrastruttura di Azure
description: Informazioni su come usare InSpec per rilevare problemi nelle distribuzioni di Azure
keywords: azure, chef, devops, macchine virtuali, panoramica, automazione, inspec
ms.service: virtual-machines-linux
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: 4193b7fdb3932cbffa2b56b5d7eee6f3b573bd99
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/17/2018
ms.locfileid: "34259607"
---
# <a name="use-inspec-for-compliance-automation-of-your-azure-infrastructure"></a>Usare InSpec per l'automazione della conformità dell'infrastruttura di Azure
[InSpec](https://www.chef.io/inspec/) è un framework gratuito e open source per la verifica e il test delle applicazioni e dell'infrastruttura. InSpec confronta lo stato reale del sistema con lo stato desiderato, definito in codice InSpec di facile scrittura e lettura. InSpec rileva le violazioni e visualizza i risultati in un report, ma consente anche all'utente di assumere il controllo della correzione. È possibile usare InSpec per convalidare lo stato delle macchine virtuali in esecuzione in Azure. È anche possibile usare InSpec per analizzare e convalidare lo stato di risorse e gruppi di risorse all'interno di una sottoscrizione.

Questo articolo descrive i vantaggi dell'uso di InSpec per semplificare la sicurezza e la conformità in Azure.

## <a name="make-compliance-easy-to-understand-and-assess"></a>Semplificare la comprensione e la valutazione della conformità
Con InSpec i requisiti operativi vengono trasformati in codice eseguibile, provvisto di versione e leggibile dall'utente. In tal modo i test possono essere inclusi in profili componibili, nei quali le eccezioni vengono definite e personalizzate in base alle esigenze.

## <a name="detect-fleet-wide-issues-and-prioritize-their-remediation"></a>Rilevare i problemi estesi e assegnare priorità alla loro correzione
Le modalità di rilevamento senza agenti di InSpec consentono di valutare rapidamente e su larga scala il livello di esposizione. I metadati predefiniti per i punteggi di impatto/gravità consentono di determinare le aree sulle quali concentrare le correzioni.

## <a name="inspect-machines-data-and-new-saas-apis"></a>Ispezionare computer, dati e nuove API SaaS
Le funzionalità di conformità delle API cloud InSpec consentono di effettuare asserzioni generiche e granulari sulla conformità del cloud e di generare report in modo continuativo.

## <a name="satisfy-audits"></a>Soddisfare i controlli
InSpec consente di rispondere alle richieste di controllo in qualsiasi momento e non solo a intervalli predeterminati, ad esempio con frequenza trimestrale o annuale. InSpec consente di iniziare un ciclo di controllo conoscendo nel dettaglio lo stato della conformità, per evitare risultati inattesi derivanti dalle conclusioni di un revisore.

## <a name="reduce-ambiguity-and-miscommunication-regarding-rules"></a>Ridurre l'ambiguità e i fraintendimenti riguardanti le regole
Nella documentazione, le configurazioni e i processi possono essere soggetti a interpretazioni diverse. Il codice eseguibile elimina le divergenze sugli elementi da valutare e promuove test tangibili con finalità chiare.

## <a name="keep-up-with-rapidly-changing-threat-and-compliance-landscapes"></a>Adattamento costante a scenari di minacce e conformità in continua evoluzione
InSpec consente di scrivere codice di rilevamento e di pubblicarlo il giorno stesso oppure di scrivere nuove regole per rispondere tempestivamente alle nuove normative. Ciò significa che l'evoluzione delle minacce e dei regolamenti non rappresenta più un'emergenza.

## <a name="next-steps"></a>Passaggi successivi
* [Creare una macchina virtuale Windows in Azure usando Chef](/azure/virtual-machines/windows/chef-automation)