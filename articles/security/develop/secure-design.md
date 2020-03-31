---
title: Progettare applicazioni sicure in Microsoft AzureDesign secure applications on Microsoft Azure
description: In questo articolo vengono illustrate le procedure consigliate da considerare durante le fasi di progettazione e dei requisiti del progetto di applicazione Web.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 889897cfd4dc8714ae3aea556f0924c9dbcd7825
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299415"
---
# <a name="design-secure-applications-on-azure"></a>Progettare applicazioni sicure in AzureDesign secure applications on Azure
In questo articolo vengono presentate le attività di sicurezza e i controlli da considerare quando si progettano applicazioni per il cloud. Sono illustrate le risorse di formazione e le domande sulla sicurezza e i concetti da considerare durante i requisiti e le fasi di progettazione del ciclo di vita dello sviluppo della [sicurezza Microsoft.](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) L'obiettivo è definire le attività e i servizi di Azure che è possibile usare per progettare un'applicazione più sicura.

In questo articolo vengono illustrate le fasi SDL seguenti:

- Formazione
- Requisiti
- Progettazione

## <a name="training"></a>Formazione
Prima di iniziare a sviluppare l'applicazione cloud, è necessario del tempo per comprendere la sicurezza e la privacy in Azure.Before you begin developing your cloud application, take take time to understand security and privacy on Azure. Eseguendo questo passaggio, è possibile ridurre il numero e la gravità delle vulnerabilità sfruttabili nell'applicazione. Sarete più preparati a reagire in modo appropriato al panorama delle minacce in continua evoluzione.

