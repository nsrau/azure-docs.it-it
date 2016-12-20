---
title: "Modello di identità federativa| Azure | Documentazione Microsoft"
description: "È possibile delegare l&quot;autenticazione a un provider di identità esterno."
categories:
- security
keywords: schema progettuale
author: dragon119
manager: bennage
ms.service: guidance
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.author: mwasson
ms.date: 11/14/2016
translationtype: Human Translation
ms.sourcegitcommit: c4963489ca53a90cab480116ec0a044501ccb5e9
ms.openlocfilehash: bf3442b1f89ea0288fd60f1177638b5cb43cc7b6

---

# <a name="federated-identity"></a>Identità federata

È possibile delegare l'autenticazione a un provider di identità esterno. Questo può semplificare lo sviluppo, ridurre al minimo i requisiti per l'amministrazione utenti e migliorare l'esperienza utente dell'applicazione.

## <a name="context-and-problem"></a>Contesto e problema

Gli utenti di solito usano più applicazioni messe a disposizione e ospitate da diverse organizzazioni con cui hanno relazioni commerciali. Ogni organizzazione potrebbe richiedere l'uso di credenziali specifiche (e diverse). Ciò potrebbe causare quanto segue:

- **Un'esperienza utente frammentaria**. Gli utenti dimenticano spesso le credenziali di accesso quando ne usano molte.

- **Esposizione di vulnerabilità della sicurezza**. Quando un utente lascia l'azienda, il suo account deve essere immediatamente sottoposto a deprovisioning. È facile trascurare questa esigenza nelle aziende di grandi dimensioni.

- **Gestione complicata degli utenti**. Gli amministratori devono gestire le credenziali per tutti gli utenti ed eseguire attività aggiuntive, ad esempio inviare promemoria per le password.

Gli utenti in genere preferiscono usare le stesse credenziali per tutte le applicazioni.

## <a name="solution"></a>Soluzione

Implementare un meccanismo di autenticazione in grado di usare l'identità federativa. Separare l'autenticazione utente dal codice dell'applicazione e delegare l'autenticazione a un provider di identità attendibile. In questo modo si semplifica lo sviluppo e si consente agli utenti di eseguire l'autenticazione usando un'ampia gamma di provider di identità (IdP), riducendo al minimo il carico amministrativo. Si consente inoltre di separare chiaramente l'autenticazione dall'autorizzazione.

I provider di identità attendibili includono le directory aziendali, i servizi di federazione locali, altri servizi token di sicurezza (STS) di partner commerciali e i provider di identità social in grado di autenticare gli utenti che hanno, ad esempio, un account Microsoft, Google, Yahoo! o Facebook.

Nella figura viene illustrato il modello di identità federativa quando un'applicazione client deve accedere a un servizio che richiede l'autenticazione. L'autenticazione viene eseguita da un IdP che interagisce con un servizio token di sicurezza. L'IdP rilascia token di sicurezza con informazioni relative all'utente autenticato. Tali informazioni, denominate attestazioni, includono l'identità dell'utente e possono includere anche altre informazioni, ad esempio l'appartenenza ai ruoli e diritti di accesso più granulari.

