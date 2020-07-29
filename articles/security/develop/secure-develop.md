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
ms.openlocfilehash: 7818ae36c785311466d2fb26ce45dcf50983145d
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87283487"
---
# <a name="develop-secure-applications-on-azure"></a>Sviluppare applicazioni sicure in Azure
In questo articolo vengono presentati i controlli e le attività di sicurezza da prendere in considerazione quando si sviluppano applicazioni per il cloud. Vengono analizzate le domande e i concetti di sicurezza da considerare durante le fasi di implementazione e verifica di Microsoft [Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx). L'obiettivo è consentire di definire le attività e i servizi di Azure che è possibile usare per sviluppare un'applicazione più protetta.

Questo articolo tratta le fasi di SDL seguenti:

- Implementazione
- Verifica

## <a name="implementation"></a>Implementazione
L'obiettivo della fase di implementazione consiste nel definire le procedure consigliate per la prevenzione anticipata e per rilevare e rimuovere i problemi di sicurezza dal codice.
Si supponga che l'applicazione verrà usata in modi in cui non era previsto usarla. Ciò consente di evitare un uso improprio accidentale o intenzionale dell'applicazione.

### <a name="perform-code-reviews"></a>Eseguire revisioni del codice

Prima di archiviare il codice, eseguire [revisioni del codice](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/code-reviews-not-primarily-finding-bugs) per aumentare la qualità complessiva del codice e ridurre il rischio di creare bug. È possibile usare [Visual Studio](https://docs.microsoft.com/azure/devops/repos/tfvc/get-code-reviewed-vs?view=vsts) per gestire il processo di revisione del codice.

### <a name="perform-static-code-analysis"></a>Eseguire l'analisi codice statico

L'[analisi codice statico](https://owasp.org/www-community/controls/Static_Code_Analysis), nota anche come *analisi del codice sorgente* viene in genere eseguita come parte di una revisione del codice. L'analisi codice statico si riferisce comunemente all'esecuzione di strumenti di analisi codice statico per individuare potenziali vulnerabilità nel codice non in esecuzione, usando tecniche come il [controllo dei dati](https://en.wikipedia.org/wiki/Taint_checking) e l'[analisi del flusso di dati](https://en.wikipedia.org/wiki/Data-flow_analysis).

Azure Marketplace offre [strumenti di sviluppo](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1&search=code%20review) che eseguono l'analisi codice statico e assistono nelle revisioni del codice.

### <a name="validate-and-sanitize-every-input-for-your-application"></a>Convalidare e purificare tutti gli input per l'applicazione

Considerare tutti gli input come non attendibili per proteggere l'applicazione dalle vulnerabilità più comuni delle applicazioni Web. I dati non attendibili sono un veicolo per gli attacchi injection. L'input per l'applicazione include parametri nell'URL, input dell'utente, dati dal database o da un'API e tutto ciò che viene passato e che un utente potrebbe potenzialmente manipolare. Un'applicazione deve [convalidare](https://owasp.org/www-project-proactive-controls/v3/en/c5-validate-inputs) che i dati siano sintatticamente e semanticamente validi prima che l'applicazione li usi in qualunque modo, inclusa la visualizzazione all'utente.

Convalidare l'input all'inizio del flusso di dati per garantire che solo i dati in formato corretto entrino nel flusso di lavoro. I dati in formato non valido non devono essere salvati nel database o causare il malfunzionamento di un componente downstream.

L'inserimento nella blacklist e l'elenco elementi consentiti sono due approcci generali per eseguire la convalida della sintassi di input:

  - L'inserimento nella blacklist tenta di verificare che l'input di un determinato utente non contenga contenuti "noti come dannosi".

  - L'elenco elementi consentiti tenta di verificare che l'input di un determinato utente corrisponda a un set di input "validi noti". L'elenco elementi consentiti basato su caratteri è un tipo di elenco elementi consentiti in cui un'applicazione controlla che l'input dell'utente contenga solo caratteri "validi noti" o che l'input corrisponda a un formato noto.
    Ad esempio, ciò potrebbe comportare la verifica che un nome utente contenga solo caratteri alfanumerici o che contenga esattamente due numeri.

L'elenco elementi consentiti è l'approccio preferito per la creazione di software protetto.
L'inserimento nella blacklist è soggetto a errori, perché non è possibile pensare a un elenco completo di input potenzialmente dannoso.

Eseguire questa operazione sul server, non sul lato client o sul server e sul lato client.

### <a name="verify-your-applications-outputs"></a>Verificare l'output dell'applicazione

Qualsiasi output presentato in modo visivo o all'interno di un documento deve essere sempre codificato e preceduto da un carattere di escape. L'[escape](https://owasp.org/www-community/Injection_Theory#Escaping_.28aka_Output_Encoding.29), noto anche come *codifica di output*, viene usato per garantire che i dati non attendibili non siano un veicolo per un attacco injection. L'escape, combinato con la convalida dei dati, fornisce difese a più livelli per aumentare la sicurezza del sistema nel suo complesso.

L'escape garantisce che tutti gli elementi vengano visualizzati come *output*. L'escape consente inoltre all'interprete di tenere presente che i dati non sono destinati a essere eseguiti e questo impedisce il funzionamento degli attacchi. Si tratta di un'altra tecnica di attacco comune denominata *cross-site scripting* (XSS).

Se si usa un framework Web da terze parti, è possibile verificare le opzioni per la codifica dell'output nei siti Web usando il [foglio informativo di prevenzione OWASP XSS](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.md).

### <a name="use-parameterized-queries-when-you-contact-the-database"></a>Usare query con parametri quando si contatta il database

Non creare mai una query di database inline "in tempo reale" nel codice e inviarla direttamente al database. Il codice dannoso inserito nell'applicazione potrebbe potenzialmente causare il furto, la cancellazione o la modifica del database. L'applicazione può essere usata anche per eseguire comandi del sistema operativo dannosi sul sistema operativo che ospita il database.

Usare invece query con parametri o stored procedure. Quando si usando query con parametri, è possibile richiamare la procedura dal codice in modo sicuro e passarla a una stringa senza preoccuparsi che venga trattata come parte dell'istruzione di query.

### <a name="remove-standard-server-headers"></a>Rimuovi intestazioni server standard

Intestazioni come Server, X-Powered-By e X-AspNet-Version rivelano informazioni sul server e sulle tecnologie sottostanti. È consigliabile eliminare queste intestazioni per evitare l'impronta digitale dell'applicazione.
Vedere [Rimozione di intestazioni server standard nei siti Web di Azure](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/).

### <a name="segregate-your-production-data"></a>Separare i dati di produzione

I dati di produzione, o dati "reali", non devono essere usati per lo sviluppo, il test o qualsiasi altro scopo rispetto a quello previsto dall'azienda. È necessario usare un set di dati mascherato ([anonimizzato](https://en.wikipedia.org/wiki/Data_anonymization)) per tutte le attività di sviluppo e test.

Ciò significa che un minor numero di utenti ha accesso ai dati reali, riducendo così la superficie di attacco. Significa anche che un minor numero di dipendenti visualizza i dati personali, eliminando una potenziale violazione della riservatezza.

### <a name="implement-a-strong-password-policy"></a>Implementare criteri password complessi

Per proteggersi da attacchi di forza bruta e attacchi a dizionario è necessario implementare criteri password complessi, per fare in modo che gli utenti creino password complesse, ad esempio con una lunghezza minima di 12 caratteri o con caratteri speciali e alfanumerici.

È possibile usare un framework di identità per creare e applicare i criteri password. Azure AD B2C facilita la gestione delle password fornendo [criteri predefiniti](../../active-directory-b2c/tutorial-create-user-flows.md#create-a-password-reset-user-flow), la [reimpostazione della password self-service](../../active-directory-b2c/user-flow-self-service-password-reset.md) e altro ancora.

Per difendersi da attacchi ad account predefiniti, verificare che tutte le chiavi e le password siano sostituibili e che vengano generate o sostituite dopo la fase di installazione.

Se l'applicazione deve generare automaticamente le password, assicurarsi che le password generate siano casuali e abbiano un'entropia elevata.

### <a name="validate-file-uploads"></a>Convalidare caricamenti di file

Se l'applicazione consente il [caricamento dei file](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload), tenere in considerazione le precauzioni che è possibile prendere per questa attività rischiosa. Il primo passaggio di molti attacchi è inserire del codice dannoso in un sistema sotto attacco. Il caricamento di un file consente all'utente malintenzionato di portare a termine questa operazione. OWASP offre soluzioni per la convalida di un file, in modo da garantire la sicurezza del file che si sta caricando.

È consigliabile installare una protezione antimalware per identificare e rimuovere virus, spyware e altro software dannoso. È possibile installare [Microsoft Antimalware](../fundamentals/antimalware.md) o una soluzione di protezione degli endpoint di un partner Microsoft, come [Trend Micro](https://www.trendmicro.com/azure/), [Broadcom](https://www.broadcom.com/products), [McAfee](https://www.mcafee.com/us/products.aspx), [Windows Defender](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10) e [Endpoint Protection](https://docs.microsoft.com/configmgr/protect/deploy-use/endpoint-protection).

[Microsoft Antimalware](../fundamentals/antimalware.md) include caratteristiche come la protezione in tempo reale, l'analisi pianificata, il monitoraggio e aggiornamento malware, l'aggiornamento delle firme e del motore, il reporting di campioni e la raccolta degli eventi di esclusione. È possibile integrare Microsoft Antimalware e soluzioni partner con [Centro sicurezza di Azure](../../security-center/security-center-partner-integration.md) per facilitare la distribuzione e i rilevamenti predefiniti (avvisi ed eventi imprevisti).

### <a name="dont-cache-sensitive-content"></a>Non memorizzare nella cache contenuto sensibile

Non memorizzare nella cache contenuto sensibile del browser. I browser possono archiviare informazioni per la memorizzazione nella cache e la cronologia. Questi file memorizzati nella cache vengono archiviati in una cartella, come la cartella Temporary Internet Files, nel caso di Internet Explorer. Quando queste pagine vengono visitate di nuovo, il browser le visualizza dalla cache. Se vengono visualizzate informazioni riservate, come l'indirizzo, i dati della carta di credito e il codice fiscale dell'utente o il nome utente, tali informazioni possono essere memorizzate nella cache del browser e quindi recuperate esaminando la cache del browser o semplicemente premendo il pulsante **Indietro** del browser.

## <a name="verification"></a>Verifica
La fase di verifica prevede un impegno completo per garantire che il codice soddisfi i principi di sicurezza e privacy stabiliti nelle fasi precedenti.

### <a name="find-and-fix-vulnerabilities-in-your-application-dependencies"></a>Trovare e correggere le vulnerabilità nelle dipendenze dell'applicazione

È possibile analizzare l'applicazione e le librerie dipendenti per identificare i componenti vulnerabili noti. I prodotti disponibili per eseguire questa analisi includono [OWASP Dependency Check](https://www.owasp.org/index.php/OWASP_Dependency_Check), [Snyk](https://snyk.io/) e [Black Duck](https://www.blackducksoftware.com/).

L'analisi delle vulnerabilità basata su [Tinfoil Security](https://www.tinfoilsecurity.com/) è disponibile per le app Web del servizio app di Azure. L'[analisi di Tinfoil Security tramite il servizio app](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) offre agli sviluppatori e agli amministratori un mezzo rapido, integrato ed economico per individuare e risolvere le vulnerabilità prima che un attore malintenzionato possa sfruttarle.

> [!NOTE]
> È anche possibile [integrare Tinfoil Security in Azure AD](../../active-directory/saas-apps/tinfoil-security-tutorial.md). L'integrazione di Tinfoil Security con Azure AD offre i vantaggi seguenti:
>  - È possibile controllare in Azure AD chi può accedere a Tinfoil Security.
>  - È possibile abilitare gli utenti per l'accesso automatico a Tinfoil Security (Single Sign-On) con gli account di Azure AD personali.
>  - È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

### <a name="test-your-application-in-an-operating-state"></a>Testare l'applicazione in uno stato operativo

Il test della sicurezza dell'applicazione dinamico (DAST) è un processo di test di un'applicazione in uno stato operativo per individuare le vulnerabilità della sicurezza. Gli strumenti DAST analizzano i programmi durante l'esecuzione, per individuare vulnerabilità di sicurezza, ad esempio danneggiamento della memoria, configurazione del server non sicura, scripting tra siti, problemi relativi alle autorizzazioni degli utenti, attacchi SQL injection e altri problemi di sicurezza critici.

Il DAST è diverso dal test di sicurezza dell'applicazione statico (SAST). Gli strumenti SAST analizzano il codice sorgente o le versioni compilate del codice quando il codice non è in esecuzione, per individuare eventuali problemi di sicurezza.

Eseguire il DAST preferibilmente con l'assistenza di un professionista della sicurezza, come un [tester della penetrazione](../fundamentals/pen-testing.md) o un analizzatore della vulnerabilità. Se non è disponibile un professionista della sicurezza, è possibile eseguire il DAST con uno scanner proxy Web e training. Collegare uno scanner DAST in anticipo per assicurarsi di non introdurre problemi di sicurezza evidenti nel codice. Per un elenco di scanner delle vulnerabilità delle applicazioni Web, vedere il sito [OWASP](https://owasp.org/www-community/Vulnerability_Scanning_Tools).

### <a name="perform-fuzz-testing"></a>Eseguire test con dati casuali

Nel [test con dati casuali](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/), si induce l'errore del programma introducendo intenzionalmente dati non validi o casuali in un'applicazione. Inducendo un errore del programma, è possibile rivelare potenziali problemi di sicurezza prima del rilascio dell'applicazione.

[Security Risk Detection](https://www.microsoft.com/en-us/security-risk-detection/) è il servizio esclusivo Microsoft per il test con dati casuali, che consente di individuare i bug critici per la sicurezza nel software.

### <a name="conduct-attack-surface-review"></a>Eseguire la verifica della superficie di attacco

Esaminare la superficie di attacco dopo il completamento del codice consente di verificare che siano state prese in considerazione tutte le modifiche di progettazione o implementazione di un'applicazione o di un sistema. Consente di garantire che tutti i nuovi vettori di attacco creati in seguito alle modifiche, inclusi i modelli di rischio, siano stati esaminati e mitigati.

È possibile avere un quadro della superficie di attacco analizzando l'applicazione. Microsoft offre uno strumento di analisi della superficie di attacco, denominato [Attack Surface Analyzer](https://www.microsoft.com/download/details.aspx?id=24487). È possibile scegliere tra molti strumenti o servizi di analisi dinamica e di analisi delle vulnerabilità commerciali, tra cui [OWASP Zed Attack Proxy Project](https://www.owasp.org/index.php/OWASP_Zed_Attack_Proxy_Project), [Arachni](http://arachni-scanner.com/), [Skipfish](https://code.google.com/p/skipfish/) e [w3af](http://w3af.sourceforge.net/). Questi strumenti di analisi eseguono la ricerca per indicizzazione dell'app e mappano le parti dell'applicazione accessibili tramite Web. È anche possibile eseguire ricerche in Azure Marketplace per [strumenti di sviluppo](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1) simili.

### <a name="perform-security-penetration-testing"></a>Eseguire test di penetrazione della sicurezza

Verificare che l'applicazione sia protetta è importante come testare qualsiasi altra funzionalità. È necessario rendere i [test di penetrazione](../fundamentals/pen-testing.md) parte integrante del processo di compilazione e distribuzione. Pianificare test di sicurezza e analisi delle vulnerabilità periodici sulle applicazioni distribuite, monitorando eventuali porte aperte, endpoint e attacchi.

### <a name="run-security-verification-tests"></a>Eseguire test di verifica della sicurezza

[Secure DevOps Kit per Azure](https://azsk.azurewebsites.net/index.html) (AzSK) contiene test di verifica della sicurezza (SVT) per più servizi della piattaforma Azure. Questi SVT vengono eseguiti periodicamente, per assicurarsi che la sottoscrizione di Azure e le diverse risorse che costituiscono l'applicazione siano in uno stato sicuro. È anche possibile automatizzare questi test usando la funzionalità di integrazione continua/distribuzione continua (CI/CD) di AzSK, che rende disponibili gli SVT come estensione di Visual Studio.

## <a name="next-steps"></a>Passaggi successivi
Negli articoli seguenti si consigliano controlli di sicurezza e attività che consentono di progettare e distribuire applicazioni sicure.

- [Progettare applicazioni sicure](secure-design.md)
- [Distribuire applicazioni sicure](secure-deploy.md)
