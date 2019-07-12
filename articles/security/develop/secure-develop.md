---
title: Sviluppo di applicazioni sicure in Microsoft Azure
description: Questo articolo illustra le procedure consigliate da prendere in considerazione durante le fasi di implementazione e la verifica del progetto di applicazione web.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: f4add4bf07178aa616e86f8a64b313630466824f
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67653272"
---
# <a name="develop-secure-applications-on-azure"></a>Sviluppare applicazioni sicure in Azure
In questo articolo ti presentiamo le attività di sicurezza e controlli da considerare quando si sviluppano applicazioni per il cloud. Domande di sicurezza e i concetti da considerare durante le fasi di implementazione e la verifica di Microsoft [Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) vengono analizzati. L'obiettivo è che consentono di definire le attività e i servizi di Azure che è possibile usare per sviluppare un'applicazione più sicura.

Le seguenti fasi SDL sono illustrate in questo articolo:

- Implementazione
- Verifica

## <a name="implementation"></a>Implementazione
Lo stato attivo della fase di implementazione è per stabilire le procedure consigliate per la prevenzione di anteprima e per rilevare e rimuovere i problemi di sicurezza dal codice.
Si supponga l'applicazione verrà utilizzato in modi che non si intendeva può quindi essere usato. Ciò consente di proteggersi da un uso improprio accidentale o intenzionale della propria applicazione.

### <a name="perform-code-reviews"></a>Eseguire le revisioni del codice

