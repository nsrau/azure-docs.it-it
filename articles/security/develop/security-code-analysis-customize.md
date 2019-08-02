---
title: Guida alla personalizzazione dell'attività di analisi del codice di Microsoft Azure
description: Questo articolo illustra la personalizzazione delle attività nell'estensione di analisi del codice di sicurezza
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
ms.openlocfilehash: ab219b71eb8cd6f6172b7d02a639301c67811b49
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68718353"
---
# <a name="how-to-configure--customize-the-build-tasks"></a>Procedura: Configurare & personalizzare le attività di compilazione

In questa pagina vengono descritte in dettaglio le opzioni di configurazione disponibili in ognuna delle attività di compilazione, a partire dalle attività per gli strumenti di analisi del codice di sicurezza seguite dalle attività di post-elaborazione

## <a name="anti-malware-scanner-task"></a>Attività scanner anti-malware

> [!NOTE]
> Per l'attività di compilazione anti-malware è necessario un agente di compilazione con Windows Defender abilitato, che è true negli agenti di compilazione "Hosted VS2017" e versioni successive. (Non verrà eseguito nell'agente "Hosted" Legacy/VS2015) Non è possibile aggiornare le firme su questi agenti, ma la firma deve essere sempre relativamente aggiornata, minore di 3 ore.

Screenshot e informazioni dettagliate sulla configurazione di seguito.

