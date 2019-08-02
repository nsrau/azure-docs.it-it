---
title: Panoramica della documentazione di analisi del codice di sicurezza Microsoft Azure
description: Questo articolo è una panoramica dell'estensione di analisi del codice di sicurezza
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
ms.openlocfilehash: 283d63bafc583f2ac9da3294644aaebd17d7c950
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68718288"
---
# <a name="about-microsoft-security-code-analysis"></a>Informazioni sull'analisi del codice di sicurezza Microsoft

**Microsoft Security Code Analysis Extension** consente ai team di integrare facilmente l'analisi del codice di sicurezza nelle pipeline di integrazione continua/recapito continuo di Azure DevOps, come consigliato dagli esperti di [SDL (Secure Development Lifecycle)](https://www.microsoft.com/securityengineering/sdl/practices) di Microsoft. La sicurezza è semplificata tramite un'esperienza utente coerente che astrae le complessità dell'esecuzione di vari strumenti. Con una distribuzione basata su NuGet degli strumenti, i team non devono più gestire l'installazione o l'aggiornamento degli strumenti. Con le interfacce della riga di comando e delle attività di compilazione di base, tutti gli utenti, dai guru degli strumenti esperti agli sviluppatori di ogni giorno, possono avere un controllo minimo o troppo elevato sugli strumenti che desiderano. I team possono inoltre sfruttare potenti funzionalità di post-elaborazione, ad esempio la pubblicazione dei log per la conservazione, la generazione di report mirati per gli sviluppatori che si occupano & la configurazione di interruzioni di compilazione

## <a name="why-microsoft-security-code-analysis"></a>Vantaggi dell'analisi del codice di sicurezza Microsoft

### <a name="security-simplified"></a>Sicurezza semplificata

L'aggiunta di strumenti di analisi del codice di sicurezza alla pipeline di Azure DevOps è semplice quanto l'aggiunta di nuove attività. Personalizzarli o usare i valori predefiniti. Le attività vengono eseguite come parte della pipeline DevOps e producono log che descrivono in dettaglio tutti i tipi di risultati.

### <a name="clean-builds"></a>Pulisci compilazioni

Dopo aver risolto i problemi iniziali segnalati dagli strumenti, è possibile configurare l'estensione in modo che interrompa le compilazioni sui nuovi problemi. La configurazione della compilazione di integrazione continua in ogni richiesta pull non è mai stata così semplice.

### <a name="set-it-and-forget-it"></a>Impostarlo e dimenticarlo

Le attività e gli strumenti di compilazione possono essere impostati per rimanere aggiornati e per impostazione predefinita. Se è disponibile una versione aggiornata dello strumento, non è necessario scaricarla e installarla. Questa estensione viene occupata automaticamente. 

>>>
### <a name="under-the-hood"></a>Dietro le quinte

Le attività di compilazione dell'estensione di analisi del codice di sicurezza Microsoft astraggono le complessità di:
  - Esecuzione di strumenti di analisi statica della sicurezza e
  - Elaborazione dei risultati dei file di log per creare un report di riepilogo o interrompere la compilazione.
>>>

## <a name="security-code-analysis-toolset"></a>Set di strumenti di analisi del codice di sicurezza

Microsoft Security Code Analysis Extension rende immediatamente disponibili le versioni più recenti degli strumenti di analisi importanti. L'estensione include strumenti Microsoft interni e open source. Gli strumenti vengono scaricati automaticamente nell'agente ospitato nel cloud dopo aver configurato & eseguire la pipeline usando l'attività di compilazione corrispondente. Di seguito è riportato il set di strumenti attualmente disponibili nell'estensione. È possibile rimanere sintonizzati e inviare suggerimenti per gli strumenti che è possibile aggiungere.

### <a name="anti-malware-scanner"></a>Scanner anti-malware

