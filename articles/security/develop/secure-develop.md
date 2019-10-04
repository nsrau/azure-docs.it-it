---
title: Sviluppare applicazioni sicure in Microsoft Azure
description: Questo articolo illustra le procedure consigliate da prendere in considerazione durante le fasi di implementazione e verifica del progetto di applicazione Web.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: c1c7dd0bd017852144139a841ff609dabf0f1a27
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68928062"
---
# <a name="develop-secure-applications-on-azure"></a>Sviluppare applicazioni sicure in Azure
In questo articolo vengono presentati i controlli e le attività di sicurezza da prendere in considerazione quando si sviluppano applicazioni per il cloud. Vengono analizzate le domande e i concetti di sicurezza da considerare durante le fasi di implementazione e di verifica di Microsoft [Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) . L'obiettivo è consentire di definire le attività e i servizi di Azure che è possibile usare per sviluppare un'applicazione più protetta.

Le fasi SDL seguenti sono descritte in questo articolo:

- Implementazione
- Verifica

## <a name="implementation"></a>Implementazione
L'obiettivo della fase di implementazione consiste nel definire le procedure consigliate per la prevenzione anticipata e per rilevare e rimuovere i problemi di sicurezza dal codice.
Si supponga che l'applicazione venga usata in modi che non si intendeva usare. Ciò consente di evitare un uso improprio accidentale o intenzionale dell'applicazione.

### <a name="perform-code-reviews"></a>Eseguire revisioni del codice

