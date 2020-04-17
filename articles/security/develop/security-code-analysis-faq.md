---
title: Domande frequenti sulla documentazione relativa all'analisi dei codici di sicurezza MicrosoftMicrosoft Security Code Analysis documentation FAQ
description: Questo articolo contiene una serie di domande frequenti sull'estensione Microsoft Security Code Analysis
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
ms.openlocfilehash: cb04a8e5a6d8c982a35cb5c448e4b6d93825bf73
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460223"
---
# <a name="frequently-asked-questions"></a>Domande frequenti
Domande? Consulta le seguenti FAQ per ulteriori informazioni.

## <a name="general-faq"></a>Domande frequenti generali

### <a name="can-i-install-the-extension-on-my-visual-studio-team-foundation-server-instance-instead-of-on-an-azure-devops-instance"></a>È possibile installare l'estensione nell'istanza di Visual Studio Team Foundation Server anziché in un'istanza di DevOps di Azure?

No. L'estensione non è disponibile per il download e l'installazione per Visual Studio Team Foundation Server.

### <a name="do-i-have-to-run-microsoft-security-code-analysis-with-my-build"></a>È necessario eseguire Microsoft Security Code Analysis con la compilazione? 

È possibile. Dipende dal tipo di strumento di analisi. Il codice sorgente potrebbe essere l'unica cosa che è necessaria, o l'output di compilazione potrebbe essere necessario.

Ad esempio, Credential Scanner (CredScan) analizza i file all'interno della struttura di cartelle del repository di codice. Grazie a questa analisi, è possibile eseguire le attività di compilazione CredScan e Publish Security Analysis Logs in una compilazione autonoma per ottenere risultati.

Per altri strumenti come BinSkim che analizzano gli elementi post-compilazione, la compilazione è necessaria per prima.

### <a name="can-i-break-my-build-when-results-are-found"></a>Posso interrompere la mia build quando vengono trovati i risultati?

Sì. È possibile introdurre un'interruzione di compilazione quando uno strumento segnala un problema o un problema nel relativo file di log. È sufficiente aggiungere l'attività di compilazione post-analisi e selezionare la casella di controllo per qualsiasi strumento per il quale si desidera interrompere la compilazione.

Nell'interfaccia utente dell'attività post-analisi, è possibile scegliere di interrompere la compilazione quando uno strumento segnala solo gli errori o entrambi gli errori e gli avvisi.

### <a name="how-do-the-command-line-arguments-in-azure-devops-differ-from-those-arguments-in-the-standalone-desktop-tools"></a>In che modo gli argomenti della riga di comando in Azure DevOps differiscono da quelli negli strumenti desktop autonomi? 

Per la maggior parte, le attività di compilazione DevOps di Azure sono wrapper diretti intorno agli argomenti della riga di comando degli strumenti di sicurezza. È possibile passare come argomenti a un'attività di compilazione qualsiasi elemento che viene normalmente passato a uno strumento della riga di comando.

Differenze notevoli:

- Gli strumenti vengono eseguiti dalla cartella di origine dell'agente (Build.SourcesDirectory) o da %BUILD_SOURCESDIRECTORY%. Un esempio è C:.\_
- I percorsi negli argomenti possono essere relativi alla radice della directory di origine elencata in precedenza. Anche i percorsi possono essere assoluti. È possibile ottenere percorsi assoluti usando le variabili di compilazione DevOps di Azure o eseguendo un agente locale con percorsi di distribuzione noti delle risorse locali.
- Gli strumenti forniscono automaticamente un percorso o una cartella del file di output. Se si specifica un percorso di output per un'attività di compilazione, tale percorso viene sostituito con un percorso al percorso noto dei log nell'agente di compilazione
- Alcuni argomenti della riga di comando aggiuntivi vengono modificati per alcuni strumenti. Un esempio è l'aggiunta o la rimozione di opzioni che garantiscono che non venga avviata alcuna GUI.