L'attività di compilazione dello scanner anti-malware è ora inclusa nell'estensione Microsoft Security Code Analysis. Deve essere eseguita su un agente di compilazione in cui è già installato Windows Defender. Per ulteriori informazioni, visitare il [sito Web Defender](https://aka.ms/defender) 

### <a name="binskim"></a>BinSkim

BinSkim è uno scanner Lightweight PE (Portable Executable) che convalida le impostazioni del compilatore e del linker e altre caratteristiche binarie rilevanti per la sicurezza. L'attività di compilazione fornisce un wrapper della riga di comando per l'applicazione BinSkim. exe. BinSkim è uno strumento open source e per altre informazioni visitare [BinSkim su GitHub](https://github.com/Microsoft/binskim)

### <a name="credential-scanner"></a>Scanner delle credenziali

Le password e altri segreti archiviati nel codice sorgente sono attualmente un problema significativo. Credential scanner è uno strumento di analisi statica proprietario che rileva credenziali, segreti, certificati e altro contenuto sensibile nel codice sorgente e nell'output di compilazione.

### <a name="microsoft-security-risk-detection"></a>Rilevamento del rischio di sicurezza Microsoft

Il rilevamento dei rischi per la sicurezza è il servizio di test fuzzy basato sul cloud Microsoft per identificare i bug di sicurezza sfruttabili nel software. Questo servizio richiede un processo di onboarding separato. Per ulteriori informazioni, visitare [MSRD in docs.Microsoft.com](https://docs.microsoft.com/security-risk-detection/)

### <a name="roslyn-analyzers"></a>Analizzatori Roslyn

Strumento di analisi statica integrato dal compilatore Microsoft per l'analisi di codice gestitoC# (e VB). Per altre informazioni, visita gli [analizzatori Roslyn in docs.Microsoft.com](https://docs.microsoft.com/dotnet/standard/analyzers/)

### <a name="tslint"></a>TSLint

TSLint è uno strumento di analisi statica estendibile che controlla il codice TypeScript per la leggibilità, la gestibilità e gli errori di funzionalità. È ampiamente supportato negli editor e nei sistemi di compilazione moderni e può essere personalizzato con regole, configurazioni e formattatori personalizzati. TSLint è uno strumento open source e per altre informazioni visitare [TSLint su GitHub](https://github.com/palantir/tslint)

## <a name="analysis-and-post-processing-of-results"></a>Analisi e post-elaborazione dei risultati

Microsoft Security Code Analysis Extension include anche tre attività di post-elaborazione utili per elaborare e analizzare i risultati trovati dalle attività degli strumenti di sicurezza. Vengono in genere aggiunti alla pipeline dopo tutte le altre attività degli strumenti.

### <a name="publish-security-analysis-logs"></a>Pubblicare i log di analisi della sicurezza
L'attività di compilazione registri di analisi della sicurezza di pubblicazione conserva i file di log degli strumenti di sicurezza eseguiti durante la compilazione per l'analisi e il completamento.

Possono essere pubblicati negli elementi del server di Azure (come file zip) o copiati in una condivisione file accessibile dall'agente di compilazione privata.

### <a name="security-report"></a>Report sulla sicurezza
L'attività di compilazione report di sicurezza analizza i file di log creati dagli strumenti di protezione eseguiti durante la compilazione e crea un unico file di report riepilogativo con tutti i problemi rilevati dagli strumenti di analisi.

L'attività può essere configurata in modo da segnalare i risultati per strumenti specifici o per tutti gli strumenti ed è anche possibile scegliere il livello di problemi (errori, errori e avvisi) da segnalare.

### <a name="post-analysis-build-break"></a>Post-analisi (break di compilazione)
L'attività di compilazione post-analisi consente al cliente di inserire un'interruzione di compilazione ed eseguire il failover della compilazione nel caso in cui uno o più strumenti di analisi segnalino i risultati o i problemi del codice.

L'attività può essere configurata in modo da interrompere la compilazione per i problemi rilevati da strumenti specifici o per tutti gli strumenti e anche in base alla gravità dei problemi rilevati (errori o avvisi).

>[!NOTE]
>Le singole attività di compilazione riusciranno, per impostazione predefinita, fino a quando lo strumento viene completato correttamente, indipendentemente dal fatto che ci siano risultati, in modo che la compilazione possa essere eseguita fino al completamento, consentendo l'esecuzione di tutti gli strumenti.

## <a name="next-steps"></a>Passaggi successivi

Per istruzioni sull'onboarding e sull'installazione dell'analisi del codice di sicurezza, vedere la Guida all'onboarding [e all'installazione](security-code-analysis-onboard.md)

Per ulteriori informazioni sulla configurazione delle attività di compilazione, vedere la [Guida alla configurazione](security-code-analysis-customize.md)

Per altre domande sull'estensione e sugli strumenti offerti, [vedere la pagina delle domande frequenti.](security-code-analysis-faq.md)