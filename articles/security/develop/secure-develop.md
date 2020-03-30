---
title: Sviluppare applicazioni sicure in Microsoft AzureDevelop secure applications on Microsoft Azure
description: In questo articolo vengono illustrate le procedure consigliate da considerare durante le fasi di implementazione e verifica del progetto di applicazione Web.
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
ms.openlocfilehash: 9d98660230e0ab9f4edcd9a7af8a3797106dd17a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255652"
---
# <a name="develop-secure-applications-on-azure"></a>Sviluppare applicazioni sicure in Azure
In questo articolo vengono presentate le attività di sicurezza e i controlli da considerare quando si sviluppano applicazioni per il cloud. Le domande sulla sicurezza e i concetti da considerare durante le fasi di implementazione e verifica del ciclo di vita dello sviluppo della [sicurezza Microsoft (SDL, Microsoft Security Development Lifecycle)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) sono trattati. L'obiettivo è quello di aiutare a definire le attività e i servizi di Azure che è possibile usare per sviluppare un'applicazione più sicura.

In questo articolo vengono illustrate le fasi SDL seguenti:

- Implementazione
- Verifica

## <a name="implementation"></a>Implementazione
L'obiettivo della fase di implementazione è stabilire le procedure consigliate per la prevenzione precoce e rilevare e rimuovere i problemi di sicurezza dal codice.
Si supponga che l'applicazione verrà utilizzata in modi che non si desidera venga utilizzato. Ciò consente di evitare un uso accidentale o intenzionale dell'applicazione.

### <a name="perform-code-reviews"></a>Eseguire revisioni del codice

