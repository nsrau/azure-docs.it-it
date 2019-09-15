---
title: Progettare applicazioni sicure in Microsoft Azure
description: Questo articolo illustra le procedure consigliate da prendere in considerazione durante le fasi di requisiti e progettazione del progetto di applicazione Web.
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
ms.openlocfilehash: 316ed596cfa49987e229004c388267286ff50927
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2019
ms.locfileid: "71000975"
---
# <a name="design-secure-applications-on-azure"></a>Progettare applicazioni sicure in Azure
In questo articolo vengono presentate le attività e i controlli di sicurezza da prendere in considerazione quando si progettano applicazioni per il cloud. Vengono analizzate le risorse di formazione e le domande e i concetti di sicurezza da prendere in considerazione durante i requisiti e le fasi di progettazione di Microsoft [Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) . L'obiettivo è consentire di definire le attività e i servizi di Azure che è possibile usare per progettare un'applicazione più protetta.

Le fasi SDL seguenti sono descritte in questo articolo:

- Formazione
- Requisiti
- Progettazione

## <a name="training"></a>Formazione
Prima di iniziare a sviluppare l'applicazione cloud, è necessario prendere tempo per comprendere la sicurezza e la privacy in Azure. Eseguendo questo passaggio, è possibile ridurre il numero e la gravità delle vulnerabilità sfruttabili nell'applicazione. Si sarà più pronti a rispondere in modo appropriato al panorama delle minacce in continua evoluzione.

