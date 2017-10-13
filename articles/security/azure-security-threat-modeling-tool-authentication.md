---
title: 'Autenticazione: Microsoft Threat Modeling Tool - Azure | Microsoft Docs'
description: Procedure di mitigazione delle minacce esposte in Threat Modeling Tool
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: e547469dc61eddd1d772571ab0919532ac91f128
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="security-frame-authentication--mitigations"></a>Infrastruttura di sicurezza: autenticazione - Procedure di mitigazione 
| Prodotto o servizio | Articolo |
| --------------- | ------- |
| **Applicazione Web**    | <ul><li>[Valutare l'uso di un meccanismo di autenticazione standard per l'autenticazione nell'applicazione Web](#standard-authn-web-app)</li><li>[Gestire in modo sicuro scenari di autenticazione non riuscita mediante le applicazioni](#handle-failed-authn)</li><li>[Abilitare l'autenticazione adattiva o incrementale](#step-up-adaptive-authn)</li><li>[Assicurarsi che le interfacce amministrative siano bloccate correttamente](#admin-interface-lockdown)</li><li>[Implementare in modo sicuro funzionalità per la password dimenticata](#forgot-pword-fxn)</li><li>[Assicurarsi che vengano implementati criteri di account e password](#pword-account-policy)</li><li>[Implementare controlli per prevenire l'enumerazione del nome utente](#controls-username-enum)</li></ul> |
| **Database** | <ul><li>[Usare l'autenticazione di Windows per la connessione a SQL Server, se possibile](#win-authn-sql)</li><li>[Usare l'autenticazione di Azure Active Directory per la connessione al database SQL, se possibile](#aad-authn-sql)</li><li>[Assicurarsi che vengano applicati i criteri di account e password in SQL Server quando viene usata la modalità di autenticazione SQL](#authn-account-pword)</li><li>[Non usare l'autenticazione SQL in database indipendenti](#autn-contained-db)</li></ul> |
| **Hub eventi di Azure** | <ul><li>[Usare credenziali di autenticazione per dispositivo con i token di firma di accesso condiviso](#authn-sas-tokens)</li></ul> |
| **Limite di trust di Azure** | <ul><li>[Abilitare Azure Multi-Factor Authentication per amministratori di Azure](#multi-factor-azure-admin)</li></ul> |
| **Limite di trust di Service Fabric** | <ul><li>[Limitare l'accesso anonimo a un cluster di Service Fabric](#anon-access-cluster)</li><li>[Assicurarsi che il certificato da client a nodo di Service Fabric sia diverso dal certificato da nodo a nodo](#fabric-cn-nn)</li><li>[Usare Azure Active Directory per autenticare client in cluster di Service Fabric](#aad-client-fabric)</li><li>[Assicurarsi che i certificati di Service Fabric vengano ottenuti da un'Autorità di certificazione (CA) approvata](#fabric-cert-ca)</li></ul> |
| **Identity Server** | <ul><li>[Usare scenari di autenticazione standard supportati da Identity Server](#standard-authn-id)</li><li>[Sostituire la cache dei token di Identity Server predefinita con un'alternativa scalabile](#override-token)</li></ul> |
| **Limite di Trust del computer** | <ul><li>[Assicurarsi che i file binari dell'applicazione distribuita abbiano una firma digitale](#binaries-signed)</li></ul> |
| **WCF** | <ul><li>[Abilitare l'autenticazione nella connessione a code MSMQ in WCF](#msmq-queues)</li><li>[WCF: non impostare il messaggio clientCredentialType su Nessuno](#message-none)</li><li>[WCF: non impostare il trasporto clientCredentialType su Nessuno](#transport-none)</li></ul> |
| **API Web** | <ul><li>[Assicurarsi che vengano usate tecniche di autenticazione standard per proteggere le API Web](#authn-secure-api)</li></ul> |
| **Azure AD** | <ul><li>[Usare scenari di autenticazione standard supportati da Azure Active Directory](#authn-aad)</li><li>[Sostituire la cache dei token di ADAL predefinita con un'alternativa scalabile](#adal-scalable)</li><li>[Assicurarsi che venga usata la proprietà TokenReplayCache per impedire la riproduzione dei token di autenticazione ADAL](#tokenreplaycache-adal)</li><li>[Usare librerie ADAL per gestire le richieste di token da client OAuth2 in AAD o in Active Directory locale](#adal-oauth2)</li></ul> |
| **Gateway IoT sul campo** | <ul><li>[Autenticare dispositivi che si connettono al gateway sul campo](#authn-devices-field)</li></ul> |
| **Gateway IoT cloud** | <ul><li>[Assicurarsi che i dispositivi che si connettono al gateway cloud vengano autenticati](#authn-devices-cloud)</li><li>[Usare credenziali di autenticazione per dispositivo](#authn-cred)</li></ul> |
| **Archiviazione di Azure** | <ul><li>[Assicurarsi che l'accesso in lettura anonimo venga concesso solo ai contenitori e ai BLOB necessari](#req-containers-anon)</li><li>[Concedere l'accesso limitato agli oggetti in Archiviazione di Azure tramite la firma di accesso condiviso o criteri di accesso archiviati](#limited-access-sas)</li></ul> |

## <a id="standard-authn-web-app"></a>Valutare l'uso di un meccanismo di autenticazione standard per l'autenticazione nell'applicazione Web

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web. | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| Dettagli | <p>L'autenticazione è il processo che permette a un'entità di dimostrare la propria identità, in genere mediante l'uso di credenziali, come un nome utente e una password. È possibile prendere in considerazione diversi protocolli di autenticazione disponibili, alcuni dei quali sono elencati di seguito:</p><ul><li>Certificati client</li><li>Basato su Windows</li><li>Basato su form</li><li>Federazione: AD FS</li><li>Federazione: Azure AD</li><li>Federazione: Identity Server</li></ul><p>Valutare l'uso di un meccanismo di autenticazione standard per identificare il processo di origine</p>|

## <a id="handle-failed-authn"></a>Gestire in modo sicuro scenari di autenticazione non riuscita mediante le applicazioni

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web. | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| Dettagli | <p>Le applicazioni che eseguono l'autenticazione esplicita degli utenti devono gestire in modo sicuro gli scenari di autenticazione non riuscita. Il meccanismo di autenticazione deve:</p><ul><li>Negare l'accesso alle risorse con privilegi quando l'autenticazione ha esito negativo.</li><li>Visualizzare un messaggio di errore generico in caso di autenticazione non riuscita e accesso negato.</li></ul><p>Verificare quanto segue:</p><ul><li>Protezione delle risorse con privilegi dopo accessi non riusciti.</li><li>Visualizzazione di un messaggio di errore generico in caso di autenticazione non riuscita e accesso negato.</li><li>Disabilitazione degli account dopo un numero eccessivo di tentativi non riusciti.</li><ul>|

## <a id="step-up-adaptive-authn"></a>Abilitare l'autenticazione adattiva o incrementale

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web. | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| Dettagli | <p>Verificare che l'applicazione abbia un passaggio di autorizzazione aggiuntivo, ad esempio con un'autenticazione adattiva o incrementale, tramite l'autenticazione a più fattori come l'invio di OTP in SMS, messaggi di posta elettronica e così via o con una richiesta di riautenticazione, in modo da coinvolgere l'utente prima di concedere l'accesso a informazioni riservate. Questa regola si applica anche alle modifiche importanti a un account o un'azione.</p><p>Ciò significa che l'adattamento dell'autenticazione deve essere implementato in modo che venga applicata correttamente l'autorizzazione sensibile al contesto per impedire la manipolazione non autorizzata, ad esempio tramite la manomissione dei parametri.</p>|

## <a id="admin-interface-lockdown"></a>Assicurarsi che le interfacce amministrative siano bloccate correttamente

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web. | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| Dettagli | La prima soluzione consiste nel concedere l'accesso all'interfaccia amministrativa solo da un determinato intervallo IP di origine. Se tale soluzione non è applicabile, è sempre consigliabile adottare un'autenticazione adattiva o incrementale per l'accesso all'interfaccia di amministrazione. |

## <a id="forgot-pword-fxn"></a>Implementare in modo sicuro funzionalità per la password dimenticata

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web. | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| Dettagli | <p>Verificare prima di tutto che la funzionalità Password dimenticata e altri percorsi di recupero inviino un collegamento con un token di attivazione di durata limitata, anziché la password stessa. Potrebbe essere necessaria una ulteriore autenticazione basata su token software, come token SMS, applicazioni mobili native e così via, prima dell'invio del collegamento. Evitare quindi di bloccare l'account utente durante il processo di recupero di una nuova password.</p><p>Questo potrebbe causare un attacco Denial of Service ogni volta che un utente malintenzionato decide di bloccare intenzionalmente gli utenti con un attacco automatizzato. Ogni volta che viene avviata la richiesta di una nuova password, il messaggio visualizzato deve essere generalizzato per impedire l'enumerazione del nome utente. Infine, occorre sempre impedire l'uso di password precedenti e implementare criteri di password complessi.</p> |

## <a id="pword-account-policy"></a>Assicurarsi che vengano implementati criteri di account e password

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web. | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| Dettagli | <p>È necessario implementare criteri di account e password in conformità con i criteri e le procedure consigliate dell'organizzazione.</p><p>Per proteggersi da attacchi di forza bruta e attacchi a dizionario è necessario implementare criteri per password complesse, per fare in modo che gli utenti creino password complesse, ad esempio con una lunghezza minima di 12 caratteri o con caratteri speciali e alfanumerici.</p><p>È anche possibile implementare i criteri di blocco account illustrati di seguito:</p><ul><li>**Blocco temporaneo:** questo tipo di blocco è utile per proteggere gli utenti da attacchi di forza bruta. Ad esempio, quando l'utente immette una password errata per tre volte l'applicazione può bloccare l'account per un minuto. Questo rallenta l'attacco di forza bruta alla password e scoraggia l'utente malintenzionato dal continuare. Se si dovessero implementare contromisure di blocco permanente, in questo esempio si otterrebbe un Denial of Service tramite il blocco permanente degli account. In alternativa, l'applicazione può generare una password monouso (OTP) e inviarla fuori banda all'utente, ad esempio tramite posta elettronica, SMS e così via. Un altro approccio consiste nell'implementare CAPTCHA quando viene raggiunto un numero limite di tentativi non riusciti.</li><li>**Blocco permanente:** questo tipo di blocco deve essere applicato quando si rileva un attacco a un'applicazione da parte di un utente. Per contrastarlo, l'account dell'utente viene bloccato in modo permanente fino al termine delle analisi del team di risposta. Dopo tale processo si può decidere se restituire l'account all'utente o intraprendere eventuali azioni legali. Questo tipo di approccio impedisce all'autore dell'attacco di penetrare ulteriormente nell'applicazione e nell'infrastruttura.</li></ul><p>Per difendersi da attacchi ad account prevedibili e predefiniti, verificare che tutte le chiavi e le password siano sostituibili e che vengano generate o sostituite dopo la fase di installazione.</p><p>Se l'applicazione deve generare automaticamente le password, assicurarsi che le password generate siano casuali e abbiano un'entropia elevata.</p>|

## <a id="controls-username-enum"></a>Implementare controlli per prevenire l'enumerazione del nome utente

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web. | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| **Passaggi** | È necessario generalizzare i messaggi di errore per prevenire l'enumerazione del nome utente. A volte non è possibile evitare la divulgazione di informazioni nelle funzionalità, ad esempio in una pagina di registrazione. In tal caso è necessario usare metodi di limitazione della frequenza, ad esempio i CAPTCHA, per prevenite attacchi automatizzati da parte di utenti malintenzionati. |

## <a id="win-authn-sql"></a>Usare l'autenticazione di Windows per la connessione a SQL Server, se possibile

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Database | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Locale |
| **Attributes (Attributi) (Attributi)**              | Versione SQL: tutte |
| **Riferimenti**              | [SQL Server: scegliere una modalità di autenticazione](https://msdn.microsoft.com/library/ms144284.aspx) |
| **Passaggi** | L'autenticazione di Windows usa il protocollo di sicurezza Kerberos, consente l'applicazione dei criteri password per la convalida della complessità delle password e supporta il blocco account e la scadenza delle password.|

## <a id="aad-authn-sql"></a>Usare l'autenticazione di Azure Active Directory per la connessione al database SQL, se possibile

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Database | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | SQL Azure |
| **Attributes (Attributi) (Attributi)**              | Versione SQL: 12 |
| **Riferimenti**              | [Connettersi al Database SQL utilizzando l’autenticazione di Azure Active Directory](https://azure.microsoft.com/documentation/articles/sql-database-aad-authentication/) |
| **Passaggi** | **Versione minima:** è necessario usare la versione 12 per consentire al database SQL di Azure di usare l'autenticazione di AAD nella directory Microsoft. |

## <a id="authn-account-pword"></a>Assicurarsi che vengano applicati i criteri di account e password in SQL Server quando viene usata la modalità di autenticazione SQL

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Database | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | [Criteri password di SQL Server](https://technet.microsoft.com/library/ms161959(v=sql.110).aspx) |
| **Passaggi** | Quando si usa l'autenticazione di SQL Server, in SQL Server vengono creati account di accesso che non sono basati su account utente di Windows. Sia il nome utente che la password vengono creati mediante SQL Server e archiviati in SQL Server. SQL Server può fare uso di meccanismi dei criteri password di Windows. Può applicare la stessa complessità e i criteri di scadenza di Windows alle password usate all'interno di SQL Server. |

## <a id="autn-contained-db"></a>Non usare l'autenticazione SQL in database indipendenti

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Database | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Locale, SQL Azure |
| **Attributes (Attributi) (Attributi)**              | Versione SQL: MSSQL2012, versione SQL: 12 |
| **Riferimenti**              | [Procedure di sicurezza consigliate con i database indipendenti](http://msdn.microsoft.com/library/ff929055.aspx) |
| **Passaggi** | L'assenza di criteri password applicati può aumentare la probabilità che vengano stabilite credenziali deboli in un database indipendente. Fare uso dell'autenticazione di Windows. |

## <a id="authn-sas-tokens"></a>Usare credenziali di autenticazione per dispositivo con i token di firma di accesso condiviso

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Hub eventi di Azure | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | [Panoramica del modello di sicurezza e autenticazione di Hub eventi](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Passaggi** | <p>Il modello di sicurezza di Hub eventi si basa su una combinazione di token di firma di accesso condiviso e autori di eventi. Il nome dell'autore rappresenta l'ID dispositivo che riceve il token. Questo permette di associare i token generati con i rispettivi dispositivi.</p><p>Tutti i messaggi vengono contrassegnati con l'iniziatore sul lato del servizio, per consentire il rilevamento di tentativi di spoofing dell'origine nel payload. Durante l'autenticazione dei dispositivi, generare un token di firma di accesso condiviso per dispositivo con ambito limitato a un autore univoco.</p>|

## <a id="multi-factor-azure-admin"></a>Abilitare Azure Multi-Factor Authentication per amministratori di Azure

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Limite di trust di Azure | 
| **Fase SDL**               | Distribuzione |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | [Informazioni su Azure Multi-Factor Authentication](https://azure.microsoft.com/documentation/articles/multi-factor-authentication/) |
| **Passaggi** | <p>Multi-Factor Authentication (MFA) è un metodo di autenticazione che richiede più di un metodo di verifica e con il quale viene aggiunto un secondo livello di sicurezza critico agli accessi e alle transazioni degli utenti. In genere richiede due o più dei metodi di verifica seguenti:</p><ul><li>Un'informazione nota (in genere una password)</li><li>Un oggetto che si possiede (un dispositivo attendibile non facile da duplicare, ad esempio un telefono)</li><li>Una caratteristica fisica dell'utente (biometrica)</li><ul>|

## <a id="anon-access-cluster"></a>Limitare l'accesso anonimo a un cluster di Service Fabric

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Limite di trust di Service Fabric | 
| **Fase SDL**               | Distribuzione |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | Ambiente: Azure  |
| **Riferimenti**              | [Scenari di sicurezza di un cluster di Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security) |
| **Passaggi** | <p>Per impedire a utenti non autorizzati di connettersi al cluster, è necessario proteggerlo, in particolare quando sono in esecuzione carichi di lavoro di produzione.</p><p>Durante la creazione di un cluster di Service Fabric, assicurarsi che la modalità di sicurezza sia impostata su "secure" e configurare il certificato server X.509 necessario. La creazione di un cluster "insecure" permette a qualsiasi utente anonimo di connettersi al cluster, se questo espone gli endpoint di gestione a Internet pubblico.</p>|

## <a id="fabric-cn-nn"></a>Assicurarsi che il certificato da client a nodo di Service Fabric sia diverso dal certificato da nodo a nodo

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Limite di trust di Service Fabric | 
| **Fase SDL**               | Distribuzione |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | Ambiente: Azure, ambiente: autonomo |
| **Riferimenti**              | [Sicurezza basata su certificati da client a nodo in Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#_client-to-node-certificate-security), [Connettersi a un cluster sicuro tramite il certificato client](https://azure.microsoft.com/documentation/articles/service-fabric-connect-to-secure-cluster/) |
| **Passaggi** | <p>La sicurezza basata su certificati da client a nodo viene configurata durante la creazione del cluster tramite il portale di Azure, modelli di Azure Resource Manager o un modello JSON autonomo specificando un certificato client di amministrazione e/o un certificato client di sola lettura.</p><p>I certificati client di amministrazione e i certificati client utente specificati devono essere diversi dai certificati primario e secondario specificati per la sicurezza da nodo a nodo.</p>|

## <a id="aad-client-fabric"></a>Usare Azure Active Directory per autenticare client in cluster di Service Fabric

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Limite di trust di Service Fabric | 
| **Fase SDL**               | Distribuzione |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | Ambiente: Azure |
| **Riferimenti**              | [Scenari di sicurezza per i cluster: raccomandazioni sulla sicurezza](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#security-recommendations) |
| **Passaggi** | I cluster in esecuzione in Azure possono anche proteggere l'accesso agli endpoint di gestione usando Azure Active Directory (AAD), oltre ai certificati client. Per i cluster di Azure è consigliabile usare la sicurezza di AAD per l'autenticazione dei client e dei certificati per la sicurezza da nodo a nodo.|

## <a id="fabric-cert-ca"></a>Assicurarsi che i certificati di Service Fabric vengano ottenuti da un'Autorità di certificazione (CA) approvata

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Limite di trust di Service Fabric | 
| **Fase SDL**               | Distribuzione |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | Ambiente: Azure |
| **Riferimenti**              | [Certificati X.509 e Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#x509-certificates-and-service-fabric) |
| **Passaggi** | <p>Service Fabric usa i certificati server X.509 per l'autenticazione dei nodi e dei client.</p><p>Aspetti importanti da considerare nell'uso dei certificati in Service Fabric:</p><ul><li>È consigliabile creare i certificati usati nei cluster che eseguono carichi di lavoro di produzione con un servizio certificati di Windows Server configurato correttamente oppure ottenerli da un'Autorità di certificazione (CA) approvata. L'Autorità di certificazione può essere una CA esterna approvata o un'infrastruttura a chiave pubblica (PKI) a gestione interna.</li><li>Non usare mai in fase di produzione certificati temporanei o di test creati con strumenti come MakeCert.exe.</li><li>È possibile usare un certificato autofirmato, ma solo per i cluster di test e non nell'ambiente di produzione.</li></ul>|

## <a id="standard-authn-id"></a>Usare scenari di autenticazione standard supportati da Identity Server

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Identity Server | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | [IdentityServer3: quadro generale](https://identityserver.github.io/Documentation/docsv2/overview/bigPicture.html) |
| **Passaggi** | <p>Di seguito sono elencate le interazioni tipiche supportate da Identity Server:</p><ul><li>Comunicazione tra browser e applicazioni Web.</li><li>Comunicazione tra applicazioni Web e API Web, talvolta in modo autonomo e talvolta per conto dell'utente.</li><li>Comunicazione tra applicazioni basate su browser e API Web.</li><li>Comunicazione tra applicazioni native e API Web.</li><li>Comunicazione tra applicazioni basate su server e API Web.</li><li>Comunicazione tra API Web e API Web, talvolta in modo autonomo e talvolta per conto dell'utente.</li></ul>|

## <a id="override-token"></a>Sostituire la cache dei token di Identity Server predefinita con un'alternativa scalabile

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Identity Server | 
| **Fase SDL**               | Distribuzione |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | [Distribuzione di Identity Server: memorizzazione nella cache](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **Passaggi** | <p>IdentityServer ha una semplice cache in memoria predefinita. Tale cache è sufficiente per le applicazioni native su scala ridotta ma, per i motivi elencati di seguito, non può essere ridimensionata per le applicazioni back-end e di livello intermedio:</p><ul><li>Queste applicazioni sono accessibili da più utenti contemporaneamente. Il salvataggio di tutti i token di accesso nello stesso archivio crea problemi di isolamento e presenta difficoltà quando si opera su vasta scala. Un numero elevato di utenti, ognuno con un numero di token pari a quello delle risorse a cui l'app accede per suo conto, può far aumentare notevolmente la quantità e il costo delle operazioni di ricerca.</li><li>Queste applicazioni vengono in genere distribuite in topologie distribuite, in cui più nodi devono avere accesso alla stessa cache.</li><li>I token memorizzati nella cache devono resistere in caso di disattivazione e riciclo del processo.</li><li>Per tutte queste ragioni, durante l'implementazione di app Web è consigliabile sostituire la cache dei token di Identity Server predefinita con un'alternativa scalabile, ad esempio Cache Redis di Azure.</li></ul>|

## <a id="binaries-signed"></a>Assicurarsi che i file binari dell'applicazione distribuita abbiano una firma digitale

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Limite di trust dei computer | 
| **Fase SDL**               | Distribuzione |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| **Passaggi** | Assicurarsi che i file binari dell'applicazione distribuita abbiano una firma digitale, in modo che sia possibile verificare l'integrità dei file binari.|

## <a id="msmq-queues"></a>Abilitare l'autenticazione nella connessione a code MSMQ in WCF

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico, .NET Framework 3 |
| **Attributes (Attributi) (Attributi)**              | N/D |
| **Riferimenti**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx) |
| **Passaggi** | Il programma non riesce ad abilitare l'autenticazione quando ci si connette a code MSMQ e un utente malintenzionato può inviare messaggi in modo anonimo alla coda per l'elaborazione. Se non si usa l'autenticazione per connettersi a una coda MSMQ usata per recapitare un messaggio a un altro programma, un utente malintenzionato potrebbe inviare un messaggio anonimo dannoso.|

### <a name="example"></a>Esempio
L'elemento `<netMsmqBinding/>` del file di configurazione WCF seguente indica a WCF di disabilitare l'autenticazione quando ci si connette a una coda MSMQ per il recapito di messaggi.
```
<bindings>
    <netMsmqBinding>
        <binding>
            <security>
                <transport msmqAuthenticationMode=""None"" />
            </security>
        </binding>
    </netMsmqBinding>
</bindings>
```
Configurare MSMQ perché venga sempre richiesta l'autenticazione del certificato o del dominio di Windows per qualsiasi messaggio in ingresso o in uscita.

### <a name="example"></a>Esempio
L'elemento `<netMsmqBinding/>` del file di configurazione WCF seguente indica a WCF di abilitare l'autenticazione quando ci si connette a una coda MSMQ. Il client viene autenticato tramite certificati X.509. Il certificato client deve essere presente nell'archivio certificati del server.
```
<bindings>
    <netMsmqBinding>
        <binding>
            <security>
                <transport msmqAuthenticationMode=""Certificate"" />
            </security>
        </binding>
    </netMsmqBinding>
</bindings>
```

## <a id="message-none"></a>WCF: non impostare il messaggio clientCredentialType su Nessuno

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | .NET Framework 3 |
| **Attributes (Attributi) (Attributi)**              | Tipo di credenziali client: nessuno |
| **Riferimenti**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify](https://vulncat.fortify.com/en/vulncat/index.html) |
| **Passaggi** | L'assenza di autenticazione indica che tutti possono accedere a questo servizio. Un servizio che non esegue l'autenticazione dei client consente l'accesso a tutti gli utenti. Configurare l'applicazione per l'autenticazione con le credenziali del client. A tale scopo, è possibile impostare il messaggio clientCredentialType su Windows o su Certificate. |

### <a name="example"></a>Esempio
```
<message clientCredentialType=""Certificate""/>
```

## <a id="transport-none"></a>WCF: non impostare il trasporto clientCredentialType su Nessuno

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico, .NET Framework 3 |
| **Attributes (Attributi) (Attributi)**              | Tipo di credenziali client: nessuno |
| **Riferimenti**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify](https://vulncat.fortify.com/en/vulncat/index.html) |
| **Passaggi** | L'assenza di autenticazione indica che tutti possono accedere a questo servizio. Un servizio che non esegue l'autenticazione dei client consente a tutti gli utenti di accedere alle funzionalità. Configurare l'applicazione per l'autenticazione con le credenziali del client. A tale scopo, è possibile impostare il trasporto clientCredentialType su Windows o su Certificate. |

### <a name="example"></a>Esempio
```
<transport clientCredentialType=""Certificate""/>
```

## <a id="authn-secure-api"></a>Assicurarsi che vengano usate tecniche di autenticazione standard per proteggere le API Web

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | [Autenticazione e autorizzazione nell'API Web ASP.NET](http://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api), [Servizi di autenticazione esterna con l'API Web ASP.NET (C#)](http://www.asp.net/web-api/overview/security/external-authentication-services) |
| **Passaggi** | <p>L'autenticazione è il processo che permette a un'entità di dimostrare la propria identità, in genere mediante l'uso di credenziali, come un nome utente e una password. È possibile prendere in considerazione diversi protocolli di autenticazione disponibili, alcuni dei quali sono elencati di seguito:</p><ul><li>Certificati client</li><li>Basato su Windows</li><li>Basato su form</li><li>Federazione: AD FS</li><li>Federazione: Azure AD</li><li>Federazione: Identity Server</li></ul><p>I collegamenti riportati nella sezione dei riferimenti permettono di ottenere informazioni dettagliate su come implementare ognuno degli schemi di autenticazione per proteggere un'API Web.</p>|

## <a id="authn-aad"></a>Usare scenari di autenticazione standard supportati da Azure Active Directory

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Azure AD | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | [Scenari di autenticazione per Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-authentication-scenarios/), [Esempi di codice di Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-code-samples/), [Guida per gli sviluppatori di Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-developers-guide/) |
| **Passaggi** | <p>Azure Active Directory (Azure AD) semplifica l'autenticazione per gli sviluppatori fornendo le identità come servizio, con il supporto per protocolli standard del settore come OAuth 2.0 e OpenID Connect. Azure AD supporta i cinque scenari di applicazione principali descritti di seguito:</p><ul><li>Da Web browser ad applicazione Web: un utente deve accedere a un'applicazione Web protetta da Azure AD.</li><li>Applicazione a pagina singola (SPA): un utente deve accedere a un'applicazione a pagina singola protetta da Azure AD.</li><li>Da applicazione nativa ad API Web: un'applicazione nativa in esecuzione in un telefono, un tablet o un PC deve autenticare un utente per ottenere risorse da un'API Web protetta da Azure AD.</li><li>Da applicazione Web ad API Web: un'applicazione Web deve ottenere risorse da un'API Web protetta da Azure AD.</li><li>Da daemon o applicazione server ad API Web: un'applicazione daemon o un'applicazione server priva di interfaccia utente Web deve ottenere risorse da un'API Web protetta da Azure AD.</li></ul><p>Per informazioni dettagliate sull'implementazione, vedere i collegamenti riportati nella sezione dei riferimenti.</p>|

## <a id="adal-scalable"></a>Sostituire la cache dei token di ADAL predefinita con un'alternativa scalabile

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Azure AD | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | [Modern Authentication with Azure Active Directory for Web Applications](https://blogs.msdn.microsoft.com/microsoft_press/2016/01/04/new-book-modern-authentication-with-azure-active-directory-for-web-applications/) (Autenticazione moderna con Azure Active Directory per applicazioni Web), [Using Redis as ADAL token cache](https://blogs.msdn.microsoft.com/mrochon/2016/09/19/using-redis-as-adal-token-cache/) (Uso di Redis come cache dei token ADAL)  |
| **Passaggi** | <p>La cache predefinita usata da ADAL (Active Directory Authentication Library) è una cache in memoria che si basa su un archivio statico, disponibile a livello di processo. Tale cache è sufficiente per le applicazioni native ma, per i motivi elencati di seguito, non può essere ridimensionata per le applicazioni back-end e di livello intermedio:</p><ul><li>Queste applicazioni sono accessibili da più utenti contemporaneamente. Il salvataggio di tutti i token di accesso nello stesso archivio crea problemi di isolamento e presenta difficoltà quando si opera su vasta scala. Un numero elevato di utenti, ognuno con un numero di token pari a quello delle risorse a cui l'app accede per suo conto, può far aumentare notevolmente la quantità e il costo delle operazioni di ricerca.</li><li>Queste applicazioni vengono in genere distribuite in topologie distribuite, in cui più nodi devono avere accesso alla stessa cache.</li><li>I token memorizzati nella cache devono resistere in caso di disattivazione e riciclo del processo.</li></ul><p>Per tutte queste ragioni, durante l'implementazione di app Web è consigliabile sostituire la cache dei token ADAL predefinita con un'alternativa scalabile, ad esempio Cache Redis di Azure.</p>|

## <a id="tokenreplaycache-adal"></a>Assicurarsi che venga usata la proprietà TokenReplayCache per impedire la riproduzione dei token di autenticazione ADAL

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Azure AD | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | [Modern Authentication with Azure Active Directory for Web Applications](https://blogs.msdn.microsoft.com/microsoft_press/2016/01/04/new-book-modern-authentication-with-azure-active-directory-for-web-applications/) (Autenticazione moderna con Azure Active Directory per applicazioni Web) |
| **Passaggi** | <p>La proprietà TokenReplayCache consente agli sviluppatori di definire una cache di riproduzione dei token, ovvero un archivio in cui salvare i token allo scopo di verificare che nessun token venga usato più di una volta.</p><p>Si tratta di una misura adatta a un tipo di attacco comune, detto attacco di riproduzione dei token, in cui un utente malintenzionato che intercetta il token inviato al momento dell'accesso potrebbe provare a inviarlo nuovamente all'app, ovvero "riprodurlo", per stabilire una nuova sessione. Ad esempio, nel flusso di concessione del codice OIDC dopo l'autenticazione dell'utente viene inviata una richiesta all'endpoint "/signin-oidc" della relying party con i parametri "id_token", "code" e "state".</p><p>La relying party convalida la richiesta e stabilisce una nuova sessione. Se un antagonista acquisisce questa richiesta e la riproduce, può stabilire una sessione ed effettuare lo spoofing dell'utente. La presenza del parametro nonce in OpenID Connect può limitare, ma non eliminare del tutto, le circostanze che permettono di mettere in atto l'attacco. Per proteggere le applicazioni, gli sviluppatori possono fornire un'implementazione di ITokenReplayCache e assegnare un'istanza a TokenReplayCache.</p>|

### <a name="example"></a>Esempio
```C#
// ITokenReplayCache defined in ADAL
public interface ITokenReplayCache
{
bool TryAdd(string securityToken, DateTime expiresOn);
bool TryFind(string securityToken);
}
```

### <a name="example"></a>Esempio
Di seguito è riportato un esempio di implementazione dell'interfaccia ITokenReplayCache. Personalizzare l'esempio e implementare il framework di memorizzazione nella cache specifico del progetto.
```C#
public class TokenReplayCache : ITokenReplayCache
{
    private readonly ICacheProvider cache; // Your project-specific cache provider
    public TokenReplayCache(ICacheProvider cache)
    {
        this.cache = cache;
    }
    public bool TryAdd(string securityToken, DateTime expiresOn)
    {
        if (this.cache.Get<string>(securityToken) == null)
        {
            this.cache.Set(securityToken, securityToken);
            return true;
        }
        return false;
    }
    public bool TryFind(string securityToken)
    {
        return this.cache.Get<string>(securityToken) != null;
    }
}
```
Le opzioni OIDC devono fare riferimento alla cache implementata tramite la proprietà "TokenValidationParameters", come indicato di seguito.
```C#
OpenIdConnectOptions openIdConnectOptions = new OpenIdConnectOptions
{
    AutomaticAuthenticate = true,
    ... // other configuration properties follow..
    TokenValidationParameters = new TokenValidationParameters
    {
        TokenReplayCache = new TokenReplayCache(/*Inject your cache provider*/);
    }
}
```

Per verificare l'efficacia di questa configurazione, accedere all'applicazione locale protetta da OIDC e acquisire la richiesta all'endpoint `"/signin-oidc"` in Fiddler. In assenza di protezione, la riproduzione di questa richiesta in Fiddler imposta un nuovo cookie di sessione. Quando la richiesta viene riprodotta dopo aver aggiunto la protezione TokenReplayCache, l'applicazione genera un'eccezione simile alla seguente: `SecurityTokenReplayDetectedException: IDX10228: The securityToken has previously been validated, securityToken: 'eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ1......`

## <a id="adal-oauth2"></a>Usare librerie ADAL per gestire le richieste di token da client OAuth2 in AAD o in Active Directory locale

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Azure AD | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | [ADAL](https://azure.microsoft.com/documentation/articles/active-directory-authentication-libraries/) |
| **Passaggi** | <p>Azure AD Authentication Library (ADAL) consente agli sviluppatori di applicazioni client di autenticare facilmente gli utenti in Active Directory locale o nel cloud e quindi di ottenere token di accesso per proteggere le chiamate API.</p><p>Azure AD Authentication Library offre numerose funzionalità che semplificano l'integrazione dell'autenticazione nelle applicazioni da parte degli sviluppatori, ad esempio il supporto asincrono, la cache di token configurabile per l'archiviazione dei token di accesso e dei token di aggiornamento e l'aggiornamento automatico dei token alla scadenza dei token di accesso, i token di aggiornamento e altre ancora.</p><p>Poiché Azure AD Authentication Library gestisce la maggior parte della complessità, gli sviluppatori possono concentrarsi sulla logica di business nell'applicazione e di proteggere le risorse pur non avendo competenze elevate in termini di sicurezza. Sono disponibili librerie separate per .NET, JavaScript (client e Node.js), iOS, Android e Java.</p>|

## <a id="authn-devices-field"></a>Autenticare dispositivi che si connettono al gateway sul campo

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Gateway IoT sul campo | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| **Passaggi** | Assicurarsi che ogni dispositivo venga autenticato dal gateway sul campo prima di accettare dati dal dispositivo e prima di agevolare le comunicazioni upstream con il gateway cloud. Assicurarsi anche che i dispositivi si connettano con credenziali per dispositivo, in modo che sia possibile identificare i singoli dispositivi in modo univoco.|

## <a id="authn-devices-cloud"></a>Assicurarsi che i dispositivi che si connettono al gateway cloud vengano autenticati

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Gateway IoT cloud | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico, C#, Node.js  |
| **Attributes (Attributi) (Attributi)**              | N/A, opzione gateway: Hub IoT di Azure |
| **Riferimenti**              | N/A, [Hub IoT di Azure con .NET](https://azure.microsoft.com/documentation/articles/iot-hub-csharp-csharp-getstarted/), [Introduzione all'hub IoT e Node.js](https://azure.microsoft.com/documentation/articles/iot-hub-node-node-getstarted), [Proteggere l'hub IoT con certificati e firma di accesso condiviso](https://azure.microsoft.com/documentation/articles/iot-hub-sas-tokens/), [Repository Git](https://github.com/Azure/azure-iot-sdks/tree/master/node) |
| **Passaggi** | <ul><li>**Generico:** il dispositivo viene autenticato tramite Transport Layer Security (TLS) o IPSec. L'infrastruttura deve supportare l'uso di una chiave precondivisa (PSK) nei dispositivi che non riescono a gestire la crittografia asimmetrica completa. Usare Azure AD, OAuth.</li><li>**C#:** quando si crea un'istanza di DeviceClient, per impostazione predefinita il metodo Create crea un'istanza di DeviceClient che usa il protocollo AMQP per comunicare con l'hub IoT. Per usare il protocollo HTTPS, usare l'override del metodo Create che consente di specificare il protocollo. Se si usa il protocollo HTTPS, è necessario aggiungere al progetto anche il pacchetto NuGet `Microsoft.AspNet.WebApi.Client` per includere lo spazio dei nomi `System.Net.Http.Formatting`.</li></ul>|

### <a name="example"></a>Esempio
```C#
static DeviceClient deviceClient;

static string deviceKey = "{device key}";
static string iotHubUri = "{iot hub hostname}";

var messageString = "{message in string format}";
var message = new Message(Encoding.ASCII.GetBytes(messageString));

deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

await deviceClient.SendEventAsync(message);
```

### <a name="example"></a>Esempio
**Node.js: autenticazione**
#### <a name="symmetric-key"></a>Chiave simmetrica
* Creare un hub IoT in Azure
* Creare una voce nel registro delle identità dei dispositivi
    ```javascript
    var device = new iothub.Device(null);
    device.deviceId = <DeviceId >
    registry.create(device, function(err, deviceInfo, res) {})
    ```
* Creare un dispositivo simulato
    ```javascript
    var clientFromConnectionString = require('azure-iot-device-amqp').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    var connectionString = 'HostName=<HostName>DeviceId=<DeviceId>SharedAccessKey=<SharedAccessKey>';
    var client = clientFromConnectionString(connectionString);
    ```
#### <a name="sas-token"></a>Token di firma di accesso condiviso
* Viene generato internamente quando si usa una chiave simmetrica, ma è anche possibile generarlo e usarlo in modo esplicito
* Definire un protocollo: `var Http = require('azure-iot-device-http').Http;`
* Creare un token di firma di accesso condiviso:
    ```javascript
    resourceUri = encodeURIComponent(resourceUri.toLowerCase()).toLowerCase();
    var deviceName = "<deviceName >";
    var expires = (Date.now() / 1000) + expiresInMins * 60;
    var toSign = resourceUri + '\n' + expires;
    // using crypto
    var decodedPassword = new Buffer(signingKey, 'base64').toString('binary');
    const hmac = crypto.createHmac('sha256', decodedPassword);
    hmac.update(toSign);
    var base64signature = hmac.digest('base64');
    var base64UriEncoded = encodeURIComponent(base64signature);
    // construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "%2fdevices%2f"+deviceName+"&sig="
    + base64UriEncoded + "&se=" + expires;
    if (policyName) token += "&skn="+policyName;
    return token;
    ```
* Connettersi tramite un token di firma di accesso condiviso: 
    ```javascript
    Client.fromSharedAccessSignature(sas, Http); 
    ```
#### <a name="certificates"></a>Certificati
* Generare un certificato X.509 autofirmato con qualsiasi strumento, ad esempio OpenSSL, per generare file CERT e KEY in cui archiviare, rispettivamente, il certificato e la chiave
* Effettuare il provisioning di un dispositivo che accetta connessioni protette tramite i certificati
    ```javascript
    var connectionString = '<connectionString>';
    var registry = iothub.Registry.fromConnectionString(connectionString);
    var deviceJSON = {deviceId:"<deviceId>",
    authentication: {
        x509Thumbprint: {
        primaryThumbprint: "<PrimaryThumbprint>",
        secondaryThumbprint: "<SecondaryThumbprint>"
        }
    }}
    var device = deviceJSON;
    registry.create(device, function (err) {});
    ```
* Connettere un dispositivo usando un certificato
    ```javascript
    var Protocol = require('azure-iot-device-http').Http;
    var Client = require('azure-iot-device').Client;
    var connectionString = 'HostName=<HostName>DeviceId=<DeviceId>x509=true';
    var client = Client.fromConnectionString(connectionString, Protocol);
    var options = {
        key: fs.readFileSync('./key.pem', 'utf8'),
        cert: fs.readFileSync('./server.crt', 'utf8')
    }; 
    // Calling setOptions with the x509 certificate and key (and optionally, passphrase) will configure the client //transport to use x509 when connecting to IoT Hub
    client.setOptions(options);
    //call fn to execute after the connection is set up
    client.open(fn);
    ```

## <a id="authn-cred"></a>Usare credenziali di autenticazione per dispositivo

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Gateway IoT cloud  | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | Opzione gateway: Hub IoT di Azure |
| **Riferimenti**              | [Token di sicurezza dell'hub IoT di Azure](https://azure.microsoft.com/documentation/articles/iot-hub-sas-tokens/) |
| **Passaggi** | Usare credenziali di autenticazione per dispositivo tramite token di firma di accesso condiviso basati sulla chiave del dispositivo o sul certificato client, anziché criteri di accesso condiviso a livello di hub IoT. Questo permette di prevenire il riutilizzo dei token di autenticazione di un dispositivo o di un gateway sul campo da parte di altri. |

## <a id="req-containers-anon"></a>Assicurarsi che l'accesso in lettura anonimo venga concesso solo ai contenitori e ai BLOB necessari

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Archiviazione di Azure | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | Tipo di archiviazione: BLOB |
| **Riferimenti**              | [Gestire l'accesso in lettura anonimo a contenitori e BLOB](https://azure.microsoft.com/documentation/articles/storage-manage-access-to-resources/), [Firme di accesso condiviso, parte 1: informazioni sul modello di firma di accesso condiviso](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/) |
| **Passaggi** | <p>Per impostazione predefinita, solo il proprietario dell'account di archiviazione può accedere a un contenitore e ai BLOB in esso contenuti. Per concedere a utenti anonimi autorizzazioni di lettura per un contenitore e i relativi BLOB, è possibile impostare le autorizzazioni del contenitore per consentire l'accesso pubblico. Gli utenti anonimi possono leggere i BLOB presenti in un contenitore accessibile pubblicamente senza effettuare l'autenticazione della richiesta.</p><p>I contenitori forniscono le seguenti opzioni per la gestione dell'accesso al contenitore:</p><ul><li>Accesso in lettura pubblico completo: i dati del BLOB e del contenitore possono essere letti tramite richiesta anonima. I client possono enumerare i BLOB all'interno del contenitore tramite richiesta anonima, ma non sono in grado di enumerare i contenitori all'interno dell'account di archiviazione.</li><li>Accesso in lettura pubblico solo per i BLOB: i dati del BLOB all'interno del contenitore possono essere letti tramite richiesta anonima, ma i dati del contenitore non sono disponibili. I client non possono enumerare i BLOB all'interno del contenitore tramite richiesta anonima.</li><li>Nessun accesso in lettura pubblico: i dati del BLOB e del contenitore possono essere letti solo dal proprietario dell'account.</li></ul><p>L'accesso anonimo è ideale per scenari in cui alcuni BLOB devono essere sempre disponibili per l'accesso in lettura anonimo. Per un controllo più capillare, è possibile creare una firma di accesso condiviso, che permette di delegare l'accesso limitato con autorizzazioni diverse e per un intervallo di tempo specificato. Assicurarsi che non venga assegnato per errore l'accesso anonimo a contenitori e BLOB, che possono contenere dati sensibili.</p>|

## <a id="limited-access-sas"></a>Concedere l'accesso limitato agli oggetti in Archiviazione di Azure tramite la firma di accesso condiviso o criteri di accesso archiviati

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Archiviazione di Azure | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D |
| **Riferimenti**              | [Firme di accesso condiviso, parte 1: informazioni sul modello di firma di accesso condiviso](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/), [Firme di accesso condiviso, parte 2: creare e usare una firma di accesso condiviso con l'archivio BLOB](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-2/), [Come delegare l'accesso agli oggetti nell'account usando firme di accesso condiviso e criteri di accesso archiviati](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies) |
| **Passaggi** | <p>La firma di accesso condiviso è uno strumento efficace per concedere ad altri client un accesso limitato agli oggetti nell'account di archiviazione, senza dover esporre la chiave di accesso dell'account. La firma di accesso condiviso è un URI che racchiude nei parametri di query tutte le informazioni necessarie per l'accesso autenticato a una risorsa di archiviazione. Per accedere alle risorse di archiviazione con la firma di accesso condiviso, il client deve solo passare la firma al costruttore o al metodo appropriato.</p><p>È possibile usare una firma di accesso condiviso quando si desidera fornire l'accesso alle risorse dell'account di archiviazione a un client al quale non si desidera fornire la chiave dell'account. Le chiavi dell'account di archiviazione includono una chiave primaria e una chiave secondaria, che garantiscono entrambi accesso amministrativo all'account e a tutte le risorse in esso presenti. Se si espone una delle chiavi dell'account, è possibile che l'account venga utilizzato in modo dannoso o non appropriato. Le firme di accesso condiviso costituiscono un'alternativa sicura per consentire ad altri client di leggere, scrivere ed eliminare dati nell'account di archiviazione sulla base delle autorizzazioni concesse e senza richiedere la chiave dell'account.</p><p>Se è disponibile un set logico di parametri simili ogni volta, è preferibile usare i criteri di accesso archiviati. Poiché l'uso di una firma di accesso condiviso derivata da criteri di accesso archiviati offre la possibilità di revocare immediatamente la firma di accesso condiviso, è consigliabile usare sempre i criteri di accesso archiviati, quando possibile.</p>|