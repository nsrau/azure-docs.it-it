---
title: Comprendere l'utilizzo delle applicazioni mobili e il comportamento degli utenti con Visual Studio App Center e i servizi di AzureUnderstand mobile application usage and user behavior with Visual Studio App Center and Azure services
description: Scopri i servizi come App Center che ti aiutano a prendere decisioni aziendali intelligenti comprendendo come gli utenti utilizzano la tua applicazione mobile.
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
# <a name="analyze-and-understand-mobile-application-use"></a>Analizzare e comprendere l'utilizzo delle applicazioni mobili
Quanto bene si capisce come gli utenti utilizzano le applicazioni? Quanti utenti attivi ha l'applicazione e come cambia l'utilizzo nel tempo? Quali sono le caratteristiche che utilizzano e quali vengono utilizzate di più? Dove si basano questi utenti? Quanti utenti utilizzano la versione più recente dell'applicazione? Tutte queste domande sono importanti da comprendere per trasformare la tua app in un'azienda di successo. Per rispondere a questo tipo di domande sull'analisi dei dati di utilizzo, è necessario raccogliere i dati di utilizzo dalle app.

Più si esaminano i dati, più si potrebbero trovare modi per migliorare l'applicazione e mantenere gli utenti soddisfatti. È importante usare i dati per trovare informazioni dettagliate utili e soddisfare gli utenti.

## <a name="importance-of-analytics"></a>Importanza dell'analisi
- Comprendere gli utenti, come interagiscono con l'applicazione e cosa li riporta a perfezionare l'applicazione e fornire esperienze straordinarie per far crescere il business.
- Tieni traccia delle tue metriche di utilizzo per prendere decisioni informate su come commercializzare la tua applicazione e servire meglio i tuoi clienti.
- Misurare le prestazioni dell'applicazione.
- Scopri quali parti del valore e delle prestazioni dell'applicazione sono alte.
- Ottieni informazioni dettagliate sui dati che riguardano la varianza e la conservazione.

Utilizzare i servizi seguenti per abilitare l'analisi delle applicazioni per dispositivi mobili.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center Analytics](/appcenter/analytics/) ti consente di far crescere il tuo pubblico concentrandoti su ciò che è importante. Offre report approfonditi e approfondimenti sulle sessioni utente, sui dispositivi principali, sulle versioni del sistema operativo e sull'analisi comportamentale. Crea facilmente eventi personalizzati per tenere traccia di qualsiasi cosa con analisi approfondite delle applicazioni.

   **Caratteristiche principali**
   - Tieni traccia gratuitamente dei modelli di utilizzo, dell'adozione da parte degli utenti e di altre metriche di coinvolgimento.
   - Identifica le tendenze, il comportamento degli utenti e il coinvolgimento tramite eventi personalizzati.
   - Ottieni metriche non aggiornate e informazioni dettagliate sull'utilizzo delle applicazioni (giornaliero, settimanale, mensile), le sessioni, le proprietà del dispositivo e i dati demografici degli utenti in un unico dashboard.
   - Esporta continuamente tutti i dati di App Center Analytics in Azure per una conservazione illimitata. App Center Analytics supporta l'esportazione in Archiviazione BLOB di Azure e Azure Application Insights.
   - Integrazione con Azure Application Insights per informazioni dettagliate ancora più approfondite, ad esempio conservazione, analisi della canalizzazione e coorti.
   - Usa l'integrazione SDK di una riga per iniziare in pochi minuti.
   - Ottieni il supporto della piattaforma per iOS, Android, macOS, tvOS, Xamarin, React Native, Unity e Cordova.

   **Riferimenti**
   - [Effettuare l'iscrizione ad App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
   - [Introduzione all'analisi di App Center](/appcenter/analytics/)

## <a name="azure-monitor"></a>Monitoraggio di Azure
Monitoraggio di Azure include [Application Insights](/azure/azure-monitor/app/app-insights-overview), che fornisce strumenti per raccogliere e analizzare i dati di telemetria per ottimizzare le prestazioni e monitorare l'applicazione per dispositivi mobili. È possibile sfruttare Application Insights usando App Center Analytics per configurare l'esportazione in Application Insights.You can take advantage of Application Insights by using App Center Analytics to set up export to Application Insights. Application Insights può eseguire query, segmentare, filtrare e analizzare i dati di telemetria degli eventi personalizzati dalle applicazioni, oltre agli strumenti di analisi forniti da App Center.Application Insights can query, segment, filter, and analyze the custom event telemetry from your applications, beyond the analytics tools that App Center provides.

**Caratteristiche principali**
   - Eseguire query sui dati di telemetria relativi agli eventi personalizzati.
   - Filtra i dati di telemetria degli eventi con potenti funzionalità di segmentazione.
   - Analizzare i modelli di conversione, conservazione e navigazione nell'applicazione. È possibile usare:
     - Imbuti per analizzare e monitorare i tassi di conversione.
     - Conservazione per analizzare il livello di conservazione degli utenti nell'applicazione nel tempo.
     - Cartelle di lavoro per combinare visualizzazioni e testo in un report condisabile.
     - Coorti per nominare e salvare gruppi specifici di utenti o eventi in modo che possano essere facilmente referenziati da altri strumenti di analisi.

**Riferimenti**
- [Portale di Azure](https://portal.azure.com/)
- [Analizzare l'applicazione per dispositivi mobili con App Center e Application Insights](/azure/azure-monitor/learn/mobile-center-quickstart)
- [Usare App Center Analytics con Application InsightsUse App Center Analytics with Application Insights](/azure/azure-monitor/app/usage-overview)

## <a name="azure-playfab"></a>Azure PlayFab
[Azure PlayFab](https://playfab.com/) offre una piattaforma back-end completa con servizi di gioco, analisi in tempo reale e LiveOps necessari per creare giochi connessi al cloud di livello mondiale. Questi servizi riducono le barriere al lancio per gli sviluppatori di giochi. Offrono sia studi grandi che piccoli soluzioni di sviluppo convenienti che si adattano ai loro giochi. I servizi possono aiutare gli studi a coinvolgere, trattenere e monetizzare i giocatori. Con PlayFab, gli sviluppatori possono utilizzare il cloud intelligente per creare e gestire giochi, analizzare i dati di gioco e migliorare le esperienze di gioco complessive.

**Caratteristiche principali**
   - Monitora i dashboard in tempo reale.
   - Valuta le prestazioni del tuo gioco attraverso le metriche principali.
   - Rivedi i riepiloghi delle prestazioni giornaliere e mensili del gioco tramite rapporti generati automaticamente. Puoi visualizzare i rapporti in Game Manager e scaricarli o recapitarli ogni giorno nella tua casella di posta.
   - Utilizzare il test A/B per eseguire esperimenti e determinare l'impostazione ottimale per una determinata variabile.
   - Usa la segmentazione per i giocatori per definire raggruppamenti automatici di giocatori.
    
**Riferimenti**
- [Portale PlayFab](https://developer.playfab.com/en-US/sign-up)
- [Analitica](/gaming/playfab/#pivot=documentation&panel=analytics)
- [Avvi rapidi](/gaming/playfab/#pivot=documentation&panel=quickstarts) 
