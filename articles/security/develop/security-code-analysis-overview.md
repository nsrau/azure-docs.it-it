---
title: Panoramica della documentazione di analisi del codice di sicurezza Microsoft
description: Questo articolo è una panoramica dell'estensione di analisi del codice di sicurezza Microsoft
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
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851469"
---
# <a name="about-microsoft-security-code-analysis"></a>Informazioni sull'analisi del codice di sicurezza Microsoft

Con l'estensione per l'analisi del codice di sicurezza Microsoft, i team possono aggiungere l'analisi del codice di sicurezza alle pipeline di integrazione e recapito continuo (CI/CD) di Azure DevOps. Questa analisi è consigliata dagli esperti [SDL (Secure Development Lifecycle)](https://www.microsoft.com/securityengineering/sdl/practices) di Microsoft.

Un UX coerente semplifica la sicurezza nascondendo la complessità degli strumenti in esecuzione. Con la distribuzione basata su NuGet degli strumenti, i team non hanno più la necessità di gestire l'installazione o l'aggiornamento degli strumenti. Con la riga di comando e le interfacce di base per le attività di compilazione, tutti gli utenti possono avere un controllo maggiore sugli strumenti che desiderano.

I team possono anche usare potenti funzionalità di postelaborazione, ad esempio:

- Pubblicazione dei log per la conservazione.
- Generazione di report interoperabili e mirati agli sviluppatori.
- Configurazione delle interruzioni di compilazione nei test di regressione.

## <a name="why-should-i-use-microsoft-security-code-analysis"></a>Perché usare l'analisi del codice di sicurezza Microsoft?

### <a name="security-simplified"></a>Sicurezza semplificata

L'aggiunta di strumenti di analisi del codice di sicurezza Microsoft alla pipeline di Azure DevOps è semplice quanto l'aggiunta di nuove attività. Personalizzare le attività o utilizzare il comportamento predefinito. Le attività vengono eseguite come parte della pipeline di Azure DevOps e producono log che descrivono in dettaglio molti tipi di risultati.

### <a name="clean-builds"></a>Pulisci compilazioni

Dopo aver risolto i problemi iniziali segnalati dagli strumenti, è possibile configurare l'estensione in modo che interrompa le compilazioni sui nuovi problemi. La configurazione di compilazioni di integrazione continua in ogni richiesta pull è facile.

### <a name="set-it-and-forget-it"></a>Impostarlo e dimenticarlo

Per impostazione predefinita, le attività e gli strumenti di compilazione sono sempre aggiornati. Se è disponibile una versione aggiornata di uno strumento, non è necessario scaricarla e installarla. L'estensione si occupa automaticamente dell'aggiornamento.

### <a name="under-the-hood"></a>Dietro le quinte

Le attività di compilazione dell'estensione nascondono le complessità di:
  - Esecuzione di strumenti di analisi statica della sicurezza.
  - Elaborazione dei risultati dei file di log per creare un report di riepilogo o interrompere la compilazione.

## <a name="microsoft-security-code-analysis-tool-set"></a>Set di strumenti di analisi del codice di sicurezza Microsoft

Microsoft Security Code Analysis Extension rende immediatamente disponibili le versioni più recenti degli strumenti di analisi importanti. L'estensione include strumenti gestiti da Microsoft e strumenti open source.

Questi strumenti vengono scaricati automaticamente nell'agente ospitato nel cloud dopo aver usato l'attività di compilazione corrispondente per configurare ed eseguire la pipeline.

Questa sezione elenca il set di strumenti attualmente disponibili nell'estensione. Osservare l'aggiunta di altri strumenti. Inviare anche suggerimenti per gli strumenti che si desidera aggiungere.

### <a name="anti-malware-scanner"></a>Scanner anti-malware

