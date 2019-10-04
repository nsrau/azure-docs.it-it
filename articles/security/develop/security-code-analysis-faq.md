---
title: Domande frequenti sulla documentazione di analisi del codice di sicurezza Microsoft
description: Questo articolo contiene le domande frequenti sull'estensione di analisi del codice di sicurezza Microsoft
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
ms.openlocfilehash: b28d02dd0ca375451f6ff75b1253ae8874bf2ab4
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828239"
---
# <a name="frequently-asked-questions"></a>Domande frequenti
Hai domande? Per ulteriori informazioni, vedere le domande frequenti seguenti.

## <a name="general-faq"></a>Domande frequenti generali

### <a name="can-i-install-the-extension-on-my-visual-studio-team-foundation-server-instance-instead-of-on-an-azure-devops-instance"></a>È possibile installare l'estensione nell'istanza di Visual Studio Team Foundation Server anziché in un'istanza di Azure DevOps?

No. L'estensione non è disponibile per il download e l'installazione di Visual Studio Team Foundation Server.

### <a name="do-i-have-to-run-microsoft-security-code-analysis-with-my-build"></a>È necessario eseguire l'analisi del codice di sicurezza Microsoft con la compilazione? 

È possibile. Dipende dal tipo di strumento di analisi. Il codice sorgente può essere l'unico elemento necessario o l'output di compilazione potrebbe essere necessario.

Ad esempio, Credential scanner (CredScan) analizza i file all'interno della struttura di cartelle del repository di codice. A causa di questa analisi, è possibile eseguire il CredScan e pubblicare le attività di compilazione dei log di analisi della sicurezza in una compilazione autonoma per ottenere risultati.

Per altri strumenti come BinSkim che analizzano gli artefatti di post-compilazione, è necessario innanzitutto compilare la compilazione.

### <a name="can-i-break-my-build-when-results-are-found"></a>È possibile interrompere la compilazione quando vengono trovati risultati?

Sì. È possibile introdurre un'interruzioni di compilazione quando uno strumento segnala un problema o un problema nel file di log. È sufficiente aggiungere l'attività di compilazione post-analisi, quindi selezionare la casella di controllo per qualsiasi strumento per il quale si desidera interrompere la compilazione.

Nell'interfaccia utente dell'attività post-analisi è possibile scegliere di interrompere la compilazione quando uno strumento segnala solo gli errori o gli errori e gli avvisi.

### <a name="how-do-the-command-line-arguments-in-azure-devops-differ-from-those-arguments-in-the-standalone-desktop-tools"></a>In che modo gli argomenti della riga di comando in Azure DevOps differiscono da quelli negli strumenti desktop autonomi? 

Nella maggior parte dei casi, le attività di compilazione di Azure DevOps sono wrapper diretti intorno agli argomenti della riga di comando degli strumenti di sicurezza. È possibile passare come argomenti a un'attività di compilazione qualsiasi elemento che si passa normalmente a uno strumento da riga di comando.

Differenze evidenti:

- Gli strumenti vengono eseguiti dalla cartella di origine dell'agente $ (Build. SourcesDirectory) o da% BUILD_SOURCESDIRECTORY%. Un esempio è C:\agent @ no__t-0work\1\s.
- I percorsi negli argomenti possono essere relativi alla radice della directory di origine elencata in precedenza. I percorsi possono anche essere assoluti. È possibile ottenere percorsi assoluti usando le variabili di compilazione di Azure DevOps o eseguendo un agente locale con i percorsi di distribuzione noti delle risorse locali.
- Gli strumenti forniscono automaticamente un percorso o una cartella di file di output. Se si fornisce un percorso di output per un'attività di compilazione, tale percorso viene sostituito con un percorso del percorso noto dei log nell'agente di compilazione
- Alcuni argomenti aggiuntivi della riga di comando sono stati modificati per alcuni strumenti. Un esempio è l'aggiunta o la rimozione di opzioni che assicurano che non venga avviata alcuna GUI.

