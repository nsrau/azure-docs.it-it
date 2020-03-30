---
title: Procedure consigliate per lo sviluppo sicuro in Microsoft AzureSecure development best practices on Microsoft Azure
description: Procedure consigliate per sviluppare codice più sicuro e distribuire un'applicazione più sicura nel cloud.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68934856"
---
# <a name="secure-development-best-practices-on-azure"></a>Procedure consigliate per lo sviluppo sicuro in AzureSecure development best practices on Azure
Questa serie di articoli presenta le attività di sicurezza e i controlli da considerare quando si sviluppano applicazioni per il cloud. Vengono trattate le fasi del ciclo di vita dello sviluppo della sicurezza microsoft (SDL) e le domande e i concetti di sicurezza da considerare durante ogni fase del ciclo di vita. L'obiettivo consiste nel definire le attività e i servizi di Azure che è possibile usare in ogni fase del ciclo di vita per progettare, sviluppare e distribuire un'applicazione più sicura.

I consigli negli articoli derivano dalla nostra esperienza con la sicurezza di Azure e dalle esperienze dei nostri clienti. È possibile utilizzare questi articoli come riferimento per ciò che è necessario considerare durante una fase specifica del progetto di sviluppo, ma si consiglia di leggere anche tutti gli articoli dall'inizio alla fine almeno una volta. La lettura di tutti gli articoli illustra i concetti che potrebbero essere stati persi nelle fasi precedenti del progetto. L'implementazione di questi concetti prima di rilasciare il prodotto consente di creare software sicuro, soddisfare i requisiti di conformità alla sicurezza e ridurre i costi di sviluppo.

Questi articoli sono destinati a essere una risorsa per progettisti software, sviluppatori e tester a tutti i livelli che creano e distribuiscono applicazioni di Azure sicure.

## <a name="overview"></a>Panoramica

La sicurezza è uno degli aspetti più importanti di qualsiasi applicazione e non è una cosa semplice da ottenere a destra. Fortunatamente, Azure offre molti servizi che consentono di proteggere l'applicazione nel cloud. Questi articoli affrontano le attività e i servizi di Azure che è possibile implementare in ogni fase del ciclo di vita dello sviluppo software per sviluppare codice più sicuro e distribuire un'applicazione più sicura nel cloud.

## <a name="security-development-lifecycle"></a>Security Development Lifecycle