L'attività di compilazione dello scanner anti-malware è ora inclusa nell'estensione Microsoft Security Code Analysis. Questa attività deve essere eseguita su un agente di compilazione in cui è già installato Windows Defender. Per ulteriori informazioni, vedere il [sito Web di Windows Defender](https://aka.ms/defender).

### <a name="binskim"></a>BinSkim

BinSkim è uno scanner leggero PE (Portable Executable) che convalida le impostazioni del compilatore, le impostazioni del linker e altre caratteristiche relative alla sicurezza dei file binari. Questa attività di compilazione fornisce un wrapper della riga di comando per l'applicazione console binskim. exe. BinSkim è uno strumento open source. Per altre informazioni, vedere [BinSkim su GitHub](https://github.com/Microsoft/binskim).

### <a name="credential-scanner"></a>Scanner delle credenziali

Le password e altri segreti archiviati nel codice sorgente rappresentano un problema significativo. Credential scanner è uno strumento di analisi statica proprietario che consente di risolvere il problema. Lo strumento rileva credenziali, segreti, certificati e altro contenuto sensibile nel codice sorgente e nell'output di compilazione.

### <a name="microsoft-security-risk-detection"></a>Rilevamento del rischio di sicurezza Microsoft

Microsoft Security Risk Detection (MSRD) è un servizio basato sul cloud per i test fuzzy. Identifica i bug di sicurezza sfruttabili nel software. Questo servizio richiede una sottoscrizione e un'attivazione separate. Per ulteriori informazioni, vedere il [centro per sviluppatori MSRD](https://docs.microsoft.com/security-risk-detection/).

### <a name="roslyn-analyzers"></a>Analizzatori di Roslyn

Gli analizzatori Roslyn sono uno strumento integrato del compilatore Microsoft per l'analisi statica del C# codice gestito e Visual Basic. Per altre informazioni, vedere [analizzatori basati su Roslyn](https://docs.microsoft.com/dotnet/standard/analyzers/).

### <a name="tslint"></a>TSLint

TSLint è uno strumento di analisi statica estendibile che controlla il codice TypeScript per migliorare la leggibilità, la gestibilità e gli errori di funzionalità. È ampiamente supportato dagli editor e dai sistemi di compilazione moderni. È possibile personalizzarla con regole, configurazioni e formattatori personalizzati. TSLint è uno strumento open source. Per altre informazioni, vedere [TSLint su GitHub](https://github.com/palantir/tslint).

## <a name="analysis-and-post-processing-of-results"></a>Analisi e post-elaborazione dei risultati

Microsoft Security Code Analysis Extension include anche tre attività di postelaborazione. Queste attività consentono di analizzare i risultati trovati dalle attività dello strumento di sicurezza. Quando vengono aggiunte a una pipeline, queste attività seguono in genere tutte le altre attività degli strumenti.

### <a name="publish-security-analysis-logs"></a>Pubblicare i log di analisi della sicurezza

L'attività di compilazione registri di analisi della sicurezza di pubblicazione conserva i file di log degli strumenti di sicurezza che vengono eseguiti durante la compilazione. È possibile leggere questi log per l'analisi e il completamento.

È possibile pubblicare i file di log Azure Artifacts come file con estensione zip. È anche possibile copiarli in una condivisione file accessibile dall'agente di compilazione privata.

### <a name="security-report"></a>Report sulla sicurezza

L'attività di compilazione report di sicurezza analizza i file di log. Questi file vengono creati dagli strumenti di sicurezza eseguiti durante la compilazione. L'attività di compilazione crea quindi un singolo file di report riepilogativo. Questo file Mostra tutti i problemi rilevati dagli strumenti di analisi.

Questa attività può essere configurata in modo da segnalare i risultati di strumenti specifici o di tutti gli strumenti. È anche possibile scegliere il livello di problema da segnalare, ad esempio solo gli errori o gli errori e gli avvisi.

### <a name="post-analysis-build-break"></a>Post-analisi (break di compilazione)

Con l'attività di compilazione post-analisi, è possibile inserire un'interruzione di compilazione che causa la mancata riuscita di una compilazione. Si inserisce un'interruzioni di compilazione se uno o più strumenti di analisi segnalano problemi nel codice.

È possibile configurare questa attività per interrompere la compilazione per i problemi rilevati da strumenti specifici o da tutti gli strumenti. È anche possibile configurarlo in base alla gravità dei problemi rilevati, ad esempio errori o avvisi.

>[!NOTE]
>Per impostazione predefinita, ogni attività di compilazione ha esito positivo se l'attività viene completata correttamente. Questo vale indipendentemente dal fatto che uno strumento rilevi problemi, in modo che la compilazione possa essere eseguita fino al completamento consentendo l'esecuzione di tutti gli strumenti.

## <a name="next-steps"></a>Passaggi successivi

Per istruzioni su come caricare e installare Microsoft Security Code Analysis, vedere la [Guida all'onboarding e all'installazione](security-code-analysis-onboard.md).

Per ulteriori informazioni sulla configurazione delle attività di compilazione, vedere la guida alla [configurazione](security-code-analysis-customize.md) o la [Guida alla configurazione di YAML](yaml-configuration.md).

Per altre domande sull'estensione e sugli strumenti offerti, vedere la [pagina delle domande frequenti](security-code-analysis-faq.md).