### <a name="can-i-run-a-build-task-like-credential-scanner-across-multiple-repositories-in-an-azure-devops-build"></a>È possibile eseguire un'attività di compilazione, ad esempio Credential scanner, in più repository in una build di Azure DevOps?

No. L'esecuzione degli strumenti di sviluppo sicuri in più repository in un'unica pipeline non è supportata.

### <a name="the-output-file-i-specified-isnt-being-created-or-i-cant-find-the-output-file-i-specified"></a>Il file di output specificato non viene creato oppure non è possibile trovare il file di output specificato

Le attività di compilazione filtrano l'input dell'utente. Per questa domanda, il percorso del file di output generato viene aggiornato in modo da essere un percorso comune nell'agente di compilazione. Per ulteriori informazioni su questo percorso, vedere le domande seguenti.

### <a name="where-are-the-output-files-generated-by-the-tools-saved"></a>Dove vengono salvati i file di output generati dagli strumenti? 

Le attività di compilazione aggiungono automaticamente i percorsi di output a questo percorso noto nell'agente di compilazione: $ (Agent. BuildDirectory) \_sdt \ Logs. Poiché si standardizza in questa posizione, tutti i team che producono o utilizzano log di analisi del codice hanno accesso all'output.

### <a name="can-i-queue-a-build-to-run-these-tasks-on-a-hosted-build-agent"></a>È possibile accodare una compilazione per eseguire queste attività in un agente di compilazione ospitato? 

Sì. Tutte le attività e gli strumenti nell'estensione possono essere eseguiti in un agente di compilazione ospitato.

>[!NOTE]
> Per l'attività di compilazione dello scanner anti-malware è necessario un agente di compilazione con Windows Defender abilitato. Visual Studio 2017 ospitato e versioni successive forniscono un agente di questo tipo. L'attività di compilazione non verrà eseguita nell'agente di Visual Studio 2015 Hosted.
>
> Sebbene non sia possibile aggiornare le firme su questi agenti, le firme devono essere sempre minori di tre ore.

### <a name="can-i-run-these-build-tasks-as-part-of-a-release-pipeline-as-opposed-to-a-build-pipeline"></a>È possibile eseguire queste attività di compilazione come parte di una pipeline di rilascio anziché una pipeline di compilazione?

Nella maggior parte dei casi, sì.

Tuttavia, Azure DevOps non supporta l'esecuzione di attività nelle pipeline di rilascio quando tali attività pubblicano elementi. Questa mancanza di supporto impedisce che l'attività pubblica log di analisi della sicurezza venga eseguita correttamente in una pipeline di rilascio. L'attività ha invece esito negativo con un messaggio di errore descrittivo.

### <a name="from-where-do-the-build-tasks-download-the-tools"></a>Da dove le attività di compilazione scaricano gli strumenti?