Usare le risorse seguenti durante la fase di training per acquisire familiarità con i servizi di Azure disponibili per gli sviluppatori e con le procedure di sicurezza consigliate in Azure:

  - La [Guida per gli sviluppatori di Azure](https://azure.microsoft.com/campaigns/developer-guide/) illustra come iniziare a usare Azure. La Guida Mostra i servizi che è possibile usare per eseguire le applicazioni, archiviare i dati, incorporare l'intelligence, compilare app per Internet e distribuire le soluzioni in modo più efficiente e sicuro.

  - [Guida introduttiva per gli sviluppatori di Azure](../../guides/developer/azure-developer-guide.md) fornisce informazioni essenziali per gli sviluppatori che vogliono iniziare a usare la piattaforma Azure per le proprie esigenze di sviluppo.

  - Gli [SDK e gli strumenti](https://docs.microsoft.com/azure/index#pivot=sdkstools) descrivono gli strumenti disponibili in Azure.

  - [Azure DevOps Services](https://docs.microsoft.com/azure/devops/) offre strumenti per la collaborazione dello sviluppo. Gli strumenti includono pipeline ad alte prestazioni, repository git gratuiti, lavagne Kanban configurabili e test di carico completi automatizzati e basati sul cloud.
    Il [Centro risorse di DevOps](https://docs.microsoft.com/azure/devops/learn/) combina le risorse per l'apprendimento delle procedure di DevOps, il controllo della versione di Git, i metodi Agile, il modo in cui collaboriamo con DevOps in Microsoft e come è possibile valutare la progressione del DevOps.

  - I [5 elementi di sicurezza da considerare prima di eseguire il push nell'](https://docs.microsoft.com/learn/modules/top-5-security-items-to-consider/index?WT.mc_id=Learn-Blog-tajanca) ambiente di produzione illustrano come proteggere le applicazioni Web in Azure e proteggere le app dagli attacchi alle applicazioni Web più comuni e pericolose.

  - [Secure DevOps kit per Azure](https://azsk.azurewebsites.net/index.html) è una raccolta di script, strumenti, estensioni e automazione che soddisfano le esigenze complete di sottoscrizione di Azure e di sicurezza delle risorse dei team DevOps che usano l'automazione estesa. Il kit Secure DevOps per Azure consente di illustrare come integrare agevolmente la sicurezza nei flussi di lavoro DevOps nativi. Il kit si rivolge a strumenti come i test di verifica della sicurezza (SVTs), che consentono agli sviluppatori di scrivere codice sicuro e testare la configurazione sicura delle applicazioni cloud nel codice e nelle fasi iniziali di sviluppo.

  - [Procedure consigliate per la sicurezza per le soluzioni di Azure](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions) offre una raccolta di procedure consigliate per la sicurezza da usare durante la progettazione, la distribuzione e la gestione delle soluzioni cloud con Azure.

## <a name="requirements"></a>Requisiti
La fase di definizione dei requisiti è un passaggio fondamentale per definire l'applicazione e le operazioni che verrà eseguita quando viene rilasciata. La fase dei requisiti è anche un tempo per considerare i controlli di sicurezza che si compileranno nell'applicazione. Durante questa fase, verranno inoltre illustrati i passaggi da eseguire in tutto il processo SDL per assicurarsi di rilasciare e distribuire un'applicazione protetta.

### <a name="consider-security-and-privacy-issues"></a>Prendere in considerazione i problemi di sicurezza e privacy
Questa fase rappresenta il momento migliore per prendere in considerazione i problemi di sicurezza e privacy di base. Definire livelli accettabili di sicurezza e privacy all'inizio di un progetto aiuta un team:

- Individuare i rischi associati ai problemi di sicurezza.
- Identificare e correggere i bug di sicurezza durante lo sviluppo.
- Applicare livelli di sicurezza e privacy stabiliti nell'intero progetto.

Quando si scrivono i requisiti per l'applicazione, assicurarsi di prendere in considerazione i controlli di sicurezza che consentono di garantire la sicurezza dell'applicazione e dei dati.

### <a name="ask-security-questions"></a>Porre domande di sicurezza
Porre domande di sicurezza, ad esempio:

  - L'applicazione contiene dati sensibili?

  - L'applicazione raccoglie o archivia i dati che richiedono l'adesione agli standard del settore e ai programmi di conformità come il [Council (FFIEC)](../blueprints/ffiec-analytics-overview.md) o gli [standard di sicurezza dei dati del settore della carta di pagamento (PCI DSS) ](../blueprints/pcidss-analytics-overview.md)?

  - L'applicazione raccoglie o contiene dati sensibili personali o del cliente che possono essere usati, in modo autonomo o con altre informazioni, per identificare, contattare o individuare una singola persona?

  - L'applicazione raccoglie o contiene dati che possono essere usati per accedere alle informazioni mediche, didattiche, finanziarie o di lavoro di un utente? Identificando la riservatezza dei dati durante la fase dei requisiti, è possibile classificare i dati e identificare il metodo di protezione dati che si utilizzerà per l'applicazione.

  - Dove e come vengono archiviati i dati? Si consideri come monitorare i servizi di archiviazione utilizzati dall'applicazione per eventuali modifiche impreviste, ad esempio tempi di risposta più lenti. Sarà possibile influenzare la registrazione per raccogliere dati più dettagliati e analizzare un problema in modo approfondito?

  - L'applicazione sarà disponibile per il pubblico (su Internet) o solo internamente? Se l'applicazione è disponibile per il pubblico, come proteggere i dati che potrebbero essere usati in modo errato? Se l'applicazione è disponibile solo a livello interno, prendere in considerazione chi all'interno dell'organizzazione deve avere accesso all'applicazione e per quanto tempo deve avere accesso.

  - Si comprende il modello di identità prima di iniziare a progettare l'applicazione? In che modo è possibile determinare che gli utenti sono quelli che dicono di essere e quali sono le autorizzazioni di un utente?

  - L'applicazione esegue attività riservate o importanti, ad esempio il trasferimento di denaro, lo sblocco di porte o la distribuzione di medicinali?
    Si consideri come verrà convalidato che l'utente che esegue un'attività sensibile è autorizzato a eseguire l'attività e come si eseguirà l'autenticazione che la persona è chi dice di essere. Authorization (AuthZ) consente di concedere a un'entità di sicurezza autenticata un'autorizzazione per eseguire un'operazione. L'autenticazione (AuthN) è l'atto di sfidare un'entità per le credenziali legittime.

  - L'applicazione esegue attività software rischiose, ad esempio consentendo agli utenti di caricare o scaricare file o altri dati? Se l'applicazione esegue attività rischiose, valutare il modo in cui l'applicazione proteggerà gli utenti dalla gestione di file o dati dannosi.

### <a name="review-owasp-top-10"></a>Esaminare i primi 10 OWASP
Prendere in considerazione la revisione dei [<span class="underline">primi 10 rischi per la sicurezza delle applicazioni OWASP</span>](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project).
OWASP Top 10 risolve i rischi di sicurezza critici per le applicazioni Web.
La conoscenza di questi rischi per la sicurezza può aiutare a prendere decisioni di requisito e progettazione che riducono al minimo questi rischi nell'applicazione.

È importante considerare i controlli di sicurezza per impedire violazioni.
Si supponga, tuttavia, che si verifichi [una violazione](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/security-in-devops) . Supponendo che una violazione contribuisca a rispondere ad alcune domande importanti sulla sicurezza in anticipo, non è necessario rispondere in caso di emergenza:

  - Come si rileva un attacco?

  - Cosa si può fare se si verifica un attacco o una violazione?

  - In che modo verrà ripristinato l'attacco, ad esempio la perdita o la manomissione dei dati?

## <a name="design"></a>Progettazione

La fase di progettazione è fondamentale per la definizione di procedure consigliate per la progettazione e le specifiche funzionali. È anche fondamentale per eseguire un'analisi dei rischi che consente di ridurre i problemi di sicurezza e privacy in un progetto.

Quando si hanno requisiti di sicurezza e si usano concetti di progettazione sicuri, è possibile evitare o ridurre al minimo le opportunità di un problema di sicurezza. Un difetto di sicurezza è una supervisione della progettazione dell'applicazione che potrebbe consentire a un utente di eseguire azioni dannose o impreviste dopo il rilascio dell'applicazione.

Durante la fase di progettazione, si può anche pensare a come applicare la sicurezza nei livelli; un livello di difesa non è necessariamente sufficiente. Cosa accade se un utente malintenzionato supera il web application firewall (WAF)? Si vuole che un altro controllo di sicurezza sia in grado di difendersi da tale attacco.

Tenendo presente questo aspetto, verranno illustrati i concetti di progettazione protetti seguenti e i controlli di sicurezza da affrontare quando si progettano applicazioni protette:

- Usare una libreria di codifica protetta e un Framework software.
- Esegue l'analisi dei componenti vulnerabili.
- Usare la modellazione delle minacce durante la progettazione dell'applicazione.
- Ridurre la superficie di attacco.
- Adottare un criterio di identità come perimetro di sicurezza primario.
- È necessario rieseguire l'autenticazione per transazioni importanti.
- Usare una soluzione di gestione delle chiavi per proteggere chiavi, credenziali e altri segreti.
- Proteggere i dati sensibili.
- Implementare misure non sicure.
- Sfruttare i vantaggi della gestione degli errori e delle eccezioni.
- Usare la registrazione e gli avvisi.

### <a name="use-a-secure-coding-library-and-a-software-framework"></a>Usare una libreria di codifica protetta e un Framework software

Per lo sviluppo, usare una libreria di codifica protetta e un Framework software con sicurezza incorporata. Gli sviluppatori possono usare le funzionalità esistenti e collaudate (crittografia, igiene di input, codifica di output, chiavi o stringhe di connessione e qualsiasi altro elemento che verrebbe considerato un controllo di sicurezza) invece di sviluppare controlli di sicurezza da zero. Questo consente di evitare problemi di progettazione e implementazione correlati alla sicurezza.

Assicurarsi di usare la versione più recente del Framework e tutte le funzionalità di sicurezza disponibili nel Framework. Microsoft offre un [set completo di strumenti di sviluppo](https://azure.microsoft.com/product-categories/developer-tools/) per tutti gli sviluppatori, che lavorano su qualsiasi piattaforma o linguaggio, per offrire applicazioni cloud. È possibile scrivere codice con la lingua scelta scegliendo tra diversi [SDK](https://azure.microsoft.com/downloads/).
È possibile trarre vantaggio dagli ambienti di sviluppo integrato (IDE) e dagli editor completi con funzionalità di debug avanzate e supporto incorporato di Azure.

Microsoft offre un'ampia gamma di [linguaggi, Framework e strumenti](https://docs.microsoft.com/azure/index#pivot=sdkstools&panel=sdkstools-all) che è possibile usare per sviluppare applicazioni in Azure. Un esempio è [Azure per gli sviluppatori .NET e .NET Core](https://docs.microsoft.com/dotnet/azure/). Per ogni linguaggio e Framework offerto, sono disponibili guide introduttive, esercitazioni e informazioni di riferimento sulle API per iniziare rapidamente.

Azure offre un'ampia gamma di servizi che è possibile usare per ospitare siti Web e applicazioni Web. Questi servizi consentono di sviluppare nel linguaggio preferito, sia .NET, .NET Core, Java, Ruby, node. js, PHP o Python.
[App Web del servizio app Azure](../../app-service/overview.md) (App Web) è uno di questi servizi.

App Web aggiunge la potenza di Microsoft Azure all'applicazione. Include sicurezza, bilanciamento del carico, scalabilità automatica e gestione automatizzata. Puoi anche sfruttare le funzionalità di DevOps nelle app Web, come la gestione dei pacchetti, gli ambienti di staging, i domini personalizzati, i certificati SSL/TLS e la distribuzione continua da Azure DevOps, GitHub, Docker Hub e altre origini.

Azure offre altri servizi che è possibile usare per ospitare siti Web e applicazioni Web. Per la maggior parte degli scenari App Web è la scelta migliore. Per un'architettura di microservizi, prendere in considerazione [Service fabric di Azure](https://azure.microsoft.com/documentation/services/service-fabric).
Se è necessario un maggiore controllo sulle VM in cui viene eseguito il codice, prendere in considerazione l'uso delle [Macchine virtuali di Azure](https://azure.microsoft.com/documentation/services/virtual-machines/).
Per altre informazioni su come scegliere tra questi servizi di Azure, vedere [confronto tra app Azure Service, macchine virtuali, Service Fabric e servizi cloud](/azure/architecture/guide/technology-choices/compute-decision-tree).

### <a name="apply-updates-to-components"></a>Applicare gli aggiornamenti ai componenti

Per evitare vulnerabilità, è necessario eseguire l'inventario continuo dei componenti lato client e lato server (ad esempio, Framework e librerie) e le relative dipendenze per gli aggiornamenti. Nuove vulnerabilità e versioni aggiornate del software vengono rilasciate continuamente. Assicurarsi di disporre di un piano continuo per il monitoraggio, la valutazione e l'applicazione di aggiornamenti o modifiche di configurazione per le librerie e i componenti utilizzati.

Vedere la pagina [Open Web Application Security Project (OWASP)](https://www.owasp.org/index.php/Main_Page) per l' [uso di componenti con vulnerabilità note](https://www.owasp.org/index.php/Top_10-2017_A9-Using_Components_with_Known_Vulnerabilities) per i suggerimenti degli strumenti. È anche possibile sottoscrivere gli avvisi di posta elettronica per le vulnerabilità di sicurezza correlate ai componenti usati.

### <a name="use-threat-modeling-during-application-design"></a>Usare la modellazione delle minacce durante la progettazione delle applicazioni

La modellazione delle minacce è il processo di identificazione delle potenziali minacce alla sicurezza per l'azienda e l'applicazione e quindi la verifica della correttezza delle mitigazioni. Il processo SDL specifica che i team devono partecipare alla modellazione delle minacce durante la fase di progettazione, quando la risoluzione di potenziali problemi è relativamente semplice ed economicamente conveniente. L'uso della modellazione delle minacce nella fase di progettazione può ridurre notevolmente il costo totale di sviluppo.

Per semplificare il processo di modellazione delle minacce, abbiamo progettato il processo [SDL Threat Modeling Tool](threat-modeling-tool.md) con gli esperti di non sicurezza. Questo strumento semplifica la modellazione delle minacce per tutti gli sviluppatori fornendo istruzioni chiare su come creare e analizzare i modelli di rischio.

Modellazione della progettazione dell'applicazione ed enumerazione delle minacce [stride](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy) , ovvero spoofing, manomissioni, ripudio, divulgazione di informazioni, Denial of Service ed elevazione dei privilegi, in tutti i confini di trust si è dimostrato un modo efficace per individuare gli errori di progettazione inizio. La tabella seguente elenca le minacce STRIDE e fornisce alcune attenuazioni di esempio che usano le funzionalità fornite da Azure. Queste mitigazioni dei rischi non funzioneranno in tutte le situazioni.

| Threat | Proprietà di sicurezza | Mitigazione potenziale della piattaforma Azure |
| ---------------------- | --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Spoofing               | Authentication        | [Richiedere connessioni HTTPS](https://docs.microsoft.com/aspnet/core/security/enforcing-ssl?view=aspnetcore-2.1&tabs=visual-studio). |
| Manomissione              | Integrità             | Convalidare i certificati SSL/TLS. Le applicazioni che usano SSL/TLS devono verificare completamente i certificati X. 509 delle entità a cui si connettono. Usare i certificati Azure Key Vault per [gestire i certificati X509](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-certificates). |
| Ripudio            | Non ripudio       | Abilitazione del [monitoraggio e diagnostica](https://docs.microsoft.com/azure/architecture/best-practices/monitoring) di Azure.|
| Diffusione di informazioni | Riservatezza       | Crittografare i dati sensibili [inattivi](../fundamentals/encryption-atrest.md) e [in transito](../fundamentals/data-encryption-best-practices.md#protect-data-in-transit). |
| Denial of Service      | Disponibilità          | Monitorare le metriche delle prestazioni per le potenziali condizioni di tipo Denial of Service. Implementare i filtri di connessione. [Protezione DDoS di Azure](../../virtual-network/ddos-protection-overview.md#next-steps), in combinazione con le procedure consigliate per la progettazione di applicazioni, fornisce una difesa contro gli attacchi DDoS.|
| Elevazione dei privilegi | Authorization         | Utilizzare Azure Active Directory <span class="underline"></span> [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md).|

### <a name="reduce-your-attack-surface"></a>Ridurre la superficie di attacco

Una superficie di attacco è la somma totale di dove potrebbero verificarsi potenziali vulnerabilità. Questo articolo è incentrato sulla superficie di attacco di un'applicazione.
L'obiettivo è quello di proteggere un'applicazione da un attacco. Un modo semplice e rapido per ridurre al minimo la superficie di attacco consiste nel rimuovere le risorse e il codice non usati dall'applicazione. Minore è l'applicazione, minore è la superficie di attacco. Ad esempio, rimuovere:

- Codice per le funzionalità non ancora rilasciate.
- Debug del codice di supporto.
- Interfacce di rete e protocolli non usati o deprecati.
- Macchine virtuali e altre risorse che non vengono usate.

Eseguire una pulizia regolare delle risorse e assicurarsi di rimuovere il codice inutilizzato è un ottimo modo per assicurarsi che vi sia un minor numero di opportunità di attacco da attori dannosi.

Un modo più dettagliato e dettagliato per ridurre la superficie di attacco consiste nel completare un'analisi della superficie di attacco. Un'analisi della superficie di attacco consente di eseguire il mapping delle parti di un sistema che devono essere esaminate e testate per le vulnerabilità della sicurezza.

Lo scopo di un'analisi della superficie di attacco consiste nel comprendere le aree di rischio in un'applicazione, in modo che gli sviluppatori e gli specialisti della sicurezza conoscano quali parti dell'applicazione sono aperte ad attacchi. Quindi, è possibile trovare modi per ridurre al minimo questo potenziale, verificare quando e come la superficie di attacco cambia e cosa significa da una prospettiva di rischio.

Un'analisi della superficie di attacco consente di identificare:

- Funzioni e parti del sistema è necessario esaminare e testare le vulnerabilità della sicurezza.
- Aree ad alto rischio di codice che richiedono la protezione della difesa in profondità (parti del sistema che è necessario difendere).
- Quando si modifica la superficie di attacco ed è necessario aggiornare una valutazione delle minacce.

Riducendo le opportunità per gli utenti malintenzionati di sfruttare una potenziale vulnerabilità o una vulnerabilità, è necessario analizzare accuratamente la superficie di attacco complessiva dell'applicazione. Include anche la disabilitazione o la limitazione dell'accesso ai servizi di sistema, l'applicazione del principio del privilegio minimo e l'utilizzo di difese a più livelli laddove possibile.

Viene illustrato [come eseguire una](secure-develop.md#conduct-attack-surface-review) verifica della superficie di attacco durante la fase di verifica del processo SDL.

> [!NOTE]
> **Qual è la differenza tra la modellazione delle minacce e l'analisi della superficie di attacco?**
La modellazione delle minacce è il processo di identificazione delle potenziali minacce per la sicurezza per l'applicazione e la garanzia che siano applicate le mitigazioni appropriate per le minacce. L'analisi della superficie di attacco identifica aree ad alto rischio di codice aperte ad attacchi. Si tratta di trovare modi per difendere le aree ad alto rischio dell'applicazione e di esaminare e testare le aree di codice prima di distribuire l'applicazione.

### <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>Adottare dei criteri di identità come perimetro di sicurezza primario

Quando si progettano applicazioni cloud, è importante espandere il perimetro di sicurezza da un approccio basato sulla rete a un approccio incentrato sulle identità. Storicamente, il perimetro di sicurezza locale primario era la rete di un'organizzazione. La maggior parte delle progettazioni di sicurezza locali usano la rete come pivot di sicurezza primario. Per le applicazioni cloud, è preferibile prendere in considerazione l'identità come perimetro di sicurezza primario.

Cosa è possibile fare per sviluppare un approccio incentrato sull'identità allo sviluppo di applicazioni Web:

- Applicare l'autenticazione a più fattori per gli utenti.
- usare piattaforme di autenticazione e autorizzazione avanzate.
- Applicare il principio del privilegio minimo.
- Implementare l'accesso just-in-time.

#### <a name="enforce-multi-factor-authentication-for-users"></a>Applicare l'autenticazione a più fattori per gli utenti

Usare l'autenticazione a due fattori. L'autenticazione a due fattori è lo standard corrente per l'autenticazione e l'autorizzazione perché evita i punti deboli di sicurezza intrinseci nei tipi di autenticazione nome utente e password. L'accesso alle interfacce di gestione di Azure (portale di Azure/PowerShell remoto) e ai servizi rivolti ai clienti deve essere progettato e configurato per l'uso di [multi-factor authentication di Azure](../../active-directory/authentication/concept-mfa-howitworks.md).

#### <a name="use-strong-authentication-and-authorization-platforms"></a>Usare piattaforme di autenticazione e autorizzazione complesse

Usare i meccanismi di autenticazione e autorizzazione forniti dalla piattaforma invece di un codice personalizzato. Questo è dovuto al fatto che lo sviluppo di codice di autenticazione personalizzato può essere soggetto a errori. Il codice commerciale, ad esempio da Microsoft, spesso viene ampiamente esaminato per la sicurezza. [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) è la soluzione di Microsoft Azure per la gestione delle identità e degli accessi. Questi strumenti e servizi Azure AD sono utili per lo sviluppo sicuro:

- [Azure ad Identity Platform (Azure ad per sviluppatori)](../../active-directory/develop/about-microsoft-identity-platform.md) è un servizio di identità cloud che gli sviluppatori usano per creare app che assicurano l'accesso agli utenti in modo sicuro. Azure AD assiste gli sviluppatori che sviluppano app LOB (line-of-business) a tenant singolo e sviluppatori che vogliono sviluppare app multi-tenant. Oltre all'accesso di base, le app compilate con Azure AD possono chiamare le API Microsoft e le API personalizzate basate sulla piattaforma di Azure AD. La piattaforma di identità Azure AD supporta protocolli standard di settore come OAuth 2,0 e OpenID Connect.

- [Azure Active Directory B2C (Azure ad B2C)](../../active-directory-b2c/index.yml) è un servizio di gestione delle identità che è possibile usare per personalizzare e controllare la modalità di iscrizione, accesso e gestione dei profili dei clienti quando usano le applicazioni. Sono incluse le applicazioni sviluppate per iOS, Android e .NET, tra le altre. Azure AD B2C Abilita queste azioni proteggendo al tempo stesso le identità dei clienti.

#### <a name="apply-the-principle-of-least-privilege"></a>Applicare il principio dei privilegi minimi

Il concetto di [privilegio minimo](https://en.wikipedia.org/wiki/Principle_of_least_privilege) significa fornire agli utenti il livello di accesso e controllo preciso necessari per svolgere il proprio lavoro e nient'altro.

Uno sviluppatore di software necessita di diritti di amministratore di dominio? Un assistente amministrativo deve poter accedere ai controlli amministrativi nei propri personal computer? La valutazione dell'accesso al software non è diversa. Se si usa il [controllo degli accessi in base al ruolo (RBAC)](../../role-based-access-control/overview.md) per fornire agli utenti diverse capacità e autorità nell'applicazione, non si darebbe accesso a tutti gli elementi. Limitando l'accesso agli elementi necessari per ogni ruolo, si limita il rischio di un problema di sicurezza che si verifica.

Assicurarsi che l'applicazione applichi il [privilegio minimo](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models#in-applications) per tutti i modelli di accesso.

> [!NOTE]
>  Le regole del privilegio minimo devono essere valide per il software e per gli utenti che creano il software. Gli sviluppatori di software possono essere un grosso rischio per la sicurezza IT se hanno un accesso troppo elevato. Le conseguenze possono essere gravi se uno sviluppatore ha finalità dannose o ha un accesso troppo elevato. Si consiglia di applicare le regole con privilegi minimi agli sviluppatori durante tutto il ciclo di vita di sviluppo.

#### <a name="implement-just-in-time-access"></a>Implementare l'accesso just-in-Time

Implementare l'accesso JIT ( *just-in-Time* ) per ridurre ulteriormente il tempo di esposizione dei privilegi. Usare [Azure ad Privileged Identity Management](../../active-directory/users-groups-roles/directory-admin-roles-secure.md#stage-3-build-visibility-and-take-full-control-of-admin-activity) per:

- Fornire agli utenti le autorizzazioni necessarie solo per JIT.
- Assegnare i ruoli per un periodo di tempo ridotto sapendo che i privilegi vengono revocati automaticamente.

### <a name="require-re-authentication-for-important-transactions"></a>Richiedi nuova autenticazione per transazioni importanti

[Richiesta tra siti falsa](https://docs.microsoft.com/aspnet/core/security/anti-request-forgery?view=aspnetcore-2.1) (noto anche come *XSRF* o *CSRF*) è un attacco contro app ospitate da Web in cui un'app Web dannosa influisce sull'interazione tra un browser client e un'app Web che considera attendibile il browser. Gli attacchi di richiesta intersito falsificazione sono possibili perché i Web browser inviano automaticamente alcuni tipi di token di autenticazione con ogni richiesta a un sito Web.
Questa forma di sfruttamento è nota anche come *attacco con un solo clic* o per la *sessione* , perché l'attacco sfrutta la sessione precedentemente autenticata dell'utente.

Il modo migliore per difendersi da questo tipo di attacco è chiedere all'utente un elemento che solo l'utente può fornire prima di ogni transazione importante, ad esempio un acquisto, la disattivazione dell'account o una modifica della password. È possibile chiedere all'utente di immettere nuovamente la password, completare un CAPTCHA o inviare un token segreto che avrebbe solo l'utente. L'approccio più comune è il token segreto.

### <a name="use-a-key-management-solution-to-secure-keys-credentials-and-other-secrets"></a>Usare una soluzione di gestione delle chiavi per proteggere chiavi, credenziali e altri segreti

Perdere chiavi e credenziali è un problema comune. un rischio ben peggiore della perdita di chiavi e credenziali consiste negli accessi non autorizzati. Gli utenti malintenzionati possono sfruttare le tecniche automatizzate e manuali per trovare chiavi e segreti archiviati in repository di codice, ad esempio GitHub. Non inserire chiavi e segreti in questi repository di codice pubblico o in un altro server.

Inserire sempre chiavi, certificati, segreti e stringhe di connessione in una soluzione di gestione delle chiavi. È possibile utilizzare una soluzione centralizzata in cui le chiavi e i segreti sono archiviati in moduli di protezione hardware (HSM). Azure fornisce un modulo di protezione hardware nel cloud con [Azure Key Vault](../../key-vault/key-vault-overview.md).

Key Vault è un *Archivio segreto*: si tratta di un servizio cloud centralizzato per l'archiviazione dei segreti delle applicazioni. Key Vault mantiene i dati riservati in modo sicuro mantenendo i segreti dell'applicazione in un'unica posizione centrale e fornendo accesso sicuro, controllo delle autorizzazioni e registrazione di accesso.

I segreti vengono archiviati in *singoli insiemi*di credenziali. Ogni insieme di credenziali dispone di criteri di configurazione e di sicurezza per controllare l'accesso. È possibile accedere ai dati tramite un'API REST o un SDK client disponibile per la maggior parte dei linguaggi di programmazione.

> [!IMPORTANT]
> Azure Key Vault è progettato per archiviare i segreti di configurazione per le applicazioni server. Non è destinato all'archiviazione dei dati che appartengono agli utenti dell'app. Ciò si riflette nelle caratteristiche di prestazioni, nell'API e nel modello di costo.
>
> I dati utente devono essere archiviati in un'altra posizione, ad esempio in un'istanza del database SQL di Azure con Transparent Data Encryption (Transparent Data Encryption) o in un account di archiviazione che usa crittografia del servizio di archiviazione di Azure. I segreti usati dall'applicazione per accedere a questi archivi dati possono essere conservati in Azure Key Vault.

### <a name="protect-sensitive-data"></a>Proteggere i dati sensibili

La protezione dei dati è una parte essenziale della strategia di sicurezza.
La classificazione dei dati e l'identificazione delle esigenze di protezione dei dati ti permettono di progettare la tua app tenendo presente la sicurezza dei dati. La classificazione (categorizzazione) dei dati archiviati in base alla sensibilità e all'effetto aziendale consente agli sviluppatori di determinare i rischi associati ai dati.

Etichettare tutti i dati applicabili come sensibili quando si progettano i formati di dati. Assicurarsi che l'applicazione consideri i dati applicabili come sensibili. Queste procedure consentono di proteggere i dati sensibili:

- Utilizzare la crittografia.
- Evitare segreti a livello di codice come chiavi e password.
- Verificare che i controlli di accesso e il controllo siano disponibili.

#### <a name="use-encryption"></a>Usa crittografia

La protezione dei dati deve essere una parte essenziale della strategia di sicurezza.
Se i dati vengono archiviati in un database o si spostano da una posizione all'altra, usare la crittografia dei [dati](../fundamentals/encryption-atrest.md) inattivi (nel database) e la crittografia dei [dati in transito](../fundamentals/data-encryption-best-practices.md#protect-data-in-transit) (in modo da e verso l'utente, il database, un'API o un endpoint di servizio). È consigliabile usare sempre i protocolli SSL/TLS per lo scambio di dati. Assicurarsi di usare la versione più recente di TLS per la crittografia (attualmente, questa è la versione 1,2).

#### <a name="avoid-hard-coding"></a>Evitare la programmazione a livello di codice

Alcune operazioni non dovrebbero mai essere hardcoded nel software. Alcuni esempi sono nomi host o indirizzi IP, URL, indirizzi di posta elettronica, nomi utente, password, chiavi dell'account di archiviazione e altre chiavi crittografiche. Prendere in considerazione l'implementazione dei requisiti relativi a quanto può o non può essere hardcoded nel codice, incluse le sezioni relative ai commenti del codice.

Quando si inseriscono commenti nel codice, assicurarsi di non salvare le informazioni riservate. Sono inclusi l'indirizzo di posta elettronica, le password, le stringhe di connessione, le informazioni sull'applicazione che saranno note solo da un utente dell'organizzazione e qualsiasi altra cosa che potrebbe dare a un utente malintenzionato un vantaggio nell'attacco dell'applicazione o dell'organizzazione .

In pratica, si supponga che tutti gli elementi del progetto di sviluppo saranno una conoscenza pubblica al momento della distribuzione. Evitare di includere dati sensibili di qualsiasi tipo nel progetto.

In precedenza, abbiamo discusso [Azure Key Vault](../../key-vault/key-vault-overview.md). È possibile usare Key Vault per archiviare segreti quali chiavi e password anziché impostarli come hardcoded. Quando si usa Key Vault in combinazione con le identità gestite per le risorse di Azure, l'app Web di Azure può accedere ai valori di configurazione segreta in modo semplice e sicuro senza archiviare segreti nel controllo del codice sorgente o nella configurazione. Per altre informazioni, vedere [gestire i segreti nelle app Server con Azure Key Vault](https://docs.microsoft.com/learn/modules/manage-secrets-with-azure-key-vault/).

### <a name="implement-fail-safe-measures"></a>Implementare misure non sicure

L'applicazione deve essere in grado di gestire in modo coerente gli [errori](https://docs.microsoft.com/dotnet/standard/exceptions/) che si verificano durante l'esecuzione. L'applicazione deve intercettare tutti gli errori e non può essere chiusa o chiusa.

È inoltre necessario assicurarsi che gli errori vengano registrati con un contesto utente sufficiente per identificare attività sospette o dannose. I log devono essere conservati per un periodo di tempo sufficiente per consentire l'analisi forense ritardata. I log devono essere in un formato che può essere facilmente utilizzato da una soluzione di gestione dei log. Verificare che vengano attivati gli avvisi relativi a errori correlati alla sicurezza. La registrazione e il monitoraggio insufficienti consentono agli utenti malintenzionati di eseguire ulteriori attacchi e mantenere la persistenza.

### <a name="take-advantage-of-error-and-exception-handling"></a>Sfruttare i vantaggi della gestione degli errori e delle eccezioni

L'implementazione della corretta [gestione](https://docs.microsoft.com/dotnet/standard/exceptions/best-practices-for-exceptions) degli errori e delle eccezioni è una parte importante del codice difensivo. La gestione degli errori e delle eccezioni è fondamentale per rendere un sistema affidabile e sicuro. Gli errori nella gestione degli errori possono comportare diversi tipi di vulnerabilità della sicurezza, ad esempio la perdita di informazioni per gli utenti malintenzionati e gli utenti malintenzionati a comprendere meglio la piattaforma e la progettazione.

Assicurarsi che:

- È possibile gestire le eccezioni in modo centralizzato per evitare [blocchi try/catch](https://docs.microsoft.com/dotnet/standard/exceptions/how-to-use-the-try-catch-block-to-catch-exceptions) duplicati nel codice.

- Tutti i comportamenti imprevisti vengono gestiti all'interno dell'applicazione.

- I messaggi visualizzati dagli utenti non perdono i dati critici ma forniscono informazioni sufficienti per spiegare il problema.

- Le eccezioni vengono registrate e forniscono informazioni sufficienti per analizzare i team forensi o di risposta agli eventi imprevisti.

[App](../../logic-apps/logic-apps-overview.md) per la logica di Azure offre un'esperienza di prima classe per la [gestione di errori ed eccezioni](../../logic-apps/logic-apps-exception-handling.md) causati da sistemi dipendenti. Puoi usare app per la logica per creare flussi di lavoro per automatizzare attività e processi che integrano app, dati, sistemi e servizi tra aziende e organizzazioni.

### <a name="use-logging-and-alerting"></a>Usare la registrazione e gli avvisi

[Registrare](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1) i problemi di sicurezza per le indagini di sicurezza e attivare gli avvisi sui problemi per assicurarsi che gli utenti conoscano i problemi in modo tempestivo. Abilitare il controllo e la registrazione su tutti i componenti. I log di controllo devono acquisire il contesto utente e identificare tutti gli eventi importanti.

Verificare che non vengano registrati dati sensibili che un utente invia al sito. Esempi di dati sensibili:

- Credenziali utente
- Numeri di previdenza sociale o altre informazioni di identificazione
- Numeri di carta di credito o altre informazioni finanziarie
- Informazioni sulla salute
- Chiavi private o altri dati che possono essere usati per decrittografare le informazioni crittografate
- Informazioni sul sistema o sull'applicazione che possono essere usate per attaccare l'applicazione in modo più efficace

Verificare che l'applicazione monitori gli eventi di gestione utenti, ad esempio gli accessi utente riusciti e non riusciti, le reimpostazioni della password, le modifiche alle password, il blocco degli account e la registrazione dell'utente. La registrazione di questi eventi consente di rilevare e reagire a comportamenti potenzialmente sospetti. Consente inoltre di raccogliere dati sulle operazioni, ad esempio gli utenti che accedono all'applicazione.

## <a name="next-steps"></a>Passaggi successivi
Negli articoli seguenti si consigliano i controlli di sicurezza e le attività che consentono di sviluppare e distribuire applicazioni sicure.

- [Sviluppare applicazioni sicure](secure-develop.md)
- [Distribuire applicazioni sicure](secure-deploy.md)
