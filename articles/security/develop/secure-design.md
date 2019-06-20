---
title: Progettazione di applicazioni sicure in Microsoft Azure
description: Questo articolo illustra le procedure consigliate da prendere in considerazione durante le fasi di progettazione di un requisito del progetto di applicazione web.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: b637f547de06d9347a1c74ad7da560be97f16881
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144438"
---
# <a name="design-secure-applications-on-azure"></a>Progettazione di applicazioni sicure in Azure
In questo articolo ti presentiamo le attività di sicurezza e controlli da considerare quando si progettano applicazioni per il cloud. Risorse e concetti per prendere in considerazione durante i requisiti e la progettazione delle fasi di Microsoft e domande di sicurezza per il training [Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) vengono analizzati. L'obiettivo è che consentono di definire le attività e i servizi di Azure che è possibile utilizzare per progettare un'applicazione più sicura.

Le seguenti fasi SDL sono illustrate in questo articolo:

- Formazione
- Requisiti
- Progettazione

## <a name="training"></a>Formazione
Prima di iniziare lo sviluppo dell'applicazione cloud, è opportuno per informazioni sulla sicurezza e privacy in Azure. Adottando questo passaggio, è possibile ridurre il numero e la gravità delle vulnerabilità sfruttabili nell'applicazione. Sarà più preparati a rispondere in modo appropriato al panorama delle minacce in continua evoluzione.

