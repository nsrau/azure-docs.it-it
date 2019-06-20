---
title: Sviluppo sicuro le procedure consigliate in Microsoft Azure
description: Procedure consigliate che consentono di sviluppare il codice più sicuro e distribuire un'applicazione più sicura nel cloud.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: ee5c043038fbb747e90bca9530cbe2e94c8a95c2
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144464"
---
# <a name="secure-development-best-practices-on-azure"></a>Sviluppo sicuro le procedure consigliate in Azure
Questa serie di articoli vengono presentate le attività di sicurezza e controlli da considerare quando si sviluppano applicazioni per il cloud. Vengono illustrate le fasi del Microsoft Security Development Lifecycle (SDL) e domande di sicurezza e i concetti da considerare durante ogni fase del ciclo di vita. L'obiettivo è che consentono di definire le attività e i servizi di Azure che è possibile usare in ogni fase del ciclo di vita per progettare, sviluppare e distribuire un'applicazione più sicura.

I suggerimenti negli articoli derivano dalla nostra esperienza con sicurezza di Azure e dalle esperienze dei nostri clienti. È possibile usare questi articoli come riferimento per aspetti da considerare durante una fase specifica del progetto di sviluppo, ma è consigliabile anche leggere tutti gli articoli dall'inizio alla fine di almeno una volta. La lettura di tutti gli articoli introduce i concetti che potrebbero aver ignorato nelle fasi precedenti del progetto. Implementazione di questi concetti prima di rilasciare il prodotto consente di creare software protetto, soddisfare i requisiti di conformità di sicurezza e ridurre i costi di sviluppo.

Questi articoli sono progettati per essere una risorsa per i progettisti di software, gli sviluppatori e tester a tutti i livelli che creare e distribuire applicazioni Azure sicure.

## <a name="overview"></a>Panoramica

La sicurezza è uno degli aspetti più importanti di tutte le applicazioni e non è un aspetto semplice per ottenere i diritti. Fortunatamente, Azure offre numerosi servizi che consentono di proteggere l'applicazione nel cloud. Questi articoli occupano le attività e i servizi di Azure è possibile implementare in ogni fase del ciclo di vita sviluppo software che consentono di sviluppare il codice più sicuro e distribuire un'applicazione più sicura nel cloud.

## <a name="security-development-lifecycle"></a>Processo SDL

Seguire le procedure consigliate per lo sviluppo di software protetti richiede l'integrazione di sicurezza in ogni fase del ciclo di vita di sviluppo di software, dall'analisi di requisito alla manutenzione, indipendentemente dal fatto la metodologia di progetto ([a cascata](https://en.wikipedia.org/wiki/Waterfall_model), [agile](https://en.wikipedia.org/wiki/Agile_software_development), o [DevOps](https://en.wikipedia.org/wiki/DevOps)). In e lo sfruttamento dei difetti di sicurezza operativa di riattivazione delle violazioni dei dati di alto profilo, dagli sviluppatori sono la comprensione di sicurezza deve essere risolta in tutto il processo di sviluppo.

Sezione più avanti è risolvere un problema durante il ciclo di vita di sviluppo, più che verranno risolti costo. Problemi di sicurezza non fanno eccezione. Se si ignorano i problemi di sicurezza nelle fasi iniziali dello sviluppo software, ogni fase che segue può ereditare le vulnerabilità della fase precedente. Nel prodotto finale sarà hanno accumulato più problemi di sicurezza e la possibilità di una violazione. Integrazione della sicurezza in ogni fase del ciclo di vita di sviluppo consente di individuare tempestivamente i problemi e contribuisce a ridurre i costi di sviluppo.

Si seguono le fasi di Microsoft [Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) per introdurre le attività e i servizi di Azure che è possibile usare per soddisfare procedure di sviluppo software sicuro in ogni fase del ciclo di vita.

Le fasi del processo SDL sono:

  - Formazione
  - Requisiti
  - Progettazione
  - Implementazione
  - Verifica
  - Versione
  - Risposta

![Security Development Lifecycle](./media/secure-dev-overview/01-sdl-phase.png)

In questi articoli che verranno raggruppate le fasi del processo SDL nella progettazione, sviluppo e distribuzione.

## <a name="engage-your-organizations-security-team"></a>Coinvolgere il team di sicurezza dell'organizzazione

L'organizzazione potrebbe avere un programma di sicurezza dell'applicazione formale che assiste l'utente con le attività di sicurezza dall'inizio alla fine durante il ciclo di vita di sviluppo. Se l'organizzazione ha team di sicurezza e conformità, assicurarsi di coinvolgere le prima di iniziare lo sviluppo dell'applicazione. Chiedere a ogni fase del processo SDL se sono presenti attività che non sono state applicate.

Siamo consapevoli che molti lettori potrebbero non disporre di un team di sicurezza o di conformità per coinvolgere. Questi articoli consentono di apprendere le domande di sicurezza e le decisioni da prendere in considerazione in ogni fase del processo SDL.

## <a name="resources"></a>Risorse

Per altre informazioni sullo sviluppo di applicazioni protette e per proteggere le applicazioni in Azure, usare le risorse seguenti:

[Microsoft Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) : il processo SDL è un processo di sviluppo software di Microsoft che consente agli sviluppatori di compilare software più sicuro. Consente di soddisfare i requisiti di conformità di sicurezza, riducendo al contempo i costi di sviluppo.

[Aprire Web Application Security Project (OWASP)](https://www.owasp.org/index.php/Main_Page) – OWASP è una comunità online che produce disponibili gratuitamente articoli, le metodologie, documentazione, strumenti e tecnologie nel campo della protezione delle applicazioni web.

[Eseguire il push a sinistra, ad esempio un capo](https://code.likeagirl.io/pushing-left-like-a-boss-part-1-80f1f007da95?WT.mc_id=docs-blog-tajanca) : una serie di articoli online che illustra i diversi tipi di attività di sicurezza dell'applicazione che gli sviluppatori devono essere completate per creare codice più sicuro.

[Piattaforma delle identità Microsoft](https://docs.microsoft.com/azure/active-directory/develop/) : piattaforma delle identità Microsoft è un'evoluzione della piattaforma del servizio e per gli sviluppatori di identità di Azure AD. È una piattaforma completa costituita da un servizio di autenticazione, librerie open source, registrazione dell'applicazione e configurazione, documentazione completa per gli sviluppatori, esempi di codice e altri contenuti per sviluppatori. La piattaforma delle identità Microsoft supporta i protocolli standard del settore come OAuth 2.0 e OpenID Connect.

[Le procedure consigliate per le soluzioni Azure](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions/) : una raccolta di procedure consigliate da utilizzare per progettare, distribuire e gestire soluzioni basate su cloud con Azure. Questo documento è destinato a essere una risorsa per i professionisti IT. Potrebbe trattarsi di designer, architetti, sviluppatori e tester che creano e distribuiscono soluzioni sicure per Azure.

[Sicurezza e linee guida per la conformità in Azure](https://servicetrust.microsoft.com/ViewPage/BlueprintOverview) : linee guida per la conformità e sicurezza in Azure sono risorse che consentono di compilare e avviare applicazioni basate sul cloud che siano conformi agli standard e normative rigide.

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti, si consiglia di controlli di sicurezza e le attività che consentono di progettare, sviluppano e distribuiscono applicazioni sicure.

- [Progettazione di applicazioni sicure](secure-design.md)
- [Sviluppo di applicazioni sicure](secure-develop.md)
- [Distribuisci applicazioni sicure](secure-deploy.md)
