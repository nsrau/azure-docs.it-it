---
title: Informazioni sull'utilizzo delle applicazioni mobili e sul comportamento degli utenti con Visual Studio App Center e i servizi di Azure
description: Informazioni sui servizi come App Center che consentono di prendere decisioni aziendali intelligenti comprendendo il modo in cui gli utenti usano l'applicazione per dispositivi mobili.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 4dc3cea4497c55dda0d8da2ca29201615dadff19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241088"
---
# <a name="analyze-and-understand-mobile-application-use"></a>Analizzare e comprendere l'uso delle applicazioni per dispositivi mobili
In che modo è possibile comprendere in che modo gli utenti usano le applicazioni? Quanti utenti attivi sono disponibili nell'applicazione e in che modo cambia l'utilizzo nel tempo? Quali sono le funzionalità che usano e quali sono più usate? Dove si basano questi utenti? Quanti utenti usano la versione più recente dell'applicazione? Tutte queste domande sono importanti da comprendere per trasformare l'app in un'azienda di successo. Per rispondere a questi tipi di domande sull'analisi di utilizzo, è necessario raccogliere i dati di utilizzo dalle app.

Più si esaminano i dati, più si potrebbero trovare modi per migliorare l'applicazione e garantire agli utenti la soddisfazione. È importante usare i dati per individuare informazioni fruibili e assicurare che gli utenti siano soddisfatti.

## <a name="importance-of-analytics"></a>Importanza dell'analisi
- Scopri gli utenti, il modo in cui interagiscono con l'applicazione e ciò che li riporta per ottimizzare la tua applicazione e offrire ottime esperienze per far crescere il tuo business.
- Tieni traccia delle metriche di utilizzo per prendere decisioni informate su come commercializzare l'applicazione e soddisfare al meglio i tuoi clienti.
- Misurare le prestazioni dell'applicazione.
- Informazioni sulle parti del valore e delle prestazioni dell'unità applicativa.
- Ottenere informazioni dettagliate basate sui dati sui problemi che riguardano la varianza e la conservazione.

Usare i servizi seguenti per abilitare l'analisi delle applicazioni per dispositivi mobili.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center Analytics](/appcenter/analytics/) consente di far crescere i destinatari concentrandosi sulle attività importanti. Offre funzionalità di creazione di report approfondite e approfondimenti sulle sessioni utente, i dispositivi principali, le versioni del sistema operativo e l'analisi comportamentale. Crea con facilità eventi personalizzati per tenere traccia di qualsiasi elemento con analisi approfondite delle applicazioni.

   **Funzionalità principali**
   - Tieni traccia dei modelli di utilizzo, dell'adozione degli utenti e di altre metriche di engagement gratuite.
   - Identifica le tendenze, il comportamento degli utenti e l'engagement tramite eventi personalizzati.
   - Ottieni metriche predefinite e approfondimenti dettagliati sull'utilizzo delle applicazioni (giornaliera, settimanale, mensile), sessioni, proprietà dei dispositivi e dati demografici degli utenti in un unico dashboard.
   - Esporta continuamente tutti i dati di analisi di App Center in Azure per una conservazione illimitata. App Center Analytics supporta l'esportazione nell'archivio BLOB di Azure e applicazione Azure Insights.
   - Integrarsi con applicazione Azure Insights per approfondimenti ancora più approfonditi, ad esempio conservazione, analisi dell'imbuto e coorti.
   - Per iniziare in pochi minuti, usare l'integrazione di SDK a una riga.
   - Ottieni supporto della piattaforma per iOS, Android, macOS, tvOS, Novell, React native, Unity e Cordova.

   **Riferimenti**
   - [Effettuare l'iscrizione ad App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
   - [Introduzione a App Center Analytics](/appcenter/analytics/)

## <a name="azure-monitor"></a>Monitoraggio di Azure
Monitoraggio di Azure include [Application Insights](/azure/azure-monitor/app/app-insights-overview), che offre strumenti per raccogliere e analizzare i dati di telemetria per ottimizzare le prestazioni e monitorare l'applicazione per dispositivi mobili. È possibile sfruttare i vantaggi di Application Insights usando App Center Analytics per configurare l'esportazione in Application Insights. Application Insights possibile eseguire query, segmentare, filtrare e analizzare i dati di telemetria degli eventi personalizzati dalle applicazioni, oltre agli strumenti di analisi forniti da App Center.

**Funzionalità principali**
   - Eseguire query sui dati di telemetria relativi agli eventi personalizzati.
   - Filtrare i dati di telemetria degli eventi con potenti funzionalità di segmentazione.
   - Analizzare i modelli di conversione, conservazione e navigazione nell'applicazione. È possibile usare:
     - Imbuti per analizzare e monitorare i tassi di conversione.
     - Conservazione per analizzare il modo in cui l'applicazione mantiene gli utenti nel tempo.
     - Cartelle di lavoro per combinare visualizzazioni e testo in un report condivisibile.
     - Coorti per assegnare un nome e un salvataggio a gruppi specifici di utenti o eventi in modo che sia possibile farvi riferimento facilmente da altri strumenti di analisi.

**Riferimenti**
- [Azure portal](https://portal.azure.com/)
- [Analizzare l'applicazione per dispositivi mobili con App Center e Application Insights](/azure/azure-monitor/learn/mobile-center-quickstart)
- [Usare App Center Analytics con Application Insights](/azure/azure-monitor/app/usage-overview)

## <a name="azure-playfab"></a>Azure PlayFab
[Azure PlayFab](https://playfab.com/) offre una piattaforma back-end completa con servizi di gioco, analisi in tempo reale e LiveOps che ti servono per creare giochi di qualità elevata e connessi al cloud. Questi servizi riducono le barriere da avviare per gli sviluppatori di giochi. Offrono soluzioni di sviluppo convenienti e di grandi dimensioni che si adattano ai giochi. I servizi possono aiutare gli Studios a coinvolgere, mantenere e monetizzare i giocatori. Con PlayFab, gli sviluppatori possono usare il cloud intelligente per creare e gestire giochi, analizzare i dati dei giochi e migliorare le esperienze di gioco generali.

**Funzionalità principali**
   - Monitorare i dashboard in tempo reale.
   - Valuta le prestazioni del tuo gioco attraverso le metriche più importanti.
   - Esaminare i riepiloghi delle prestazioni quotidiane e mensili del gioco tramite report generati automaticamente. È possibile visualizzare i report in Game Manager e scaricarli o recapitarli quotidianamente.
   - Usare un test A/B per eseguire esperimenti e determinare l'impostazione ottimale per una determinata variabile.
   - Utilizzare la segmentazione per i giocatori per definire raggruppamenti automatici di giocatori.
    
**Riferimenti**
- [Portale di PlayFab](https://developer.playfab.com/en-US/sign-up)
- [Analisi](/gaming/playfab/#pivot=documentation&panel=analytics)
- [Guide introduttive](/gaming/playfab/#pivot=documentation&panel=quickstarts) 