Prima di archiviare codice, condurre [revisioni del codice](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/code-reviews-not-primarily-finding-bugs) per aumentare la qualità complessiva del codice e ridurre il rischio di creare bug. È possibile usare [Visual Studio](https://docs.microsoft.com/azure/devops/repos/tfvc/get-code-reviewed-vs?view=vsts) per gestire il processo di revisione del codice.

### <a name="perform-static-code-analysis"></a>Eseguire l'analisi statica del codice

[Analisi statica del codice](https://www.owasp.org/index.php/Static_Code_Analysis) (noto anche come *analisi del codice di origine*) viene in genere eseguita come parte di una revisione del codice. Analisi statica del codice si intende di solito esegue strumenti di analisi per individuare le vulnerabilità potenziali nel codice non in esecuzione usando tecniche come codice statico [causare alterazioni verifica](https://en.wikipedia.org/wiki/Taint_checking) e [analisi di flusso di dati](https://en.wikipedia.org/wiki/Data-flow_analysis).

Azure Marketplace offre [strumenti di sviluppo](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1&search=code%20review) che eseguono l'analisi statica del codice e supporto per le revisioni del codice.

### <a name="validate-and-sanitize-every-input-for-your-application"></a>Convalidare e purificare ogni input per l'applicazione

Considerare tutto l'input come non attendibile per proteggere l'applicazione dalle vulnerabilità di applicazioni web più comuni. I dati non attendibili sono un mezzo per attacchi intrusivi nel codice. Input per l'applicazione include i parametri nell'URL di input da parte dell'utente, i dati dal database o da un'API, e tutto ciò che viene passato in cui un utente potrebbe potenzialmente modificare. Un'applicazione deve chiamare [convalidare](https://www.owasp.org/index.php/OWASP_Proactive_Controls_2016#4:_Validate_All_Inputs) che i dati siano sintatticamente e semanticamente validi prima che l'applicazione usa i dati in alcun modo (tra cui visualizzarli all'utente).

Convalidare l'input all'inizio del flusso di dati per assicurare che solo i dati corretto inserisca il flusso di lavoro. Per evitare che i dati in formato non valido nel database di persistenza o generando un malfunzionamento in un componente a valle.

Blacklisting e nell'elenco elementi consentiti sono due approcci generali per eseguire la convalida della sintassi di input:

  - La disattivazione di prova verificare che un input dell'utente specificato non contiene contenuto "noto per essere dannose".

  - Nell'elenco elementi consentiti prova a verificare che un input dell'utente specificato corrisponda a un set di input "valida". Elenco elementi consentiti in base al carattere è una forma di elenco elementi consentiti in cui un'applicazione verifica che l'input dell'utente contiene caratteri solo "noto buono" o che l'input corrisponda un formato noto.
    Ad esempio, questo potrebbe comportare per verificare che un nome utente contiene solo caratteri alfanumerici o che contiene esattamente due numeri.

Nell'elenco elementi consentiti è l'approccio consigliato per la creazione di software protetti.
La disattivazione è soggetta a errori perché non è possibile pensare a un elenco completo di input potenzialmente errati.

Eseguire questo lavoro nel server, non sul lato client (o sul server e sul lato client).

### <a name="verify-your-applications-outputs"></a>Verificare gli output dell'applicazione

Qualsiasi output che presentano visivamente o all'interno di un documento deve sempre essere con codifica e caratteri di escape. [Eseguire l'escape](https://www.owasp.org/index.php/Injection_Theory#Escaping_.28aka_Output_Encoding.29), noto anche come *codifica output*, viene usato per garantire che i dati non attendibili non sono un mezzo per attacchi di tipo injection. Eseguire l'escape, combinato con la convalida dei dati, fornisce le difese a più livelli per aumentare la sicurezza del sistema nel suo complesso.

Eseguire l'escape assicura che tutto ciò viene visualizzato come *output.* Eseguire l'escape consente inoltre l'interprete di sapere che i dati non sono progettati per essere eseguita e questo impedisce gli attacchi da in esecuzione. Si tratta di un'altra tecnica di attacco comune chiamata *cross-site scripting* (XSS).

Se si usa un framework web di terze parti, è possibile verificare le opzioni per la codifica dell'output in siti Web tramite il [foglio informativo sulla prevenzione di XSS OWASP](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.md).

### <a name="use-parameterized-queries-when-you-contact-the-database"></a>Usare query con parametri quando si contatta il database

Non creare mai una query a database inline "in modo immediato" nel codice e inviarlo direttamente al database. Codice dannoso inserito nell'applicazione potrebbe causare potenzialmente essere rubati, cancellato o modificato il database. L'applicazione può essere utilizzata anche per eseguire i comandi del sistema operativo dannosi nel sistema operativo che ospita il database.

Usare invece le query con parametri o stored procedure. Quando si usano le query con parametri, è possibile richiamare la procedura dal codice in modo sicuro e passa una stringa senza doversi preoccupare che verrà considerato come parte dell'istruzione di query.

### <a name="remove-standard-server-headers"></a>Rimuovere le intestazioni del server standard

Intestazioni come Server, X-Powered-By, e X-AspNet-Version rivelano informazioni sul server e tecnologie sottostanti. È consigliabile eliminare queste intestazioni per evitare di creare impronte digitali dell'applicazione.
Visualizzare [rimuovendo le intestazioni di server standard nei siti Web Azure](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/).

### <a name="segregate-your-production-data"></a>Separare i dati di produzione

I dati di produzione o dati "reali", non devono essere utilizzati per lo sviluppo, test o scopi diversi rispetto a ciò che l'azienda deve. Con una maschera ([resi anonimi](https://en.wikipedia.org/wiki/Data_anonymization)) set di dati deve essere utilizzato per i progetti di sviluppo e test.

Ciò significa che meno persone hanno accesso ai dati reali, che riduce la superficie di attacco. Significa anche un minor numero di dipendenti vedere dei dati personali, eliminando una potenziale violazione in riservatezza.

### <a name="implement-a-strong-password-policy"></a>Implementare criteri password complessi

Per difendersi dagli attacchi di forza bruta e basata su dizionario un'ipotesi, è necessario implementare criteri password complessi per garantire che gli utenti creino password complesse (ad esempio, la lunghezza minima di 12 caratteri e che richiedono di caratteri speciali e alfanumerici).

È possibile usare un framework di identità per creare e applicare i criteri password. Azure AD B2C consente di gestione delle password, fornendo [i criteri predefiniti](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows#create-a-password-reset-user-flow), [reimpostazione password self-service](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-sspr)e così via.

Per difendersi da attacchi ad account predefiniti, verificare che tutte le chiavi e le password siano sostituibili e che si sta generate o sostituite dopo l'installazione di risorse.

Se l'applicazione deve generare automaticamente le password, assicurarsi che le password generate siano casuali e abbiano un'entropia elevata.

### <a name="validate-file-uploads"></a>Convalidare i caricamenti di file

Se l'applicazione consente [caricamenti di file](https://www.owasp.org/index.php/Unrestricted_File_Upload), prendere in considerazione le precauzioni da eseguire per questa attività rischiosa. Il primo passaggio di numerosi attacchi consiste nell'inserire codice dannoso in un sistema che è sotto attacco. Tramite il caricamento di un file consente all'utente malintenzionato di ottenere questo risultato. OWASP offre soluzioni per la convalida di un file per garantire che il file di cui che si sta caricando.

Protezione antimalware aiuta a identificare e rimuovere virus, spyware e altro software dannoso. È possibile installare [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) o una soluzione di protezione degli endpoint di un partner Microsoft ([Trend Micro](https://www.trendmicro.com/azure/), [Symantec](https://www.symantec.com/products), [McAfee](https://www.mcafee.com/us/products.aspx), [Windows Defender](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10) e [System Center Endpoint Protection](https://docs.microsoft.com/sccm/protect/deploy-use/endpoint-protection)).

[Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) include funzionalità quali protezione in tempo reale, analisi pianificata, monitoraggio e aggiornamento malware, gli aggiornamenti delle firme, gli aggiornamenti del motore, esempi di reporting e la raccolta degli eventi di esclusione. È possibile integrare Microsoft Antimalware e soluzioni partner con [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-partner-integration) per facilitare la distribuzione e i rilevamenti predefiniti (avvisi ed eventi imprevisti).

### <a name="dont-cache-sensitive-content"></a>Non memorizzare nella cache di contenuti sensibili

Non memorizzare nella cache di contenuti sensibili nel browser. I browser possono archiviare le informazioni per la memorizzazione nella cache e cronologia. I file memorizzati nella cache vengono archiviati in una cartella, ad esempio la cartella di file temporanei Internet, nel caso di Internet Explorer. Quando queste pagine vengono visitate di nuovo, il browser visualizza le pagine dalla cache. Se all'utente vengono visualizzate informazioni riservate (indirizzo, della carta di credito, numero di previdenza sociale, nome utente), le informazioni potrebbero essere memorizzate nella cache del browser ed essere recuperato esaminando la cache del browser oppure premendo semplicemente il browser  **Indietro** pulsante.

## <a name="verification"></a>Verifica
La fase di verifica comporta un impegno completa per garantire che il codice soddisfi i principi di sicurezza e privacy che sono stati stabiliti nelle fasi precedenti.

### <a name="find-and-fix-vulnerabilities-in-your-application-dependencies"></a>Individuare e correggere le vulnerabilità nelle dipendenze dell'applicazione

Si analizza l'applicazione e dalle librerie dipendenti per identificare tutti i componenti vulnerabili noti. Prodotti che sono disponibili per eseguire questa analisi comprendono [OWASP Dependency Check](https://www.owasp.org/index.php/OWASP_Dependency_Check),[Snyk](https://snyk.io/), e [Black Duck](https://www.blackducksoftware.com/).

Analisi delle vulnerabilità con tecnologia [Tinfoil Security](https://www.tinfoilsecurity.com/) è disponibile per le app Web di servizio App di Azure. [Tinfoil Security scanning tramite il servizio App](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) offre agli sviluppatori e agli amministratori un modo veloce, integrato e conveniente di individuazione e indirizzamento le vulnerabilità prima che un attore malintenzionato può sfruttare i vantaggi di essi.

> [!NOTE]
> È anche possibile [integrare Tinfoil Security con Azure AD](https://docs.microsoft.com/azure/active-directory/saas-apps/tinfoil-security-tutorial). L'integrazione di Tinfoil Security con Azure AD offre i vantaggi seguenti:
>  - In Azure AD, è possibile controllare chi può accedere a Tinfoil Security.
>  - Gli utenti possono accedere automaticamente a Tinfoil Security (single sign-on) con i propri account Azure AD.
>  - È possibile gestire gli account in un'unica posizione centrale, il portale di Azure.

### <a name="test-your-application-in-an-operating-state"></a>Testare l'applicazione in uno stato operativo

Test della sicurezza dinamica dell'applicazione (DAST) è un processo di test di un'applicazione in uno stato operativo per individuare le vulnerabilità di sicurezza. Strumenti DAST analizzare programmi mentre sono in esecuzione per individuare le vulnerabilità di sicurezza, ad esempio il danneggiamento della memoria, configurazione del server non protetto, il cross-site scripting, problemi relativi al privilegio utente, attacchi SQL injection e altri problemi di sicurezza critici.

DAST è diversa dalla sicurezza delle applicazioni statici test (SAST). Strumenti SAST analizzano il codice sorgente o le versioni del codice di compilazione al momento non è in esecuzione il codice per trovare difetti di protezione.

Eseguire preferibilmente DAST, con l'assistenza di un professionista di sicurezza (un [tester di penetrazione](https://docs.microsoft.com/azure/security/azure-security-pen-testing) o assessor vulnerabilità). Se un professionista della protezione non è disponibile, è possibile eseguire manualmente il DAST con uno scanner di proxy web e di training. Plug-in uno scanner DAST sin dall'inizio per garantire che i problemi di sicurezza ovvia non introdurre nel codice. Vedere le [OWASP](https://www.owasp.org/index.php/Category:Vulnerability_Scanning_Tools) sito per un elenco degli scanner delle vulnerabilità dell'applicazione web.

### <a name="perform-fuzz-testing"></a>Eseguire test con dati casuali

Nelle [test con dati casuali](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/), errori dei programmi è indurre introducendo deliberatamente i dati in formato non corretto o casuale a un'applicazione. Generazione di errori dei programmi consente di individuare potenziali problemi di sicurezza prima del rilascio dell'applicazione.

[Rilevamento dei rischi di sicurezza](https://docs.microsoft.com/security-risk-detection/) è il Microsoft univoco test con dati casuali servizio per individuare eventuali bug critico per la sicurezza nel software.

### <a name="conduct-attack-surface-review"></a>Condurre una revisione della superficie di attacco

Esaminare la superficie di attacco dopo il completamento del codice aiuta a garantire che qualsiasi progettazione o l'implementazione di modifiche a un'applicazione o sistema è stato considerato. Garantisce che eventuali nuovi vettori di attacchi che sono stati creati in seguito a modifiche, tra cui modelli di rischio, sono stati esaminati e risolti.

È possibile compilare un'immagine della superficie di attacco mediante l'analisi dell'applicazione. Microsoft offre uno strumento di analisi della superficie di attacco denominato [Attack Surface Analyzer](https://www.microsoft.com/download/details.aspx?id=24487). È possibile scegliere tra molti test dinamici commerciale e gli strumenti o servizi, tra cui di analisi delle vulnerabilità [OWASP zzata attacco Proxy Project](https://www.owasp.org/index.php/OWASP_Zed_Attack_Proxy_Project), [Arachni](http://arachni-scanner.com/), [Skipfish](https://code.google.com/p/skipfish/), e [w3af](http://w3af.sourceforge.net/). Questi strumenti di analisi di una ricerca per indicizzazione l'app ed eseguire il mapping le parti dell'applicazione che sono accessibili tramite il web. È inoltre possibile cercare nel Marketplace di Azure per simili [strumenti di sviluppo](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1).

### <a name="perform-security-penetration-testing"></a>Eseguire test di penetrazione di sicurezza

È importante come testare qualsiasi altra funzionalità verificare che l'applicazione sia protetta. Rendere [test di penetrazione](https://docs.microsoft.com/azure/security/azure-security-pen-testing) una parte standard del processo di compilazione e distribuzione. Pianificare test periodici di sicurezza e analisi delle vulnerabilità sulle applicazioni distribuite e il monitoraggio per le porte aperte, endpoint e attacchi.

### <a name="run-security-verification-tests"></a>Eseguire test di verifica della sicurezza

[Secure DevOps Kit per Azure](https://azsk.azurewebsites.net/index.html) (AzSK) contiene SVTs per più servizi della piattaforma Azure. Si esegue queste SVTs periodicamente per verificare che la sottoscrizione di Azure e le diverse risorse che costituiscono l'applicazione siano in uno stato protetto. È anche possibile automatizzare i test usando la funzionalità di estensioni/integrazione continua (CI/CD) di distribuzione di AzSK, che rende disponibile come estensione di Visual Studio SVTs.

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti, si consiglia di controlli di sicurezza e le attività che consentono di progettare e distribuiscono applicazioni sicure.

- [Progettazione di applicazioni sicure](secure-design.md)
- [Distribuisci applicazioni sicure](secure-deploy.md)
