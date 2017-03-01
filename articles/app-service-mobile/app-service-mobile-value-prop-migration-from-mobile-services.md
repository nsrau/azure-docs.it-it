---
title: "In che modo può essere utile il servizio app per gli utenti di Servizi mobili?"
description: "Informazioni sui vantaggi che il servizio app può portare ai progetti di Servizi mobili esistenti."
services: app-service\mobile
documentationcenter: ios
author: adrianhall
manager: erikre
editor: 
ms.assetid: 26b68a11-8352-4f78-acd2-e4e0ec177781
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/01/2016
ms.author: adrianha
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: f3b75c10ed0012d682562eb943cb67fc8176ddbb
ms.lasthandoff: 12/08/2016


---
# <a name="getting-started"> </a>In che modo può essere utile il servizio app per gli utenti di Servizi mobili?
## <a name="overview"></a>Panoramica
Il servizio mobile esistente è al sicuro e continuerà ad essere supportato. Vi sono tuttavia alcuni vantaggi per le app per dispositivi mobili, offerti dalla piattaforma del *servizio app di Azure* , a oggi non disponibili in Servizi mobili:

* Offerte semplificate e più economiche per app che includono client sia Web che mobili.
* Nuove funzionalità host che includono processi Web, CName personalizzati e monitoraggio migliorato.
* Integrazione chiavi in mano con Gestione traffico.
* Connettività con risorse locali e VPN usando reti virtuali e connessioni ibride.
* Monitoraggio, avvisi e risoluzione dei problemi delle app con NewRelic o AppInsights
* Spettro più ampio di risorse di calcolo sottostanti e prezzi.
* Scalabilità automatica predefinita, bilanciamento del carico e monitoraggio delle prestazioni.
* Funzionalità predefinite di gestione temporanea, backup, rollback e test in ambiente di produzione.

## <a name="new-hosting-features"></a>Nuove funzionalità di hosting
Nel *servizio app di Azure* il codice back-end dell'*app per dispositivi mobili* viene eseguito nello stesso contenitore dell'app Web e dell'app per le API. È pertanto possibile sfruttare tutte le funzionalità disponibili in questo contenitore, comprese alcune di quelle non attualmente presenti in Servizi mobili:

* Aggiunta di logica back-end in continua esecuzione tramite processi Web
* Garanzia che il codice back-end sia sempre in esecuzione
* Uso di CName personalizzati per fornire nomi descrittivi e stabili agli endpoint dei back-end mobili
* Scalabilità geografica dell'app con Gestione traffico
* Inserimento di tutte le librerie e i pacchetti desiderati.
* (Per .NET) Uso delle funzionalità di ASP.NET, tra cui MVC
* (Per Node. js) Sfruttare qualsiasi libreria JavaScript pura dell'ecosistema di Node, incluse le librerie comuni di MVC.

## <a name="access-on-premises-data-using-vnet"></a>Accesso ai dati locali tramite reti virtuali
Con Servizi mobili è oggi già possibile usare connessioni ibride per accedere alle risorse locali. Vi sono tuttavia alcune situazioni in cui una soluzione VPN è preferibile. Con il *servizio app di Azure* , è possibile usare la rete virtuale di Azure per il codice back-end delle app per dispositivi mobili.

## <a name="use-your-favorite-backend-language"></a>Uso del linguaggio back-end preferito
Il *servizio app di Azure* offre un supporto più ampio e avanzato per le piattaforme ASP.NET e Node. js, incluso l'accesso ai runtime più recenti.

## <a name="set-up-automatic-scale"></a>Impostazione della scalabilità automatica
Con Servizi mobili, tutte le istanze del codice back-end vengono eseguite in macchine virtuali del tipo Piccola. *servizio app di Azure* permette di selezionare le dimensioni delle macchine virtuali da un set di opzioni molto più ampio. È anche possibile aumentare le prestazioni o il numero di istanze per gestire qualsiasi carico di lavoro in ingresso dei clienti, in base a varie metriche delle prestazioni.

## <a name="be-in-the-know"></a>Tutto sotto controllo
Il monitoraggio e gli avvisi consentono di reagire ai problemi in tempo reale grazie alle notifiche automatiche all'utente e al suo team. L'integrazione di avanzate funzionalità analitiche e di monitoraggio fornite da New Relic e AppInsights consentono di ottenere informazioni ancora più complete sulle prestazioni dell'app per dispositivi mobili. Con il *servizio app di Azure* è ora possibile configurare avvisi in base a diverse metriche delle prestazioni, sia a livello di codice che tramite portale di Azure.

## <a name="keep-your-assets-safe"></a>Asset sempre al sicuro
Backup automatico del back-end e del database. Il codice e i dati sono sempre al sicuro in situazioni di emergenza e possono essere facilmente ripristinati, consentendo la conduzione del business in tutta serenità.

## <a name="ready-stage-go"></a>Sviluppo, test, produzione
Con il *servizio app di Azure* è ora possibile creare più ambienti privati di test e staging per le app per dispositivi mobili. Usare questi ambienti per testare le app prima della distribuzione. Passaggio alla produzione con tempo di inattività pari a zero. Le app Web sono precaricate per garantire la miglior esperienza utente possibile.

È possibile iniziare a usare il *servizio app* sperimentando i vantaggi per il servizio mobile esistente seguendo questa [esercitazione](app-service-mobile-migrating-from-mobile-services.md).