### <a name="can-i-run-a-build-task-like-credential-scanner-across-multiple-repositories-in-an-azure-devops-build"></a>È possibile eseguire un'attività di compilazione come Credential Scanner in più repository in una compilazione DevOps di Azure?

No. L'esecuzione degli strumenti di sviluppo sicuro in più repository in una singola pipeline non è supportata.

### <a name="the-output-file-i-specified-isnt-being-created-or-i-cant-find-the-output-file-i-specified"></a>Il file di output specificato non è stato creato o non riesco a trovare il file di output specificato

Le attività di compilazione filtrano l'input dell'utente. In questa domanda in particolare, aggiornano il percorso del file di output generato in modo che sia un percorso comune nell'agente di compilazione. Per ulteriori informazioni su questa posizione, vedere le domande seguenti.

### <a name="where-are-the-output-files-generated-by-the-tools-saved"></a>Dove vengono salvati i file di output generati dagli strumenti? 

Le attività di compilazione aggiungono automaticamente i percorsi di output a questo\_percorso noto nell'agente di compilazione: (Agent.BuildDirectory) sdt-logs. Poiché si standardizza questa posizione, tutti i team che producono o utilizzano i log di analisi del codice hanno accesso all'output.

### <a name="can-i-queue-a-build-to-run-these-tasks-on-a-hosted-build-agent"></a>È possibile accodare una compilazione per eseguire queste attività in un agente di compilazione ospitato? 

Sì. Tutte le attività e gli strumenti nell'estensione possono essere eseguiti in un agente di compilazione ospitato.

>[!NOTE]
> L'attività di compilazione Anti-Malware Scanner richiede un agente di compilazione con Windows Defender abilitato. Ospitato Visual Studio 2017 e versioni successive forniscono tale agente. L'attività di compilazione non verrà eseguita nell'agente ospitato di Visual Studio 2015.
>
> Anche se le firme non possono essere aggiornate su questi agenti, le firme devono sempre essere meno di tre ore.

### <a name="can-i-run-these-build-tasks-as-part-of-a-release-pipeline-as-opposed-to-a-build-pipeline"></a>È possibile eseguire queste attività di compilazione come parte di una pipeline di rilascio anziché una pipeline di compilazione?

Nella maggior parte dei casi, sì.

Tuttavia, Azure DevOps non supporta l'esecuzione di attività all'interno di pipeline di rilascio quando tali attività pubblicano elementi. Questa mancanza di supporto impedisce la corretta esecuzione dell'attività Pubblica registri analisi sicurezza in una pipeline di rilascio. L'attività ha invece esito negativo con un messaggio di errore descrittivo.

### <a name="from-where-do-the-build-tasks-download-the-tools"></a>Da dove le attività di compilazione scaricano gli strumenti?

Le attività di compilazione possono scaricare i pacchetti NuGet degli strumenti dal feed Gestione pacchetti DevOps di Azure.Build tasks can download the tools' NuGet packages from the [Azure DevOps Package Management feed](https://securitytools.pkgs.visualstudio.com/_packaging/SecureDevelopmentTools/nuget/v3/index.json). Le attività di compilazione possono anche usare Gestione pacchetti di nodi, che deve essere preinstallato nell'agente di compilazione. Un esempio di tale installazione è il comando **npm install tslint**.

### <a name="what-effect-does-installing-the-extension-have-on-my-azure-devops-organization"></a>Che effetto ha l'installazione dell'estensione sull'organizzazione DevOps di Azure? 

Al momento dell'installazione, le attività di compilazione di sicurezza fornite dall'estensione diventano disponibili per tutti gli utenti dell'organizzazione. Quando si crea o si modifica una pipeline di Azure, queste attività sono disponibili nell'elenco di raccolta di attività di compilazione. In caso contrario, l'installazione dell'estensione nell'organizzazione DevOps di Azure non ha alcun effetto. L'installazione non modifica le impostazioni dell'account, le impostazioni di progetto o le pipeline.

