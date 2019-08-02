---
title: Domande frequenti sulla documentazione di analisi del codice di sicurezza Microsoft Azure
description: Questo articolo contiene domande frequenti sull'estensione di analisi del codice di sicurezza
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
ms.openlocfilehash: 1fc5e83c2c46a7da2a4b56879a2d596405fd8bee
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68718314"
---
# <a name="frequently-asked-questions"></a>Domande frequenti
Hai domande? Per ulteriori informazioni, consultare le domande frequenti riportate di seguito.

## <a name="general-faqs"></a>Domande frequenti generali

### <a name="can-i-install-the-extension-on-my-tfs-not-azure-devops-server"></a>È possibile installare l'estensione nel server TFS (non Azure DevOps)? 

No, l'estensione non è disponibile per il download e l'installazione per TFS.

### <a name="do-i-have-to-run-microsoft-security-code-analysis-with-my-build"></a>È necessario eseguire l'analisi del codice di sicurezza Microsoft con la compilazione? 

Sì e no. A seconda del tipo di strumento di analisi, il codice sorgente può essere l'unico elemento necessario o l'output della compilazione potrebbe essere necessario. Ad esempio, poiché Credential scanner analizza i file all'interno della struttura di cartelle del repository di codice, è possibile eseguire lo scanner delle credenziali e pubblicare le attività di compilazione dei log di analisi della sicurezza in una compilazione autonoma per recuperare i risultati.
Per gli altri strumenti che analizzano gli elementi di post-compilazione, ad esempio BinSkim, la compilazione sarà obbligatoria per prima.

### <a name="can-i-break-my-build-when-results-are-found"></a>È possibile interrompere la compilazione quando vengono trovati risultati? 
Sì, è possibile introdurre un'interruzioni di compilazione quando uno strumento segnala un problema, una ricerca nel file di log. È sufficiente aggiungere l'attività di compilazione post-analisi e selezionare la casella di controllo per tutti gli strumenti per i quali si desidera interrompere la compilazione. È possibile scegliere di interrompere la compilazione quando uno strumento segnala errori o avvisi ed errori nell'interfaccia utente dell'attività post-analisi.

### <a name="how-are-the-command-line-arguments-different-in-azure-devops-than-they-are-in-the-standalone-desktop-tools"></a>In che modo gli argomenti della riga di comando sono diversi in Azure DevOps rispetto a quelli inclusi negli strumenti desktop autonomi? 

Nella maggior parte dei casi, le attività di compilazione di Azure DevOps sono wrapper diretti intorno agli argomenti della riga di comando degli strumenti di sicurezza. Qualsiasi elemento normalmente passa allo strumento dalla riga di comando dal desktop, è possibile passare all'input degli argomenti dell'attività di compilazione.
Di seguito è riportato un elenco di differenze evidenti:
 - Lo strumento verrà eseguito dalla cartella di origine dell'agente $ (Build. SourcesDirectory) o% BUILD_SOURCESDIRECTORY%. Esempio: Work\1\s\_C:\agent 
 - I percorsi negli argomenti possono essere relativi alla radice della directory di origine elencata sopra o Absolute eseguendo un agente locale con i percorsi di distribuzione noti delle risorse locali o usando le variabili di compilazione di Azure DevOps
 - Gli strumenti forniranno automaticamente un percorso o una cartella di file di output se viene specificato un percorso di output, che verrà rimosso e sostituito con un percorso del percorso dei log noti nell'agente di compilazione
 - Alcuni parametri della riga di comando aggiuntivi vengono purificati e rimossi in alcuni strumenti, ad esempio l'aggiunta o la rimozione di opzioni per assicurarsi che non venga avviata alcuna GUI.

### <a name="can-i-run-a-build-task-for-example-credential-scanner-across-multiple-repositories-in-an-azure-devops-build"></a>È possibile eseguire un'attività di compilazione (ad esempio, Credential scanner) in più repository in una build di Azure DevOps? 

No, non è attualmente supportata l'esecuzione di strumenti di sviluppo protetti su più repository in una singola pipeline.

###  <a name="the-output-file-i-specified-is-not-being-created--i-cant-find-the-output-file-i-specified"></a>Il file di output specificato non viene creato/non è possibile trovare il file di output specificato 

Le attività di compilazione attualmente desterilizzano l'input dell'utente e aggiornano il percorso del file di output generato in un percorso comune nell'agente di compilazione. Per ulteriori informazioni su questo percorso, vedere le domande seguenti.

### <a name="where-are-the-output-files-generated-by-the-tools-saved"></a>Dove vengono salvati i file di output generati dagli strumenti? 

