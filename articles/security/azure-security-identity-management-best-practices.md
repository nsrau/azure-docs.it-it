<properties
   pageTitle="Procedure consigliate per la sicurezza con il controllo di accesso e la gestione delle identità di Azure | Microsoft Azure"
   description="Questo articolo illustra una serie di procedure consigliate per il controllo di accesso e la gestione delle identità usando le funzionalità integrate di Azure."
   services="security"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="yurid"/>

# Procedure consigliate per la sicurezza con il controllo di accesso e la gestione delle identità di Azure

Molti considerano l'identità come il nuovo livello limite per la sicurezza, sostituendo in questo ruolo la prospettiva tradizionale incentrata sulla rete. Questa evoluzione nell'attenzione per la sicurezza e negli investimenti è dovuta al fatto che i perimetri di rete sono diventati sempre più permeabili e la difesa perimetrale non può più essere efficace quanto prima dell'esplosione delle applicazioni cloud e dei dispositivi [BYOD](http://aka.ms/byodcg).

Questo articolo illustra una serie di procedure consigliate per la sicurezza con il controllo di accesso e la gestione delle identità di Azure. Le procedure consigliate si basano sull'esperienza di tecnici e clienti con [Azure AD](../active-directory/active-directory-whatis.md).

Per ogni procedura consigliata verrà illustrato:

- Qual è la procedura consigliata
- Il motivo per cui si vuole abilitare tale procedura consigliata
- Quale potrebbe essere il risultato se non fosse possibile abilitare la procedura consigliata
- Alternative possibili alla procedura consigliata
- Come imparare ad abilitare la procedura consigliata

Questo articolo sulle procedure consigliate per la sicurezza con il controllo di accesso e la gestione delle identità di Azure si basa su pareri condivisi, nonché sulle capacità e sui set di funzionalità della piattaforma Azure esistenti quando l'articolo è stato scritto. Le opinioni e le tecnologie cambiano nel tempo e questo articolo verrà aggiornato regolarmente per riflettere tali modifiche.

Procedure consigliate per la sicurezza con il controllo di accesso e la gestione delle identità di Azure illustrate in questo articolo:

- Centralizzare la gestione delle identità
- Abilitare il Single Sign-On (SSO)
- Distribuire la gestione delle password
- Applicare l'autenticazione a più fattori (MFA) per gli utenti
- Usare il controllo degli accessi in base al ruolo
- Controllare i percorsi di creazione delle risorse con Resource Manager
- Guidare gli sviluppatori nell'utilizzo delle funzionalità di identità per app SaaS
- Monitorare attivamente le attività sospette

## Centralizzare la gestione delle identità

Per la protezione dell'identità è importante fare in modo che il reparto IT possa gestire gli account da un'unica posizione rispetto al punto in cui l'account è stato creato. Nella maggior parte delle organizzazioni la directory dell'account primario del reparto IT è in locale, ma si stanno affermando anche distribuzioni cloud ibride. È quindi importante comprendere come integrare le directory cloud e quelle locali e offrire un'esperienza coerente all'utente finale.

Per realizzare questo scenario di [soluzione ibrida di gestione delle identità](../active-directory/active-directory-hybrid-identity-design-considerations-overview.md), è consigliabile procedere come segue:

- Sincronizzare la directory locale con la directory cloud usando Azure AD Connect.
- Attuare la federazione dell'identità locale con la directory cloud usando [Active Directory Federation Services](https://msdn.microsoft.com/library/bb897402.aspx) (AD FS).

Le organizzazioni che non provvedono all'integrazione dell'identità locale con l'identità cloud sono soggette a un maggiore sovraccarico amministrativo nella gestione degli account, che aumenta le probabilità di errore e le violazioni della sicurezza.

Per altre informazioni sulla sincronizzazione con Azure AD, vedere [Integrazione delle identità locali con Azure Active Directory](../active-directory/active-directory-aadconnect.md).

## Abilitare il Single Sign-On (SSO)

La gestione di più directory rappresenta un problema di amministrazione non solo per il reparto IT, ma anche per gli utenti finali che devono tenere a mente più password. L'accesso [SSO](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) offre agli utenti la possibilità di usare lo stesso set di credenziali per accedere alle risorse necessarie, indipendentemente dalla posizione di tali risorse in locale o nel cloud.