Usare le risorse seguenti durante la fase di formazione per acquisire familiarità con i servizi di Azure disponibili per gli sviluppatori e alle procedure consigliate di sicurezza in Azure:

  - [Guida per sviluppatori Azure](https://azure.microsoft.com/campaigns/developer-guide/) illustra come iniziare a usare Azure. La guida illustra quali servizi è possibile usare per eseguire le applicazioni archiviano i dati, incorporare l'intelligenza, crea App IoT e distribuire le soluzioni in modo più efficiente e sicuro.

  - [Guida introduttiva per sviluppatori Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide) fornisce informazioni essenziali per gli sviluppatori che desiderano iniziare a usare la piattaforma Azure per esigenze di relativi allo sviluppo.

  - [SDK e strumenti](https://docs.microsoft.com/azure/index#pivot=sdkstools) vengono descritti gli strumenti disponibili in Azure.

  - [Servizi di Azure DevOps](https://docs.microsoft.com/azure/devops/?view=vsts#pivot=index&panel=indexA) fornisce gli strumenti di collaborazione di sviluppo. Gli strumenti includono le pipeline a prestazioni elevate, repository Git gratuiti, lavagne Kanban configurabili e il test di carico automatizzato e basato sul cloud completo.
    Il [Centro risorse di DevOps](https://docs.microsoft.com/azure/devops/learn/) combina le nostre risorse di apprendimento DevOps procedure consigliate, Git controllo della versione, le metodologie agile, come lavoriamo con DevOps presso Microsoft e come è possibile valutare il proprio progressione DevOps.

  - [Primi 5 elementi di sicurezza da considerare prima di eseguire il push in produzione](https://docs.microsoft.com/learn/modules/top-5-security-items-to-consider/index?WT.mc_id=Learn-Blog-tajanca) illustra come proteggere le applicazioni web in Azure e per proteggere le app contro gli attacchi di applicazioni web più comuni e pericoloso.

  - [Secure DevOps Kit per Azure](https://azsk.azurewebsites.net/index.html) è una raccolta di script, strumenti, estensioni e automazioni che affronta complete Azure sottoscrizioni e risorse alle esigenze di sicurezza di team DevOps che utilizzano l'automazione completa. Il Secure DevOps Kit per Azure Mostra come integrare senza problemi di sicurezza nei flussi di lavoro DevOps nativi. Il kit risolve gli strumenti come un test di verifica della sicurezza (SVTs), che consentono agli sviluppatori di scrivere codice protetto e testare la configurazione protetta delle applicazioni cloud in più fasi di sviluppo iniziali e di codifica.

  - [Le procedure consigliate per le soluzioni Azure](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions) fornisce una raccolta di sicurezza consigliate da usare come progettare, distribuire e gestire le soluzioni cloud con Azure.

## <a name="requirements"></a>Requisiti
La fase di definizione dei requisiti è un passaggio fondamentale nella definizione di ciò che l'applicazione è e cosa farà quando viene rilasciato. La fase dei requisiti è anche un tempo per pensare ai controlli di sicurezza che si compilerà l'applicazione. Durante questa fase, è anche possibile iniziare i passaggi che vengono intraprese in tutto il processo SDL per assicurare di rilasciare e distribuire un'applicazione protetta.

### <a name="consider-security-and-privacy-issues"></a>Prendere in considerazione i problemi di sicurezza e privacy
Questa fase è il momento migliore per prendere in considerazione i problemi di privacy e sicurezza fondamentale. Definire livelli accettabili di sicurezza e privacy all'inizio di un progetto consente un team:

- Comprendere i rischi associati a problemi di sicurezza.
- Identificare e correggere gli errori di protezione durante lo sviluppo.
- Applicare stabiliti i livelli di sicurezza e privacy in tutta l'intero progetto.

Quando si scrivono i requisiti per l'applicazione, assicurarsi di prendere in considerazione i controlli di sicurezza che consentono di proteggere l'applicazione e dati.

### <a name="ask-security-questions"></a>Porre domande di sicurezza
Porre domande di sicurezza, ad esempio:

  - L'applicazione contenga dati sensibili?

  - La mia applicazione raccogliere o archiviare i dati che richiede di essere conformi agli standard del settore e programmi di conformità, ad esempio la [Federal finanziari istituto esame Council (FFIEC)](https://docs.microsoft.com/azure/security/blueprints/ffiec-analytics-overview) o il [Payment Card settore Data Security standard (PCI DSS)](https://docs.microsoft.com/azure/security/blueprints/pcidss-analytics-overview)?

  - La mia applicazione raccogliere o contengono dati sensibili cliente o personale che possono essere utilizzati, in modo autonomo o con altre informazioni, per identificare, contattare o di individuare una singola persona?

  - La mia applicazione raccogliere o contenere dati che possono essere utilizzati per accedere ai medici di un individuo, didattici, finanziari, o posti di lavoro? Che identifica la riservatezza dei dati durante la fase dei requisiti consente di classificare i dati e identificare il metodo di protezione dati che si utilizzerà per l'applicazione.

  - Come e dove vengono archiviati i dati? Si consideri come monitorare i servizi di archiviazione usate dall'applicazione per riscontrare eventuali cambiamenti inattesi (ad esempio tempi di risposta). Sarà in grado di influenzare la registrazione per raccogliere dati più dettagliati e analizzare i problemi in modo approfondito?

  - L'applicazione sarà disponibile al pubblico (in internet) o internamente solo? Se l'applicazione è disponibile al pubblico, come proteggere i dati che potrebbero essere raccolte perché non venga usato in modo errato? Se l'applicazione è disponibile internamente solo, prendere in considerazione quali utenti dell'organizzazione devono avere accesso all'applicazione e quanto tempo hanno accesso.

  - Sei consapevole il modello di identità prima di iniziare la progettazione dell'applicazione? Come si determina che gli utenti siano chi affermano sono e ciò che l'utente è autorizzato a eseguire?

  - L'applicazione esegue le attività sensibili o importanti (ad esempio, il trasferimento di denaro, sbloccare le porte o recapito medicina)?
    Prendere in considerazione come si verificherà che l'utente che esegue un'attività sensibile è autorizzato a eseguire l'attività e modalità di autenticazione che l'utente sia chi dice che sono. L'autorizzazione (AuthZ) è l'atto di concessione di un'autorizzazione dell'entità di sicurezza autenticata per eseguire un'operazione. L'autenticazione (AuthN) è l'atto di credenziali legittime a una parte.

  - L'applicazione esegue qualsiasi attività di software a rischio, ad esempio consentire agli utenti di caricare o scaricare file o altri dati? Se l'applicazione esegue attività rischiose, prendere in considerazione l'applicazione verrà protezione gli utenti da come la gestione di file dannosi o dati.

### <a name="review-owasp-top-10"></a>10 principali OWASP di revisione
È consigliabile rivedere le [ <span class="underline">rischi per la sicurezza delle applicazioni per Top 10 OWASP</span>](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project).
Top 10 OWASP risolve i rischi di sicurezza critico per le applicazioni web.
La consapevolezza di tali rischi di sicurezza consentono di prendere decisioni di progettazione di un requisito che riducono al minimo tali rischi nell'applicazione.

È importante considerare i controlli di sicurezza per impedire violazioni della sicurezza.
Tuttavia, si vogliono [presumerne una](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/security-in-devops) si verificherà. Supponendo che una violazione consente di individuare alcune importanti domande sulla sicurezza in anticipo, in modo che non è necessario rispondere in caso di emergenza:

  - Come rileverà un attacco?

  - Cosa verrà? se si verifica un attacco o violazione

  - Modo mi connetterò per il ripristino dall'attacco, ad esempio dati divulgazione o alterazione?

## <a name="design"></a>Progettazione

La fase di progettazione è fondamentale per stabilire le procedure consigliate per la progettazione e le specifiche funzionali. Inoltre è fondamentale per l'esecuzione di analisi dei rischi che consente di ridurre i problemi di sicurezza e privacy in tutto il progetto.

Quando si siano soddisfatti i requisiti di sicurezza e utilizzare i concetti di progettazione protetta, è possibile evitare o ridurre al minimo le opportunità per un difetto di protezione. Una vulnerabilità di sicurezza è considerata una distrazione nella progettazione dell'applicazione che potrebbe consentire a un utente di eseguire azioni dannose o impreviste, dopo il rilascio dell'applicazione.

Durante la fase di progettazione, anche considerare come si può applicare la sicurezza nei livelli; un livello di difesa non è necessariamente sufficiente. Cosa accade se un utente malintenzionato riesce a superare il web application firewall (WAF). È necessario un altro controllo di sicurezza in grado di difesa da tale attacco.

Con questa premessa, illustreremo i seguenti concetti infrastruttura protetta e i controlli di sicurezza che è necessario tenere presente durante la progettazione di applicazioni sicure:

- Usare una libreria di codifica sicura e un framework software.
- Analisi per i componenti vulnerabili.
- Usare durante la progettazione di applicazioni di modellazione delle minacce.
- Ridurre la superficie di attacco.
- Adottare un criterio di identità come perimetro di sicurezza primario.
- Richiedere una nuova autenticazione per le transazioni importanti.
- Usare una soluzione di gestione delle chiavi per proteggere le chiavi, credenziali e altri segreti.
- Proteggere i dati sensibili.
- Implementazione di misure in modalità provvisoria.
- Sfruttare i vantaggi di errore e la gestione delle eccezioni.
- Usare la registrazione e gli avvisi.

### <a name="use-a-secure-coding-library-and-a-software-framework"></a>Usare una libreria di codifica sicura e un framework software

Per lo sviluppo, usare una libreria di codifica sicura e un framework software che dispone di sicurezza incorporati. Gli sviluppatori possono usare esistenti, comprovate funzionalità (crittografia, depurazione input, la codifica dell'output, le chiavi o le stringhe di connessione e qualsiasi altro elemento che possa essere considerato un controllo di sicurezza) rispetto allo sviluppo di controlli di sicurezza da zero. Ciò consente di salvaguardarsi da progettazione relative alla sicurezza e ai punti deboli di implementazione.

Assicurarsi che si usa la versione più recente di tutte le funzionalità di sicurezza disponibili nel framework e il framework. Microsoft offre una serie completa [set di strumenti di sviluppo](https://azure.microsoft.com/product-categories/developer-tools/) per tutti gli sviluppatori, in qualsiasi piattaforma o linguaggio, per offrire applicazioni cloud. È possibile scrivere codice con il linguaggio di propria scelta, scegliendo da vari [SDK](https://azure.microsoft.com/downloads/).
È possibile sfruttare i vantaggi di ambienti completi di sviluppo integrato (IDE) ed editor che hanno avanzate funzionalità di debug e supporto di Azure predefinito.

Microsoft offre un'ampia gamma [linguaggi, Framework e strumenti](https://docs.microsoft.com/azure/index#pivot=sdkstools&panel=sdkstools-all) che è possibile usare per sviluppare applicazioni in Azure. Ad esempio [Azure per sviluppatori .NET e .NET Core](https://docs.microsoft.com/dotnet/azure/). Per ciascuna lingua e framework che offriamo, troverai guide introduttive, esercitazioni e riferimento sulle API che consentono di iniziare rapidamente.

Azure offre un'ampia gamma di servizi che è possibile usare per ospitare applicazioni e siti web. Questi servizi consentono di sviluppare nel tuo linguaggio preferito, ovvero .NET, .NET Core, Java, Ruby, Node. js, PHP o Python.
[App Web di Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-web-overview) (app Web) è uno di questi servizi.

Siti Web di Azure aggiunge la potenza di Microsoft Azure all'applicazione. Include sicurezza, bilanciamento del carico, la scalabilità automatica e gestione automatizzata. È possibile anche sfruttare le funzionalità DevOps nelle App Web, come Gestione pacchetti, gli ambienti, i domini personalizzati, certificati SSL/TLS e distribuzione continua da DevOps di Azure, GitHub, Hub Docker e altre origini di gestione temporanea.

Azure offre altri servizi che è possibile usare per ospitare applicazioni e siti web. Per la maggior parte degli scenari App Web è la scelta migliore. Per un'architettura di microservizi, prendere in considerazione [Azure Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric).
Se è necessario un maggiore controllo sulle VM in cui viene eseguito il codice, prendere in considerazione l'uso delle [Macchine virtuali di Azure](https://azure.microsoft.com/documentation/services/virtual-machines/).
Per altre informazioni su come scegliere tra questi servizi di Azure, vedere una [confronto tra servizio App di Azure, macchine virtuali, Service Fabric e servizi Cloud](https://docs.microsoft.com/azure/app-service/choose-web-site-cloud-service-vm).

### <a name="apply-updates-to-components"></a>Applicare gli aggiornamenti ai componenti

Per evitare le vulnerabilità, è necessario continuamente effettuare l'inventario sia i componenti lato client e lato server (ad esempio, Framework e librerie) e le relative dipendenze per gli aggiornamenti. Nuove vulnerabilità e le versioni di aggiornamento del software vengono rilasciate costantemente. Assicurarsi che si dispone di un piano in corso il monitoraggio, valutare e applicare gli aggiornamenti o modifiche di configurazione per le librerie e i componenti usati.

Vedere le [Open Web Application Security Project (OWASP)](https://www.owasp.org/index.php/Main_Page) pagina [usando i componenti con vulnerabilità note ottenute](https://www.owasp.org/index.php/Top_10-2017_A9-Using_Components_with_Known_Vulnerabilities) per suggerimenti dello strumento. È inoltre possibile sottoscrivere gli avvisi di posta elettronica per le vulnerabilità di sicurezza che riguardano i componenti usati.

### <a name="use-threat-modeling-during-application-design"></a>Usare la modellazione delle minacce durante la progettazione delle applicazioni

La modellazione delle minacce è il processo di identificazione di potenziali minacce alla sicurezza per il business e l'applicazione e assicurarsi che soluzioni di attenuazione appropriate siano soddisfatti. Il processo SDL specifica che i team devono partecipare rischi durante la fase di progettazione, quando la risoluzione dei problemi potenziali è relativamente semplice ed economicamente conveniente. Uso delle minacce modellazione delle minacce nella fase di progettazione può ridurre notevolmente il costo totale di sviluppo.

È stato progettato per semplificare il processo di modellazione, la [SDL Threat Modeling Tool](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) con pensando agli esperti non relativi alla sicurezza. Questo strumento semplifica la modellazione delle minacce per tutti gli sviluppatori fornendo istruzioni chiare su come creare e analizzare i modelli di rischio.

La progettazione di applicazioni di modellazione e l'enumerazione [STRIDE](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy) minacce, ovvero Spoofing, manomissione, ripudio, diffusione di informazioni, Denial of Service e l'elevazione dei privilegi, ovvero in tutti i trust dei limiti ha dimostrato in modo efficace per intercettare gli errori di progettazione sin dall'inizio. Nella tabella seguente sono elencate le minacce STRIDE e offre alcune mitigazioni di esempio che usano le funzionalità fornite da Azure. Queste mitigazioni dei rischi non funzioneranno in tutte le situazioni.

| Threat | Proprietà di sicurezza | Mitigazione di potenziale della piattaforma Azure |
| ---------------------- | --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Spoofing               | Authentication        | [Richiedi connessioni HTTPS](https://docs.microsoft.com/aspnet/core/security/enforcing-ssl?view=aspnetcore-2.1&tabs=visual-studio). |
| Manomissione              | Integrità             | Convalidare i certificati SSL/TLS. Le applicazioni che usano SSL/TLS devono verificare completamente i certificati X.509 delle entità di che cui si connettono. Usare i certificati di Azure Key Vault per [gestire x509 i certificati](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-certificates). |
| Ripudio            | Non ripudio       | Abilitazione del [monitoraggio e diagnostica](https://docs.microsoft.com/azure/architecture/best-practices/monitoring) di Azure.|
| Divulgazione di informazioni | Riservatezza       | Crittografare i dati sensibili [inattivi](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest) e [durante il transito](https://docs.microsoft.com/azure/security/azure-security-data-encryption-best-practices#protect-data-in-transit). |
| Denial of Service      | Disponibilità          | Monitorare le metriche delle prestazioni per potenziali condizioni di DOS. Implementare i filtri di connessione. [Protezione DDoS di Azure](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview#next-steps), combinati con procedure consigliate per progettazione dell'applicazione, che fornisce una difesa contro gli attacchi DDoS.|
| Elevazione dei privilegi | Authorization         | Usare Azure Active Directory <span class="underline"> </span> [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure).|

### <a name="reduce-your-attack-surface"></a>Ridurre la superficie di attacco

Una superficie di attacco è il totale Somma di in cui potrebbero verificarsi le potenziali vulnerabilità. In questo white paper, focalizzata sulla superficie di attacco di un'applicazione.
Lo stato attivo è sulla protezione di un'applicazione da attacchi esterni. Un modo semplice e rapido per ridurre al minimo la superficie di attacco consiste nel rimuovere le risorse inutilizzate e il codice dall'applicazione. Il valore più piccolo dell'applicazione, il valore più piccolo la superficie di attacco. Ad esempio, rimuovere:

- Codice per la funzionalità che ancora stato ancora rilasciato.
- Debug del codice di supporto.
- Interfacce di rete e protocolli che non vengono usate o che sono state deprecate.
- Le macchine virtuali e altre risorse che non è in uso.

Eseguire operazioni regolari di pulizia delle risorse e verificare di rimuovere codice inutilizzato sono grandi occasioni per assicurarsi che siano minori possibilità di attori dannosi agli attacchi.

Un modo più dettagliato e approfondito per ridurre la superficie di attacco è per completare un'analisi della superficie di attacco. Un'analisi della superficie di attacco consente di mappare le parti di un sistema che devono essere controllati e testati per le vulnerabilità di sicurezza.

Lo scopo di un'analisi della superficie di attacco consiste nel comprendere le aree di rischio in un'applicazione in modo che gli sviluppatori e gli specialisti della protezione sono a conoscenza di quali parti dell'applicazione sono vulnerabili agli attacchi. Quindi, è possibile trovare modi per ridurre al minimo questa traccia potenziale, come e quando cambia la superficie di attacco, il che significa una prospettiva di rischio.

Un'analisi della superficie di attacco consente di identificare:

- Funzioni e le parti del sistema è necessario esaminare e testare le vulnerabilità di sicurezza.
- Aree ad alto rischio di codice che richiedono la protezione di difesa in profondità (parti del sistema che è necessario per la difesa).
- Quando si modifica la superficie di attacco e necessario aggiorna una valutazione delle minacce.

Riducendo le opportunità per gli utenti malintenzionati di sfruttare un potenziale punto debole o vulnerabilità è necessario analizzare accuratamente superficie di attacco complessiva dell'applicazione. Include inoltre la disabilitazione o la limitazione dell'accesso ai servizi di sistema, applicare il principio del privilegio minimo, e utilizzo di difese su più livelli laddove possibile.

Illustreremo [condurre una revisione della superficie di attacco](secure-develop.md#conduct-attack-surface-review) durante la fase di verifica del processo SDL.

> [!NOTE]
> **Che cos'è la differenza tra la modellazione delle minacce e analisi della superficie di attacco?**
La modellazione delle minacce è il processo di identificazione di potenziali minacce alla sicurezza per l'applicazione e garantire la corretta mitigazioni contro le minacce posto. Analisi della superficie di attacco identifica le aree ad alto rischio di codice che sono vulnerabili agli attacchi. Questa operazione implica la ricerca di modi per difendere le aree ad alto rischio dell'applicazione e la revisione e le aree di codice di test prima di distribuire l'applicazione.

### <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>Adottare dei criteri di identità come perimetro di sicurezza primario

Quando si progettano le applicazioni cloud, è importante espandere il protezione perimetrale lo stato attivo da un approccio incentrato sulla rete per un approccio incentrato sulle identità. In passato, il perimetro di sicurezza locale primario era una rete dell'organizzazione. La maggior parte delle progettazioni di sicurezza di un'istanza locale di usare la rete come punto pivot di sicurezza primario. Per le applicazioni cloud, meglio vengono gestite da passare all'identità come perimetro di sicurezza primario.

Cose da fare per sviluppare un approccio incentrato sulle identità per lo sviluppo di applicazioni web:

- Applicare multi-factor authentication per gli utenti.
- usare piattaforme di autenticazione e autorizzazione avanzate.
- Applicare il principio del privilegio minimo.
- Implementare l'accesso just-in-time.

#### <a name="enforce-multi-factor-authentication-for-users"></a>Applicare multi-factor authentication per gli utenti

Usare l'autenticazione a due fattori. L'autenticazione a due fattori è lo standard corrente per l'autenticazione e autorizzazione perché evita di protezioni inerenti nei tipi di nome utente e password di autenticazione. Accesso alle interfacce di gestione di Azure (portale/istanza remota di Azure PowerShell) e ai servizi per i clienti deve essere progettata e configurata per l'utilizzo [Azure multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication).

#### <a name="use-strong-authentication-and-authorization-platforms"></a>Utilizzare le piattaforme di autenticazione e autorizzazione sicuri

Usare i meccanismi di autenticazione e autorizzazione forniti dalla piattaforma invece di un codice personalizzato. Questo avviene perché lo sviluppo di codice di autenticazione personalizzato può essere soggetto a errori. Il codice commerciale (ad esempio, da Microsoft) spesso viene esaminata in modo esteso per la sicurezza. [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) è la soluzione di Microsoft Azure per la gestione delle identità e degli accessi. Questi strumenti di Azure AD e servizi utili per lo sviluppo sicuro:

- [Azure piattaforma delle identità di Active Directory (Azure AD per sviluppatori)](https://docs.microsoft.com/azure/active-directory/develop/about-microsoft-identity-platform) è un servizio di identità cloud usato dagli sviluppatori per compilare App che gli utenti di accedere in modo sicuro. Azure AD consente agli sviluppatori che compilano a tenant singolo le app line-of-business (LOB) e gli sviluppatori in cerca di sviluppare App multi-tenant. Oltre all'accesso aggiuntivo di base, le app che vengono compilate tramite Azure AD possono chiamare APIs Microsoft e le API personalizzate che si basano sulla piattaforma di Azure AD. La piattaforma delle identità di Azure AD supporta i protocolli standard del settore come OAuth 2.0 e OpenID Connect.

- [Azure Active B2C di Directory (Azure AD B2C)](https://docs.microsoft.com/azure/active-directory-b2c/) è un servizio di gestione di identità è possibile usare per personalizzare e controllare come i clienti di effettuare l'iscrizione, accesso e gestiscono i rispettivi profili quando usano le applicazioni. Ciò include le applicazioni sviluppate per iOS, Android e .NET, tra gli altri. Azure AD B2C consente queste azioni proteggendo le identità dei clienti.

#### <a name="apply-the-principle-of-least-privilege"></a>Applicazione del principio del privilegio minimo

Il concetto di [privilegio](https://en.wikipedia.org/wiki/Principle_of_least_privilege) significa fornendo agli utenti il livello esatto di accesso e controllo necessarie per svolgere il proprio lavoro e nient'altro.

Uno sviluppatore di software deve disporre dei diritti di amministratore di dominio? Un Assistente amministrativo è necessario accedere a controlli amministrativi sui propri computer personali? La valutazione dell'accesso al software non è diverso. Se si usa [controllo di accesso basato sui ruoli (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) per fornire capacità diversi utenti e l'autorità nell'applicazione, non sarebbe di dare a ogni accesso a tutti gli elementi. Limitando l'accesso a quelle necessarie per ogni ruolo, si limita il rischio di un problema di sicurezza in corso.

Assicurarsi che l'applicazione garantisce [privilegio](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models#in-applications) tutta relativi modelli di accesso.

> [!NOTE]
>  Le regole del privilegio minimo necessario applicare al software e alle persone la creazione del software. Gli sviluppatori di software possono essere un enorme rischi alla sicurezza IT se dispongono di accesso eccessivo. Le conseguenze possono essere gravi se uno sviluppatore ha dannosi o viene assegnato l'accesso eccessivo. È consigliabile che le regole del privilegio minimo essere applicate per gli sviluppatori in tutto il ciclo di vita di sviluppo.

#### <a name="implement-just-in-time-access"></a>Implementare l'accesso just-in-time

Implementare *just-in-time* accesso (JIT) per ridurre ulteriormente il tempo di esposizione dei privilegi. Uso [Azure AD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure#stage-3-build-visibility-and-take-full-control-of-admin-activity) per:

- Concedere agli utenti le autorizzazioni necessarie solo JIT.
- Assegnare i ruoli per un periodo di tempo ridotto sapendo che i privilegi vengono revocati automaticamente.

### <a name="require-re-authentication-for-important-transactions"></a>Richiedere una nuova autenticazione per le transazioni importanti

[Richiesta intersito falsa](https://docs.microsoft.com/aspnet/core/security/anti-request-forgery?view=aspnetcore-2.1) (noto anche come *XSRF* oppure *CSRF*) è un attacco contro applicazioni ospitate sul web in cui un'app web dannoso influenza l'interazione tra un browser client e un sito web App che consideri attendibile tale browser. Richiesta intersito falsa attacchi sono possibili in quanto i browser web inviano alcuni tipi di token di autenticazione automaticamente con ogni richiesta a un sito Web.
Questo modulo di sfruttamento è noto anche come un *attacco di un solo clic* oppure *sessione puntato* perché l'attacco sfrutta l'utente autenticato del precedentemente sessione.

Il modo migliore per evitare questo tipo di attacco consiste nel chiedere all'utente per qualcosa che può fornire solo l'utente prima di ogni transazione importanti, ad esempio un acquisto, la disattivazione di account o una modifica della password. Si potrebbe chiedere all'utente di immettere nuovamente la password, completare un captcha o invia un token segreto che potrebbe avere solo l'utente. L'approccio più comune è il token del segreto.

### <a name="use-a-key-management-solution-to-secure-keys-credentials-and-other-secrets"></a>Usare una soluzione di gestione delle chiavi per proteggere le chiavi, le credenziali e altri segreti

Perdere chiavi e credenziali è un problema comune. un rischio ben peggiore della perdita di chiavi e credenziali consiste negli accessi non autorizzati. Gli utenti malintenzionati possono sfruttare i vantaggi delle tecniche automatizzati e manuali per trovare le chiavi e segreti archiviati nel repository di codice come GitHub. Non inserire chiavi e segreti in questi archivi di codice pubblica o in qualsiasi altro server.

Inserire sempre le chiavi, i certificati, i segreti e le stringhe di connessione in una soluzione di gestione delle chiavi. È possibile usare una soluzione centralizzata in cui sono archiviati le chiavi e segreti in moduli di protezione hardware (HSM). Azure offre un modulo di protezione hardware nel cloud con [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis).

Key Vault è un *archivio segreto*: si tratta di un servizio cloud centralizzata per l'archiviazione dei segreti dell'applicazione. Key Vault mantiene i dati riservati sicuro mantenendo i segreti dell'applicazione in un'unica posizione centrale e fornire accesso sicuro, il controllo delle autorizzazioni e la registrazione di accesso.

I segreti vengono memorizzati in singoli *insiemi di credenziali*. Ogni insieme di credenziali ha la propria configurazione e criteri di sicurezza per controllare l'accesso. Accedere ai dati tramite un'API REST o tramite un SDK che è disponibile per la maggior parte dei linguaggi di programmazione client.

> [!IMPORTANT]
> Azure Key Vault è progettato per archiviare i segreti di configurazione per le applicazioni server. Non è destinato l'archiviazione dei dati che appartengono agli utenti di app. Questo si riflette nelle relative caratteristiche di prestazioni, API e modello di costo.
>
> I dati utente devono essere archiviati altrove, ad esempio in un'istanza di Database SQL di Azure con Transparent Data Encryption (TDE) oppure in un account di archiviazione che utilizza la crittografia del servizio di archiviazione di Azure. È possono mantenere i segreti utilizzati dall'applicazione per accedere a questi archivi dati in Azure Key Vault.

### <a name="protect-sensitive-data"></a>Proteggere i dati sensibili

La protezione dei dati è una parte essenziale della strategia di sicurezza.
Classificazione dei dati e identificare la protezione dei dati deve consente di progettare l'app con tenendo conto della sicurezza dei dati. Classificazione (categorizzazione) i dati archiviati dalla sensibilità e impatto aziendale consente agli sviluppatori di determinare i rischi associati a dati.

Tutti i dati applicabili etichetta sensibile quando si progettano i formati di dati. Assicurarsi che l'applicazione gestisce i dati applicabili come sensibili. Queste procedure consentono di proteggere i dati sensibili:

- Usare la crittografia.
- Evitare di impostare come hardcoded i segreti, ad esempio le chiavi e password.
- Assicurarsi che siano presenti i controlli di accesso e controllo.

#### <a name="use-encryption"></a>Usare la crittografia

La protezione dei dati deve essere una parte essenziale della strategia di sicurezza.
Se i dati sono archiviati in un database o se sposta avanti e indietro tra i percorsi, usare la crittografia dei [dati inattivi](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest) (all'interno di database) e la crittografia dei [i dati in transito](https://docs.microsoft.com/azure/security/azure-security-data-encryption-best-practices#protect-data-in-transit) (in modo da e verso l'utente, il database, un'API o endpoint di servizio). È consigliabile usare sempre i protocolli SSL/TLS per lo scambio di dati. Assicurarsi di usare la versione più recente di TLS per la crittografia (attualmente, questa è la versione 1.2).

#### <a name="avoid-hard-coding"></a>Evitare di impostare come hardcoded

Alcuni elementi non devono essere mai impostate come hardcoded nel software. Alcuni esempi sono i nomi host o IP indirizzi, URL, gli indirizzi di posta elettronica, i nomi utente, password, chiavi dell'account di archiviazione e altre chiavi di crittografia. È consigliabile implementare i requisiti di tutto ciò che può o non possono essere impostate come hardcoded nel codice, inclusi nelle sezioni commento del codice.

Quando si inseriscono commenti nel codice, assicurarsi di non salvare le informazioni riservate. Ciò include l'indirizzo di posta elettronica, password, stringhe di connessione, informazioni sull'applicazione che potrebbe essere nota solo da un utente nell'organizzazione e altri elementi che è possibile assegnare un utente malintenzionato un vantaggio in attaccare l'applicazione o l'organizzazione .

In pratica, si presuppone che tutti gli elementi di progetto di sviluppo saranno pubblici della Knowledge base quando viene distribuita. Evitare di includere dati sensibili di qualsiasi tipo nel progetto.

Versioni precedenti, abbiamo discusso [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis). È possibile usare Key Vault per archiviare i segreti, ad esempio le chiavi e password, anziché a livello di codice. Quando si usa Key Vault in combinazione con identità gestite per le risorse di Azure, app web di Azure possono accedere i valori di configurazione segreto semplice e sicuro senza archiviare tutti i segreti nel controllo del codice sorgente o configurazione. Per altre informazioni, vedere [gestire i segreti nelle tue App server con Azure Key Vault](https://docs.microsoft.com/learn/modules/manage-secrets-with-azure-key-vault/).

### <a name="implement-fail-safe-measures"></a>Implementazione di misure in modalità provvisoria

L'applicazione deve essere in grado di gestire [errori](https://docs.microsoft.com/dotnet/standard/exceptions/) che si verificano durante l'esecuzione in modo coerente. L'applicazione deve rilevare tutti gli errori e uno esito negativo chiusi o sicuri.

È anche necessario assicurarsi che gli errori vengono registrati con il contesto utente sufficienti per identificare attività sospette o dannoso. I log devono essere mantenuti per un tempo sufficiente consentire le analisi forensi ritardata. I log devono essere in un formato che può essere facilmente utilizzato da una soluzione di gestione di log. Assicurarsi che vengono generati avvisi per gli errori correlati alla sicurezza. Insufficiente di registrazione e monitoraggio consente agli utenti malintenzionati attaccare i sistemi e mantenere la persistenza ulteriormente.

### <a name="take-advantage-of-error-and-exception-handling"></a>Sfruttare i vantaggi di errore e la gestione delle eccezioni

Implementazione di correggere l'errore e [la gestione delle eccezioni](https://docs.microsoft.com/dotnet/standard/exceptions/best-practices-for-exceptions) è una parte importante di codifica difensive. Errore e la gestione delle eccezioni sono essenziali per prendere un sistema affidabile e sicura. Diversi tipi di vulnerabilità di sicurezza, ad esempio verifica una perdita di informazioni a utenti malintenzionati e aiuta gli utenti malintenzionati di comprendere meglio la piattaforma e la progettazione possono causare errori nella gestione degli errori.

Assicurarsi che:

- Gestire le eccezioni in modo centralizzato per evitare duplicati [blocchi try/catch](https://docs.microsoft.com/dotnet/standard/exceptions/how-to-use-the-try-catch-block-to-catch-exceptions) nel codice.

- Tutti i comportamenti imprevisti vengono gestiti all'interno dell'applicazione.

- I messaggi che vengono visualizzati agli utenti non determinare una perdita di dati critici, ma forniscono informazioni sufficienti per spiegare il problema.

- Le eccezioni vengono registrate e che forniscono informazioni sufficienti per consentire ai team di risposta forensi o un evento imprevisto da analizzare.

[Le app di Azure per la logica](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview) offre un'esperienza all'avanguardia per [gestione di errori ed eccezioni](https://docs.microsoft.com/azure/logic-apps/logic-apps-exception-handling) che sono causati da sistemi dipendenti. È possibile usare App per la logica per creare flussi di lavoro per automatizzare le attività e processi che si integrano App, dati, sistemi e servizi tra aziende e organizzazioni.

### <a name="use-logging-and-alerting"></a>Usare la registrazione e gestione degli avvisi

[Log](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1) rilascia la sicurezza per le indagini di sicurezza e attivare gli avvisi sui problemi per garantire che le persone conoscono i problemi in modo tempestivo. Abilitare il controllo e la registrazione su tutti i componenti. I log di controllo devono acquisire il contesto utente e identificare tutti gli eventi importanti.

Verificare di non accedere i dati sensibili che un utente invia al sito. Esempi di dati sensibili:

- Credenziali dell'utente
- Numeri di previdenza sociale o altre informazioni di identificazione
- Numeri di carta di credito o altre informazioni finanziarie
- Informazioni sulla salute
- Le chiavi private o altri dati che possono essere usati per decrittografare le informazioni crittografate
- Informazioni sul sistema o sull'applicazione che possono essere usate per attaccare l'applicazione in modo più efficace

Assicurarsi che l'applicazione monitori gli eventi di gestione utente, ad esempio gli accessi utente riusciti e non riusciti, la reimpostazione della password, le modifiche delle password, il blocco degli account e registrazione dell'utente. La registrazione per questi eventi consente di rilevare e rispondere a potenziali comportamenti sospetti. Inoltre, consente di raccogliere dati operativi, ad esempio chi accede all'applicazione.

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti, si consiglia di controlli di sicurezza e le attività che consentono di sviluppano e distribuiscono applicazioni sicure.

- [Sviluppo di applicazioni sicure](secure-develop.md)
- [Distribuisci applicazioni sicure](secure-deploy.md)