Le attività di compilazione aggiungono automaticamente i percorsi di output al percorso noto seguente nell'agente di compilazione $ (Agent. BuildDirectory\_) sdt\logs. Grazie alla standardizzazione in questa posizione, è possibile garantire l'accesso ad altri team che producono log di analisi del codice o che li utilizzano.

### <a name="can-i-queue-a-build-to-run-these-tasks-on-a-hosted-build-agent"></a>È possibile accodare una compilazione per eseguire queste attività in un agente di compilazione ospitato? 

Sì, tutte le attività e gli strumenti nell'estensione possono essere eseguiti in un agente di compilazione ospitato.

>[!NOTE]
> Per l'attività di compilazione anti-malware è necessario un agente di compilazione con Windows Defender abilitato, che è true negli agenti di compilazione "Hosted VS2017" o versioni successive. Non verrà eseguito nell'agente "Hosted" Legacy/VS2015. Non è possibile aggiornare le firme su questi agenti, ma la firma deve essere sempre relativamente aggiornata, minore di 3 ore.
>

### <a name="can-i-run-these-build-tasks-as-part-of-a-release-pipeline-as-opposed-to-a-build-pipeline"></a>È possibile eseguire queste attività di compilazione come parte di una pipeline di rilascio (in contrapposizione a una pipeline di compilazione)? 
Nella maggior parte dei casi, sì. Tuttavia, le attività che pubblicano gli artefatti non sono supportate da Azure DevOps per l'esecuzione dall'interno delle pipeline di versione: "L'unica categoria di attività che non prevede l'utilizzo della versione sono quelle che pubblicano gli artefatti. Questo perché, al momento, non è disponibile il supporto per la pubblicazione di artefatti all'interno della versione ".
Ciò impedisce l'esecuzione corretta dell'attività "pubblicazione dei log di analisi della sicurezza" da una pipeline di rilascio. non avrà esito positivo, con un messaggio di errore descrittivo.