Usare le risorse seguenti durante la fase di formazione per acquisire familiarità con i servizi di Azure disponibili per gli sviluppatori e con le procedure consigliate per la sicurezza in Azure:Use the following resources during the training stage to familiarze yourself with the Azure services that are available for developers and with security best practices on Azure:

  - La guida per gli sviluppatori di Azure illustra come iniziare a usare [Azure.Developer's guide to Azure](https://azure.microsoft.com/campaigns/developer-guide/) shows you how to get started with Azure. La guida mostra i servizi che è possibile utilizzare per eseguire le applicazioni, archiviare i dati, incorporare informazioni, creare app IoT e distribuire le soluzioni in modo più efficiente e sicuro.

  - [Guida introduttiva per gli sviluppatori](../../guides/developer/azure-developer-guide.md) di Azure fornisce informazioni essenziali per gli sviluppatori che desiderano iniziare a usare la piattaforma Azure per le proprie esigenze di sviluppo.

  - SDK e strumenti descrivono gli strumenti disponibili in [Azure.SDKs](https://docs.microsoft.com/azure/index?pivot=sdkstools) and tools describes the tools that are available on Azure.

  - [Servizi DevOps](https://docs.microsoft.com/azure/devops/) di Azure offre strumenti di collaborazione allo sviluppo. Gli strumenti includono pipeline ad alte prestazioni, repository Git gratuiti, schede Kanban configurabili e test di carico automatizzati e basati su cloud.
    [DevOps Resource Center](https://docs.microsoft.com/azure/devops/learn/) combina le nostre risorse per l'apprendimento delle procedure DevOps, il controllo della versione Git, i metodi agili, il modo in cui lavoriamo con DevOps in Microsoft e come è possibile valutare la propria progressione DevOps.

  - I 5 elementi di sicurezza da considerare prima di [eseguire il push nell'ambiente di produzione](https://docs.microsoft.com/learn/modules/top-5-security-items-to-consider/index?WT.mc_id=Learn-Blog-tajanca) illustrano come proteggere le applicazioni Web in Azure e le app dagli attacchi alle applicazioni Web più comuni e pericolosi.

  - [Secure DevOps Kit for Azure](https://azsk.azurewebsites.net/index.html) è una raccolta di script, strumenti, estensioni e automazioni che soddisfa le esigenze complete di sottoscrizione di Azure e sicurezza delle risorse dei team DevOps che usano un'automazione estesa. Il kit DevOps sicuro per Azure può mostrare come integrare senza problemi la sicurezza nei flussi di lavoro DevOps nativi. Il kit affronta strumenti come i test di verifica della sicurezza (SVT), che possono aiutare gli sviluppatori a scrivere codice sicuro e testare la configurazione sicura delle loro applicazioni cloud nelle fasi di codifica e sviluppo iniziali.

  - Le procedure consigliate per la sicurezza per le [soluzioni di Azure](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions) offrono una raccolta di procedure consigliate per la sicurezza da usare durante la progettazione, distribuzione e gestione delle soluzioni cloud tramite Azure.Security best practices for Azure solutions provides a collection of security best practices to use as you design, deploy, and manage your cloud solutions by using Azure.

## <a name="requirements"></a>Requisiti
La fase di definizione dei requisiti è un passaggio cruciale nella definizione di ciò che l'applicazione è e che cosa eseguirà quando viene rilasciata. La fase dei requisiti è anche un momento per pensare ai controlli di sicurezza che verranno compilati nell'applicazione. Durante questa fase, si inizieranno anche i passaggi da eseguire in tutto il processo SDL per assicurarsi di rilasciare e distribuire un'applicazione protetta.

### <a name="consider-security-and-privacy-issues"></a>Considerare i problemi di sicurezza e privacy
Questa fase è il momento migliore per considerare i problemi fondamentali di sicurezza e privacy. La definizione di livelli accettabili di sicurezza e privacy all'inizio di un progetto consente a un team di:

- Comprendere i rischi associati ai problemi di sicurezza.
- Identificare e correggere i bug di sicurezza durante lo sviluppo.
- Applica livelli stabiliti di sicurezza e privacy durante l'intero progetto.

Quando si scrivono i requisiti per l'applicazione, assicurarsi di considerare i controlli di sicurezza che consentono di proteggere l'applicazione e i dati.

### <a name="ask-security-questions"></a>Porre domande di sicurezza
Poni domande di sicurezza come:

  - La mia applicazione contiene dati sensibili?

  - La mia applicazione raccoglie o memorizza dati che richiedono l'adesione agli standard del settore e ai programmi di conformità come il [Federal Financial Institution Examination Council (FFIEC)](../blueprints/ffiec-analytics-overview.md) o il Payment Card Industry Data Security Standards [(PCI DSS)](../blueprints/pcidss-analytics-overview.md)?

  - La mia domanda raccoglie o contiene dati personali o dei clienti sensibili che possono essere utilizzati, da soli o con altre informazioni, per identificare, contattare o individuare una singola persona?

  - La mia applicazione raccoglie o contiene dati che possono essere utilizzati per accedere alle informazioni mediche, didattiche, finanziarie o di lavoro di un individuo? L'identificazione della riservatezza dei dati durante la fase dei requisiti consente di classificare i dati e identificare il metodo di protezione dei dati da utilizzare per l'applicazione.

  - Dove e come vengono memorizzati i miei dati? Valutare come monitorare i servizi di archiviazione utilizzati dall'applicazione per eventuali modifiche impreviste, ad esempio tempi di risposta più lenti. Sarai in grado di influenzare la registrazione per raccogliere dati più dettagliati e analizzare un problema in profondità?

  - La mia domanda sarà disponibile al pubblico (su Internet) o solo internamente? Se l'applicazione è disponibile al pubblico, come è possibile proteggere i dati che potrebbero essere raccolti dall'utilizzo nel modo corretto? Se l'applicazione è disponibile solo internamente, considerare chi nell'organizzazione deve avere accesso all'applicazione e per quanto tempo devono avere accesso.

  - Si comprende il modello di identità prima di iniziare la progettazione dell'applicazione? Come si determinerà che gli utenti sono chi dicono di essere e che cosa un utente è autorizzato a fare?

  - La mia domanda esegue attività sensibili o importanti (come trasferire denaro, sbloccare porte o fornire medicinali)?
    Valutare come si convalida che l'utente che esegue un'attività sensibile è autorizzato a eseguire l'attività e come verrà autenticato che la persona è chi si dice di essere. L'autorizzazione (Auth) è l'atto di concedere a un'entità di sicurezza autenticata l'autorizzazione per eseguire un'operazione. L'autenticazione (AuthN) è l'atto di sfidare una parte per le credenziali legittime.

  - La mia applicazione esegue attività software rischiose, come consentire agli utenti di caricare o scaricare file o altri dati? Se l'applicazione esegue attività rischiose, considerare il modo in cui l'applicazione proteggerà gli utenti dalla gestione di dati o file dannosi.

### <a name="review-owasp-top-10"></a>Recensione OWASP top 10
Valutare la possibilità di esaminare i 10 rischi per la sicurezza delle applicazioni [<span class="underline">di OWASP.</span>](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
OWASP Top 10 risolve i rischi critici per la sicurezza per le applicazioni web.
La consapevolezza di questi rischi per la sicurezza può aiutare a prendere decisioni sui requisiti e sulla progettazione che riducono al minimo tali rischi nell'applicazione.

Pensare ai controlli di sicurezza per prevenire le violazioni è importante.
Tuttavia, si desidera anche presupporre che si verificherà [una violazione.](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/security-in-devops) Supponendo che una violazione aiuta a rispondere ad alcune domande importanti sulla sicurezza in anticipo, in modo che non devono essere risolte in caso di emergenza:

  - Come posso rilevare un attacco?

  - Cosa devo fare in caso di attacco o violazione?

  - Come posso riprendermi dall'attacco come perdite di dati o manomissioni?

## <a name="design"></a>Progettazione

La fase di progettazione è fondamentale per stabilire le procedure consigliate per la progettazione e le specifiche funzionali. È inoltre fondamentale per l'esecuzione di analisi dei rischi che consente di ridurre i problemi di sicurezza e privacy in tutto il progetto.

Quando si dispone di requisiti di sicurezza e si utilizzano concetti di progettazione sicura, è possibile evitare o ridurre al minimo le opportunità per un difetto di sicurezza. Un difetto di sicurezza è una svista nella progettazione dell'applicazione che potrebbe consentire a un utente di eseguire azioni dannose o impreviste dopo il rilascio dell'applicazione.

Durante la fase di progettazione, anche pensare a come è possibile applicare la sicurezza a strati; un livello di difesa non è necessariamente sufficiente. Cosa succede se un utente malintenzionato supera il firewall dell'applicazione Web (WAF)? Vuoi un altro controllo di sicurezza in atto per difenderti da quell'attacco.

Tenendo presente questo aspetto, vengono illustrati i seguenti concetti di progettazione sicura e i controlli di sicurezza che è necessario affrontare quando si progettano applicazioni protette:

- Utilizzare una libreria di codifica sicura e un framework software.
- Eseguire la scansione dei componenti vulnerabili.
- Utilizzare la modellazione delle minacce durante la progettazione dell'applicazione.
- Riduci la superficie di attacco.
- Adottare un criterio di identità come perimetro di sicurezza primario.
- Richiedere una riautenticazione per le transazioni importanti.
- Usare una soluzione di gestione delle chiavi per proteggere chiavi, credenziali e altri segreti.
- Proteggere i dati sensibili.
- Implementare misure di sicurezza indistaluogo.
- Sfrutta i vantaggi della gestione degli errori e delle eccezioni.
- Utilizzare la registrazione e gli avvisi.

### <a name="use-a-secure-coding-library-and-a-software-framework"></a>Utilizzare una libreria di codifica sicura e un framework software

Per lo sviluppo, utilizzare una libreria di codifica sicura e un framework software con sicurezza incorporata. Gli sviluppatori possono utilizzare funzionalità esistenti e collaudate (crittografia, servizi igienico-sanitari di input, codifica di output, chiavi o stringhe di connessione e qualsiasi altra cosa che verrebbe considerata un controllo di sicurezza) invece di sviluppare controlli di sicurezza da zero. Ciò consente di proteggersi da difetti di progettazione e implementazione relativi alla sicurezza.

Assicurarsi di utilizzare la versione più recente del framework e tutte le funzionalità di sicurezza disponibili nel framework. Microsoft offre un set completo [di strumenti di sviluppo](https://azure.microsoft.com/product-categories/developer-tools/) per tutti gli sviluppatori, lavorando su qualsiasi piattaforma o linguaggio, per fornire applicazioni cloud. È possibile codificare con la lingua desiderata scegliendo tra vari [SDK.](https://azure.microsoft.com/downloads/)
È possibile sfruttare gli ambienti di sviluppo integrato (IDE) completi e gli editor con funzionalità di debug avanzate e il supporto incorporato di Azure.You can take advantage of full-featured integrated development environments (IDEs) and editors that have advanced debugging capabilities and built-in Azure support.

Microsoft offre un'ampia gamma di linguaggi, framework e strumenti che è possibile usare per sviluppare applicazioni in Azure.Microsoft offers a variety of [languages, frameworks, and tools](https://docs.microsoft.com/azure/index?pivot=sdkstools&panel=sdkstools-all) that you can use to develop applications on Azure. Un esempio è [Azure per gli sviluppatori .NET e .NET Core.](https://docs.microsoft.com/dotnet/azure/) Per ogni linguaggio e framework che offriamo, troverai guide introduttive, esercitazioni e riferimenti API per aiutarti a iniziare rapidamente.

Azure offre un'ampia gamma di servizi che è possibile usare per ospitare siti Web e applicazioni Web. Questi servizi consentono di sviluppare nel linguaggio preferito, che si tratti di .NET, .NET Core, Java, Ruby, Node.js, PHP o Python.
[App web app di Azure](../../app-service/overview.md) (app Web) è uno di questi servizi.

App Web aggiunge la potenza di Microsoft Azure all'applicazione. Include sicurezza, bilanciamento del carico, scalabilità automatica e gestione automatizzata. È anche possibile sfruttare le funzionalità DevOps nelle app Web, ad esempio la gestione dei pacchetti, gli ambienti di gestione temporanea, i domini personalizzati, i certificati SSL/TLS e la distribuzione continua da Azure DevOps, GitHub, Docker Hub e altre origini.

Azure offre altri servizi che è possibile usare per ospitare siti Web e applicazioni Web. Per la maggior parte degli scenari App Web è la scelta migliore. Per un'architettura di microservizio, si consideri [Azure Service Fabric.](https://azure.microsoft.com/documentation/services/service-fabric)
Se è necessario un maggiore controllo sulle VM in cui viene eseguito il codice, prendere in considerazione l'uso delle [Macchine virtuali di Azure](https://azure.microsoft.com/documentation/services/virtual-machines/).
Per altre informazioni su come scegliere tra questi servizi di Azure, vedere un [confronto tra Servizio app di Azure, Macchine virtuali, Infrastruttura servizio e Servizi cloud.](/azure/architecture/guide/technology-choices/compute-decision-tree)

### <a name="apply-updates-to-components"></a>Applicare gli aggiornamenti ai componenti

Per evitare vulnerabilità, è necessario eseguire continuamente l'inventario dei componenti lato client e lato server (ad esempio framework e librerie) e delle relative dipendenze per gli aggiornamenti. Le nuove vulnerabilità e le versioni aggiornate del software vengono rilasciate continuamente. Assicurarsi di disporre di un piano continuo per monitorare, valutare e applicare aggiornamenti o modifiche di configurazione alle librerie e ai componenti utilizzati.

Per suggerimenti sugli strumenti, vedere la pagina [OWASP (Open Web Application Security Project)](https://www.owasp.org/index.php/Main_Page) [sull'utilizzo di componenti con vulnerabilità note.](https://www.owasp.org/index.php/Top_10-2017_A9-Using_Components_with_Known_Vulnerabilities) È inoltre possibile sottoscrivere avvisi di posta elettronica per le vulnerabilità di sicurezza correlate ai componenti utilizzati.

### <a name="use-threat-modeling-during-application-design"></a>Usare la modellazione delle minacce durante la progettazione delle applicazioni

La modellazione delle minacce è il processo di identificazione di potenziali minacce alla sicurezza per l'azienda e l'applicazione e quindi di garantire che siano state verificate soluzioni di attenuazione appropriate. Il processo SDL specifica che i team devono impegnarsi nella modellazione delle minacce durante la fase di progettazione, quando la risoluzione di potenziali problemi è relativamente semplice ed economica. L'utilizzo della modellazione delle minacce in fase di progettazione può ridurre notevolmente il costo totale di sviluppo.

Per facilitare il processo di modellazione delle minacce, abbiamo progettato [lo strumento di modellazione delle minacce SDL](threat-modeling-tool.md) tenendo presente gli esperti non relativi alla sicurezza. Questo strumento semplifica la modellazione delle minacce per tutti gli sviluppatori fornendo indicazioni chiare su come creare e analizzare i modelli di minaccia.

La modellazione della progettazione dell'applicazione e l'enumerazione delle minacce [STRIDE(](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy) Spoofing, Tampering, Repudiation, Information Disclosure, Denial of Service e Elevation of Privilege) attraverso tutti i limiti di attendibilità si è dimostrata un modo efficace per rilevare gli errori di progettazione nelle prime fasi. The following table lists the STRIDE threats and gives some example mitigations that use features provided by Azure. Queste mitigazioni dei rischi non funzioneranno in tutte le situazioni.

| Threat | Proprietà di sicurezza | Potenziale mitigazione della piattaforma Azure |
| ---------------------- | --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Spoofing               | Authentication        | [Richiedi connessioni HTTPS](https://docs.microsoft.com/aspnet/core/security/enforcing-ssl?view=aspnetcore-2.1&tabs=visual-studio). |
| Manomissione              | Integrità             | Convalidare i certificati SSL/TLS. Le applicazioni che utilizzano SSL/TLS devono verificare completamente i certificati X.509 delle entità a cui si connettono. Usare i certificati dell'insieme di chiavi di Azure per [gestire i certificati x509.](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-certificates) |
| Ripudio            | Non ripudio       | Abilitazione del [monitoraggio e diagnostica](https://docs.microsoft.com/azure/architecture/best-practices/monitoring) di Azure.|
| Divulgazione di informazioni | Riservatezza       | Crittografare i dati sensibili [inattivi](../fundamentals/encryption-atrest.md) e [in transito](../fundamentals/data-encryption-best-practices.md#protect-data-in-transit). |
| Denial of Service      | Disponibilità          | Monitorare le metriche delle prestazioni per le potenziali condizioni di rifiuto del servizio. Implementare i filtri di connessione. [La protezione DDoS](../../virtual-network/ddos-protection-overview.md#next-steps)di Azure, combinata con le procedure consigliate per la progettazione delle applicazioni, fornisce difesa contro gli attacchi DDoS.|
| Elevazione dei privilegi | Autorizzazione         | Usare Gestione <span class="underline"> </span> [identità con privilegi](../../active-directory/privileged-identity-management/pim-configure.md)di Azure Active Directory .|

### <a name="reduce-your-attack-surface"></a>Riduci la superficie di attacco

Una superficie di attacco è la somma totale di dove potrebbero verificarsi potenziali vulnerabilità. In questo articolo, ci concentriamo sulla superficie di attacco di un'applicazione.
L'obiettivo è proteggere un'applicazione da attacchi. Un modo semplice e rapido per ridurre al minimo la superficie di attacco consiste nel rimuovere le risorse e il codice inutilizzati dall'applicazione. Più piccola è l'applicazione, minore è la superficie di attacco. Ad esempio, rimuovere:

- Codice per le funzionalità non ancora rilasciate.
- Debug del codice di supporto.
- Interfacce di rete e protocolli che non vengono utilizzati o che sono stati deprecati.Network interfaces and protocols that aren't used or which have been deprecated.
- Macchine virtuali e altre risorse non in uso.

Fare pulizia regolare delle risorse e garantire che si rimuove il codice inutilizzato sono ottimi modi per garantire che ci sono meno opportunità per gli attori maligni di attaccare.

Un modo più dettagliato e approfondito per ridurre la superficie di attacco consiste nel completare un'analisi della superficie di attacco. Un'analisi della superficie di attacco consente di mappare le parti di un sistema che devono essere esaminate e testate per verificarne la vulnerabilità.

Lo scopo di un'analisi della superficie di attacco è comprendere le aree di rischio in un'applicazione in modo che gli sviluppatori e gli specialisti della sicurezza siano consapevoli di quali parti dell'applicazione sono aperte agli attacchi. Quindi, è possibile trovare modi per ridurre al minimo questo potenziale, tenere traccia di quando e come cambia la superficie di attacco e cosa questo significa dal punto di vista del rischio.

Un'analisi della superficie di attacco consente di identificare:

- Funzioni e parti del sistema che è necessario esaminare e testare le vulnerabilità di sicurezza.
- Aree di codice ad alto rischio che richiedono una protezione approfondita (parti del sistema da difendere).
- Quando si modifica la superficie di attacco e si desidera aggiornare una valutazione delle minacce.

La riduzione delle opportunità per gli aggressori di sfruttare un potenziale punto debole o vulnerabilità richiede di analizzare a fondo la superficie di attacco complessiva dell'applicazione. Include anche la disabilitazione o la limitazione dell'accesso ai servizi di sistema, l'applicazione del principio dei privilegi minimi e l'impiego di difese a più livelli, ove possibile.

Discuteremo di condurre una revisione della superficie di [attacco](secure-develop.md#conduct-attack-surface-review) durante la fase di verifica del processo SDL.

> [!NOTE]
> **Qual è la differenza tra la modellazione delle minacce e l'analisi della superficie di attacco?**
La modellazione delle minacce è il processo di identificazione di potenziali minacce alla sicurezza per l'applicazione e di garantire che siano in atto soluzioni corretti contro le minacce. L'analisi della superficie di attacco identifica le aree di codice ad alto rischio che sono aperte agli attacchi. Si tratta di trovare modi per difendere le aree ad alto rischio dell'applicazione e di esaminare e testare tali aree di codice prima di distribuire l'applicazione.

### <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>Adottare dei criteri di identità come perimetro di sicurezza primario

Quando si progettano applicazioni cloud, è importante espandere l'attenzione del perimetro di sicurezza da un approccio incentrato sulla rete a un approccio incentrato sull'identità. Storicamente, il perimetro di sicurezza locale primario era la rete di un'organizzazione. La maggior parte delle progettazioni di sicurezza locali usa la rete come pivot di sicurezza primario. Per le applicazioni cloud, è consigliabile considerare l'identità come perimetro di sicurezza primario.

Operazioni che è possibile eseguire per sviluppare un approccio incentrato sull'identità per lo sviluppo di applicazioni Web:

- Applicare l'autenticazione a più fattori per gli utenti.
- usare piattaforme di autenticazione e autorizzazione avanzate.
- Applicare il principio dei privilegi minimi.
- Implementare l'accesso just-in-time.

#### <a name="enforce-multi-factor-authentication-for-users"></a>Applicare l'autenticazione a più fattori per gli utentiEnforce multi-factor authentication for users

Usare l'autenticazione a due fattori. L'autenticazione a due fattori è lo standard corrente per l'autenticazione e l'autorizzazione perché evita i punti deboli della sicurezza intrinseci nei tipi di autenticazione nome utente e password. L'accesso alle interfacce di gestione di Azure (portale di Azure/PowerShell remoto) e ai servizi rivolti ai clienti deve essere progettato e configurato per l'uso di [Azure Multi-Factor Authentication.](../../active-directory/authentication/concept-mfa-howitworks.md)

#### <a name="use-strong-authentication-and-authorization-platforms"></a>Usare piattaforme di autenticazione e autorizzazione robuste

Usare i meccanismi di autenticazione e autorizzazione forniti dalla piattaforma invece di un codice personalizzato. Ciò è dovuto al fatto che lo sviluppo di codice di autenticazione personalizzato può essere soggetto a errori. Il codice commerciale (ad esempio, da Microsoft) viene spesso ampiamente rivisto per motivi di sicurezza. [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) è la soluzione di Microsoft Azure per la gestione delle identità e degli accessi. Questi strumenti e servizi di Azure AD aiutano nello sviluppo sicuro:These Azure AD tools and services help with secure development:

- [La piattaforma di identità di Azure AD (Azure AD per sviluppatori)](../../active-directory/develop/about-microsoft-identity-platform.md) è un servizio di identità cloud usato dagli sviluppatori per creare app che accedono in modo sicuro agli utenti. Azure AD assiste gli sviluppatori che creano app line-of-business e sviluppatori single-tenant che desiderano sviluppare app multi-tenant. Oltre all'accesso di base, le app create tramite Azure AD possono chiamare le API Microsoft e le API personalizzate basate sulla piattaforma Azure AD. La piattaforma di identità di Azure AD supporta protocolli standard del settore come OAuth 2.0 e OpenID Connect.The Azure AD identity platform supports industry-standard protocols like OAuth 2.0 and OpenID Connect.

- [Azure Active Directory B2C (Azure AD B2C)](../../active-directory-b2c/index.yml) è un servizio di gestione delle identità che è possibile usare per personalizzare e controllare il modo in cui i clienti si iscrivono, accedono e gestiscono i propri profili quando usano le applicazioni. Sono incluse, tra gli altri, le applicazioni sviluppate per iOS, Android e .NET. Azure AD B2C abilita queste azioni proteggendo al contempo le identità dei clienti.

#### <a name="apply-the-principle-of-least-privilege"></a>Applicare il principio dei privilegi minimi

Il concetto di [privilegio minimo](https://en.wikipedia.org/wiki/Principle_of_least_privilege) significa dare agli utenti il livello preciso di accesso e controllo di cui hanno bisogno per svolgere il loro lavoro e niente di più.

Uno sviluppatore di software avrebbe bisogno dei diritti di amministratore di dominio? Un assistente amministrativo dovrebbe accedere ai controlli amministrativi sul proprio personal computer? Valutare l'accesso al software non è diverso. Se si utilizza il controllo degli accessi [in base](../../role-based-access-control/overview.md) al ruolo per offrire agli utenti capacità e autorizzazioni diverse nell'applicazione, non si concederebbe a tutti l'accesso a tutto. Limitando l'accesso a ciò che è necessario per ogni ruolo, si limita il rischio che si verifichi un problema di sicurezza.

Assicurarsi che l'applicazione applichi privilegi [minimi](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models#in-applications) in tutti i modelli di accesso.

> [!NOTE]
>  Le regole di privilegio minimo devono essere applicate al software e alle persone che creano il software. Gli sviluppatori di software possono essere un rischio enorme per la sicurezza IT se hanno accesso troppo. Le conseguenze possono essere gravi se uno sviluppatore ha intenti dannosi o viene dato troppo accesso. È consigliabile applicare le regole dei privilegi minimi agli sviluppatori durante l'intero ciclo di vita dello sviluppo.

#### <a name="implement-just-in-time-access"></a>Implementare l'accesso just-in-time

Implementare l'accesso *Just-In-Time* (JIT) per ridurre ulteriormente il tempo di esposizione dei privilegi. Usare [La gestione delle identità con privilegi di Azure AD](../../active-directory/users-groups-roles/directory-admin-roles-secure.md#stage-3-build-visibility-and-take-full-control-of-admin-activity) per:Use Azure AD Privileged Identity Management to:

- Concedere agli utenti le autorizzazioni necessarie solo.
- Assegnare i ruoli per un periodo di tempo ridotto sapendo che i privilegi vengono revocati automaticamente.

### <a name="require-re-authentication-for-important-transactions"></a>Richiedere la riautenticazione per le transazioni importantiRequire re-authentication for important transactions

[La contraffazione](https://docs.microsoft.com/aspnet/core/security/anti-request-forgery?view=aspnetcore-2.1) delle richieste intersito (nota anche come *XSRF* o *CSRF)* è un attacco contro le app ospitate sul Web in cui un'app Web dannosa influenza l'interazione tra un browser client e un'app Web attendibile per tale browser. Gli attacchi di contraffazione delle richieste tra siti sono possibili perché i browser Web inviano automaticamente alcuni tipi di token di autenticazione ad ogni richiesta a un sito Web.
Questa forma di sfruttamento è anche conosciuta come un *attacco con un clic* o una sessione di *guida* perché l'attacco sfrutta la sessione precedentemente autenticata dell'utente.

Il modo migliore per difendersi da questo tipo di attacco è chiedere all'utente qualcosa che solo l'utente può fornire prima di ogni transazione importante, come un acquisto, la disattivazione dell'account o una modifica della password. È possibile chiedere all'utente di immettere nuovamente la password, completare un captcha o inviare un token segreto che solo l'utente avrebbe. L'approccio più comune è il token segreto.

### <a name="use-a-key-management-solution-to-secure-keys-credentials-and-other-secrets"></a>Usare una soluzione di gestione delle chiavi per proteggere chiavi, credenziali e altri segretiUse a key management solution to secure keys, credentials, and other secrets

Perdere chiavi e credenziali è un problema comune. un rischio ben peggiore della perdita di chiavi e credenziali consiste negli accessi non autorizzati. Gli aggressori possono sfruttare le tecniche automatizzate e manuali per trovare chiavi e segreti che vengono memorizzati in archivi di codice come GitHub. Non inserire chiavi e segreti in questi repository di codice pubblico o in qualsiasi altro server.

Inserisci sempre le chiavi, i certificati, i segreti e le stringhe di connessione in una soluzione di gestione delle chiavi. È possibile utilizzare una soluzione centralizzata in cui chiavi e segreti vengono archiviati in moduli di sicurezza hardware (HSM). Azure offre un HSM nel cloud con L'insieme di credenziali delle chiavi di [Azure.Azure](../../key-vault/key-vault-overview.md)provides you with an HSM in the cloud with Azure Key Vault .

Key Vault è un *archivio segreto:* è un servizio cloud centralizzato per l'archiviazione dei segreti delle applicazioni. Key Vault mantiene i tuoi dati riservati al sicuro mantenendo i segreti dell'applicazione in un'unica posizione centrale e fornendo accesso sicuro, controllo delle autorizzazioni e registrazione dell'accesso.

I segreti sono *memorizzati*in singoli vault . Ogni insieme di credenziali ha i propri criteri di configurazione e sicurezza per controllare l'accesso. È possibile accedere ai dati tramite un'API REST o un SDK client disponibile per la maggior parte dei linguaggi di programmazione.

> [!IMPORTANT]
> L'insieme di credenziali delle chiavi di Azure è progettato per archiviare i segreti di configurazione per le applicazioni server. Non è destinato all'archiviazione di dati appartenenti agli utenti dell'app. Ciò si riflette nelle caratteristiche di prestazioni, nell'API e nel modello di costi.
>
> I dati utente devono essere archiviati altrove, ad esempio in un'istanza del database SQL di Azure con Transparent Data Encryption (TDE) o in un account di archiviazione che usa Crittografia del servizio di archiviazione di Azure.User data should be stored elsewhere, like in an Azure SQL Database instance that has Transparent Data Encryption (TDE) or in a storage account that uses Azure Storage Service Encryption. I segreti usati dall'applicazione per accedere a questi archivi dati possono essere conservati nell'insieme di credenziali delle chiavi di Azure.Secrets that are used by your application to access these data stores can be kept in Azure Key Vault.

### <a name="protect-sensitive-data"></a>Proteggere i dati sensibili

La protezione dei dati è una parte essenziale della strategia di sicurezza.
La classificazione dei dati e l'identificazione delle esigenze di protezione dei dati consentono di progettare l'app tenendo presente la sicurezza dei dati. La classificazione (categorizzazione) dei dati archiviati in base alla riservatezza e all'impatto aziendale consente agli sviluppatori di determinare i rischi associati ai dati.

Etichetta tutti i dati applicabili come sensibili quando progetti i formati dati. Assicurarsi che l'applicazione consideri i dati applicabili come sensibili. Queste procedure possono aiutarti a proteggere i tuoi dati sensibili:

- Utilizzare la crittografia.
- Evita segreti hardcoded come chiavi e password.
- Assicurarsi che i controlli di accesso e il controllo siano in atto.

#### <a name="use-encryption"></a>Utilizzare la crittografia

La protezione dei dati dovrebbe essere una parte essenziale della strategia di sicurezza.
Se i dati sono archiviati in un database o se si spostano avanti e indietro tra le posizioni, usare la crittografia dei [dati inattivi](../fundamentals/encryption-atrest.md) (nel database) e la crittografia dei [dati in transito](../fundamentals/data-encryption-best-practices.md#protect-data-in-transit) (in viaggio da e verso l'utente, il database, un'API o l'endpoint del servizio). Si consiglia di utilizzare sempre i protocolli SSL/TLS per lo scambio di dati. Assicurarsi di utilizzare la versione più recente di TLS per la crittografia (attualmente, questa è la versione 1.2).

#### <a name="avoid-hard-coding"></a>Evitare hardcoded

Alcune cose non dovrebbero mai essere hardcoded nel software. Alcuni esempi sono nomi host o indirizzi IP, URL, indirizzi di posta elettronica, nomi utente, password, chiavi dell'account di archiviazione e altre chiavi di crittografia. Valutare la possibilità di implementare requisiti relativi a ciò che può o non può essere hardcoded nel codice, incluse le sezioni di commento del codice.

Quando si inseriscono commenti nel codice, assicurarsi di non salvare le informazioni riservate. Sono inclusi l'indirizzo di posta elettronica, le password, le stringhe di connessione, le informazioni sull'applicazione che verrebbero conosciute solo da un utente dell'organizzazione e qualsiasi altra cosa che potrebbe offrire a un utente malintenzionato un vantaggio nell'attaccare l'applicazione o l'organizzazione .

Fondamentalmente, si supponga che tutti gli elementi nel progetto di sviluppo saranno di dominio pubblico quando viene distribuito. Evitare di includere dati sensibili di qualsiasi tipo nel progetto.

In precedenza, è stato illustrato [L'insieme di credenziali delle chiavi](../../key-vault/key-vault-overview.md)di Azure.Earlier, we discussed Azure Key Vault . È possibile utilizzare Key Vault per archiviare segreti come chiavi e password invece di codice a livello di codice. Quando si usa l'insieme di credenziali delle chiavi in combinazione con le identità gestite per le risorse di Azure, l'app Web di Azure può accedere ai valori di configurazione dei segreti in modo semplice e sicuro senza archiviare segreti nel controllo del codice sorgente o nella configurazione. Per altre informazioni, vedere Gestire i segreti nelle app server con l'insieme di credenziali delle chiavi di Azure.To learn more, see [Manage secrets in your server apps with Azure Key Vault](https://docs.microsoft.com/learn/modules/manage-secrets-with-azure-key-vault/).

### <a name="implement-fail-safe-measures"></a>Implementare misure di sicurezza per i guasti

L'applicazione deve essere in grado di gestire gli [errori](https://docs.microsoft.com/dotnet/standard/exceptions/) che si verificano durante l'esecuzione in modo coerente. L'applicazione deve intercettare tutti gli errori e non riuscire sicuro o chiuso.

È inoltre necessario assicurarsi che gli errori vengano registrati con un contesto utente sufficiente per identificare attività sospette o dannose. I registri devono essere conservati per un tempo sufficiente per consentire un'analisi forense ritardata. I log devono essere in un formato facilmente utilizzato da una soluzione di gestione dei log. Assicurarsi che vengano attivati avvisi per gli errori correlati alla sicurezza. La registrazione e il monitoraggio insufficienti consentono agli utenti malintenzionati di attaccare ulteriormente i sistemi e mantenere la persistenza.

### <a name="take-advantage-of-error-and-exception-handling"></a>Sfruttare la gestione degli errori e delle eccezioni

L'implementazione della corretta gestione degli errori e delle [eccezioni](https://docs.microsoft.com/dotnet/standard/exceptions/best-practices-for-exceptions) è una parte importante della codifica difensiva. La gestione degli errori e delle eccezioni è fondamentale per rendere un sistema affidabile e sicuro. Gli errori nella gestione degli errori possono portare a diversi tipi di vulnerabilità della sicurezza, ad esempio la perdita di informazioni agli utenti malintenzionati e aiutare gli utenti malintenzionati a comprendere meglio la piattaforma e la progettazione.

Assicurarsi che:

- Le eccezioni vengono gestite in modo centralizzato per evitare [blocchi try/catch](https://docs.microsoft.com/dotnet/standard/exceptions/how-to-use-the-try-catch-block-to-catch-exceptions) duplicati nel codice.

- Tutti i comportamenti imprevisti vengono gestiti all'interno dell'applicazione.

- I messaggi visualizzati agli utenti non perdono dati critici, ma forniscono informazioni sufficienti per spiegare il problema.

- Le eccezioni vengono registrate e forniscono informazioni sufficienti per l'analisi da parte dei team di analisi forense o di risposta agli incidenti.

[App per la logica](../../logic-apps/logic-apps-overview.md) di Azure offre un'esperienza di prima classe per [la gestione di errori ed eccezioni](../../logic-apps/logic-apps-exception-handling.md) causati da sistemi dipendenti. È possibile usare app per la logica per creare flussi di lavoro per automatizzare attività e processi che integrano app, dati, sistemi e servizi tra aziende e organizzazioni.

### <a name="use-logging-and-alerting"></a>Usare la registrazione e gli avvisiUse logging and alerting

[Registra](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1) i problemi di sicurezza per le indagini di sicurezza e attiva avvisi sui problemi per assicurarsi che le persone sappiano tempestivamente dei problemi. Abilitare il controllo e la registrazione su tutti i componenti. I log di controllo devono acquisire il contesto utente e identificare tutti gli eventi importanti.

Verifica di non registrare i dati sensibili che un utente invia al tuo sito. Esempi di dati sensibili:

- Credenziali dell'utente
- Numeri di previdenza sociale o altre informazioni di identificazione
- Numeri di carta di credito o altre informazioni finanziarie
- Informazioni sulla salute
- Chiavi private o altri dati che possono essere utilizzati per decrittografare le informazioni crittografate
- Informazioni sul sistema o sull'applicazione che possono essere usate per attaccare l'applicazione in modo più efficace

Assicurarsi che l'applicazione monitori gli eventi di gestione degli utenti, ad esempio accessi utente riusciti e non riusciti, reimpostazione della password, modifiche della password, blocco account e registrazione utente. La registrazione per questi eventi consente di rilevare e reagire a comportamenti potenzialmente sospetti. Consente inoltre di raccogliere i dati delle operazioni, ad esempio chi accede all'applicazione.

## <a name="next-steps"></a>Passaggi successivi
Negli articoli seguenti sono consigliati i controlli di sicurezza e le attività che consentono di sviluppare e distribuire applicazioni protette.

- [Sviluppare applicazioni sicure](secure-develop.md)
- [Distribuire applicazioni sicure](secure-deploy.md)