![Panoramica dell'autenticazione federata](images/federated-identity-overview.png)


Questo modello viene spesso definito controllo di accesso basato sulle attestazioni. Le applicazioni e i servizi autorizzano l'accesso alle funzionalità basate sulle attestazioni contenute nel token. Il servizio che richiede l'autenticazione deve considerare attendibile il provider di identità. L'applicazione client contatta il provider di identità che esegue l'autenticazione. Se l'autenticazione ha esito positivo, il provider di identità restituisce un token contenente le attestazioni che identificano l'utente per il servizio STS. Si noti che il provider di identità e il servizio token di sicurezza possono essere lo stesso servizio. Il servizio STS può trasformare e ottimizzare le attestazioni nel token in base a regole predefinite, prima di restituirlo al client. L'applicazione client può quindi passare questo token al servizio come prova dell'identità.

>  Possono essere presenti altri servizi token di sicurezza nella catena di trust. Ad esempio, nello scenario descritto in seguito, un servizio token di sicurezza locale ritiene attendibile un altro servizio token di sicurezza che è responsabile dell'accesso a un provider di identità per autenticare l'utente. Questo approccio è comune in scenari aziendali in cui sono presenti un servizio token di sicurezza e una directory locali.

L'autenticazione federata offre una soluzione basata su standard per il problema dell'attendibilità delle identità tra domini diversi ed è in grado di supportare l'accesso Single Sign-On. Viene usata sempre più di frequente in tutti i tipi di applicazioni, soprattutto in quelle ospitate nel cloud, poiché supporta il Single Sign-On senza richiedere una connessione di rete diretta ai provider di identità. L'utente non deve immettere le credenziali per ogni applicazione. Ciò aumenta la protezione poiché viene impedita la creazione delle credenziali necessarie per accedere a molte applicazioni diverse e le credenziali dell'utente sono nascoste per tutti tranne il provider di identità originale. Le applicazioni vedono solo le informazioni dell'identità autenticata contenute all'interno del token.

Il vantaggio principale offerto dall'identità federativa è il fatto che la gestione delle identità e delle credenziali è responsabilità del provider di identità. L'applicazione o il servizio non richiede l'uso di funzionalità di gestione di identità. Inoltre, negli scenari aziendali, la directory aziendale non richiede informazioni sull'utente se considera attendibile il provider di identità. In questo modo si elimina tutto il carico amministrativo dovuto alla gestione delle identità degli utenti nella directory.

## <a name="issues-and-considerations"></a>Considerazioni e problemi

Quando si progettano applicazioni che implementano l'autenticazione federata, tenere presente quanto segue:

- L'autenticazione può essere un singolo punto di errore. Se si distribuisce l'applicazione a più data center, considerare la possibilità di distribuire il meccanismo di gestione delle identità agli stessi data center per mantenere l'affidabilità e la disponibilità dell'applicazione.

- Gli strumenti di autenticazione consentono di configurare il controllo di accesso in base alle attestazioni di ruolo contenute nel token di autenticazione. Ciò è noto anche come controllo di accesso basato sui ruoli (RBAC) e può consentire un livello più granulare di controllo sull'accesso a funzionalità e risorse.

- A differenza di una directory aziendale, l'autenticazione basata sulle attestazioni che usa i provider di identità social generalmente non offre informazioni sull'utente autenticato a parte un indirizzo di posta elettronica e forse un nome. Alcuni provider di identità social, ad esempio un account Microsoft, usano solo un identificatore univoco. In genere, l'applicazione deve gestire alcune informazioni sugli utenti registrati ed essere in grado di associare queste informazioni all'identificatore contenuto nelle attestazioni nel token. Ciò avviene di solito durante la registrazione quando l'utente accede per la prima volta all'applicazione e in seguito le informazioni vengono inserite nel token come attestazioni aggiuntive dopo ogni autenticazione.

- Se è presente più di un provider di identità configurato per il servizio token di sicurezza, è necessario rilevare a quale provider di identità deve essere reindirizzato l'utente per l'autenticazione. Questo processo è detto individuazione dell'area di autenticazione. Il servizio token di sicurezza potrebbe essere in grado di eseguire questa operazione automaticamente in base a un indirizzo di posta elettronica o un nome utente specificato dall'utente, un sottodominio dell'applicazione a cui accede l'utente, l'ambito dell'indirizzo IP dell'utente o il contenuto di un cookie memorizzato nel browser dell'utente. Ad esempio, se l'utente ha immesso un indirizzo di posta elettronica nel dominio Microsoft, ad esempio user@live.com,, il servizio token di sicurezza reindirizzerà l'utente alla pagina di accesso dell'account Microsoft. Nelle visite successive, il servizio token di sicurezza può usare un cookie per indicare che l'ultimo accesso è stato effettuato con un account Microsoft. Se l'individuazione automatica non è in grado di determinare l'area di autenticazione principale, il servizio token di sicurezza visualizza una pagina di individuazione dell'area di autenticazione in cui sono elencati i provider di identità attendibili e l'utente deve selezionare quello che vuole utilizzare.

## <a name="when-to-use-this-pattern"></a>Quando usare questo modello

Questo modello è utile per scenari come:

- **Single Sign-On nell'azienda**. In questo scenario i dipendenti vengono autenticati per le applicazioni aziendali ospitate nel cloud all'esterno dei limiti di protezione aziendale, in modo che non siano obbligati a effettuare l'accesso ogni volta che visitano un'applicazione. L'esperienza utente è identica a quella in cui si usano le applicazioni locali per cui si è autenticati quando si accede a una rete aziendale e da questo momento i dipendenti hanno accesso a tutte le applicazioni pertinenti senza dover eseguire nuovamente l'accesso. 

- **Identità federativa con più partner**. In questo scenario è necessario autenticare sia i dipendenti aziendali che i partner aziendali che non hanno un account nella directory aziendale. Ciò è comune nelle applicazioni business-to-business, applicazioni integrate con servizi di terze parti in cui le aziende con sistemi IT diversi hanno risorse unite o condivise.

- **Identità federativa nelle applicazioni SaaS**. In questo scenario i fornitori di software indipendenti offrono un servizio pronto per l'uso per più client o tenant. Ogni tenant viene autenticato usando un provider di identità appropriato. Ad esempio, gli utenti aziendali useranno le proprie credenziali aziendali, mentre i consumer e i client del tenant useranno le credenziali di identità social.

Questo modello potrebbe non essere utile nelle situazioni seguenti:

- Tutti gli utenti dell'applicazione possono essere autenticati da un solo provider di identità e non è obbligatorio eseguire l'autenticazione usando altri provider di identità. Questo è tipico nelle applicazioni aziendali che usano una directory aziendale (accessibile nell'applicazione) per l'autenticazione, usando una VPN o, in uno scenario ospitato nel cloud, una connessione di rete virtuale tra la directory locale e l'applicazione.

- L'applicazione originariamente è stata creata usando un diverso meccanismo di autenticazione, probabilmente con archivi utente personalizzati, oppure non è in grado di gestire gli standard di negoziazione usati dalle tecnologie basate su attestazioni. L'adeguamento del controllo di accesso e dell'autenticazione basata su attestazioni nelle applicazioni esistenti può essere complesso ed economicamente non conveniente.

## <a name="example"></a>Esempio

Un'organizzazione ospita un'applicazione SaaS in Microsoft Azure. L'applicazione include un sito Web che i tenant possono usare per gestire l'applicazione per i propri utenti. L'applicazione consente ai tenant di accedere al sito Web usando un'identità federativa generata da Active Directory Federation Services (ADFS) quando un utente è autenticato da tale organizzazione con Active Directory.

![Come accedono all'applicazione gli utenti di un sottoscrittore aziendale di grandi dimensioni](images/federated-identity-multitenat.png)


Nella figura viene illustrato come i tenant eseguono l'autenticazione con il proprio provider di identità (passaggio 1), in questo caso ADFS. Dopo aver autenticato un tenant, ADFS rilascia un token. Il browser client inoltra il token al provider federativo dell'applicazione SaaS, che considera attendibili i token emessi da ADFS del tenant, per ottenere un token valido per il provider federativo SaaS (passaggio 2). Se necessario, il provider federativo SaaS esegue una trasformazione delle attestazioni contenute nel token in attestazioni che l'applicazione è in grado di riconoscere (passaggio 3) prima di restituire il nuovo token al browser client. L'applicazione considera attendibili i token rilasciati dal provider federativo SaaS e usa le attestazioni nel token per applicare le regole di autorizzazione (passaggio 4).

I tenant non dovranno ricordare credenziali separate per accedere all'applicazione e un amministratore della società del tenant può configurare nel proprio servizio ADFS l'elenco di utenti che possono accedere all'applicazione.

## <a name="related-guidance"></a>Informazioni correlate

- [Microsoft Azure Active Directory](https://azure.microsoft.com/services/active-directory/)
- [Active Directory Domain Services](https://msdn.microsoft.com/library/bb897402.aspx)
- [Active Directory Federation Services](https://msdn.microsoft.com/library/bb897402.aspx)
- [Gestione delle identità per le applicazioni multi-tenant in Microsoft Azure](https://azure.microsoft.com/documentation/articles/guidance-multitenant-identity/)
- [Applicazioni multi-tenant in Azure](https://azure.microsoft.com/documentation/articles/dotnet-develop-multitenant-applications/)



<!--HONumber=Nov16_HO3-->