Prima di archiviare il codice, eseguire [revisioni](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/code-reviews-not-primarily-finding-bugs) del codice per aumentare la qualità complessiva del codice e ridurre il rischio di creare bug. È possibile utilizzare [Visual Studio](https://docs.microsoft.com/azure/devops/repos/tfvc/get-code-reviewed-vs?view=vsts) per gestire il processo di revisione del codice.

### <a name="perform-static-code-analysis"></a>Eseguire l'analisi statica del codice

[L'analisi statica del codice](https://www.owasp.org/index.php/Static_Code_Analysis) (nota anche come analisi del *codice sorgente)* viene in genere eseguita come parte di una revisione del codice. L'analisi statica del codice si riferisce in genere all'esecuzione di strumenti di analisi statica del codice per individuare potenziali vulnerabilità nel codice non in esecuzione utilizzando tecniche come il [controllo taint](https://en.wikipedia.org/wiki/Taint_checking) e [l'analisi](https://en.wikipedia.org/wiki/Data-flow_analysis)del flusso di dati .

Azure Marketplace offre strumenti di [sviluppo](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1&search=code%20review) che eseguono analisi del codice statico e facilitano le revisioni del codice.

### <a name="validate-and-sanitize-every-input-for-your-application"></a>Convalidare e sanificare ogni input per l'applicazione

Considerare tutti gli input come non attendibili per proteggere l'applicazione dalle vulnerabilità più comuni delle applicazioni Web. I dati non attendibili sono un veicolo per gli attacchi di iniezione. L'input per l'applicazione include parametri nell'URL, input dall'utente, dati dal database o da un'API e tutto ciò che viene passato in quanto un utente potrebbe potenzialmente modificare. Un'applicazione deve [verificare](https://www.owasp.org/index.php/OWASP_Proactive_Controls_2016#4:_Validate_All_Inputs) che i dati siano sintattici e semanticamente validi prima che l'applicazione utilizzi i dati in qualsiasi modo (inclusa la visualizzazione all'utente).

Convalidare l'input nelle prime prime fasi del flusso di dati per assicurarsi che solo i dati formati correttamente entrino nel flusso di lavoro. Non si desidera che i dati in formato non corretto persistono nel database o l'attivazione di un malfunzionamento in un componente a valle.

La blacklisting e la whitelist sono due approcci generali per eseguire la convalida della sintassi di input:Blacklisting and whitelisting are two general approaches to performing input syntax validation:

  - La blacklist tenta di verificare che un determinato input dell'utente non contenga contenuto "noto per essere dannoso".

  - L'inserimento nella whitelisting tenta di verificare che un determinato input dell'utente corrisponda a un set di input "noti buoni". L'inserimento nella whitelist basato su caratteri è una forma di whitelisting in cui un'applicazione verifica che l'input dell'utente contenga solo caratteri "noti validi" o che l'input corrisponda a un formato noto.
    Ad esempio, ciò potrebbe comportare la verifica che un nome utente contenga solo caratteri alfanumerici o che contenga esattamente due numeri.

L'inserimento nella whitelist è l'approccio preferito per la creazione di software sicuro.
La blacklisting è soggetta a errori perché è impossibile pensare a un elenco completo di input potenzialmente non valido.

Eseguire questa operazione sul server, non sul lato client (o sul lato server e sul lato client).

### <a name="verify-your-applications-outputs"></a>Verificare gli output dell'applicazioneVerify your application's outputs

Qualsiasi output presente visivamente o all'interno di un documento deve sempre essere codificato e sottoposto a escape. [L'escambio](https://www.owasp.org/index.php/Injection_Theory#Escaping_.28aka_Output_Encoding.29), noto anche come codifica di *output*, viene utilizzato per garantire che i dati non attendibili non siano un veicolo per un attacco di iniezione. L'escaping, combinato con la convalida dei dati, fornisce difese a più livelli per aumentare la sicurezza del sistema nel suo complesso.

L'escaping assicura che tutto venga visualizzato come *output.* L'esecuzione di un'attività consente inoltre all'interprete di sapere che i dati non sono destinati all'esecuzione e questo impedisce il funzionamento degli attacchi. Questa è un'altra tecnica di attacco comune chiamata *cross-site scripting* (XSS).

Se si utilizza un framework web di terze parti, è possibile verificare le opzioni per la codifica dell'output sui siti Web utilizzando il foglio trucco di [prevenzione OWASP XSS](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.md).

### <a name="use-parameterized-queries-when-you-contact-the-database"></a>Utilizzare query con parametri quando si contatta il database

Non creare mai una query di database inline "al volo" nel codice e inviarla direttamente al database. Il codice dannoso inserito nell'applicazione potrebbe causare il furto, la cancellazione o la modifica del database. L'applicazione può essere utilizzata anche per eseguire comandi dannosi del sistema operativo nel sistema operativo che ospita il database.

Utilizzare invece query con parametri o stored procedure. Quando si utilizzano query con parametri, è possibile richiamare la routine dal codice in modo sicuro e passarle una stringa senza preoccuparsi che verrà considerata come parte dell'istruzione di query.

### <a name="remove-standard-server-headers"></a>Rimuovere le intestazioni standard del server

Intestazioni come Server, X-Powered-By e X-AspNet-Version rivelano informazioni sul server e sulle tecnologie sottostanti. Si consiglia di eliminare queste intestazioni per evitare l'impronta digitale dell'applicazione.
Vedere rimozione delle intestazioni dei server standard nei siti Web di [Azure.See removing standard server headers on Azure websites](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/).

### <a name="segregate-your-production-data"></a>Segrega i dati di produzione

I dati di produzione, o dati "reali", non devono essere utilizzati per lo sviluppo, il test o qualsiasi altro scopo oltre a quello previsto dall'azienda. Un set di dati mascherato ([anonimizzato](https://en.wikipedia.org/wiki/Data_anonymization)) deve essere utilizzato per tutti gli sviluppi e test.

Ciò significa che meno persone hanno accesso ai tuoi dati reali, riducendo così la superficie di attacco. Significa anche che meno dipendenti vedono i dati personali, il che elimina una potenziale violazione della riservatezza.

### <a name="implement-a-strong-password-policy"></a>Implementare criteri per le password complesse

Per difendersi da ipotesi basate sulla forza bruta e sul dizionario, è necessario implementare un criterio per le password complesse per garantire che gli utenti creino una password complessa (ad esempio, 12 caratteri di lunghezza minima e che richiedono caratteri alfanumerici e speciali).

È possibile utilizzare un framework di identità per creare e applicare criteri password. Azure AD B2C consente di utilizzare la gestione delle password fornendo [criteri predefiniti,](../../active-directory-b2c/tutorial-create-user-flows.md#create-a-password-reset-user-flow) [la reimpostazione della password self-service](../../active-directory-b2c/user-flow-self-service-password-reset.md)e altro ancora.

Per difendersi dagli attacchi agli account predefiniti, verificare che tutte le chiavi e le password siano sostituibili e che vengano generate o sostituite dopo l'installazione delle risorse.

Se l'applicazione deve generare automaticamente le password, assicurarsi che le password generate siano casuali e che abbiano un'entropia elevata.

### <a name="validate-file-uploads"></a>Convalidare i caricamenti di file

Se l'applicazione consente il caricamento di [file,](https://www.owasp.org/index.php/Unrestricted_File_Upload)prendere in considerazione le precauzioni che è possibile adottare per questa attività rischiosa. Il primo passo in molti attacchi è quello di ottenere un po 'di codice dannoso in un sistema che è sotto attacco. L'utilizzo di un caricamento di file consente all'utente malintenzionato di eseguire questa operazione. OWASP offre soluzioni per la convalida di un file per garantire che il file che stai caricando sia sicuro.

La protezione antimalware consente di identificare e rimuovere virus, spyware e altro software dannoso. È possibile installare [Microsoft Antimalware](../fundamentals/antimalware.md) o la soluzione di protezione degli endpoint di un partner Microsoft ([Trend Micro](https://www.trendmicro.com/azure/), [Broadcom](https://www.broadcom.com/products), [McAfee](https://www.mcafee.com/us/products.aspx), [Windows Defender](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)ed Endpoint [Protection](https://docs.microsoft.com/configmgr/protect/deploy-use/endpoint-protection)).

[Microsoft Antimalware](../fundamentals/antimalware.md) include funzionalità come la protezione in tempo reale, l'analisi pianificata, la correzione malware, gli aggiornamenti delle firme, gli aggiornamenti dei motori, i report degli esempi e la raccolta di eventi di esclusione. È possibile integrare Microsoft Antimalware e soluzioni partner con [Centro sicurezza di Azure](../../security-center/security-center-partner-integration.md) per facilitare la distribuzione e i rilevamenti predefiniti (avvisi ed eventi imprevisti).

### <a name="dont-cache-sensitive-content"></a>Non memorizzare nella cache contenuti sensibili

Non memorizzare nella cache il contenuto sensibile del browser. I browser possono memorizzare informazioni per la memorizzazione nella cache e la cronologia. I file memorizzati nella cache vengono archiviati in una cartella come la cartella File temporanei Internet, nel caso di Internet Explorer. Quando si fa nuovamente riferimento a queste pagine, il browser visualizza le pagine dalla relativa cache. Se le informazioni sensibili (indirizzo, dati della carta di credito, numero di previdenza sociale, nome utente) vengono visualizzate all'utente, le informazioni potrebbero essere memorizzate nella cache del browser ed essere recuperabili esaminando la cache del browser o semplicemente premendo il pulsante **Indietro** del browser.

## <a name="verification"></a>Verifica
La fase di verifica prevede uno sforzo completo per garantire che il codice soddisfi i principi di sicurezza e privacy stabiliti nelle fasi precedenti.

### <a name="find-and-fix-vulnerabilities-in-your-application-dependencies"></a>Individuare e correggere le vulnerabilità nelle dipendenze dell'applicazioneFind and fix vulnerabilities in your application dependencies

L'applicazione e le relative librerie dipendenti vengono scansionate per identificare eventuali componenti vulnerabili noti. I prodotti disponibili per eseguire questa scansione includono [OWASP Dependency Check](https://www.owasp.org/index.php/OWASP_Dependency_Check),[Snyk](https://snyk.io/)e [Black Duck](https://www.blackducksoftware.com/).

L'analisi delle vulnerabilità basata su Tinfoil Security è disponibile per le app Web del servizio app di Azure.Vulnerability scanning powered by [Tinfoil Security](https://www.tinfoilsecurity.com/) is available for Azure App Service Web Apps. La scansione della [sicurezza di Tinfoil tramite App Service](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) offre agli sviluppatori e agli amministratori un mezzo rapido, integrato ed economico per scoprire e affrontare le vulnerabilità prima che un attore malintenzionato possa sfruttarle.

> [!NOTE]
> È anche possibile [integrare la sicurezza Tinfoil con Azure AD.](../../active-directory/saas-apps/tinfoil-security-tutorial.md) L'integrazione di Tinfoil Security con Azure AD offre i vantaggi seguenti:Integrating Tinfoil Security with Azure AD provides you with the following benefits:
>  - In Azure AD, you can control who has access to Tinfoil Security.
>  - Gli utenti possono accedere automaticamente a Tinfoil Security (Single Sign-On) usando i propri account Azure AD.
>  - È possibile gestire gli account in un'unica posizione centrale, il portale di Azure.You can manage your accounts in a single, central location, the Azure portal.

### <a name="test-your-application-in-an-operating-state"></a>Testare l'applicazione in uno stato operativoTest your application in an operating state

Il test di sicurezza dinamico delle applicazioni (DAST, Dynamic Application Security Testing) è un processo di test di un'applicazione in uno stato operativo per individuare le vulnerabilità della sicurezza. Gli strumenti DAST analizzano i programmi durante l'esecuzione per individuare vulnerabilità della sicurezza, ad esempio danneggiamento della memoria, configurazione del server non protetta, cross-site scripting, problemi di privilegi utente, SQL injection e altri problemi critici relativi alla sicurezza.

DAST è diverso dai test di sicurezza delle applicazioni statiche (SAST). Gli strumenti SAST analizzano il codice sorgente o le versioni compilate del codice quando il codice non è in esecuzione per individuare i difetti di sicurezza.

Eseguire DAST, preferibilmente con l'assistenza di un professionista della sicurezza (un tester di [penetrazione](../fundamentals/pen-testing.md) o un valutatore di vulnerabilità). Se un professionista della sicurezza non è disponibile, è possibile eseguire DAST manualmente con uno scanner proxy web e alcuni corsi di formazione. Collegare uno scanner DAST in anticipo per assicurarsi di non introdurre problemi di sicurezza evidenti nel codice. Per un elenco degli scanner per le vulnerabilità delle applicazioni Web, vedere il sito [OWASP.](https://www.owasp.org/index.php/Category:Vulnerability_Scanning_Tools)

### <a name="perform-fuzz-testing"></a>Eseguire test fuzz

Nei [test fuzz,](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/)si induce un errore del programma introducendo deliberatamente dati in formato non corretto o casuali a un'applicazione. L'induzione dell'errore del programma consente di rivelare potenziali problemi di sicurezza prima del rilascio dell'applicazione.

[Security Risk Detection](https://docs.microsoft.com/security-risk-detection/) è il servizio di test fuzz univoco di Microsoft per individuare i bug critici per la sicurezza nel software.

### <a name="conduct-attack-surface-review"></a>Condurre la revisione della superficie di attacco

La revisione della superficie di attacco dopo il completamento del codice consente di verificare che sia stata presa in considerazione qualsiasi modifica di progettazione o implementazione di un'applicazione o di un sistema. Consente di garantire che tutti i nuovi vettori di attacco creati in seguito alle modifiche, inclusi i modelli di minaccia, siano stati rivisti e attenuati.

È possibile creare un'immagine della superficie di attacco eseguendo la scansione dell'applicazione. Microsoft offre uno strumento di analisi della superficie di attacco chiamato [Attack Surface Analyzer](https://www.microsoft.com/download/details.aspx?id=24487). È possibile scegliere tra molti strumenti o servizi di test dinamici e di scansione delle vulnerabilità commerciali, tra cui [OWASP :ed Attack Proxy Project](https://www.owasp.org/index.php/OWASP_Zed_Attack_Proxy_Project), [Arachni](http://arachni-scanner.com/), [Skipfish](https://code.google.com/p/skipfish/)e [w3af](http://w3af.sourceforge.net/). Questi strumenti di analisi eseguono la ricerca per indicizzazione dell'app e mappano le parti dell'applicazione accessibili tramite il Web. È anche possibile cercare in Azure Marketplace strumenti di [sviluppo](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1)simili.

### <a name="perform-security-penetration-testing"></a>Eseguire test di penetrazione della sicurezza

Garantire la sicurezza dell'applicazione è importante quanto testare qualsiasi altra funzionalità. Rendere i test di [penetrazione](../fundamentals/pen-testing.md) una parte standard del processo di compilazione e distribuzione. Pianifica i normali test di sicurezza e l'analisi delle vulnerabilità sulle applicazioni distribuite e monitora le porte, gli endpoint e gli attacchi aperti.

### <a name="run-security-verification-tests"></a>Eseguire test di verifica della sicurezza

[Secure DevOps Kit for Azure](https://azsk.azurewebsites.net/index.html) (AzSK) contiene SVT per più servizi della piattaforma Azure.Secure DevOps Kit for Azure (AzSK) contains SVTs for multiple services of the Azure platform. Eseguire questi SVT periodicamente per assicurarsi che la sottoscrizione di Azure e le diverse risorse che costituiscono l'applicazione siano in uno stato sicuro. È anche possibile automatizzare questi test usando la funzionalità di estensioni di integrazione/distribuzione continua (CI/CD) di AzSK, che rende gli SVT disponibili come estensione di Visual Studio.You can also automate these tests by using the continuous integration/continuous deployment (CI/CD) extensions of AzSK, which makes SVTs available as a Visual Studio extension.

## <a name="next-steps"></a>Passaggi successivi
Negli articoli seguenti sono consigliati i controlli di sicurezza e le attività che consentono di progettare e distribuire applicazioni protette.

- [Progettare applicazioni sicure](secure-design.md)
- [Distribuire applicazioni sicure](secure-deploy.md)