![Personalizzazione dell'attività di compilazione dello scanner anti-malware](./media/security-tools/5-add-anti-malware-task600.png) 

- Impostazioni per Type = **Basic**
- Con Type = **Custom**, è possibile fornire argomenti della riga di comando per personalizzare l'analisi.

Windows Defender usa il client Windows Update per scaricare e installare le firme. Se l'aggiornamento della firma ha esito negativo nell'agente di compilazione, è probabile che il codice di errore HRESULT provenga da Windows Update. Per ulteriori informazioni su Windows Update errori e la mitigazione, visitare [questa](https://docs.microsoft.com/windows/deployment/update/windows-update-error-reference) pagina e la [pagina TechNet](https://social.technet.microsoft.com/wiki/contents/articles/15260.windows-update-agent-error-codes.aspx)

## <a name="binskim-task"></a>Attività BinSkim

> [!NOTE]
> Come prerequisito per l'esecuzione dell'attività BinSkim, è necessario che la compilazione soddisfi una delle condizioni seguenti.
>    - La compilazione produce artefatti binari dal codice gestito.
>    - Si dispone di elementi binari di cui è stato eseguito il commit che si desidera analizzare con BinSkim.

Screenshot e informazioni dettagliate sulla configurazione di seguito. 

![Installazione di BinSkim](./media/security-tools/7-binskim-task-details.png)  
1. Impostare la configurazione della build su debug per produrre file di debug * **. pdb** . Vengono usati da BinSkim per eseguire il mapping dei problemi rilevati nel file binario di output nel codice sorgente. 
2. Scegliere tipo = **Basic** & Function = **Analyze** per evitare la ricerca e la creazione della riga di comando. 
3. **Target** : uno o più identificatori di un file, di una directory o di un modello di filtro che viene risolto in uno o più file binari da analizzare. 
    - Più destinazioni devono essere separate da un **punto e virgola (;)** . 
    - Può essere un singolo file o contenere caratteri jolly.
    - Le directory devono sempre terminare con\*
    - Esempi:

           *.dll;*.exe
           $(BUILD_STAGINGDIRECTORY)\*
           $(BUILD_STAGINGDIRECTORY)\*.dll;$(BUILD_STAGINGDIRECTORY)\*.exe;
4. Se si seleziona tipo = **riga di comando**, 
     - Verificare che il primo argomento di **BinSkim. exe** sia il verbo **analizza** usando percorsi completi o percorsi relativi alla directory di origine.
     - Per l'input della **riga di comando** , più destinazioni devono essere separate da uno spazio.
     - È possibile omettere il parametro del file **/o** o **/output** ; verrà aggiunto o sostituito. 
     - **Configurazione della riga di comando standard** 

           analyze $(Build.StagingDirectory)\* --recurse --verbose
           analyze *.dll *.exe --recurse --verbose
          > [!NOTE]
          > Il finale \* è molto importante quando si specifica una directory o directory per la destinazione. 

Per altri dettagli su BinSkim sugli argomenti della riga di comando, sulle regole per ID o sui codici di uscita, vedere il [manuale dell'utente di BinSkim](https://github.com/Microsoft/binskim/blob/master/docs/UserGuide.md)

## <a name="credential-scanner-task"></a>Attività scanner credenziali
Screenshot e informazioni dettagliate sulla configurazione di seguito.
 
![Personalizzazione dello scanner delle credenziali](./media/security-tools/3-taskdetails.png)

Le opzioni disponibili includono 
  - **Formato di output** : TSV/CSV/Sarif/PREfast
  - **Versione dello strumento** Consigliabile Ultima
  - **Analizza cartella** : cartella nel repository da analizzare
  - **Cerca in tipo di file** : opzioni per individuare il file di ricerca utilizzato per l'analisi.
  - **File di eliminazione** : è possibile usare un file [JSON](https://json.org/) per l'eliminazione dei problemi nel log di output. altre informazioni sono disponibili nella sezione risorse. 
  - **Output dettagliato** -spiegazione automatica 
  - **Dimensioni batch** : numero di thread simultanei utilizzati per eseguire gli scanner di credenziali in parallelo. Il valore predefinito è 20 (il valore deve essere compreso tra 1 e 2147483647).
  - **Timeout corrispondenza** : periodo di tempo impiegato per il tentativo di ricerca di una corrispondenza prima di abbandonare il controllo. 
  - **Dimensioni buffer di lettura analisi file** -dimensioni buffer durante la lettura del contenuto in byte. (Il valore predefinito è 524288) 
  - Numero **massimo di byte letti** dall'analisi dei file: numero massimo di byte da leggere da un determinato file durante l'analisi del contenuto. (Il valore predefinito è 104857600) 
  - **Esegui questa attività** (in **Opzioni di controllo**): specifica quando eseguire l'attività. Scegliere "condizioni personalizzate" per specificare condizioni più complesse. 
  - **Versione** : compilare la versione dell'attività all'interno di Azure DevOps. Non usato di frequente. 

## <a name="microsoft-security-risk-detection-task"></a>Attività di rilevamento del rischio Microsoft per la sicurezza
> [!NOTE]
> È necessario creare e configurare un account con il servizio di rilevamento dei rischi come prerequisito per poter usare questa attività. Questo servizio richiede un processo di onboarding separato; non è' plug-and-Play ' come la maggior parte delle altre attività in questa estensione. Fare riferimento al [rilevamento del rischio Microsoft](https://aka.ms/msrddocs) per [la sicurezza e al rilevamento del rischio Microsoft per la sicurezza: ](https://docs.microsoft.com/security-risk-detection/how-to/) Procedura per istruzioni.

Informazioni dettagliate sulla configurazione di seguito.

Immettere i dati necessari; ogni opzione ha la guida del testo del passaggio del mouse.
   - **Nome dell'endpoint del servizio DevOps di Azure per MSRD**: Se è stato creato un tipo generico di endpoint del servizio Azure DevOps per archiviare l'URL dell'istanza di MSRD (è stato caricato in) e il token di accesso dell'API REST, è possibile scegliere tale endpoint di servizio. In caso contrario, fare clic sul collegamento Gestisci per creare e configurare un nuovo endpoint del servizio per questa attività MSRD. 
   - **ID account**: Si tratta di un GUID che può essere recuperato dall'URL dell'account MSRD.
   - **URL dei file binari**: Elenco delimitato da punti e virgola degli URL disponibili pubblicamente (che devono essere usati dal computer di fuzzing per scaricare i file binari).
   - **URL dei file di inizializzazione**: Elenco delimitato da punti e virgola degli URL disponibili pubblicamente (che devono essere usati dal computer di fuzzing per scaricare i seed). Questo campo è facoltativo se i file di inizializzazione vengono scaricati insieme ai file binari.
   - **Tipo piattaforma sistema operativo**: Tipo di piattaforma del sistema operativo (Windows o Linux) dei computer in cui eseguire il processo di fuzzing.
   - Edizione di **Windows/Linux**: Edizione del sistema operativo dei computer in cui eseguire il processo di fuzzing. È possibile sovrascrivere il valore predefinito se i computer hanno un'edizione diversa del sistema operativo.
   - **Script di installazione pacchetto**: Fornire lo script da eseguire in un computer di test per installare il programma di destinazione del test e le relative dipendenze prima dell'invio del processo di fuzzing.
   - **Parametri di invio dei processi**:
       - **Directory**di inizializzazione: Percorso della directory nel computer di fuzzing contenente i semi.
       - **Estensione**di inizializzazione: Estensione di file dei Seed
       - **Eseguibile del driver di test**: Percorso dell'eseguibile di destinazione nel computer di fuzzing.
       - **Architettura eseguibile del driver di test**: L'architettura del file eseguibile di destinazione (x86 o amd64).
       - **Argomenti del driver di test**: Argomenti della riga di comando passati all'eseguibile della destinazione test. Si noti che il simbolo "% TestFile%", incluse le virgolette doppie, verrà automaticamente sostituito con il percorso completo del file di destinazione che il driver di test dovrebbe analizzare ed è obbligatorio.
       - Il **processo del driver di test viene terminato al completamento del test**: Selezionare questa opzione per terminare il driver di test al completamento; Deselezionare la casella di controllo se il driver di test deve essere chiuso forzatamente.
       - **Durata massima (in secondi)** : Fornire una stima del tempo ragionevolmente previsto per il programma di destinazione per l'analisi di un file di input. Più accurata è questa stima, più efficiente è l'esecuzione di fuzzing.
       - Il **driver di test può essere eseguito ripetutamente**: Controllare se il driver di test può essere eseguito ripetutamente senza dipendere da uno stato globale permanente/condiviso.
       - Il **driver di test può essere rinominato**: Controllare se è possibile rinominare il file eseguibile del driver di test e continuare a funzionare correttamente.
       - **L'applicazione di fuzzing viene eseguita come singolo processo del sistema operativo**: Controllare se il driver di test viene eseguito in un singolo processo del sistema operativo. Deselezionare l'opzione se il driver di test genera processi aggiuntivi.


## <a name="roslyn-analyzers-task"></a>Attività analizzatori Roslyn
> [!NOTE]
> Come prerequisito per l'esecuzione dell'attività di Roslyn Analyzer, la compilazione deve soddisfare le condizioni seguenti.
>  - La definizione di compilazione include l'attività di compilazione MSBuild o VSBuild incorporata per C# compilare il codice (o VB). Questa attività si basa sull'input e l'output dell'attività di compilazione specifica per eseguire nuovamente la compilazione di MSBuild con gli analizzatori Roslyn abilitati.
>  - Per l'agente di compilazione che esegue questa attività di compilazione è installato Visual Studio 2017 v 15.5 o versione successiva (compilatore versione 2.6. x).
>

Informazioni dettagliate sulla configurazione di seguito.

Le opzioni disponibili includono 
- **RuleSet** -SDL richiesto, SDL consigliato oppure è possibile usare un set di regole personalizzato personalizzato.
- **Versione** degli analizzatori Consigliabile Ultima
- **File di eliminazione avvisi del compilatore** : un file di testo con un elenco di ID avvisi che devono essere eliminati. 
- **Esegui questa attività** (in **Opzioni di controllo**): specifica quando eseguire l'attività. Scegliere "**condizioni personalizzate**" per specificare condizioni più complesse. 

> [!NOTE]
> - Gli analizzatori Roslyn sono integrati dal compilatore e possono essere eseguiti solo come parte della compilazione CSC. exe. Questa attività richiede pertanto la riproduzione o la riesecuzione del comando del compilatore eseguito in precedenza nella compilazione. Questa operazione viene eseguita eseguendo una query su VSTS per i log delle attività di compilazione MSBuild (non esiste un altro modo per consentire all'attività di ottenere in modo affidabile la riga di comando per la compilazione di MSBuild dalla definizione di compilazione. è stata valutata l'aggiunta di una casella di testo a mano libera per consentire agli utenti di immettere le , ma sarebbe difficile mantenerli aggiornati e sincronizzati con la build principale). Per le compilazioni personalizzate è necessario riprodurre l'intero set di comandi, non solo i comandi del compilatore, e non è semplice o affidabile per abilitare gli analizzatori Roslyn in questi casi. 
> - Gli analizzatori Roslyn sono integrati con il compilatore e richiedono la richiamata della compilazione. Questa attività di compilazione viene implementata ricompilando i C# progetti già compilati usando solo l'attività di compilazione MSBuild/VSBuild, nella stessa definizione di compilazione/compilazione, ma in questo caso con gli analizzatori abilitati. Se questa attività di compilazione viene eseguita nello stesso agente dell'attività di compilazione originale, l'output dell'attività di compilazione MSBuild/VSBuild originale verrà sovrascritto nella cartella origini di '', dall'output di questa attività di compilazione. L'output di compilazione sarà lo stesso, ma si consiglia di eseguire MSBuild, copiare l'output nella directory di staging degli artefatti e quindi eseguire Roslyn.
>

Per altre risorse per l'attività analizzatore Roslyn controlla gli [analizzatori Roslyn in Microsoft docs](https://docs.microsoft.com/dotnet/standard/analyzers/)

Il pacchetto dell'analizzatore installato e usato da questa attività di compilazione è disponibile [qui](https://www.nuget.org/packages/Microsoft.CodeAnalysis.FxCopAnalyzers) 

## <a name="tslint-task"></a>Attività TSLint

Per altre informazioni su TSLint, visitare il [repository GitHub TSLint](https://github.com/palantir/tslint)
>[!NOTE] 
>Come si può notare, TSLint verrà deprecato in 2019 (origine: [Repository GitHub TSLint](https://github.com/palantir/tslint) Il team sta attualmente esaminando [ESLint](https://github.com/eslint/eslint) come alternativa e la creazione di una nuova attività per ESLint è disponibile nella roadmap.

## <a name="publish-security-analysis-logs-task"></a>Attività pubblica log di analisi della sicurezza
Screenshot e informazioni dettagliate sulla configurazione di seguito.

![Personalizzazione dell'analisi della sicurezza di pubblicazione](./media/security-tools/9-publish-security-analsis-logs600.png)  

- **Nome artefatto** : può essere qualsiasi identificatore di stringa
- **Tipo** di artefatto: è possibile pubblicare i log nel server Azure-DevOps o in una condivisione file accessibile dall'agente di compilazione. 
- **Strumenti** : è possibile scegliere di mantenere i log per gli strumenti singoli o specifici o selezionare **tutti gli strumenti** per conservare tutti i log. 

## <a name="security-report-task"></a>Attività report di sicurezza
Screenshot e informazioni dettagliate sulla configurazione di seguito.  
![Post-analisi del programma di installazione](./media/security-tools/4-createsecurityanalysisreport600.png) 
- **Report** -scegliere i file di report da creare; ne verrà creato uno in ogni format **console**, **TSV**e/o **HTML** 
- **Strumenti** : selezionare gli strumenti nella definizione di compilazione per cui si desidera un riepilogo dei problemi rilevati. Per ogni strumento selezionato, può essere disponibile un'opzione per indicare se si desidera visualizzare solo gli errori o gli errori e gli avvisi nel report. 
- **Opzioni avanzate** : è possibile scegliere di registrare un avviso o un errore (e interrompere l'attività) nel caso in cui non esistano log per uno degli strumenti selezionati.
È possibile personalizzare la cartella dei log di base in cui si trovano i log, ma questo non è uno scenario tipico. 

## <a name="post-analysis-task"></a>Attività di post-analisi
Screenshot e informazioni dettagliate sulla configurazione di seguito.

![Personalizzazione della post-analisi](./media/security-tools/a-post-analysis600.png) 
- **Strumenti** : selezionare gli strumenti nella definizione di compilazione per cui si vuole inserire un'operazione di compilazione in base ai risultati. Per ogni strumento selezionato, può essere disponibile un'opzione che consente di scegliere se si desidera interrompere solo gli errori o gli errori e gli avvisi. 
- **Report** : è possibile scrivere facoltativamente i risultati trovati e causare l'interruzioni di compilazione nella finestra della console di Azure DevOps e nel file di log. 
- **Opzioni avanzate** : è possibile scegliere di registrare un avviso o un errore (e interrompere l'attività) nel caso in cui non esistano log per uno degli strumenti selezionati.

## <a name="next-steps"></a>Passaggi successivi

Per altre domande sull'estensione e sugli strumenti offerti, [vedere la pagina delle domande frequenti.](security-code-analysis-faq.md)


