---
title: Personalizzare le attività di analisi del codice di sicurezza MicrosoftCustomize Microsoft Security Code Analysis tasks
titleSuffix: Azure
description: In questo articolo viene descritta la personalizzazione delle attività nell'estensione Microsoft Security Code Analysis
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
ms.openlocfilehash: 6cdf892651407defc21f359a8e3b326b4af63b62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77499996"
---
# <a name="configure-and-customize-the-build-tasks"></a>Configurare e personalizzare le attività di compilazione

In questo articolo vengono descritte in dettaglio le opzioni di configurazione disponibili in ognuna delle attività di compilazione. L'articolo inizia con le attività per gli strumenti di analisi del codice di sicurezza. Termina con le attività di post-elaborazione.

## <a name="anti-malware-scanner-task"></a>Attività scanner antimalware

>[!NOTE]
> L'attività di compilazione Anti-Malware Scanner richiede un agente di compilazione con Windows Defender abilitato. Ospitato Visual Studio 2017 e versioni successive forniscono tale agente. L'attività di compilazione non verrà eseguita nell'agente ospitato di Visual Studio 2015.
>
> Anche se le firme non possono essere aggiornate su questi agenti, le firme devono sempre essere meno di tre ore.

I dettagli della configurazione delle attività sono riportati nella schermata e nel testo seguenti.