Le attività di compilazione possono scaricare i pacchetti NuGet degli strumenti dal [feed di gestione pacchetti di Azure DevOps](https://securitytools.pkgs.visualstudio.com/_packaging/SecureDevelopmentTools/nuget/v3/index.json). Le attività di compilazione possono inoltre utilizzare node Package Manager, che deve essere preinstallato nell'agente di compilazione. Un esempio di questa installazione è il comando **NPM install tslint**.

### <a name="what-effect-does-installing-the-extension-have-on-my-azure-devops-organization"></a>Quale effetto ha l'installazione dell'estensione nell'organizzazione DevOps di Azure? 

Al momento dell'installazione, le attività di compilazione di sicurezza fornite dall'estensione diventano disponibili per tutti gli utenti dell'organizzazione. Quando si crea o si modifica una pipeline di Azure, queste attività sono disponibili nell'elenco di raccolta delle attività di compilazione. In caso contrario, l'installazione dell'estensione nell'organizzazione di Azure DevOps non ha alcun effetto. L'installazione non modifica le impostazioni dell'account, le impostazioni del progetto o le pipeline.

### <a name="does-installing-the-extension-modify-my-existing-azure-pipelines"></a>L'installazione dell'estensione comporta la modifica delle pipeline di Azure esistenti? 

No. L'installazione dell'estensione rende disponibili le attività di compilazione della sicurezza per l'aggiunta alle pipeline. È ancora necessario aggiungere o aggiornare le definizioni di compilazione, in modo che gli strumenti possano funzionare con il processo di compilazione.

## <a name="task-specific-faq"></a>Domande frequenti specifiche per le attività

In questa sezione sono elencate le domande specifiche per le attività di compilazione.

### <a name="credential-scanner"></a>Scanner delle credenziali

#### <a name="what-are-common-suppression-scenarios-and-examples"></a>Che cosa sono gli esempi e gli scenari di eliminazione comuni?

Ecco i dettagli di due degli scenari di eliminazione più comuni.

##### <a name="to-suppress-all-occurrences-of-a-given-secret-within-the-specified-path"></a>Per escludere tutte le occorrenze di un determinato segreto entro il percorso specificato

La chiave hash del segreto dal file di output CredScan è obbligatoria come illustrato nell'esempio seguente.

        {
            "tool": "Credential Scanner",
            "suppressions": [
            {
                "hash": "CLgYxl2FcQE8XZgha9/UbKLTkJkUh3Vakkxh2CAdhtY=",
                "_justification": "Secret used by MSDN sample, it is fake."
            }
          ]
        }

>[!WARNING]
> La chiave hash viene generata da una parte del valore corrispondente o del contenuto del file. Qualsiasi revisione del codice sorgente può modificare la chiave hash e disabilitare la regola di eliminazione.

##### <a name="to-suppress-all-secrets-in-a-specified-file-or-to-suppress-the-secrets-file-itself"></a>Per escludere tutti i segreti in un file specificato o per escludere il file Secrets stesso

L'espressione di file può essere un nome di file. Può anche essere la parte baseName di un percorso di file completo o un nome di file. I caratteri jolly non sono supportati.

Gli esempi seguenti illustrano come escludere il file \<InputPath > \src\JS\lib\angular.js

Esempi di regole di eliminazione valide:

- \<InputPath > \src\JS\lib\angular.js-disattiva il file nel percorso specificato
- \src\JS\lib\angular.js
- \JS\lib\angular.js
- \lib\angular.js
- angolare. js: Disattiva tutti i file con lo stesso nome

        {
            "tool": "Credential Scanner",
            "suppressions": [
            {
                "file": "\\files\\AdditonalSearcher.xml", 
                "_justification": "Additional CredScan searcher specific to my team"
            },
            {
                "file": "\\files\\unittest.pfx", 
                "_justification": "Legitimate UT certificate file with private key"
            }
          ]
        }      

>[!WARNING] 
> Tutti i segreti futuri aggiunti al file verranno anche eliminati automaticamente.

#### <a name="what-are-recommended-guidelines-for-managing-secrets"></a>Quali sono le linee guida consigliate per la gestione dei segreti?

Le risorse seguenti consentono di gestire in modo sicuro i segreti e accedere a informazioni sensibili dalle applicazioni:

 - [Insieme di credenziali chiave Azure](../../key-vault/index.yml)
 - [Azure Active Directory (Azure AD)](../../sql-database/sql-database-aad-authentication.md)
 - [Azure AD identità del servizio gestita (MSI)](https://azure.microsoft.com/blog/keep-credentials-out-of-code-introducing-azure-ad-managed-service-identity/)
 - [Identità gestite per le risorse di Azure](../../active-directory/managed-identities-azure-resources/overview.md)
 - [Identità gestite nel servizio app Azure e funzioni di Azure](../../app-service/overview-managed-identity.md)
 - [Libreria AppAuthentication](../../key-vault/service-to-service-authentication.md)


Per altre informazioni, vedere il post di Blog sulla [gestione dei segreti in modo sicuro nel cloud](https://devblogs.microsoft.com/visualstudio/managing-secrets-securely-in-the-cloud/).

#### <a name="can-i-write-my-own-custom-searchers"></a>È possibile scrivere ricerche personalizzate?

Credential scanner si basa su un set di ricerca di contenuto comunemente definito nel file buildsearchers. XML. Il file contiene una matrice di oggetti serializzati XML che rappresentano un oggetto **ContentSearcher** . Il programma viene distribuito con un set di ricerche ben collaudate. È anche possibile implementare ricerche personalizzate.

Un ricercatore di contenuti viene definito come segue:

- **Nome**: Nome descrittivo del ricercatore da usare nei file di output dello scanner delle credenziali. Si consiglia di usare la convenzione di denominazione dei casi di cammello per i nomi di ricerca.
- **RuleId**: ID opaco stabile del ricercatore:
    - A un ricercatore predefinito di Credential scanner viene assegnato un valore **RuleId** come CSCAN0010, CSCAN0020 o CSCAN0030. L'ultima cifra è riservata per potenzialmente unire o dividere i gruppi di ricerca tramite espressioni regolari (Regex).
    - Il valore **RuleId** per un ricercatore personalizzato deve avere il proprio spazio dei nomi. Gli esempi includono CSCAN-\<Namespace @ no__t-10010, CSCAN-\<Namespace @ no__t-30020 e CSCAN-\<Namespace @ no__t-50030.
    - Un nome di ricerca completo è la combinazione di un valore **RuleId** e di un nome di ricerca. Gli esempi includono CSCAN0010. KeyStoreFiles e CSCAN0020. Base64EncodedCertificate.
- **ResourceMatchPattern**: Espressione regolare delle estensioni di file da controllare rispetto al Cercatore.
- **ContentSearchPatterns**: Matrice di stringhe contenente le istruzioni Regex per le quali trovare una corrispondenza. Se non sono definiti criteri di ricerca, vengono restituiti tutti i file corrispondenti al valore **ResourceMatchPattern** .
- **ContentSearchFilters**: Matrice di stringhe contenente le istruzioni Regex per filtrare i falsi positivi specifici del Searcher.
- **MatchDetails**: Un messaggio descrittivo, istruzioni di mitigazione o entrambi da aggiungere per ogni corrispondenza del ricercatore.
- **Consiglio**: Il contenuto del campo Suggerimenti per una corrispondenza utilizzando il formato di report PREfast.
- **Gravità**: Intero che riflette il livello di gravità di un problema. Il livello di gravità più alto ha il valore 1.

  ![XML che mostra la configurazione dello scanner di credenziali](./media/security-tools/6-credscan-customsearchers.png)

### <a name="roslyn-analyzers"></a>Analizzatori Roslyn

#### <a name="what-are-common-errors-when-using-the-roslyn-analyzers-task"></a>Quali sono gli errori comuni quando si usa l'attività degli analizzatori Roslyn?

##### <a name="the-project-was-restored-using-a-wrong-microsoftnetcoreapp-version"></a>Il progetto è stato ripristinato con una versione di Microsoft. NETCore. app non corretta

Messaggio di errore completo:

"Error: Il progetto è stato ripristinato con Microsoft. NETCore. app versione *x*. x. x, ma con le impostazioni correnti, verrebbe invece usata la versione *y. y. y* . Per risolvere questo problema, assicurarsi che le stesse impostazioni vengano usate per il ripristino e per le operazioni successive, ad esempio la compilazione o la pubblicazione. Questo problema si verifica in genere se la proprietà RuntimeIdentifier viene impostata durante la compilazione o la pubblicazione ma non durante il ripristino.

Poiché le attività di analizzatore Roslyn vengono eseguite come parte della compilazione, l'albero di origine nel computer di compilazione deve essere in uno stato compilabile.

Un passaggio tra i passaggi principali per la compilazione e l'analizzatore Roslyn potrebbe aver inserito l'albero di origine in uno stato che impedisce la compilazione. Questo passaggio aggiuntivo è probabilmente **dotnet. exe Publish**. Provare a duplicare il passaggio che esegue il ripristino di NuGet immediatamente prima del passaggio degli analizzatori Roslyn. Questo passaggio duplicato potrebbe riportare l'albero di origine in uno stato compilabile.

##### <a name="cscexe-cant-create-an-analyzer-instance"></a>CSC. exe non è in grado di creare un'istanza dell'analizzatore

Messaggio di errore completo:

"' CSC. exe ' terminato con codice di errore 1. Impossibile creare un'istanza dell'analizzatore *aaaa* da C: \\*bbbb*. dll: Impossibile caricare il file o l'assembly ' Microsoft. CodeAnalysis, Version =*x.x.* x. x, Culture = neutral, PublicKeyToken = 31bf3856ad364e35' o una delle relative dipendenze. The system cannot find the file specified" (Valore 'cwd' '/src' non valido. Impossibile trovare il file specificato)

Verificare che il compilatore supporti gli analizzatori Roslyn. L'esecuzione del comando **csc. exe/version** dovrebbe indicare un valore di versione 2,6 o successiva.

A volte un file con estensione csproj può sostituire l'installazione di Visual Studio del computer di compilazione facendo riferimento a un pacchetto di Microsoft.Net. Compilers. Se non si prevede di usare una versione specifica del compilatore, rimuovere i riferimenti a Microsoft.Net. Compilers. In caso contrario, verificare che anche la versione del pacchetto a cui si fa riferimento sia 2,6 o successiva.

Provare a ottenere il percorso del log degli errori, specificato nell'opzione **/ErrorLog di CSC. exe** . L'opzione e il percorso vengono visualizzati nel log per l'attività di compilazione analizzatori Roslyn. Potrebbero avere un aspetto simile a **/ErrorLog: F:\ts-Services-123 @ no__t-1work\456\s\Some\Project\Code\Code.csproj.Sarif**

##### <a name="the-c-compiler-version-isnt-recent-enough"></a>La C# versione del compilatore non è abbastanza recente

Per ottenere le versioni più recenti del C# compilatore, passare a [Microsoft.NET. Compilers](https://www.nuget.org/packages/Microsoft.Net.Compilers). Per ottenere la versione installata, eseguire **csc. exe/version** al prompt dei comandi. Assicurarsi di fare riferimento a un pacchetto NuGet Microsoft.Net. Compilers versione 2,6 o successiva.

##### <a name="msbuild-and-vsbuild-logs-arent-found"></a>I registri MSBuild e VSBuild non sono stati trovati

L'attività di compilazione degli analizzatori Roslyn deve eseguire una query su Azure DevOps per il log MSBuild dall'attività di compilazione di MSBuild. Se l'attività analizzatore viene eseguita immediatamente dopo l'attività MSBuild, il log non sarà ancora disponibile. Inserire altre attività tra l'attività MSBuild e l'attività analizzatori Roslyn. Esempi di altre attività includono BinSkim e anti-malware scanner.

## <a name="next-steps"></a>Passaggi successivi

Se è necessaria assistenza aggiuntiva, il supporto per l'analisi del codice di sicurezza Microsoft è disponibile dal lunedì al venerdì dalle 9:00 alle 5:00 ora solare Pacifico.

  - Onboarding: Per iniziare, contattare i responsabili dell'account tecnico.
  
  - Supporto Inviare un messaggio di posta elettronica al [supporto Microsoft Security Analysis code](mailto:mscahelp@microsoft.com?Subject=Microsoft%20Security%20Code%20Analysis%20Support%20Request).

  >[!NOTE] 
  >È possibile che non si disponga di una relazione di supporto a pagamento con Microsoft. In alternativa, è possibile che si disponga di un'offerta di supporto che impedisce di acquistare servizi dal catalogo Phoenix. Se una di queste condizioni è vera, visitare i [servizi di supporto Home page](https://www.microsoft.com/enterprise/services/support) per altre informazioni.
