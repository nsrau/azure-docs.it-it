---
title: Introduzione al benchmark di sicurezza di Azure
description: Introduzione al benchmark di sicurezza
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: f3b492534bedde50d2413b88fa38cd6325ed5df7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91328259"
---
# <a name="azure-security-benchmark-introduction"></a>Introduzione al benchmark di sicurezza di Azure

I nuovi servizi e funzionalità vengono rilasciati ogni giorno in Azure, gli sviluppatori stanno rapidamente pubblicando nuove applicazioni cloud basate su questi servizi e gli utenti malintenzionati cercano sempre nuovi modi per sfruttare le risorse configurate in modo errato. Il cloud si sposta velocemente, gli sviluppatori si spostano rapidamente e gli utenti malintenzionati sono sempre in movimento. Come è possibile rimanere al passo e assicurarsi che le distribuzioni cloud siano sicure? In che modo le procedure di sicurezza per i sistemi cloud sono diverse rispetto ai sistemi locali? Come è possibile monitorare la coerenza tra molti team di sviluppo indipendenti?

Microsoft ha scoperto che l'uso di *benchmark di sicurezza* può aiutarti a proteggere rapidamente le distribuzioni cloud. Le raccomandazioni di benchmark del provider di servizi cloud offrono un punto di partenza per la selezione di impostazioni di configurazione della sicurezza specifiche nell'ambiente in uso e consentono di ridurre rapidamente i rischi per l'organizzazione.

Il benchmark di sicurezza di Azure include una raccolta di consigli sulla sicurezza a elevato utilizzo che è possibile usare per proteggere i servizi usati in Azure:

- **Controlli di sicurezza**: queste raccomandazioni sono generalmente applicabili nei servizi tenant di Azure e Azure. Ogni raccomandazione identifica un elenco di stakeholder che in genere sono coinvolti nella pianificazione, nell'approvazione o nell'implementazione del benchmark. 
- **Baseline del servizio**: questi applicano i controlli ai singoli servizi di Azure per fornire consigli sulla configurazione di sicurezza del servizio.

## <a name="implement-the-azure-security-benchmark"></a>Implementare il benchmark di sicurezza di Azure
- **Pianificare** l'implementazione del benchmark di sicurezza di Azure esaminando la [documentazione](overview.md) per i controlli aziendali e le linee di base specifiche del servizio per pianificare il Framework di controllo e il modo in cui viene eseguito il mapping a linee guida come CIS (Controls v 7.1) e NIST (SP800-53) Framework.
- **Monitorare** la conformità con lo stato del benchmark di sicurezza di Azure (e altri set di controllo) usando il [dashboard di conformità normativa](../../security-center/security-center-compliance-dashboard.md)del Centro sicurezza di Azure.
- **Definire Guardrails** per automatizzare le configurazioni protette e applicare la conformità al benchmark di sicurezza di Azure e ad altri requisiti dell'organizzazione con i progetti di Azure e i criteri di Azure.
 
Si noti che il benchmark di sicurezza di Azure V2 è allineato alle procedure consigliate per la [sicurezza di Microsoft](/security/compass/microsoft-security-compass-introduction) (in precedenza la bussola di sicurezza di Azure), in modo che il benchmark di sicurezza di Azure fornisca una singola visualizzazione consolidata dei consigli

## <a name="common-use-cases"></a>Casi d'uso comuni

Il benchmark di sicurezza di Azure viene spesso usato per risolvere questi problemi comuni per clienti o partner di servizi che sono:
- Una novità di Azure e la ricerca di procedure consigliate per la sicurezza per garantire una distribuzione sicura.
- Miglioramento del comportamento di sicurezza delle distribuzioni di Azure esistenti per definire la priorità dei principali rischi e mitigazioni.
- Approvazione dei servizi di Azure per l'uso da tecnologia e utilizzo aziendale per soddisfare specifiche linee guida sulla sicurezza.
- Soddisfare i requisiti normativi per i clienti provenienti da settori governativi o ad alta regolamentazione, ad esempio finanza e Healthcare (o fornitori di servizi che devono compilare sistemi per questi clienti). Questi clienti devono assicurarsi che la configurazione di Azure soddisfi le funzionalità di sicurezza specificate in un Framework di settore, ad esempio CIS, NIST o PCI. Il benchmark di sicurezza di Azure offre un approccio efficiente con i controlli già pre-mappati a questi benchmark di settore.

## <a name="terminology"></a>Terminologia

I termini "Control", "benchmark" e "baseline" vengono usati spesso nella documentazione di benchmark di sicurezza di Azure ed è importante comprendere come Azure usa tali termini.


| Termine | Descrizione | Esempio |
|--|--|--|
| Controllo | Un controllo è una descrizione di alto livello di una funzionalità o di un'attività che deve essere risolta e non è specifica di una tecnologia o di un'implementazione. | La protezione dei dati è uno dei controlli di sicurezza. Questo controllo contiene azioni specifiche che devono essere risolte per garantire la protezione dei dati. |
| Benchmark | Un benchmark contiene raccomandazioni sulla sicurezza per una tecnologia specifica, ad esempio Azure. Le raccomandazioni vengono classificate in base al controllo a cui appartengono. | Il benchmark di sicurezza di Azure include i consigli sulla sicurezza specifici della piattaforma Azure |
| Di base | Una linea di base è l'implementazione del benchmark per il singolo servizio di Azure. Ogni organizzazione decide la raccomandazione di benchmark e le configurazioni corrispondenti sono necessarie nell'ambito di implementazione di Azure. | L'azienda Contoso cerca di abilitare le funzionalità di sicurezza di Azure SQL seguendo la configurazione consigliata nella baseline della sicurezza di Azure SQL.

I commenti e suggerimenti sul benchmark di sicurezza di Azure sono benvenuti. Si consiglia di fornire commenti nell'area commenti e suggerimenti riportata di seguito. Se si preferisce condividere l'input in modo più privato con il team di benchmark di sicurezza di Azure, è possibile compilare il modulo all'indirizzo https://aka.ms/AzSecBenchmark