Usare SSO per consentire agli utenti di accedere alle [applicazioni SaaS](../active-directory/active-directory-appssoaccess-whatis.md) in base all'account aziendale in Azure AD. Questo vale non solo per le app SaaS Microsoft, ma anche per altre app come [Google Apps](../active-directory/active-directory-saas-google-apps-tutorial.md) e [Salesforce](../active-directory/active-directory-saas-salesforce-tutorial.md). L'applicazione può essere configurata per l'uso di Azure AD come provider di [identità basata su SAML](../active-directory/fundamentals-identity.md). Come controllo di sicurezza, Azure AD non rilascia un token per l'accesso all'applicazione a meno che l'accesso non sia stato concesso con Azure AD. L'accesso può essere concesso direttamente agli utenti o attraverso un gruppo di cui sono membri.

> [AZURE.NOTE] La decisione di usare il Single Sign-On influisce sulla modalità di integrazione della directory locale con la directory cloud. Per usare SSO, è necessario usare la federazione, perché la sincronizzazione della directory offre solo un'[esperienza "Same Sign-On"](../active-directory/active-directory-aadconnect.md).

Nelle organizzazioni che non adottano il Single Sign-On per gli utenti e le applicazioni gli utenti devono gestire più password. Questo aumenta direttamente le probabilità che vengano usate password vulnerabili oppure le stesse password più volte.

Per altre informazioni sull'uso di SSO con Azure AD, vedere [Gestione e personalizzazione di AD FS con Azure AD Connect](../active-directory/active-directory-aadconnect-federation-management.md).

## Distribuire la gestione delle password

In scenari in cui sono presenti più tenant o si vuole consentire agli utenti di [reimpostare la propria password](../active-directory/active-directory-passwords-update-your-own-password.md) è importante adottare criteri di sicurezza adatti per prevenire l'uso improprio. In Azure è possibile usare la funzionalità di reimpostazione della password self-service e personalizzare le opzioni di sicurezza per soddisfare i requisiti aziendali.

È molto importante ottenere commenti e suggerimenti dagli utenti e imparare dalla loro esperienza di esecuzione di questi passaggi. In base all'esperienza degli utenti, elaborare un piano per attenuare i problemi che possono verificarsi durante la distribuzione per un gruppo di dimensioni maggiori. È anche consigliabile usare il [report Attività di registrazione reimpostazione password](../active-directory/active-directory-passwords-get-insights.md) per monitorare gli utenti che eseguono la registrazione.

Le organizzazioni che vogliono evitare le richieste di supporto per la modifica delle password ma consentono agli utenti di reimpostare le proprie password sono più soggette a volumi elevati di richieste di assistenza a causa di problemi con le password. Nelle organizzazioni con più tenant è fondamentale implementare questo tipo di funzionalità e consentire agli utenti di reimpostare le proprie password entro limiti precisi stabiliti nei criteri di sicurezza delle password.