### <a name="from-where-do-the-build-tasks-download-the-tools"></a>Da dove le attività di compilazione scaricano gli strumenti? 
Le attività di compilazione a) scaricano i pacchetti NuGet per gli strumenti dal [feed di gestione pacchetti di Azure DevOps](https://securitytools.pkgs.visualstudio.com/_packaging/SecureDevelopmentTools/nuget/v3/index.json) seguente o usando node Package Manager, che deve essere preinstallato nell'agente di compilazione (ad esempio: "NPM install tslint").

### <a name="what-effect-will-installing-the-extension-have-on-my-azure-devops-organization"></a>Quale effetto avrà l'installazione dell'estensione nell'organizzazione DevOps di Azure? 

Al momento dell'installazione, le attività di compilazione della sicurezza fornite dall'estensione diventeranno disponibili per l'uso da parte di tutti gli utenti dell'organizzazione. Quando si crea o si modifica una pipeline di Azure, queste attività saranno disponibili per l'aggiunta dall'elenco di raccolta delle attività di compilazione. In caso contrario, l'installazione dell'estensione nell'organizzazione di Azure DevOps non ha alcun effetto. Non modifica le pipeline o le impostazioni di un account o di progetto.

### <a name="will-installing-the-extension-modify-my-existing-azure-pipelines"></a>Si installerà l'estensione per modificare la Azure Pipelines esistente? 

No. L'installazione dell'estensione renderà disponibili le attività di compilazione di sicurezza da aggiungere all'Azure Pipelines. Gli utenti devono ancora aggiungere o aggiornare le definizioni di compilazione per integrare gli strumenti nel processo di compilazione.

## <a name="task-specific-faqs"></a>Domande frequenti specifiche per le attività

In questa sezione vengono elencate le domande frequenti specifiche per le attività di compilazione.

### <a name="credential-scanner-faqs"></a>Domande frequenti su Credential scanner

#### <a name="what-are-common-suppressions-scenarios-and-examples"></a>Che cosa sono gli scenari e gli esempi di eliminazione comuni? 
Di seguito sono descritti due degli scenari di eliminazione più comuni:
##### <a name="suppress-all-occurrences-of-a-given-secret-within-the-specified-path"></a>Disattiva tutte le occorrenze di un determinato segreto nel percorso specificato 
La chiave hash del segreto dal file di output dello scanner delle credenziali è obbligatoria come illustrato nell'esempio seguente
   
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
> La chiave hash viene generata da una parte del valore corrispondente o del contenuto del file. Qualsiasi revisione del codice sorgente potrebbe modificare la chiave hash e disabilitare la regola di eliminazione. 

##### <a name="to-suppress-all-secrets-in-a-specified-file-or-to-suppress-the-secrets-file-itself"></a>Per escludere tutti i segreti in un file specificato (o per non visualizzare il file Secrets) 
L'espressione di file può essere un nome file o una parte suffissa del nome/percorso file completo. I caratteri jolly non sono supportati. 

**Esempio** 

File da visualizzare: [InputPath] \src\JS\lib\angular.js 

Regole di eliminazione valide: 
- [InputPath] \src\JS\lib\angular.js--disattiva il file nel percorso specificato
- \src\JS\lib\angular.js
- \JS\lib\angular.js
- \lib\angular.js
- angolare. js: consente di escludere qualsiasi file con lo stesso nome

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
> Anche tutti i segreti futuri aggiunti al file verranno eliminati automaticamente. 

#### <a name="what-are-recommended-secrets-management-guidelines"></a>Informazioni sulle linee guida per la gestione dei segreti 
Quando si rilevano i segreti hardcoded in modo tempestivo e si mitigano i rischi, è ancora meglio se è possibile impedire che i segreti vengano archiviati completamente. A questo proposito, Microsoft ha rilasciato CredScan Code Analyzer come parte dell' [estensione Microsoft DevLabs](https://marketplace.visualstudio.com/items?itemName=VSIDEDevOpsMSFT.ContinuousDeliveryToolsforVisualStudio) per Visual Studio. Durante la fase di anteprima, fornisce agli sviluppatori un'esperienza inline per il rilevamento di potenziali segreti nel codice, offrendo loro la possibilità di risolvere questi problemi in tempo reale. Per ulteriori informazioni, fare riferimento a [questo](https://devblogs.microsoft.com/visualstudio/managing-secrets-securely-in-the-cloud/) Blog sulla gestione dei segreti in modo sicuro nel cloud. Di seguito sono riportate alcune risorse aggiuntive che consentono di gestire i segreti e di accedere a informazioni riservate dall'interno delle applicazioni in modo sicuro: 
 - [Insieme di credenziali chiave Azure](https://docs.microsoft.com/azure/key-vault/)
 - [Azure Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)
 - [Azure AD identità del servizio gestita](https://azure.microsoft.com/blog/keep-credentials-out-of-code-introducing-azure-ad-managed-service-identity/)
 - [Identità del servizio gestita per le risorse di Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)
 - [identità del servizio gestita di Azure](https://docs.microsoft.com/azure/app-service/overview-managed-identity)
 - [Libreria AppAuthentication](https://docs.microsoft.com/azure/key-vault/service-to-service-authentication)

#### <a name="can-i-write-my-own-custom-searchers"></a>È possibile scrivere ricerche personalizzate?

Credential scanner si basa su un set di ricerche di contenuto comunemente definite nel file **buildsearchers. XML** . Il file contiene una matrice di oggetti serializzati XML che rappresentano un oggetto ContentSearcher. Il programma viene distribuito con un set di ricerca che è stato testato correttamente, ma consente anche di implementare ricerche personalizzate. 

Un ricercatore di contenuti viene definito come segue: 

- **Nome** : il nome descrittivo del ricercatore da usare nel file di output dello scanner delle credenziali. È consigliabile usare la convenzione di denominazione Camel case per i nomi di ricerca. 
- **RuleId** : ID opaco stabile del ricercatore. 
    - I cercatori predefiniti dello scanner di credenziali vengono assegnati con RuleIds, ad esempio CSCAN0010, CSCAN0020, CSCAN0030 e così via. L'ultima cifra è riservata per l'Unione o la divisione del gruppo di espressioni regolari di ricerca.
    - RuleId per i cercatori personalizzati deve avere il proprio spazio dei nomi nel formato: CSCAN-{namespace} 0010, CSCAN-{namespace} 0020, CSCAN-{namespace} 0030 e così via.
    - Il nome completo del ricercatore è la combinazione di RuleId e il nome del ricercatore. Esempio di CSCAN0010. KeyStoreFiles, CSCAN0020. Base64EncodedCertificate e così via.
- **ResourceMatchPattern** : Regex delle estensioni di file da controllare per il ricerca
- **ContentSearchPatterns** : matrice di stringhe contenenti istruzioni Regex per la corrispondenza. Se non sono definiti criteri di ricerca, verranno restituiti tutti i file corrispondenti al criterio di corrispondenza delle risorse.
- **ContentSearchFilters** : matrice di stringhe contenenti istruzioni Regex per filtrare i falsi positivi specifici del ricercatore.
- **Matchdetails** : un messaggio descrittivo e/o istruzioni di mitigazione da aggiungere per ogni corrispondenza del ricercatore.
- **Consiglio** : fornisce il contenuto del campo Suggerimenti per una corrispondenza usando il formato di report PREfast.
- **Gravità** : valore intero per riflettere la gravità del problema (valore massimo = 1).
![Installazione di Credential scanner](./media/security-tools/6-credscan-customsearchers.png)

### <a name="roslyn-analyzers-faqs"></a>Domande frequenti sugli analizzatori Roslyn

#### <a name="what-are-the-most-common-errors-when-using-the-roslyn-analyzers-task"></a>Quali sono gli errori più comuni quando si usa l'attività degli analizzatori Roslyn?

**Errore: Il progetto è stato ripristinato con Microsoft. NETCore. app versione x. x. x, ma con le impostazioni correnti, verrebbe invece usata la versione y. y. y. Per risolvere questo problema, assicurarsi che le stesse impostazioni vengano usate per il ripristino e per le operazioni successive, ad esempio la compilazione o la pubblicazione. Questo problema si verifica in genere se la proprietà RuntimeIdentifier viene impostata durante la compilazione o la pubblicazione ma non durante il ripristino:**

Gli analizzatori Roslyn vengono eseguiti come parte della compilazione, pertanto l'albero di origine nel computer di compilazione deve essere in uno stato compilabile. Un passaggio (probabilmente "DotNet. exe Publish") tra la build principale e gli analizzatori Roslyn potrebbe aver inserito l'albero di origine in uno stato non compilabile. Forse duplicando il passaggio che esegue un ripristino di NuGet, appena prima del passaggio di Roslyn Analyzers, riporterà la struttura di origine in uno stato compilabile.

**chiusura di "CSC. exe" con codice di errore 1. Impossibile creare un'istanza dell'analizzatore AAAA da C:\BBBB.dll: Impossibile caricare il file o l'assembly ' Microsoft. CodeAnalysis, Version = X.x. x. x, Culture = neutral, PublicKeyToken = 31bf3856ad364e35' o una delle relative dipendenze. Il sistema non è in grado di trovare il file specificato.**

Verificare che il compilatore supporti gli analizzatori Roslyn. "CSC. exe/version" deve segnalare almeno la versione 2.6. x. In alcuni casi, i singoli file con estensione csproj possono eseguire l'override dell'installazione di Visual Studio del computer di compilazione, facendo riferimento a un pacchetto di Microsoft.Net. Compilers. Se l'uso di una versione specifica del compilatore non è intenzionale, rimuovere i riferimenti a Microsoft.Net. Compilers. In caso contrario, assicurarsi che anche il pacchetto a cui si fa riferimento sia almeno v 2.6. x. Provare a ottenere il log degli errori, che è possibile trovare nel parametro/ErrorLog: dalla riga di comando csc. exe (disponibile nel log dell'attività di compilazione Roslyn). Potrebbe avere un aspetto simile al seguente:/ErrorLog:\_f:\ts-Services-123 work\456\s\Some\Project\Code\Code.csproj.Sarif

**Il C# compilatore non è abbastanza recente (deve essere > = 2,6)**

Le versioni più recenti del C# compilatore vengono rilasciate https://www.nuget.org/packages/Microsoft.Net.Compilers qui:. Per ottenere la versione installata in uso, eseguire `C:\>csc.exe /version` dal prompt dei comandi. Assicurarsi di non avere alcun riferimento a un pacchetto NuGet Microsoft.Net. Compilers < versione 2.6.

**Log MSBuild/VSBuild non trovati**

A causa del funzionamento dell'attività, questa attività deve eseguire una query su Azure DevOps per il log MSBuild dall'attività di compilazione di MSBuild. Se questa attività viene eseguita immediatamente dopo l'attività di compilazione MSBuild, il log non sarà ancora disponibile. Inserire altre attività di compilazione, incluse le attività di compilazione SecDevTools, ad esempio Binskim, l'analisi antimalware e altre ancora, tra l'attività di compilazione MSBuild e l'attività di compilazione analizzatori Roslyn. 

## <a name="next-steps"></a>Passaggi successivi

Se è necessaria assistenza aggiuntiva, il supporto per l'analisi del codice di sicurezza Microsoft è disponibile dal lunedì al venerdì 9:00 AM-5:00 PM (ora solare Pacifico)

  - Onboarding: per iniziare, contattare i responsabili dell'account tecnico. 
  >[!NOTE] 
  >Se non si ha già una relazione di supporto a pagamento con Microsoft o se si dispone di un'offerta di supporto che non consente di acquistare servizi dal catalogo Phoenix, visitare i [servizi di supporto Home page](https://www.microsoft.com/enterprise/services/support) per altre informazioni.

  - Supporto-invia un messaggio di posta elettronica al team [Microsoft Security Analysis code](mailto:mscahelp@microsoft.com?Subject=Microsoft%20Security%20Code%20Analysis%20Support%20Request)