Prima di archiviare il codice, eseguire le [revisioni del codice](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/code-reviews-not-primarily-finding-bugs) per aumentare la qualità complessiva del codice e ridurre il rischio di creare bug. È possibile usare [Visual Studio](https://docs.microsoft.com/azure/devops/repos/tfvc/get-code-reviewed-vs?view=vsts) per gestire il processo di revisione del codice.

### <a name="perform-static-code-analysis"></a>Eseguire l'analisi statica del codice

[Analisi statica del codice](https://www.owasp.org/index.php/Static_Code_Analysis) (noto anche come *analisi del codice sorgente*) viene in genere eseguito come parte di una revisione del codice. L'analisi statica del codice si riferisce comunemente all'esecuzione di strumenti di analisi del codice statici per individuare potenziali vulnerabilità nel codice non in esecuzione utilizzando tecniche quali [il controllo](https://en.wikipedia.org/wiki/Taint_checking) dei guasti e l' [analisi del flusso di dati](https://en.wikipedia.org/wiki/Data-flow_analysis).

Azure Marketplace offre [strumenti di sviluppo](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1&search=code%20review) che eseguono l'analisi statica del codice e facilitano le revisioni del codice.

### <a name="validate-and-sanitize-every-input-for-your-application"></a>Convalidare e purificare tutti gli input per l'applicazione

Considerare tutti gli input come non attendibili per proteggere l'applicazione dalle vulnerabilità più comuni dell'applicazione Web. I dati non attendibili sono un veicolo per gli attacchi injection. L'input per l'applicazione include i parametri nell'URL, l'input dell'utente, i dati del database o da un'API e tutto ciò che viene passato a un utente potrebbe potenzialmente manipolare. Un'applicazione deve [verificare](https://www.owasp.org/index.php/OWASP_Proactive_Controls_2016#4:_Validate_All_Inputs) che i dati siano sintatticamente e semanticamente validi prima che l'applicazione usi i dati in qualsiasi modo, inclusa la visualizzazione all'utente.

Convalidare l'input all'inizio del flusso di dati per garantire che solo i dati in formato corretto entrino nel flusso di lavoro. Non si desidera che i dati in formato non valido siano salvati nel database o che si verifichi un malfunzionamento in un componente downstream.

Per l'esecuzione della convalida della sintassi di input sono disponibili due approcci generali:

  - L'inserimento nella blacklist tenta di verificare che un dato utente specificato non contenga contenuto "noto come dannoso".

  - L'elenco elementi consentiti tenta di verificare che un determinato input utente corrisponda a un set di input "noti". L'elenco elementi consentiti basato su caratteri è un tipo di elenco elementi consentiti in cui un'applicazione controlla che l'input dell'utente contenga solo caratteri "noti" o che l'input corrisponda a un formato noto.
    Ad esempio, ciò potrebbe comportare la verifica che un nome utente contenga solo caratteri alfanumerici o che contenga esattamente due numeri.

L'elenco elementi consentiti è l'approccio preferito per la creazione di software protetto.
La disattivazione è soggetta a errori perché non è possibile pensare a un elenco completo di input potenzialmente errato.

Eseguire questa operazione sul server, non sul lato client (o sul server e sul lato client).

### <a name="verify-your-applications-outputs"></a>Verificare gli output dell'applicazione

Qualsiasi output presente in modo visivo o all'interno di un documento deve essere sempre codificato e preceduto da un carattere di escape. L' [escape](https://www.owasp.org/index.php/Injection_Theory#Escaping_.28aka_Output_Encoding.29), noto anche come *codifica di output*, viene usato per garantire che i dati non attendibili non siano un veicolo per un attacco injection. L'escape, combinato con la convalida dei dati, fornisce difese a più livelli per aumentare la sicurezza del sistema nel suo complesso.

L'escape garantisce che tutti gli elementi vengano visualizzati come *output.* L'escape consente inoltre all'interprete di tenere presente che i dati non sono destinati a essere eseguiti e ciò impedisce il funzionamento degli attacchi. Si tratta di un'altra tecnica di attacco comune chiamata XSS ( *cross-site scripting* ).

Se si usa un framework Web da terze parti, è possibile verificare le opzioni per la codifica dell'output nei siti Web usando il foglio informativo sulla [prevenzione XSS OWASP](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.md).

### <a name="use-parameterized-queries-when-you-contact-the-database"></a>Usare query con parametri quando si contatta il database

Non creare mai una query di database inline "in tempo reale" nel codice e inviarla direttamente al database. Il codice dannoso inserito nell'applicazione potrebbe potenzialmente causare il furto, la cancellazione o la modifica del database. L'applicazione può essere usata anche per eseguire comandi del sistema operativo dannosi sul sistema operativo che ospita il database.

Utilizzare invece query con parametri o stored procedure. Quando si utilizzano query con parametri, è possibile richiamare la procedura dal codice in modo sicuro e passarla a una stringa senza preoccuparsi che venga trattata come parte dell'istruzione di query.

### <a name="remove-standard-server-headers"></a>Rimuovi intestazioni server standard

Le intestazioni come server, X-Powered-by e X-AspNet-Version rivelano informazioni sul server e sulle tecnologie sottostanti. È consigliabile eliminare queste intestazioni per evitare l'impronta digitale dell'applicazione.
Vedere [rimozione delle intestazioni del server standard nei siti Web di Azure](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/).

### <a name="segregate-your-production-data"></a>Separare i dati di produzione

I dati di produzione, o dati reali, non devono essere usati per lo sviluppo, il test o qualsiasi altro scopo rispetto a quello previsto dall'azienda. Un set di dati mascherato ([resi anonimi](https://en.wikipedia.org/wiki/Data_anonymization)) deve essere usato per tutte le attività di sviluppo e test.

Ciò significa che un minor numero di utenti ha accesso ai dati reali, riducendo così la superficie di attacco. Significa anche che un minor numero di dipendenti Visualizza i dati personali, eliminando una potenziale violazione della riservatezza.

### <a name="implement-a-strong-password-policy"></a>Implementare criteri password complesse

Per difendersi dalla forza bruta e dall'ipotesi basata su dizionario, è necessario implementare un criterio di password complessa per assicurarsi che gli utenti creino una password complessa (ad esempio, una lunghezza minima di 12 caratteri e che richiedono caratteri alfanumerici e speciali).

È possibile usare un Framework di identità per creare e applicare i criteri password. Azure AD B2C consente la gestione delle password fornendo [criteri predefiniti](../../active-directory-b2c/tutorial-create-user-flows.md#create-a-password-reset-user-flow), reimpostazione della [password self-service](../../active-directory-b2c/active-directory-b2c-reference-sspr.md)e altro ancora.

Per difendersi dagli attacchi sugli account predefiniti, verificare che tutte le chiavi e le password siano sostituibili e che siano generate o sostituite dopo l'installazione delle risorse.

Se l'applicazione deve generare automaticamente le password, verificare che le password generate siano casuali e che abbiano un'entropia elevata.

### <a name="validate-file-uploads"></a>Convalidare i caricamenti di file

Se l'applicazione consente il [caricamento di file](https://www.owasp.org/index.php/Unrestricted_File_Upload), prendere in considerazione le precauzioni che è possibile intraprendere per questa attività rischiosa. Il primo passaggio di molti attacchi consiste nell'ottenere codice dannoso in un sistema in attacco. L'uso di un caricamento di file aiuta l'utente malintenzionato a eseguire questa operazione. OWASP offre soluzioni per la convalida di un file, in modo da garantire la sicurezza del file che si sta caricando.

La protezione antimalware consente di identificare e rimuovere virus, spyware e altro software dannoso. È possibile installare [Microsoft Antimalware](../fundamentals/antimalware.md) o una soluzione di protezione degli endpoint di un partner Microsoft ([Trend Micro](https://www.trendmicro.com/azure/), [Symantec](https://www.symantec.com/products), [McAfee](https://www.mcafee.com/us/products.aspx), [Windows Defender](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10) e [System Center Endpoint Protection](https://docs.microsoft.com/sccm/protect/deploy-use/endpoint-protection)).

[Microsoft antimalware](../fundamentals/antimalware.md) include funzionalità come la protezione in tempo reale, l'analisi pianificata, la correzione malware, gli aggiornamenti delle firme, gli aggiornamenti del motore, la creazione di report di esempio e la raccolta di eventi di esclusione. È possibile integrare Microsoft Antimalware e soluzioni partner con [Centro sicurezza di Azure](../../security-center/security-center-partner-integration.md) per facilitare la distribuzione e i rilevamenti predefiniti (avvisi ed eventi imprevisti).

### <a name="dont-cache-sensitive-content"></a>Non memorizzare nella cache contenuto sensibile

Non memorizzare nella cache contenuto sensibile nel browser. I browser possono archiviare informazioni per la memorizzazione nella cache e la cronologia. I file memorizzati nella cache vengono archiviati in una cartella come la cartella dei file temporanei Internet, nel caso di Internet Explorer. Quando si fa riferimento a queste pagine, il browser Visualizza le pagine dalla relativa cache. Se per l'utente vengono visualizzate informazioni sensibili (indirizzo, dettagli della carta di credito, numero di previdenza sociale, nome utente), le informazioni potrebbero essere archiviate nella cache del browser e possono essere recuperate esaminando la cache del browser o semplicemente premendo il pulsante del browser  **Pulsante indietro** .

## <a name="verification"></a>Verifica
La fase di verifica prevede un impegno completo per garantire che il codice soddisfi i principi di sicurezza e privacy stabiliti nelle fasi precedenti.

### <a name="find-and-fix-vulnerabilities-in-your-application-dependencies"></a>Trovare e correggere le vulnerabilità nelle dipendenze dell'applicazione

È possibile analizzare l'applicazione e le librerie dipendenti per identificare i componenti vulnerabili noti. I prodotti disponibili per eseguire questa analisi includono il [controllo](https://www.owasp.org/index.php/OWASP_Dependency_Check)delle dipendenze di OWASP,[Snyk](https://snyk.io/)e [Black Duck](https://www.blackducksoftware.com/).

L'analisi delle vulnerabilità basata [sulla sicurezza di stagno](https://www.tinfoilsecurity.com/) è disponibile per le app Web del servizio app Azure. L' [analisi della sicurezza di stagno tramite il servizio app](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) offre agli sviluppatori e agli amministratori un mezzo rapido, integrato ed economico per individuare e risolvere le vulnerabilità prima che un attore malintenzionato possa sfruttarli.

> [!NOTE]
> È anche possibile [integrare la sicurezza di stagno con Azure ad](../../active-directory/saas-apps/tinfoil-security-tutorial.md). L'integrazione della sicurezza di alluminio con Azure AD offre i vantaggi seguenti:
>  - In Azure AD, è possibile controllare chi ha accesso alla sicurezza di stagno.
>  - Gli utenti possono eseguire l'accesso automatico a Stagnol Security (Single Sign-on) con i propri account Azure AD.
>  - È possibile gestire gli account in un'unica posizione centrale, il portale di Azure.

### <a name="test-your-application-in-an-operating-state"></a>Testare l'applicazione in uno stato operativo

Dynamic Application Security Testing (DAST) è un processo di test di un'applicazione in uno stato operativo per individuare le vulnerabilità della sicurezza. Gli strumenti DAST analizzano i programmi durante l'esecuzione per individuare vulnerabilità di sicurezza, ad esempio danneggiamento della memoria, configurazione del server non sicura, scripting tra siti, problemi relativi ai privilegi utente, attacchi SQL injection e altri problemi di sicurezza critici.

DAST è diverso dal test di sicurezza dell'applicazione statica (SAST). Gli strumenti di SAST analizzano il codice sorgente o le versioni compilate del codice quando il codice non è in esecuzione per individuare eventuali problemi di sicurezza.

Eseguire DAST, preferibilmente con l'assistenza di un professionista della sicurezza (un [tester](../fundamentals/pen-testing.md) di penetrazione o un analizzatore di vulnerabilità). Se un professionista della sicurezza non è disponibile, è possibile eseguire DAST con uno scanner proxy Web e una formazione. Collegare un DAST scanner in anticipo per assicurarsi di non introdurre problemi di sicurezza evidenti nel codice. Vedere il sito [OWASP](https://www.owasp.org/index.php/Category:Vulnerability_Scanning_Tools) per un elenco di scanner di vulnerabilità di applicazioni Web.

### <a name="perform-fuzz-testing"></a>Esegui test fuzzy

Nei [test fuzzy](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/)si induce l'errore del programma definendo intenzionalmente dati non validi o casuali in un'applicazione. L'indurre errore del programma consente di rivelare potenziali problemi di sicurezza prima del rilascio dell'applicazione.

Il [rilevamento dei rischi di sicurezza](https://docs.microsoft.com/security-risk-detection/) è il servizio di test fuzzy Microsoft univoco per individuare i bug critici per la sicurezza nel software.

### <a name="conduct-attack-surface-review"></a>Verifica della superficie di attacco di condotta

Esaminando la superficie di attacco dopo il completamento del codice, è possibile verificare che siano state prese in considerazione tutte le modifiche di progettazione o implementazione di un'applicazione o di un sistema. Consente di garantire che tutti i nuovi vettori di attacco creati in seguito alle modifiche, inclusi i modelli di rischio, siano stati rivisti e mitigati.

Per creare un'immagine della superficie di attacco, è possibile analizzare l'applicazione. Microsoft offre uno strumento di analisi della superficie di attacco denominato [Attack Surface Analyzer](https://www.microsoft.com/download/details.aspx?id=24487). È possibile scegliere tra molti strumenti o servizi di analisi dinamica e di analisi delle vulnerabilità commerciali, tra cui il [progetto proxy di attacco OWASP Zed](https://www.owasp.org/index.php/OWASP_Zed_Attack_Proxy_Project), [Arachni](http://arachni-scanner.com/), [Skipfish](https://code.google.com/p/skipfish/)e [w3af](http://w3af.sourceforge.net/). Questi strumenti di analisi eseguono la ricerca per indicizzazione dell'app e mappano le parti dell'applicazione accessibili tramite Web. È anche possibile eseguire ricerche in Azure Marketplace per [strumenti di sviluppo](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1)simili.

### <a name="perform-security-penetration-testing"></a>Eseguire test di penetrazione della sicurezza

Assicurarsi che l'applicazione sia protetta è importante quanto il test di qualsiasi altra funzionalità. Eseguire [test](../fundamentals/pen-testing.md) di penetrazione in una parte standard del processo di compilazione e distribuzione. Pianificare test di sicurezza e analisi delle vulnerabilità regolari sulle applicazioni distribuite e monitorare le porte aperte, gli endpoint e gli attacchi.

### <a name="run-security-verification-tests"></a>Esegui test di verifica della sicurezza

[Secure DevOps kit per Azure](https://azsk.azurewebsites.net/index.html) (AzSK) contiene SVTs per più servizi della piattaforma Azure. Questi SVTs vengono eseguiti periodicamente per assicurarsi che la sottoscrizione di Azure e le diverse risorse che costituiscono l'applicazione siano in uno stato sicuro. È anche possibile automatizzare questi test usando la funzionalità di integrazione continua/distribuzione continua (CI/CD) di AzSK, che rende SVTs disponibile come estensione di Visual Studio.

## <a name="next-steps"></a>Passaggi successivi
Negli articoli seguenti si consigliano i controlli di sicurezza e le attività che consentono di progettare e distribuire applicazioni sicure.

- [Progettare applicazioni protette](secure-design.md)
- [Distribuire applicazioni sicure](secure-deploy.md)