Seguire le procedure consigliate per lo sviluppo sicuro del software richiede l'integrazione della sicurezza in ogni fase del ciclo di vita dello sviluppo software, dall'analisi dei requisiti alla manutenzione, indipendentemente dalla metodologia del progetto ([waterfall](https://en.wikipedia.org/wiki/Waterfall_model), [agile](https://en.wikipedia.org/wiki/Agile_software_development)o [DevOps](https://en.wikipedia.org/wiki/DevOps)). Sulla scia di violazioni dei dati di alto profilo e dello sfruttamento dei difetti di sicurezza operativa, sempre più sviluppatori stanno comprendendo che la sicurezza deve essere affrontata durante tutto il processo di sviluppo.

Più tardi si risolve un problema nel ciclo di vita di sviluppo, più tale correzione vi costerà. I problemi di sicurezza non fanno eccezione. Se si ignorano i problemi di sicurezza nelle prime fasi dello sviluppo del software, ogni fase che segue potrebbe ereditare le vulnerabilità della fase precedente. Il prodotto finale avrà accumulato più problemi di sicurezza e la possibilità di una violazione. La creazione di sicurezza in ogni fase del ciclo di vita dello sviluppo consente di rilevare tempestivamente i problemi e di ridurre i costi di sviluppo.

Seguiamo le fasi del ciclo di vita dello sviluppo della sicurezza Microsoft [(SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) per introdurre attività e servizi di Azure che è possibile usare per soddisfare procedure di sviluppo software sicure in ogni fase del ciclo di vita.

Le fasi Di SDL sono:

  - Formazione
  - Requisiti
  - Progettazione
  - Implementazione
  - Verifica
  - Versione
  - Risposta

![Security Development Lifecycle](./media/secure-dev-overview/01-sdl-phase.png)

In questi articoli le fasi del processo SDL vengono raggruppate in fasi di progettazione, sviluppo e distribuzione.

## <a name="engage-your-organizations-security-team"></a>Coinvolgi il team di sicurezza della tua organizzazione

L'organizzazione potrebbe disporre di un programma formale di sicurezza delle applicazioni che consente di accedere alle attività di sicurezza dall'inizio alla fine durante il ciclo di vita dello sviluppo. Se l'organizzazione dispone di team di sicurezza e conformità, assicurarsi di coinvolgerli prima di iniziare a sviluppare l'applicazione. Chiedere loro in ogni fase del processo SDL se vi sono attività che vi siete persi.

Siamo consapevoli che molti lettori potrebbero non avere un team di sicurezza o conformità da coinvolgere. Questi articoli possono aiutarvi a prendere in considerazione le domande e le decisioni sulla sicurezza che è necessario prendere in considerazione in ogni fase del livello SDL.

## <a name="resources"></a>Risorse

Usare le risorse seguenti per altre informazioni sullo sviluppo di applicazioni sicure e per proteggere le applicazioni in Azure:Use the following resources to learn more about developing secure applications and to help secure your applications on Azure:

[Microsoft Security Development Lifecycle (SDL):](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) Il processo di sviluppo software è un processo di sviluppo software di Microsoft che consente agli sviluppatori di creare software più sicuro. Consente di soddisfare i requisiti di conformità alla sicurezza riducendo al contempo i costi di sviluppo.

[Open Web Application Security Project (OWASP):](https://www.owasp.org/index.php/Main_Page) OWASP è una community online che produce articoli, metodologie, documentazione, strumenti e tecnologie disponibili gratuitamente nel campo della sicurezza delle applicazioni Web.

[Pushing Left, Like a Boss](https://code.likeagirl.io/pushing-left-like-a-boss-part-1-80f1f007da95?WT.mc_id=docs-blog-tajanca) – Una serie di articoli online che delinea diversi tipi di attività di sicurezza delle applicazioni che gli sviluppatori dovrebbero completare per creare codice più sicuro.

[Piattaforma di identità Microsoft:](../../active-directory/develop/index.yml) la piattaforma di identità Microsoft è un'evoluzione del servizio di identità di Azure AD e della piattaforma per sviluppatori. Si tratta di una piattaforma completa che consiste in un servizio di autenticazione, librerie open source, registrazione e configurazione dell'applicazione, documentazione completa per sviluppatori, esempi di codice e altro contenuto per sviluppatori. La piattaforma di identità Microsoft supporta protocolli standard del settore come OAuth 2.0 e OpenID Connect.

Procedure consigliate per la sicurezza per le [soluzioni di Azure:](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions/) raccolta di procedure consigliate per la sicurezza da usare durante la progettazione, distribuzione e gestione di soluzioni cloud tramite Azure.Security best practices for Azure solutions – A collection of security best practices to use when you design, deploy, and manage cloud solutions by using Azure. Questo documento è destinato a essere una risorsa per i professionisti IT. Potrebbe trattarsi di designer, architetti, sviluppatori e tester che creano e distribuiscono soluzioni sicure per Azure.

[Blueprint di sicurezza e conformità in Azure:](https://servicetrust.microsoft.com/ViewPage/BlueprintOverview) i blueprint di sicurezza e conformità di Azure sono risorse che consentono di creare e lanciare applicazioni basate sul cloud conformi a normative e standard rigorosi.

## <a name="next-steps"></a>Passaggi successivi
Negli articoli seguenti sono consigliati i controlli di sicurezza e le attività che consentono di progettare, sviluppare e distribuire applicazioni protette.

- [Progettare applicazioni sicure](secure-design.md)
- [Sviluppare applicazioni sicure](secure-develop.md)
- [Distribuire applicazioni sicure](secure-deploy.md)
