---
title: Procedure consigliate per lo sviluppo sicuro in Microsoft Azure
description: Procedure consigliate per sviluppare codice più sicuro e distribuire un'applicazione più protetta nel cloud.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: c4314a0dcbbcb907ef4d6de0a2788cf04dfe1641
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934856"
---
# <a name="secure-development-best-practices-on-azure"></a>Procedure consigliate per lo sviluppo sicuro in Azure
Questa serie di articoli presenta le attività e i controlli di sicurezza da prendere in considerazione quando si sviluppano applicazioni per il cloud. Vengono analizzate le fasi di Microsoft Security Development Lifecycle (SDL) e le domande e i concetti di sicurezza da considerare durante ogni fase del ciclo di vita. L'obiettivo è consentire di definire le attività e i servizi di Azure che è possibile usare in ogni fase del ciclo di vita per progettare, sviluppare e distribuire un'applicazione più protetta.

I consigli negli articoli provengono dalla nostra esperienza con la sicurezza di Azure e dalle esperienze dei nostri clienti. È possibile usare questi articoli come riferimento per gli elementi da prendere in considerazione durante una fase specifica del progetto di sviluppo, ma è consigliabile leggere anche tutti gli articoli dall'inizio alla fine almeno una volta. La lettura di tutti gli articoli presenta i concetti che potrebbero mancare nelle fasi precedenti del progetto. L'implementazione di questi concetti prima di rilasciare il prodotto può essere utile per creare software protetto, soddisfare i requisiti di conformità della sicurezza e ridurre i costi di sviluppo.

Questi articoli sono destinati a essere una risorsa per progettisti, sviluppatori e tester di software a tutti i livelli che compilano e distribuiscono applicazioni Azure sicure.

## <a name="overview"></a>Panoramica

La sicurezza è uno degli aspetti più importanti di tutte le applicazioni e non è un'operazione semplice da ottenere correttamente. Fortunatamente, Azure offre molti servizi che consentono di proteggere l'applicazione nel cloud. Questi articoli indirizzano le attività e i servizi di Azure che è possibile implementare in ogni fase del ciclo di vita di sviluppo del software per facilitare lo sviluppo di codice più sicuro e la distribuzione di un'applicazione più sicura nel cloud.

## <a name="security-development-lifecycle"></a>Ciclo di vita dello sviluppo della sicurezza

Le seguenti procedure consigliate per lo sviluppo di software sicuro richiedono l'integrazione della sicurezza in ogni fase del ciclo di vita dello sviluppo del software, dall'analisi dei requisiti alla manutenzione, indipendentemente dalla metodologia del progetto ([waterfall](https://en.wikipedia.org/wiki/Waterfall_model), [agile](https://en.wikipedia.org/wiki/Agile_software_development) o [DevOps](https://en.wikipedia.org/wiki/DevOps)). In seguito alle violazioni dei dati di alto profilo e all'exploit dei difetti di sicurezza operativa, più sviluppatori sono in grado di comprendere che la sicurezza deve essere risolta durante tutto il processo di sviluppo.

Più avanti si corregge un problema nel ciclo di vita di sviluppo, maggiore sarà il costo della correzione. I problemi di sicurezza non fanno eccezione. Se si ignorano i problemi di sicurezza nelle fasi iniziali dello sviluppo del software, ogni fase che segue potrebbe ereditare le vulnerabilità della fase precedente. Il prodotto finale avrà accumulato più problemi di sicurezza e la possibilità di una violazione. La creazione di sicurezza in ogni fase del ciclo di vita dello sviluppo consente di rilevare tempestivamente i problemi e di ridurre i costi di sviluppo.

Seguiamo le fasi di Microsoft [Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) per introdurre le attività e i servizi di Azure che puoi usare per soddisfare le procedure di sviluppo software sicure in ogni fase del ciclo di vita.

Le fasi SDL sono:

  - Formazione
  - Requisiti
  - Progettazione
  - Implementazione
  - Verifica
  - Release
  - Risposta

![Ciclo di vita dello sviluppo della sicurezza](./media/secure-dev-overview/01-sdl-phase.png)

In questi articoli vengono raggruppate le fasi SDL per la progettazione, lo sviluppo e la distribuzione.

## <a name="engage-your-organizations-security-team"></a>Coinvolgere il team di sicurezza dell'organizzazione

È possibile che l'organizzazione disponga di un programma formale per la sicurezza delle applicazioni che assiste le attività di sicurezza dall'inizio alla fine durante il ciclo di vita dello sviluppo. Se l'organizzazione dispone di team di sicurezza e conformità, assicurarsi di coinvolgerli prima di iniziare a sviluppare l'applicazione. Chiedere a ogni fase del processo SDL se sono presenti attività mancanti.

È chiaro che molti lettori potrebbero non avere un team di sicurezza o conformità per impegnarsi. Questi articoli possono essere utili per le domande e le decisioni di sicurezza da prendere in considerazione in ogni fase del processo SDL.

## <a name="resources"></a>Risorse

Usare le risorse seguenti per altre informazioni sullo sviluppo di applicazioni protette e per proteggere le applicazioni in Azure:

[Microsoft Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) : SDL è un processo di sviluppo software di Microsoft che consente agli sviluppatori di creare software più sicuro. Consente di soddisfare i requisiti di conformità della sicurezza riducendo al tempo stesso i costi di sviluppo.

[Open Web Application Security Project (OWASP)](https://www.owasp.org/index.php/Main_Page) : OWASP è una community online che produce articoli, metodologie, documentazione, strumenti e tecnologie disponibili gratuitamente nel settore della sicurezza delle applicazioni Web.

A [sinistra, come un capo,](https://code.likeagirl.io/pushing-left-like-a-boss-part-1-80f1f007da95?WT.mc_id=docs-blog-tajanca) una serie di articoli online che delineano diversi tipi di attività di sicurezza dell'applicazione che gli sviluppatori devono completare per creare codice più sicuro.

[Piattaforma](../../active-directory/develop/index.yml) di identità Microsoft: la piattaforma di identità Microsoft è un'evoluzione del servizio di identità Azure ad e della piattaforma per sviluppatori. Si tratta di una piattaforma con funzionalità complete costituita da un servizio di autenticazione, librerie open source, registrazione e configurazione dell'applicazione, documentazione completa per gli sviluppatori, esempi di codice e altri contenuti per gli sviluppatori. La piattaforma Microsoft Identity supporta protocolli standard di settore come OAuth 2,0 e OpenID Connect.

[Procedure di sicurezza consigliate per le soluzioni di Azure](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions/) : una raccolta di procedure consigliate per la sicurezza da usare per la progettazione, la distribuzione e la gestione di soluzioni cloud con Azure. Questo documento è destinato a essere una risorsa per i professionisti IT. Potrebbe trattarsi di designer, architetti, sviluppatori e tester che creano e distribuiscono soluzioni sicure per Azure.

[Progetti di sicurezza e conformità in Azure](https://servicetrust.microsoft.com/ViewPage/BlueprintOverview) : i progetti di sicurezza e conformità di Azure sono risorse che consentono di creare e avviare applicazioni basate sul cloud conformi a normative e standard rigorosi.

## <a name="next-steps"></a>Passaggi successivi
Negli articoli seguenti si consigliano i controlli di sicurezza e le attività che consentono di progettare, sviluppare e distribuire applicazioni protette.

- [Progettare applicazioni protette](secure-design.md)
- [Sviluppare applicazioni sicure](secure-develop.md)
- [Distribuire applicazioni sicure](secure-deploy.md)