![Configurazione dell'attività di compilazione Anti-Malware Scanner](./media/security-tools/5-add-anti-malware-task600.png)

Nella casella di riepilogo **Tipo** della schermata è selezionata **l'opzione Di base.** Selezionare **Personalizzato** per fornire argomenti della riga di comando che personalizzano l'analisi.

Windows Defender utilizza il client Windows Update per scaricare e installare le firme. Se l'aggiornamento della firma non riesce nell'agente di compilazione, è probabile che il codice di errore **HRESULT** provenga da Windows Update.

Per ulteriori informazioni sugli errori di Windows Update e sulla relativa attenuazione, vedere Codici di errore di [Windows Update per componente](https://docs.microsoft.com/windows/deployment/update/windows-update-error-reference) e l'articolo di TechNet Windows Update Agent - Codici di [errore](https://social.technet.microsoft.com/wiki/contents/articles/15260.windows-update-agent-error-codes.aspx).

Per informazioni sulla configurazione di YAML per questa attività, consultare le nostre [opzioni YAML Anti-Malware](yaml-configuration.md#anti-malware-scanner-task)

## <a name="binskim-task"></a>Attività BinSkim

> [!NOTE]
> Prima di eseguire l'attività BinSkim, la compilazione deve soddisfare una delle condizioni seguenti:Before you can run the BinSkim task, your build must meet one of these conditions:
>  - La compilazione produce elementi binari dal codice gestito.
>  - Si dispone di elementi binari di cui è stato eseguito il commit che si desidera analizzare con BinSkim.

I dettagli della configurazione delle attività sono mostrati nella schermata e nell'elenco seguenti.

![Configurazione dell'attività di compilazione BinSkim](./media/security-tools/7-binskim-task-details.png)

- Impostare la configurazione di compilazione su Debug in modo che vengano prodotti file di debug con estensione pdb. BinSkim utilizza questi file per eseguire il mapping dei problemi nei file binari di output al codice sorgente.
- Per evitare ricerche e creazione di una riga di comando personalizzata:
     - Nell'elenco **Tipo** selezionare **Di base**.
     - Nell'elenco **Funzione** selezionare **Analizza**.
- In **Destinazione**immettere uno o più identificatori per un file, una directory o un modello di filtro. Questi identificatori vengono risolti in uno o più file binari da analizzare:These specifiers resolve to one or more binaries to be analyzed:
    - Più destinazioni specificate devono essere separate da un punto e virgola (;).
    - Un identificatore può essere un singolo file o contenere caratteri jolly.
    - Le specifiche della \\directory devono sempre terminare con il simbolo .
    - Esempi:

           *.dll;*.exe
           $(BUILD_STAGINGDIRECTORY)\*
           $(BUILD_STAGINGDIRECTORY)\*.dll;$(BUILD_STAGINGDIRECTORY)\*.exe;

- Se si seleziona **Riga di comando** nell'elenco **Tipo,** è necessario eseguire binskim.exe:
     - Assicurarsi che i primi argomenti di binskim.exe siano **l'analisi** dei verbi seguita da una o più specifiche di percorso. Ogni percorso può essere un percorso completo o un percorso relativo alla directory di origine.
     - Più percorsi di destinazione devono essere separati da uno spazio.
     - È possibile omettere l'opzione **/o** o **/output.** Il valore di output viene aggiunto o sostituito automaticamente.
     - Le configurazioni della riga di comando standard vengono visualizzate di seguito.

           analyze $(Build.StagingDirectory)\* --recurse --verbose
           analyze *.dll *.exe --recurse --verbose

          > [!NOTE]
          > Il simbolo \\di cancelazione è importante se si specificano le directory per la destinazione.

Per ulteriori informazioni sugli argomenti della riga di comando di BinSkim, sulle regole in base all'ID o sui codici di uscita, consultare la [Guida dell'utente di BinSkim](https://github.com/Microsoft/binskim/blob/master/docs/UserGuide.md).

Per informazioni sulla configurazione di YAML per questa attività, consulta le nostre [opzioni BinSkim YAML](yaml-configuration.md#binskim-task)

## <a name="credential-scanner-task"></a>Attività Scanner credenziali

I dettagli della configurazione delle attività sono mostrati nella schermata e nell'elenco seguenti.

![Configurazione dell'attività di compilazione di Credential Scanner](./media/security-tools/3-taskdetails.png)

Le opzioni disponibili includono:

  - **Formato di output**: I valori disponibili includono **TSV**, **CSV**, **SARIF**e **PREfast**.
  - **Versione strumento**: Si consiglia di selezionare **Più recente**.
  - **Scan Folder (Cartella di scansione):** la cartella del repository da analizzare.
  - **Tipo file ricercatori**: Le opzioni per individuare il file searcher utilizzato per la scansione.
  - **Suppressions File**: Un file [JSON](https://json.org/) può eliminare i problemi nel log di output. Per altre informazioni sugli scenari di eliminazione, vedere la sezione Domande frequenti di questo articolo.
  - **Output dettagliato**: Autoesplicativo.
  - **Dimensione batch:** numero di thread simultanei utilizzati per eseguire User Scanner. Il valore predefinito è 20. I valori possibili sono compresi tra 1 e 2.147.483.647.
  - **Timeout corrispondenza**: La quantità di tempo in secondi da spendere per tentare una corrispondenza di ricerca prima di abbandonare il controllo.
  - **File Scan Read Buffer Size (Dimensioni buffer lettura file):** la dimensione in byte del buffer utilizzato durante la lettura del contenuto. Il valore predefinito è 524.288.  
  - Numero massimo byte letti dall'analisi **dei file:** il numero massimo di byte da leggere da un file durante l'analisi del contenuto. Il valore predefinito è 104.857.600.
  - **Opzioni di controllo** > Esegui**questa attività**: Specifica quando verrà eseguita l'attività. Selezionare **Condizioni personalizzate** per specificare condizioni più complesse.
  - **Versione**: La versione dell'attività di compilazione all'interno di Azure DevOps.Version : The build task version within Azure DevOps. Questa opzione non viene utilizzata di frequente.

Per informazioni sulla configurazione di YAML per questa attività, consultare le opzioni [YAML di Credential Scanner](yaml-configuration.md#credential-scanner-task)

## <a name="microsoft-security-risk-detection-task"></a>Attività di rilevamento dei rischi per la sicurezza Microsoft

> [!NOTE]
> È necessario creare e configurare un account con il servizio Microsoft Security Risk Detection (MSRD) prima di utilizzare l'attività MSRD. Questo servizio richiede un processo di onboarding separato. A differenza della maggior parte delle altre attività in questa estensione, questa attività richiede una sottoscrizione separata con MSRD.
>
> Fare riferimento a [Rilevamento dei rischi](https://aka.ms/msrddocs) per la sicurezza Microsoft e [Rilevamento dei rischi per](https://docs.microsoft.com/security-risk-detection/how-to/) la sicurezza Microsoft: procedura per istruzioni.

I dettagli per la configurazione di questa attività sono illustrati nell'elenco seguente. Per qualsiasi elemento dell'interfaccia utente, è possibile passare il puntatore del mouse su tale elemento per ottenere assistenza.

   - **Nome endpoint del servizio DevOps di Azure per MSRD:** un tipo generico di endpoint del servizio DevOps di Azure archivia l'URL dell'istanza MSRD in elenco e il token di accesso all'API REST. Se è stato creato un endpoint di questo tipo, è possibile specificarlo qui. In caso contrario, selezionare il collegamento **Gestisci** per creare e configurare un nuovo endpoint del servizio per questa attività MSRD.
   - **Account ID**: UN GUID che può essere recuperato dall'URL dell'account MSRD.
   - **URL a binari:** elenco delimitato da punti e virgola di URL disponibili pubblicamente. Il computer fuzzing utilizza questi URL per scaricare i file binari.
   - **URL dei**file di serie : un elenco delimitato da punti e virgola di URL disponibili pubblicamente. La macchina fuzzing utilizza questi URL per scaricare i semi. La specifica di questo valore è facoltativa se i file di seeding vengono scaricati insieme ai file binari.
   - **Tipo di piattaforma del**sistema operativo: piattaforma del sistema operativo (OS) dei computer che eseguono il processo di fuzzing. I valori disponibili sono **Windows** e **Linux**.
   - **Windows Edition / Linux Edition**: L'edizione del sistema operativo delle macchine che eseguono il processo fuzzing. È possibile sovrascrivere il valore predefinito se i computer hanno un'edizione del sistema operativo diversa.
   - **Script di installazione del pacchetto**: lo script da eseguire in un computer di test. Questo script installa il programma di destinazione del test e le relative dipendenze prima dell'invio del processo di fuzzing.
   - **Parametri invio lavoro**:
       - **Seed Directory**: Il percorso della directory sulla macchina fuzzing che contiene i semi.
       - **Seed Extension**: L'estensione del nome file dei semi.
       - **Test Driver Executable (Eseguibile driver**di test): percorso del file eseguibile di destinazione nel computer fuzzing.
       - **Architettura eseguibile del driver di**test: architettura del file eseguibile di destinazione. I valori disponibili sono **x86** e **amd64**.
       - **Argomenti del driver di**test : gli argomenti della riga di comando passati al file eseguibile di test. L'argomento "%testfile%", incluse le virgolette, viene sostituito automaticamente con il percorso completo del file di destinazione. Questo file viene analizzato dal driver di test ed è obbligatorio.
       - **Test Driver Process Exits Upon Test Completion (Uscita al completamento del test):** selezionare questa casella di controllo se il driver di test deve essere terminato al completamento. Deselezionarla se il driver di test deve essere chiuso con la chiusura con la rete.
       - **Durata massima (in secondi):** stima del tempo più lungo ragionevolmente previsto richiesto dal programma di destinazione per analizzare un file di input. Maggiore è l'accuratezza della stima, maggiore è l'efficienza di esecuzione dell'app fuzzing.
       - **Test Driver può essere eseguito ripetutamente:** selezionare questa casella di controllo se il driver di test può essere eseguito ripetutamente senza dipendere da uno stato globale persistente o condiviso.
       - **Test Driver Può essere rinominato**: Selezionare questa casella di controllo se il file eseguibile del driver di test può essere rinominato e funziona comunque correttamente.
       - **L'applicazione fuzzing viene eseguita come un singolo processo del**sistema operativo: selezionare questa casella di controllo se il driver di test viene eseguito in un singolo processo del sistema operativo. Deselezionarla se il driver di test genera processi aggiuntivi.

Per informazioni sulla configurazione di YAML per questa attività, consultare le [opzioni YAML per](yaml-configuration.md#microsoft-security-risk-detection-task) il rilevamento dei rischi per la sicurezza Microsoft

## <a name="roslyn-analyzers-task"></a>Attività Analizzatori di roslyn

> [!NOTE]
> Prima di eseguire l'attività Analizzatori Roslyn, la compilazione deve soddisfare queste condizioni:Before you can run the Roslyn Analyzers task, your build needs to meet these conditions:
>
> - La definizione di compilazione include l'attività di compilazione predefinita MSBuild o VSBuild per compilare il codice in C o Visual Basic. L'attività Analyzers si basa sull'input e sull'output dell'attività predefinita per eseguire la compilazione MSBuild con gli analizzatori Roslyn abilitati.
> - L'agente di compilazione che esegue questa attività di compilazione dispone di Visual Studio 2017 versione 15.5 o successiva installato, in modo che utilizzi la versione del compilatore 2.6 o successiva.

I dettagli della configurazione delle attività sono riportati nell'elenco e nella nota seguenti.

Le opzioni disponibili includono:

- **Set**di regole : I valori sono **SDL Required**, **SDL Recommended**o il proprio set di regole personalizzato.
- **Versione Analizzatori**: Si consiglia di selezionare **Più recente**.
- File di eliminazione degli **avvisi del compilatore**: un file di testo con un elenco di ID avvisi che vengono eliminati.
- **Opzioni di controllo** > Esegui**questa attività**: Specifica quando verrà eseguita l'attività. Scegliere **Condizioni personalizzate** per specificare condizioni più complesse.

> [!NOTE]
>
> - Gli analizzatori Roslyn sono integrati con il compilatore e possono essere eseguiti solo come parte della compilazione csc.exe. Di conseguenza, questa attività richiede che il comando del compilatore eseguito in precedenza nella compilazione venga riprodotto o eseguito di nuovo. Questa riproduzione o esecuzione viene eseguita eseguendo una query di Visual Studio Team Services (VSTS) per i log dell'attività di compilazione MSBuild.
>
>   Non esiste altro modo per l'attività ottenere in modo affidabile la riga di comando di compilazione MSBuild dalla definizione di compilazione. È stata presa in considerazione l'aggiunta di una casella di testo in formato libero per consentire agli utenti di immettere le righe di comando. Ma allora sarebbe difficile mantenere quelle linee di comando up-to-date e in sincronia con la build principale.
>
>   Le compilazioni personalizzate richiedono la riproduzione dell'intero set di comandi, non solo dei comandi del compilatore. In questi casi, l'abilitazione di Analizzatori Roslyn non è banale o affidabile.
>
> - Gli analizzatori Roslyn sono integrati con il compilatore. Per essere richiamati, gli analizzatori Roslyn richiedono la compilazione.
>
>   Questa nuova attività di compilazione viene implementata ricompilando i progetti C , che sono già stati compilati. La nuova attività usa solo le attività di compilazione MSBuild e VSBuild nella stessa definizione di compilazione o compilazione dell'attività originale. Tuttavia, in questo caso la nuova attività li utilizza con Roslyn Analyzers abilitato.
>
>   Se la nuova attività viene eseguita sullo stesso agente dell'attività originale, l'output della nuova attività sovrascrive l'output dell'attività originale nella cartella di origine *s.* Anche se l'output di compilazione è lo stesso, si consiglia di eseguire MSBuild, copiare l'output nella directory di gestione temporanea degli elementi e quindi eseguire Roslyn Analyzers.

Per ulteriori risorse per l'attività Analizzatori Roslyn, vedere [Gli analizzatori basati su Roslyn](https://docs.microsoft.com/dotnet/standard/analyzers/) in Microsoft Docs.

È possibile trovare il pacchetto dell'analizzatore installato e utilizzato da questa attività di compilazione nella pagina NuGet [Microsoft.CodeAnalysis.FxCopAnalyzers](https://www.nuget.org/packages/Microsoft.CodeAnalysis.FxCopAnalyzers).

Per informazioni sulla configurazione di YAML per questa attività, consulta le nostre [opzioni YAML di Roslyn Analyzers](yaml-configuration.md#roslyn-analyzers-task)

## <a name="tslint-task"></a>Attività TSLint

Per ulteriori informazioni su TSLint, visitare il [repository TSLint GitHub](https://github.com/palantir/tslint).

>[!NOTE] 
>Come si potrebbe essere a conoscenza, la home page [repository TSLint GitHub](https://github.com/palantir/tslint) dice che TSLint sarà deprecato a volte in 2019. Microsoft sta esaminando [ESLint](https://github.com/eslint/eslint) come attività alternativa.

Per informazioni sulla configurazione di YAML per questa attività, consulta le nostre [opzioni TSLint YAML](yaml-configuration.md#tslint-task)

## <a name="publish-security-analysis-logs-task"></a>Attività Pubblica registri analisi sicurezza

I dettagli della configurazione delle attività sono mostrati nella schermata e nell'elenco seguenti.

![Configurazione dell'attività di compilazione Pubblica log analisi sicurezza](./media/security-tools/9-publish-security-analsis-logs600.png)  

- **Nome artefatto**: Qualsiasi identificatore di stringa.
- **Tipo di elemento:** a seconda della selezione, è possibile pubblicare i log nel server DevOps di Azure o in un file condiviso accessibile all'agente di compilazione.
- **Strumenti**: È possibile scegliere di conservare i registri per strumenti specifici oppure selezionare **Tutti gli strumenti** per conservare tutti i registri.

Per informazioni sulla configurazione di YAML per questa attività, consulta le nostre [opzioni Pubblica registri di sicurezza YAML](yaml-configuration.md#publish-security-analysis-logs-task)

## <a name="security-report-task"></a>Attività Rapporto sicurezza

I dettagli della configurazione del Rapporto di sicurezza sono riportati nella schermata e nell'elenco seguenti.

![Configurazione dell'attività di compilazione Report sicurezza](./media/security-tools/4-createsecurityanalysisreport600.png)

- **Report**: selezionare uno dei formati **Console pipeline**, **File TSV**e **File HTML.** Viene creato un file di report per ogni formato selezionato.
- **Strumenti:** selezionare gli strumenti nella definizione di compilazione per i quali si desidera un riepilogo dei problemi rilevati. Per ogni strumento selezionato, potrebbe essere disponibile un'opzione per scegliere se visualizzare solo gli errori o visualizzare sia gli errori che gli avvisi nel rapporto di riepilogo.
- **Opzioni avanzate**: Se non sono presenti registri per uno degli strumenti selezionati, è possibile scegliere di registrare un avviso o un errore. Se si registra un errore, l'attività ha esito negativo.
- **Cartella Registri di base**: È possibile personalizzare la cartella dei registri di base in cui si trovano i registri. Ma questa opzione non viene in genere utilizzata.

Per informazioni sulla configurazione di YAML per questa attività, consulta le nostre [opzioni YAML del rapporto sulla sicurezza](yaml-configuration.md#security-report-task)

## <a name="post-analysis-task"></a>Attività post-analisi

I dettagli della configurazione delle attività sono mostrati nella schermata e nell'elenco seguenti.

![Configurazione dell'attività di compilazione post-analisi](./media/security-tools/a-post-analysis600.png)

- **Strumenti:** selezionare gli strumenti nella definizione di compilazione per i quali si desidera inserire in modo condizionale un'interruzione di compilazione. Per ogni strumento selezionato, potrebbe essere disponibile un'opzione per scegliere se si desidera interrompere solo gli errori o su errori e avvisi.
- **Report**: È possibile scrivere facoltativamente i risultati che causano l'interruzione di compilazione. I risultati vengono scritti nella finestra della console DevOps di Azure e nel file di log.
- **Opzioni avanzate**: Se non sono presenti registri per uno degli strumenti selezionati, è possibile scegliere di registrare un avviso o un errore. Se si registra un errore, l'attività ha esito negativo.

Per informazioni sulla configurazione di YAML per questa attività, consulta le nostre [opzioni YAML post-analisi](yaml-configuration.md#post-analysis-task)

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sulla configurazione basata su YAML, fare riferimento alla guida alla [configurazione YAML](yaml-configuration.md).

Se hai altre domande sull'estensione Security Code Analysis e sugli strumenti offerti, dai un'occhiata [alla nostra pagina FAQ](security-code-analysis-faq.md).