### <a name="does-installing-the-extension-modify-my-existing-azure-pipelines"></a>L'installazione dell'estensione modifica le pipeline di Azure esistenti? 

No. L'installazione dell'estensione rende disponibili le attività di compilazione di sicurezza per l'aggiunta alle pipeline. È comunque necessario aggiungere o aggiornare le definizioni di compilazione, in modo che gli strumenti possano funzionare con il processo di compilazione.

## <a name="task-specific-faq"></a>Domande frequenti specifiche sulle attività

Le domande specifiche per le attività di compilazione sono elencate in questa sezione.

### <a name="credential-scanner"></a>Scanner credenziali

#### <a name="what-are-common-suppression-scenarios-and-examples"></a>Quali sono gli esempi e gli scenari di soppressione comuni?

Di seguito sono riportati i dettagli di due degli scenari di eliminazione più comuni.

##### <a name="to-suppress-all-occurrences-of-a-given-secret-within-the-specified-path"></a>Per sopprimere tutte le occorrenze di un determinato segreto all'interno del percorso specificato

La chiave hash del segreto del file di output CredScan è obbligatoria, come illustrato nell'esempio seguente.

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
> La chiave hash viene generata da una parte del valore o del contenuto del file corrispondente. Qualsiasi revisione del codice sorgente può modificare la chiave hash e disabilitare la regola di eliminazione.

##### <a name="to-suppress-all-secrets-in-a-specified-file-or-to-suppress-the-secrets-file-itself"></a>Per sopprimere tutti i segreti in un file specificato o per sopprimere il file dei segreti stesso

L'espressione di file può essere un nome di file. Può anche essere la parte basename di un percorso di file completo o un nome di file. I caratteri jolly non sono supportati.

Negli esempi seguenti viene illustrato \<come eliminare il file InputPath>

Esempi di regole di eliminazione valide:

- \<InputPath>'src'JS'lib'angular.js - elimina il file nel percorso specificato
- file JS
- file con estensione js
- file con estensione lib.angular.js
- angular.js - elimina qualsiasi file con lo stesso nome

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
> Tutti i segreti futuri aggiunti al file verranno eliminati automaticamente.

#### <a name="what-are-recommended-guidelines-for-managing-secrets"></a>Quali sono le linee guida consigliate per la gestione dei segreti?

