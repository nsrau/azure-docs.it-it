---
title: Panoramica della documentazione relativa all'analisi dei codici di sicurezza Microsoft
description: Questo articolo è una panoramica dell'estensione Microsoft Security Code Analysis
author: vharindra
manager: sukhans
ms.author: terrylan
ms.date: 07/31/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 963bc909b69962cded0a50d717e3a653d3d69769
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74851469"
---
# <a name="about-microsoft-security-code-analysis"></a>Informazioni sull'analisi del codice di sicurezza Microsoft

Con l'estensione Microsoft Security Code Analysis, i team possono aggiungere l'analisi del codice di sicurezza alle pipeline di integrazione e recapito continuo (CI/CD) di Azure DevOps.With the Microsoft Security Code Analysis extension, teams can add security code analysis to their Azure DevOps continuous integration and delivery pipelines (CI/CD) pipelines. Questa analisi è consigliata dagli esperti del ciclo di vita dello sviluppo sicuro [(SDL)](https://www.microsoft.com/securityengineering/sdl/practices) di Microsoft.

Un'esperienza utente coerente semplifica la sicurezza nascondendo la complessità degli strumenti in esecuzione. Con la distribuzione degli strumenti basata su NuGet, i team non devono più gestire l'installazione o l'aggiornamento degli strumenti. Con le interfacce della riga di comando e di base per le attività di compilazione, tutti gli utenti possono avere tutto il controllo sugli strumenti che vogliono.

I team possono anche usare potenti funzionalità di post-elaborazione, ad esempio:Teams can also use powerful postprocessing capabilities such as:

- Registri di pubblicazione per la conservazione.
- Generazione di report utilizzabili e incentrati sugli sviluppatori.
- Configurazione delle pause di compilazione nei test di regressione.

## <a name="why-should-i-use-microsoft-security-code-analysis"></a>Perché dovrei usare Microsoft Security Code Analysis?

### <a name="security-simplified"></a>Sicurezza semplificata

L'aggiunta di strumenti di analisi del codice di sicurezza Microsoft alla pipeline DevOps di Azure è semplice come aggiungere nuove attività. Personalizzare le attività o utilizzare il comportamento predefinito. Le attività vengono eseguite come parte della pipeline DevOps di Azure e producono log che descrivono in dettaglio molti tipi di risultati.

### <a name="clean-builds"></a>Compilazioni pulite

Dopo aver risolto i problemi iniziali segnalati dagli strumenti, è possibile configurare l'estensione per interrompere le compilazioni su nuovi problemi.L'impostazione dell'integrazione continua si basa su ogni richiesta pull è semplice.

### <a name="set-it-and-forget-it"></a>Impostalo e dimenticalo

Per impostazione predefinita, le attività di compilazione e gli strumenti rimangono aggiornati. Se è disponibile una versione aggiornata di uno strumento, non è necessario scaricarla e installarla. L'estensione si occupa dell'aggiornamento per te.

### <a name="under-the-hood"></a>dietro le quinte

Le attività di compilazione dell'estensione nascondono le complessità di:The extension's build tasks hide the complexities of:
  - Esecuzione di strumenti di sicurezza di analisi statica.
  - Elaborazione dei risultati dai file di log per creare un report di riepilogo o interrompere la compilazione.

## <a name="microsoft-security-code-analysis-tool-set"></a>Set di strumenti di Microsoft Security Code Analysis

L'estensione Microsoft Security Code Analysis rende disponibili le versioni più recenti di importanti strumenti di analisi. L'estensione include sia gli strumenti gestiti da Microsoft che gli strumenti open source.

Questi strumenti vengono scaricati automaticamente nell'agente ospitato nel cloud dopo aver utilizzato l'attività di compilazione corrispondente per configurare ed eseguire la pipeline.

In questa sezione è elencato il set di strumenti attualmente disponibili nell'estensione. Guarda per l'aggiunta di altri strumenti. Inoltre, inviaci i tuoi suggerimenti per gli strumenti che vuoi che aggiungiamo.

### <a name="anti-malware-scanner"></a>Scanner antimalware

L'attività di compilazione Anti-Malware Scanner è ora inclusa nell'estensione Microsoft Security Code Analysis. Questa attività deve essere eseguita in un agente di compilazione con Windows Defender già installato. Per ulteriori informazioni, vedere il [sito Web Windows Defender](https://aka.ms/defender).

### <a name="binskim"></a>BinSkim

BinSkim è uno scanner leggero PE (Portable Executable) che convalida le impostazioni del compilatore, le impostazioni del linker e altre caratteristiche rilevanti per la sicurezza dei file binari. Questa attività di compilazione fornisce un wrapper della riga di comando per l'applicazione console binskim.exe. BinSkim è uno strumento open source. Per ulteriori informazioni, consultate [BinSkim su GitHub.](https://github.com/Microsoft/binskim)

### <a name="credential-scanner"></a>Scanner credenziali

Le password e altri segreti archiviati nel codice sorgente sono un problema significativo. Credential Scanner è uno strumento proprietario di analisi statica che consente di risolvere questo problema. Lo strumento rileva credenziali, segreti, certificati e altro contenuto riservato nel codice sorgente e nell'output di compilazione.

### <a name="microsoft-security-risk-detection"></a>Rilevamento dei rischi per la sicurezza Microsoft

Microsoft Security Risk Detection (MSRD) è un servizio basato su cloud per il test fuzz. Identifica i bug di sicurezza sfruttabili nel software. Questo servizio richiede un abbonamento e un'attivazione separati. Per ulteriori informazioni, vedere [MSRD Developer Center](https://docs.microsoft.com/security-risk-detection/).

### <a name="roslyn-analyzers"></a>Analizzatori di Roslyn

Roslyn Analyzers è lo strumento integrato del compilatore di Microsoft per l'analisi statica del codice Visual Basic e gestito. Per ulteriori informazioni, consultate [Analizzatori basati su Roslyn.](https://docs.microsoft.com/dotnet/standard/analyzers/)

### <a name="tslint"></a>TSLint (Informazioni in base alla

TSLint è uno strumento di analisi statica estensibile che controlla la leggibilità, la manutenibilità e gli errori di funzionalità nel codice TypeScript. È ampiamente supportato da editor moderni e sistemi di compilazione. È possibile personalizzarlo con regole, configurazioni e formattatori personalizzati. TSLint è uno strumento open source. Per ulteriori informazioni, vedere [TSLint su GitHub](https://github.com/palantir/tslint).

## <a name="analysis-and-post-processing-of-results"></a>Analisi e post-elaborazione dei risultati

L'estensione Microsoft Security Code Analysis include inoltre tre attività di post-elaborazione. Queste attività consentono di analizzare i risultati ottenuti dalle attività dello strumento di sicurezza. Quando vengono aggiunte a una pipeline, queste attività in genere seguono tutte le altre attività dello strumento.

### <a name="publish-security-analysis-logs"></a>Pubblicazione dei registri di analisi della sicurezza

L'attività di compilazione Pubblica log analisi sicurezza mantiene i file di log degli strumenti di sicurezza eseguiti durante la compilazione. È possibile leggere questi registri per l'analisi e il follow-up.

È possibile pubblicare i file di log in Elementi di Azure come file con estensione zip. È anche possibile copiarli in una condivisione file accessibile dall'agente di compilazione privato.

### <a name="security-report"></a>Rapporto sulla sicurezza

L'attività di compilazione Report sicurezza analizza i file di log. Questi file vengono creati dagli strumenti di sicurezza eseguiti durante la compilazione. L'attività di compilazione crea quindi un singolo file di report di riepilogo. Questo file mostra tutti i problemi rilevati dagli strumenti di analisi.

È possibile configurare questa attività per riportare i risultati per strumenti specifici o per tutti gli strumenti. È inoltre possibile scegliere il livello di problema da segnalare, ad esempio solo errori o errori e avvisi.

### <a name="post-analysis-build-break"></a>Post-analisi (interruzione di compilazione)Post-Analysis (build break)

Con l'attività di compilazione post-analisi, è possibile inserire un'interruzione di compilazione che provoca un errore di compilazione. Inserire un'interruzione di compilazione se uno o più strumenti di analisi segnalano problemi nel codice.

È possibile configurare questa attività per interrompere la compilazione per i problemi rilevati da strumenti specifici o da tutti gli strumenti. È inoltre possibile configurarlo in base alla gravità dei problemi rilevati, ad esempio errori o avvisi.

>[!NOTE]
>Per impostazione specifica, ogni attività di compilazione ha esito positivo se l'attività viene completata correttamente. Ciò è vero se uno strumento rileva o meno problemi, in modo che la compilazione possa essere eseguita fino al completamento consentendo l'esecuzione di tutti gli strumenti.

## <a name="next-steps"></a>Passaggi successivi

Per istruzioni su come eseguire l'onboarding e l'installazione di Microsoft Security Code Analysis, fare riferimento alla [guida all'onboarding e all'installazione](security-code-analysis-onboard.md).

Per ulteriori informazioni sulla configurazione delle attività di compilazione, vedere la [guida alla configurazione](security-code-analysis-customize.md) o la guida alla configurazione [YAML](yaml-configuration.md).

Se hai ulteriori domande sull'estensione e gli strumenti offerti, dai un'occhiata alla nostra [pagina FAQ](security-code-analysis-faq.md).