Per altre informazioni sulla reimpostazione della password, vedere [Distribuzione di Gestione delle password e formazione degli utenti per l'uso](../active-directory/active-directory-passwords-best-practices.md).

## Applicare l'autenticazione a più fattori (MFA) per gli utenti

Per le organizzazioni che devono essere conformi agli standard di settore, ad esempio [PCI DSS versione 3.2](http://blog.pcisecuritystandards.org/preparing-for-pci-dss-32), l'autenticazione a più fattori è una funzionalità indispensabile per l'autenticazione degli utenti. L'uso dell'autenticazione a più fattori per autenticare gli utenti permette alle organizzazioni non solo di essere conformi agli standard di settore, ma anche di attenuare gli attacchi con furto di credenziali, ad esempio [Pass-the-Hash (PtH)](http://aka.ms/PtHPaper).

Quando si abilita Azure MFA per gli utenti, si aggiunge un secondo livello di sicurezza agli accessi e alle transazioni degli utenti. In questo caso, una transazione potrebbe accedere a un documento che si trova in un file server o in SharePoint Online. Grazie a Azure MFA, i reparti IT possono anche ridurre la probabilità di accesso ai dati dell'organizzazione usando credenziali compromesse.

Ad esempio: è possibile applicare Azure MFA per gli utenti e configurarlo per l'uso di una telefonata o di un messaggio di testo di verifica. Se le credenziali dell'utente vengono compromesse, l'autore dell'attacco non può accedere alle risorse perché non ha accesso al telefono dell'utente. Le organizzazioni che non aggiungono livelli supplementari di protezione delle identità sono più vulnerabili agli attacchi con furto di credenziali, con conseguente rischio di compromissione dei dati.

In alternativa, le organizzazioni che vogliono mantenere in locale tutto il controllo dell'autenticazione possono usare il [server Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication-get-started-server.md), detto anche MFA locale. Questo metodo consentirà comunque di applicare l'autenticazione a più fattori, mantenendo il server MFA in locale.

Per altre informazioni su Azure MFA, vedere l'articolo [Introduzione ad Azure Multi-Factor Authentication nel cloud](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).

## Usare il controllo degli accessi in base al ruolo

Per le organizzazioni che intendono applicare criteri di sicurezza per l'accesso ai dati è fondamentale limitare l'accesso in base a principi di [riservatezza](https://en.wikipedia.org/wiki/Need_to_know) e [privilegi minimi](https://en.wikipedia.org/wiki/Principle_of_least_privilege). Il controllo degli accessi in base al ruolo di Azure consente di assegnare autorizzazioni a utenti, gruppi e applicazioni in un determinato ambito. L'ambito di un'assegnazione di ruolo può essere una sottoscrizione, un gruppo di risorse o una singola risorsa.

Si possono sfruttare i [ruoli predefiniti del controllo degli accessi in base al ruolo](../active-directory/role-based-access-built-in-roles.md) in Azure per assegnare privilegi agli utenti. È consigliabile usare il ruolo *Collaboratore Account di archiviazione* per gli operatori cloud che devono gestire account di archiviazione e il ruolo *Collaboratore Account di archiviazione classico* per gestire gli account di archiviazione della versione classica. Per quanto riguarda gli operatori cloud che devono gestire le macchine virtuali e l'account di archiviazione, è consigliabile aggiungerli al ruolo *Collaboratore Macchina virtuale*.

Le organizzazioni che non applicano il controllo di accesso ai dati con funzionalità come il controllo degli accessi in base al ruolo potrebbero concedere più privilegi del necessario agli utenti. Questo può comportare la compromissione dei dati perché gli utenti potrebbero avere accesso a tipi di dati, come importanti dati aziendali, a cui non dovrebbero accedere.

Per altre informazioni sul controllo degli accessi in base al ruolo di Azure, vedere l'articolo [Controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-control-configure.md).

## Controllare i percorsi di creazione delle risorse con Resource Manager

Permettere agli operatori cloud di eseguire attività senza infrangere convenzioni necessarie per la gestione delle risorse dell'organizzazione è molto importante. Per controllare i percorsi in cui le risorse vengono create, è consigliabile impostarli come hardcoded a livello dell'organizzazione.

A tale scopo, l'organizzazione può creare criteri di sicurezza con definizioni che descrivono le risorse o le azioni negate in modo specifico. Le definizioni dei criteri vengono assegnate all'ambito desiderato, ad esempio la sottoscrizione, un gruppo di risorse o una singola risorsa.

> [AZURE.NOTE] Questo metodo non equivale al controllo degli accessi in base al ruolo, ma lo usa per autenticare gli utenti che hanno i privilegi necessari per creare tali risorse.

È possibile usare [Azure Resource Manager](../resource-group-overview.md) per creare criteri personalizzati anche per scenari in cui l'organizzazione vuole consentire solo le operazioni associate al centro di costo appropriato. In caso contrario, la richiesta viene negata.

Le organizzazioni che non controllano le modalità di creazione delle risorse sono più soggette a un uso improprio del servizio da parte degli utenti, che potrebbero creare più risorse del necessario. La protezione avanzata del processo di creazione delle risorse è un passaggio importante per la protezione di uno scenario multi-tenant.

Per altre informazioni sulla creazione di criteri con Azure Resource Manager, vedere [Usare i criteri per gestire le risorse e controllare l'accesso](../resource-manager-policy.md).

## Guidare gli sviluppatori nell'utilizzo delle funzionalità di identità per app SaaS

L'identità dell'utente viene usata in molti scenari quando gli utenti accedono ad [app SaaS](https://azure.microsoft.com/marketplace/active-directory/all/) che possono essere integrate in directory locali o cloud. Gli sviluppatori stessi dovrebbero usare una metodologia sicura per lo sviluppo di queste applicazioni, ad esempio [Microsoft Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/default.aspx). Azure AD semplifica l'autenticazione per gli sviluppatori fornendo le identità come servizio, con il supporto per protocolli standard del settore come [OAuth 2.0](http://oauth.net/2/) e [OpenID Connect](http://openid.net/connect/), nonché librerie open source per diverse piattaforme.

Assicurarsi di registrare qualsiasi applicazione che affida l'autenticazione ad Azure AD. Si tratta di una procedura obbligatoria. Questo perché Azure AD deve coordinare le comunicazioni con l'applicazione quando gestisce l'accesso SSO o lo scambio di token. La sessione dell'utente scade al termine del periodo di validità del token emesso da Azure AD. Valutare sempre se tale periodo di validità è necessario per l'applicazione o se è possibile ridurlo. La riduzione della durata può fungere da misura di sicurezza che forza la disconnessione degli utenti in base a un periodo di inattività.

Le organizzazioni che non applicano il controllo dell'identità per l'accesso alle app e non forniscono indicazioni agli sviluppatori per l'integrazione sicura delle app con il sistema di gestione delle identità possono essere più soggette ad attacchi con furto di credenziali. Vedere in proposito la descrizione di [gestione della sessione e autenticazione vulnerabile nella top 10 OWASP (Open Web Application Security Project)](https://www.owasp.org/index.php/OWASP_Top_Ten_Cheat_Sheet).

Per altre informazioni sugli scenari di autenticazione per app SaaS, vedere [Scenari di autenticazione per Azure AD](../active-directory/active-directory-authentication-scenarios.md).

## Monitorare attivamente le attività sospette

Secondo il [report sulla violazione dei dati 2016 redatto da Verizon](http://www.verizonenterprise.com/verizon-insights-lab/dbir/2016/), la compromissione delle credenziali è ancora in aumento e sta diventando una delle attività più redditizie per il crimine informatico. Per questo motivo è importante avere un sistema di monitoraggio attivo delle identità che possa rilevare rapidamente i comportamenti sospetti e attivare avvisi per richiedere analisi aggiuntive. Azure AD offre due importanti funzionalità che consentono alle organizzazioni di monitorare le identità: i [report anomalie](../active-directory/active-directory-view-access-usage-reports.md) di Azure AD Premium e Azure AD [Identity Protection](../active-directory/active-directory-identityprotection.md).

Usare i report anomalie per identificare i tentativi di accesso [senza tracciamento](../active-directory/active-directory-reporting-sign-ins-from-unknown-sources.md), gli attacchi di [forza bruta](../active-directory/active-directory-reporting-sign-ins-after-multiple-failures.md) contro un account specifico, i tentativi di accesso da più posizioni, l'accesso da [dispositivi infetti](../active-directory/active-directory-reporting-sign-ins-from-possibly-infected-devices.md) e gli indirizzi IP sospetti. Tenere presente che si tratta di report. È quindi necessario predisporre processi e procedure in modo che gli amministratori IT possano eseguire tali report ogni giorno o su richiesta, in genere in uno scenario di risposta a eventi imprevisti.

Azure AD Identity Protection è invece un sistema di monitoraggio attivo che contrassegna i rischi correnti nel relativo dashboard e invia notifiche di riepilogo giornaliere tramite posta elettronica. È consigliabile modificare il livello di rischio in base ai requisiti aziendali. Il livello di rischio è un'indicazione della gravità, misurata come alta, media o bassa, di un evento di rischio. Il livello di rischio consente agli utenti di Identity Protection di classificare in ordine di priorità le azioni da intraprendere per ridurre il rischio per l'organizzazione.

Le organizzazioni che non monitorano attivamente i sistemi di identità sono esposti al rischio di compromissione delle credenziali utente. Se l'organizzazione non è a conoscenza dello svolgimento di attività sospette con tali credenziali, non potrà attenuare questo tipo di minaccia. Per altre informazioni in merito, vedere [Azure Active Directory Identity Protection](../active-directory/active-directory-identityprotection.md).

<!---HONumber=AcomDC_0817_2016-->