Le risorse seguenti consentono di gestire in modo sicuro i segreti e di accedere a informazioni riservate dall'interno delle applicazioni:The following resources help you securely manage secrets and access sensitive information from within your applications:

 - [Insieme di credenziali chiave di Azure](../../key-vault/index.yml)
 - [Azure Active Directory (Azure AD)](../../sql-database/sql-database-aad-authentication.md)
 - [Identità del servizio gestito di Azure AD (MSI)Azure AD Managed Service Identity (MSI)](https://azure.microsoft.com/blog/keep-credentials-out-of-code-introducing-azure-ad-managed-service-identity/)
 - [Identità gestite per le risorse di Azure](../../active-directory/managed-identities-azure-resources/overview.md)
 - [Identità gestite nel servizio app di Azure e nelle funzioni di AzureManaged identities in Azure App Service and Azure Functions](../../app-service/overview-managed-identity.md)
 - [Libreria AppAuthentication](../../key-vault/general/service-to-service-authentication.md)


Per ulteriori informazioni, vedere il post di blog [Gestione sicuramente dei segreti nel cloud](https://devblogs.microsoft.com/visualstudio/managing-secrets-securely-in-the-cloud/).

#### <a name="can-i-write-my-own-custom-searchers"></a>Posso scrivere i miei ricercatori personalizzati?

Credential Scanner si basa su un set di ricerca di contenuto comunemente definiti nel file buildsearchers.xml. Il file contiene una matrice di oggetti serializzati XML che rappresentano un **ContentSearcher** oggetto. Il programma è distribuito con una serie di ricercatori ben collaudati. Ma è possibile implementare i propri ricercatori personalizzati troppo.

Un ricercatore di contenuto è definito come segue:A content searcher is defined as follows:

- **Nome**: Il nome descrittivo della ricerca da utilizzare nei file di output di Credential Scanner. Si consiglia di utilizzare la convenzione di denominazione dei maiuscole/minuscole dei cammelli per i nomi degli utenti.
- **RuleId**: L'ID opaco stabile del ricercatore:
    - A un ricercatore predefinito di Credential Scanner viene assegnato un valore **RuleId** come CSCAN0010, CSCAN0020 o CSCAN0030. L'ultima cifra è riservata all'unione o alla divisione di gruppi di ricerca tramite espressioni regolari (regex).
    - Il valore **RuleId** per un ricercatore personalizzato deve avere il proprio spazio dei nomi. Gli esempi includono\<\>CSCAN- Namespace\<0010, CSCAN- Namespace\>\<0020 e CSCAN- Namespace\>0030.
    - Un nome di ricerca completo è la combinazione di un valore **RuleId** e un nome di ricerca. Gli esempi includono CSCAN0010. KeyStoreFiles e CSCAN0020. Base64EncodedCertificate.
- **ResourceMatchPattern:** regex delle estensioni di file da confrontare con il ricercatore.
- **ContentSearchPatterns**: matrice di stringhe contenenti istruzioni regex da confrontare. Se non sono definiti criteri di ricerca, vengono restituiti tutti i file che corrispondono al valore **ResourceMatchPattern.If** no search patterns are defined, all files matching the ResourceMatchPattern value are returned.
- **ContentSearchFilters**: matrice di stringhe contenenti istruzioni regex per filtrare i falsi positivi specifici della ricerca.
- **MatchDetails**: Un messaggio descrittivo, istruzioni di attenuazione o entrambi da aggiungere per ogni corrispondenza del ricercatore.
- **Raccomandazione:** il contenuto del campo suggerimenti per una corrispondenza utilizzando il formato di report PREfast.
- **Gravità**: Un numero intero che riflette il livello di gravità di un problema. Il livello di gravità più alto ha il valore 1.The highest severity level has the value 1.

  ![XML che mostra la configurazione di Credential Scanner](./media/security-tools/6-credscan-customsearchers.png)

### <a name="roslyn-analyzers"></a>Analizzatori di Roslyn

#### <a name="what-are-common-errors-when-using-the-roslyn-analyzers-task"></a>Quali sono gli errori comuni quando si utilizza l'attività Analizzatori Roslyn?

##### <a name="the-project-was-restored-using-a-wrong-microsoftnetcoreapp-version"></a>Il progetto è stato ripristinato utilizzando una versione errata di Microsoft.NETCore.App

Il messaggio di errore completo:

"Errore: il progetto è stato ripristinato utilizzando Microsoft.NETCore.App versione *x.x.x*, ma con le impostazioni correnti, verrà utilizzata la versione *y.y.y.* To resolve this issue, make sure the same settings are used for restore and for subsequent operations such as build or publish. Typically this issue can occur if the RuntimeIdentifier property is set during build or publish but not during restore" (Il progetto è stato ripristinato tramite Microsoft.NETCore.App versione 2.0.0, ma con le impostazioni correnti avrebbe dovuto essere usata la versione 2.0.6. Per risolvere il problema, assicurarsi che vengano usate le stesse impostazioni per il comando restore e per operazioni successive, quali build o publish. Questo problema si presenta, in genere, se la proprietà RuntimeIdentifier viene impostata durante l'operazione build o publish ma non durante restore).

Poiché le attività di Analizzatori Roslyn vengono eseguite come parte della compilazione, l'albero di origine nel computer di compilazione deve essere in uno stato compilabile.

Un passaggio tra la compilazione principale e i passaggi di Analizzatori Roslyn potrebbe aver messo l'albero di origine in uno stato che impedisce la compilazione. Questo passaggio aggiuntivo è probabilmente **dotnet.exe publish**. Provare a duplicare il passaggio che esegue un ripristino NuGet appena prima del passaggio di Analizzatori Roslyn. Questo passaggio duplicato potrebbe riportare l'albero di origine in uno stato compilabile.

##### <a name="cscexe-cant-create-an-analyzer-instance"></a>csc.exe non è in grado di creare un'istanza dell'analizzatore

Il messaggio di errore completo:

"'csc.exe' è terminato con codice di errore 1 -- Impossibile\\creare un'istanza dell'analizzatore *AAAA* da C:*BBBB*.dll: Impossibile caricare il file o l'assembly 'Microsoft.CodeAnalysis, Version'X.X.X.X , Culture 'neutral, PublicKeyToken'31bf3856ad364e35' o una delle relative dipendenze.*X.X.X.X* The system cannot find the file specified" (Valore 'cwd' '/src' non valido. Impossibile trovare il file specificato)

Verificare che il compilatore supporti gli analizzatori di Roslyn. L'esecuzione del comando **csc.exe /version** deve riportare un valore di versione pari a 2.6 o versione successiva.

In alcuni passaggi, un file con estensione csproj può eseguire l'override dell'installazione di Visual Studio del computer di compilazione facendo riferimento a un pacchetto da Microsoft.Net.Compilers. Se non si intende utilizzare una versione specifica del compilatore, rimuovere i riferimenti a Microsoft.Net.Compilers. In caso contrario, assicurarsi che anche la versione del pacchetto di riferimento sia 2.6 o successiva.

Provare a ottenere il percorso del log degli errori, specificato nell'opzione **csc.exe /errorlog.** L'opzione e il percorso vengono visualizzati nel log per l'attività di compilazione Analizzatori Roslyn. Potrebbero essere simili **a /errorlog:F:\_**

##### <a name="the-c-compiler-version-isnt-recent-enough"></a>La versione del compilatore di C'è abbastanza recente

Per ottenere le versioni più recenti del compilatore C, passare a [Microsoft.Net.Compilers](https://www.nuget.org/packages/Microsoft.Net.Compilers). Per ottenere la versione installata, eseguire **csc.exe /version** al prompt dei comandi. Assicurarsi di fare riferimento a un pacchetto Microsoft.Net.Compilers NuGet versione 2.6 o successiva.

##### <a name="msbuild-and-vsbuild-logs-arent-found"></a>I log msBuild e VSBuild non vengono trovati

L'attività di compilazione di Roslyn Analyzers deve eseguire una query su Azure DevOps per il log MSBuild dall'attività di compilazione MSBuild.The Roslyn Analyzers build task needs to query Azure DevOps for the MSBuild log from the MSBuild build task. Se l'attività analizzatore viene eseguita immediatamente dopo l'attività MSBuild, il log non sarà ancora disponibile. Inserire altre attività tra l'attività MSBuild e l'attività Roslyn Analyzers. Esempi di altre attività includono BinSkim e Anti-Malware Scanner.

## <a name="next-steps"></a>Passaggi successivi

Se hai bisogno di ulteriore assistenza, il supporto per l'analisi dei codici di sicurezza Microsoft è disponibile dal lunedì al venerdì dalle 9:00 alle 17:00 (ora solare Pacifico).

- Onboarding: consulta la nostra [documentazione sull'onboarding](security-code-analysis-onboard.md)
  
- Supporto: invia un'e-mail al nostro team al [supporto per l'analisi dei codici](mailto:mscahelp@microsoft.com?Subject=Microsoft%20Security%20Code%20Analysis%20Support%20Request) di sicurezza Microsoft