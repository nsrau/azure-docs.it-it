---
title: "Comprendere le identità di Azure | Microsoft Docs"
description: "Ottenere una conoscenza di base dei termini, dei concetti e delle raccomandazioni relativi alle soluzioni di gestione delle identità di Microsoft Azure, per migliorare il processo decisionale in termini di governance delle identità per la propria organizzazione."
keywords: 
author: jeffgilb
manager: mtillman
ms.reviewer: jsnow
ms.author: jeffgilb
ms.date: 7/17/2017
ms.topic: article
ms.prod: 
ms.service: azure
ms.technology: 
ms.assetid: 
ms.custom: it-pro
ms.openlocfilehash: 4438917db93c37ddbba3e7ee692b2e3c065d2beb
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="understand-azure-identity-solutions"></a>Informazioni sulle soluzioni di gestione delle identità di Azure
Microsoft Azure Active Directory (Azure AD) è una soluzione cloud completa per la gestione delle identità e degli accessi che fornisce servizi di directory, governance delle identità e gestione dell'accesso alle applicazioni. Azure AD [abilita il single sign-on (SSO)](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-sso) rapidamente su oltre 1000 applicazioni preintegrate commerciali e personalizzate nella [raccolta di applicazioni Azure AD](https://azure.microsoft.com/marketplace/active-directory/all/). Molte di queste applicazioni sono già in uso, ad esempio Office 365, Salesforce.com, Box, ServiceNow e Workday.

Una singola directory di Azure AD viene associata automaticamente a una sottoscrizione di Azure al momento della sua creazione. Come il servizio di gestione delle identità in Azure, Azure AD fornisce tutte le funzioni di gestione delle identità e di controllo degli accessi per le risorse basate su cloud. Queste risorse possono includere utenti, applicazioni e gruppi per un singolo tenant (organizzazione) come mostrato nel seguente diagramma:

![Azure Active Directory](./media/understand-azure-identity-solutions/azure-ad.png)

Microsoft Azure offre diverse modalità per sfruttare l'identità come servizio (IDaaS), con livelli variabili di complessità concepiti per soddisfare le esigenze delle singole organizzazioni. Il resto di questo articolo aiuta a comprendere la terminologia e i concetti base di identità di Azure e fornisce indicazioni per facilitare la scelta tra le opzioni disponibili.

## <a name="terms-to-know"></a>Termini chiave

Prima di prendere una decisione relativa alla soluzione di identità di Azure per l'organizzazione, è necessario avere una conoscenza di base dei termini comunemente usati a proposito dei servizi di gestione delle identità di Azure.

|Termini chiave| DESCRIZIONE|
|-----|-----|
|Sottoscrizione di Azure |Le sottoscrizioni vengono usate per effettuare i pagamenti relativi ai servizi cloud di Azure e sono in genere collegate a una carta di credito. È possibile disporre di più sottoscrizioni, ma può essere difficile condividere le risorse tra le sottoscrizioni.|
|Tenant di Azure | Un tenant di Azure AD rappresenta un'unica organizzazione. Si tratta di un'istanza attendibile e dedicata di Azure AD che viene creata automaticamente quando un'organizzazione si registra a una sottoscrizione a un servizio cloud Microsoft, ad esempio Azure, Intune o Office 365. I tenant possano accedere ai servizi in un ambiente dedicato (tenant singolo) o in un ambiente condiviso con altre organizzazioni (multi-tenant).|
|Directory di Azure AD | Ogni tenant di Azure è dotato di una directory di Azure AD dedicata e attendibile che contiene gli utenti, i gruppi e le applicazioni del tenant. Viene usata per eseguire le funzioni di gestione degli accessi e delle identità relative alle risorse del tenant. Poiché viene eseguito automaticamente il provisioning di un'unica directory di Azure AD per rappresentare l'organizzazione quando si effettua l'iscrizione a un servizio cloud di Microsoft, come Azure, Microsoft Intune o Office 365, talvolta si noterà che i termini *tenant*, *Azure AD* e *directory di Azure AD* vengono usati in modo intercambiabile. |
|Dominio personalizzato | Quando ci si registra per la prima volta a una sottoscrizione a un servizio cloud Microsoft il tenant (organizzazione) usa un nome di dominio *.onmicrosoft.com*. La maggior parte delle organizzazioni tuttavia dispongono di uno o più nomi di dominio impegnati nello svolgimento delle proprie attività di business e che gli utenti finali usano per accedere alle risorse aziendali. È possibile aggiungere il nome di dominio personalizzato ad Azure AD, in modo che il nome di dominio sia familiare agli utenti, ad esempio *alice@contoso.com* anziché *alice@contoso.onmicrosoft.com*. |
|Account Azure AD | Si tratta delle identità create tramite Azure AD o un altro servizio cloud Microsoft, ad esempio Office 365. Sono archiviate in Azure AD e accessibili da una qualsiasi delle sottoscrizioni di servizi cloud dell'organizzazione. |
|Amministratore della sottoscrizione di Azure| L'amministratore account è la persona che ha eseguito l'accesso o ha acquistato la sottoscrizione di Azure. Possono usare [Centro account](https://account.azure.com/Subscriptions) ed eseguire diverse attività di gestione, ad esempio creare e annullare sottoscrizioni, modificare la fatturazione per una sottoscrizione oppure modificare l'amministratore del servizio. |
|Amministrazione globale di Azure AD | Gli amministratori globali di Azure AD hanno accesso completo a tutte le funzionalità amministrative di Azure AD. Per impostazione predefinita, la persona che registra la sottoscrizione al servizio cloud Microsoft diventa automaticamente un amministratore globale. È possibile avere più amministratori globali; solo gli amministratori globali possono assegnare gli [altri ruoli di amministratore](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) agli utenti. |
|Account Microsoft | Account Microsoft (creati dall'utente per uso personale): garantiscono l'accesso ai prodotti e ai servizi cloud Microsoft destinati agli utenti privati come Outlook (Hotmail), OneDrive, Xbox LIVE o Office 365. Queste identità vengono create e archiviate nel sistema di account delle identità degli utenti gestito da Microsoft.|
|Account aziendali o dell'istituto di istruzione | Sono rilasciati da un amministratore per uso aziendale/accademico e garantiscono l'accesso a tutti i servizi cloud Microsoft per grandi aziende, ad esempio Azure, Intune o Office 365.|


## <a name="concepts-to-understand"></a>Concetti chiave

Ora che si conoscono i termini di base di identità di Azure, si devono ottenere ulteriori informazioni sui concetti di identità di Azure per prendere una decisione informata relativamente al servizio di gestione delle identità di Azure.

|Concetto chiave |DESCRIZIONE|
|-----|-----|
|[Associare le sottoscrizioni di Azure ad Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-how-subscriptions-associated-directory) |Ogni sottoscrizione di Azure ha una relazione di trust con una directory di Azure AD, necessaria per autenticare gli utenti, i servizi e i dispositivi. *Più sottoscrizioni possono considerare attendibile la stessa directory di Azure AD, ma una sottoscrizione considererà attendibile solo una singola directory di Azure AD*. Questa relazione di trust è diversa dalla relazione tra la sottoscrizione e le altre risorse in Azure, ad esempio siti Web, database e così via, le quali sono da considerarsi più come risorse figlio di una sottoscrizione. Se la sottoscrizione scade, non sarà più possibile accedere alle risorse associate alla sottoscrizione diversa da Azure AD. Tuttavia, la directory Azure AD rimane in Azure, quindi sarà possibile associarvi un'altra sottoscrizione e continuare a gestire le risorse del tenant.|
|[Come funzionano le licenze di Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-get-started-azure-portal) | Quando si acquista o si attiva Enterprise Mobility Suite, Azure AD Premium o Azure AD Basic, la directory viene aggiornata con la sottoscrizione, inclusi il relativo periodo di validità e le licenze prepagate. Quando la sottoscrizione è attiva, il servizio può essere gestito dagli amministratori globali di Azure AD e usato dagli utenti dotati di licenza. Le informazioni relative alla sottoscrizione, tra cui il numero di licenze disponibili o assegnate, sono disponibili nel portale di Azure nel pannello **Licenze** >  **di Azure Active Directory**. Questa è anche la posizione migliore per gestire le assegnazioni delle licenze.|
|[Controllo di accesso basato sui ruoli nel portale di Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is)|Il controllo di accesso in base al ruolo aiuta nella gestione specifica degli accessi per le risorse di Azure. Un numero eccessivo di autorizzazioni può esporre un account agli attacchi. Un numero di autorizzazioni insufficiente ostacola l'efficienza del lavoro dei dipendenti. Il modello di controllo di accesso in base al ruolo consente di assegnare ai dipendenti le esatte autorizzazioni necessarie in base ai tre ruoli di base validi per tutti i gruppi di risorse: proprietario, collaboratore, lettore. È inoltre possibile creare fino a 2.000 [ruoli Controllo degli accessi in base al ruolo](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles) per soddisfare esigenze specifiche. |
|[Identità ibrida](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)|L'identità ibrida viene ottenuta tramite l'integrazione di AD DS locale con Azure AD tramite [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect). Ciò consente quindi di fornire agli utenti un'identità comune per le applicazioni di Office 365, Azure e delle app locali o e SaaS integrate con Azure AD. Con l'identità ibrida è possibile estendere in modo efficace l'ambiente locale al cloud per la gestione dell'accesso e delle identità.|

### <a name="the-difference-between-windows-server-ad-ds-and-azure-ad"></a>Differenza tra Windows Server AD DS e Azure AD
Sia Azure Active Directory (Azure AD) che Active Directory locale (Active Directory Domain Services o AD DS) sono sistemi che archiviano i dati nella directory e gestiscono le comunicazioni tra utenti e risorse, compresi processi di accesso degli utenti, autenticazione e ricerche nelle directory.

Se si ha già familiarità con Windows Server Active Directory Domain Services (AD DS), introdotto per la prima volta in Windows 2000 Server, è probabile che si conoscano già i concetti di base di un servizio di gestione delle identità. Tuttavia, è anche importante comprendere che Azure AD non è semplicemente un controller di dominio nel cloud. Si tratta di un metodo innovativo che offre identità come servizio (IDaaS) in Azure e richiede un approccio totalmente nuovo per adottare appieno le funzionalità basate su cloud e proteggere l'organizzazione dalle minacce attuali. 

AD DS è un ruolo server in Windows Server; ciò significa che può essere distribuito in macchine virtuali o fisiche. Dispone di una struttura gerarchica basata su X.500. Usa DNS per l'individuazione degli oggetti, supporta interazioni tramite LDAP e usa principalmente Kerberos per l'autenticazione. Active Directory abilita unità organizzative e oggetti Criteri di gruppo, oltre ad aggiungere computer al dominio; inoltre vengono create relazioni di trust tra i domini.

I reparti IT hanno protetto per anni il proprio perimetro di sicurezza usando AD DS, ma le aziende odierne, senza più confini perimetrali e impegnate a supportare le esigenze di gestione delle identità di dipendenti, clienti e partner, esigono nuovi piani di controllo. Azure AD è la risposta a questa esigenza. La protezione si è spostata oltre il firewall aziendale, ed è oggi nel cloud, dove Azure AD protegge le risorse aziendali e l'accesso fornendo agli utenti un'identità comune, sia locale che cloud. Fornisce agli utenti la flessibilità necessaria per accedere in modo sicuro alle applicazioni necessarie per svolgere il proprio lavoro da qualsiasi dispositivo. I controlli di protezione dati basati sul rischio seamless, supportati da funzionalità di machine learning e da report dettagliati, sono anche disponibili in quanto il reparto IT deve mantenere i protetti i dati aziendali.

Azure AD è un servizio directory pubblico a più clienti, il che significa che in Azure AD è possibile creare un tenant per i server cloud e le applicazioni come Office 365. Utenti e gruppi vengono creati in una struttura piatta senza unità organizzative o oggetti Criteri di gruppo. L'autenticazione viene eseguita tramite protocolli, come SAML, WS-Federation e OAuth. È possibile eseguire query in Azure AD, tuttavia invece di usare LDAP è necessario usare un'API REST chiamata API Graph AD. Tutte queste operazioni possono essere eseguite su HTTP e HTTPS.

### <a name="extend-office-365-management-and-security-capabilities"></a>Estendere le funzionalità di gestione e protezione di Office 365
Siete già utenti di Office 365? È possibile accelerare la trasformazione digitale estendendo le funzionalità integrate di Office 365 con Azure AD per proteggere tutte le risorse agevolando una produttività sicura all'intera forza lavoro. Quando si usa Azure AD, oltre alle funzionalità di Office 365, è possibile proteggere l'intero portfolio di applicazioni con un'identità che consenta l'accesso SSO a tutte le applicazioni. Le funzionalità di accesso condizionale sono ampliabili non solo in base allo stato del dispositivo, ma in base all'utente, alla posizione, all'applicazione e anche al rischio. Le funzionalità multi-factor authentication (MFA) sono in grado di offrire ulteriore protezione quando necessario. Aggiungono capacità di supervisione dei privilegi utente e forniscono l'accesso amministrativo su richiesta, just-in-time. Grazie alla funzionalità self-service offerte da Azure AD, ad esempio reimpostazione delle password dimenticate, richieste di accesso all'applicazione, creazione e gestione dei gruppi, gli utenti saranno più produttivi e invieranno meno ticket di supporto tecnico.

> [!TIP]
> Per altre informazioni sull'uso della gestione delle identità di Azure AD con Office 365 [Richiedere l'e-book](https://info.microsoft.com/Extend-Office-365-security-with-EMS.html).

## <a name="microsoft-azure-identity-solutions"></a>Soluzioni di gestione delle identità di Microsoft Azure

Microsoft Azure offre diverse modalità per la gestione delle identità degli utenti, sia che queste vengano gestite completamente in locale, solo nel cloud, o anche in punti intermedi. Tali opzioni includono AD DS "fai da te" in Azure, Azure Active Directory (Azure AD), Soluzione ibrida di gestione delle identità e Azure AD Domain Services.

### <a name="do-it-yourself-diy-ad-ds"></a>AD DS "fai da te"
Le aziende con un footprint ridotto nel cloud possono usare **AD DS "fai da te" in Azure**. Questa opzione supporta numerosi scenari con Windows Server AD DS, particolarmente adatti per la distribuzione di macchine virtuali (VM) in Azure. È possibile creare una macchina virtuale di Azure come controller di dominio in esecuzione in un data center lontano connesso alla rete remota. Da qui, la macchina virtuale può supportare le richieste di autenticazione provenienti dagli utenti remoti e migliorare le prestazioni di autenticazione. Questa opzione è ideale anche come soluzione alternativa relativamente conveniente ai costosi siti di ripristino di emergenza, poiché consente di ospitare in Azure un numero ridotto di controller di dominio e una singola rete virtuale. Potrebbe essere necessario infine distribuire un'applicazione in Azure, ad esempio SharePoint, che richiede AD DS di Windows Server, ma non ha dipendenze dalla rete locale o dall'istanza di Active Directory di Windows Server aziendale. In questo caso, è possibile la distribuzione di una foresta isolata in Azure per soddisfare requisiti della farm del server SharePoint. È anche supportata la distribuzione di applicazioni di rete che richiedono la connettività alla rete locale e all'istanza di Active Directory locale.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)
**Azure AD standalone** è una soluzione basata sul cloud globale e autonoma per la gestione delle identità e dell'accesso come servizio (IDaaS). che offre un solido set di funzionalità per gestire utenti e gruppi. Il servizio aiuta a proteggere l'accesso ad applicazioni locali e cloud, tra cui i servizi Web Microsoft come Office 365 e molte applicazioni SaaS (Software as a Service) non Microsoft. Azure AD è disponibile in tre edizioni: gratuita, Basic e Premium. Azure AD migliora l'efficacia organizzativa ed estende la sicurezza oltre il firewall perimetrale, promuovendo l'adozione di un nuovo piano di controllo protetto grazie alle funzionalità di machine learning e sicurezza avanzata di Azure.

### <a name="hybrid-identity"></a>Identità ibrida
Invece di scegliere tra le soluzioni di gestione delle identità basate su cloud o in locale, molti CIO lungimiranti e capaci di anticipare il percorso a lungo termine della propria azienda, estendono le directory locali nel cloud adottando soluzioni **ibride per la gestione delle identità**. Queste soluzioni offrono una gestione autenticamente globale delle identità e degli accessi incentivando l'accesso sicuro e produttivo alle applicazioni necessarie agli utenti per svolgere il proprio lavoro.

> [!TIP]
> Per altre informazioni su come i CIO hanno integrato Azure Active Directory nelle proprie strategie IT, scaricare la [Guida per CIO ad Azure Active Directory](https://aka.ms/AzureADCIOGuide).

### <a name="azure-ad-domain-services"></a>Servizi di dominio Azure Active Directory
**Azure AD Domain Services** rappresenta un'opzione basata sul cloud per usare AD DS per il controllo semplificato della configurazione delle VM di Azure e per soddisfare i requisiti di gestione delle identità locali per le attività di sviluppo e test delle applicazioni di rete. Azure AD Domain Services non è un metodo per spostare l'infrastruttura AD DS locale nelle VM di Azure gestite da Azure AD Domain Services. Le VM di Azure nei domini gestiti dovrebbero al contrario essere usate per supportare lo sviluppo, il test e lo spostamento nel cloud delle applicazioni locali che richiedono i metodi di autenticazione AD DS.

## <a name="common-scenarios-and-recommendations"></a>Scenari comuni e raccomandazioni

Di seguito sono riportati alcuni scenari comuni di gestione delle identità e degli accessi con raccomandazioni riguardo all'opzione di gestione delle identità di Azure più appropriata per ciascuno di essi.

|Scenario di gestione delle identità| Raccomandazione|
|-----|-----|
|La mia organizzazione ha effettuato grandi investimenti in Windows Server Active Directory, ma ora intende estendere la gestione delle identità al cloud.| La soluzione di gestione delle identità di Azure più diffusa è l'[identità ibrida](https://docs.microsoft.com/azure/active-directory/active-directory-hybrid-identity-design-considerations-overview). Se sono già stati effettuati investimenti in AD DS locale, Azure AD Connect consente di estendere facilmente le identità nel cloud.|
|La mia azienda è nata nel cloud e non ha effettuato alcun investimento in soluzioni di gestione delle identità locali.| [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) è la scelta ideale per le aziende con ambenti esclusivamente cloud senza investimenti in locale.|
|Ho bisogno di una configurazione semplificata della VM di Azure e di pochi controlli per soddisfare le esigenze di identità locali per attività di sviluppo e test delle applicazioni.|[Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) è un'ottima scelta se è necessario usare AD DS per il controllo di configurazioni semplificate di VM di Azure o se si intende sviluppare o migrare applicazioni locali legacy e compatibili con la directory nel cloud.|  
|La mia azienda deve supportare poche macchine virtuali in Azure ma ha già investito molto in Active Directory (AD DS) locale.|La soluzione ["fai da te" AD DS](https://msdn.microsoft.com/library/azure/jj156090.aspx) è l'opzione ideale per usare le macchine virtuali di Azure quando è necessario supportare poche macchine virtuali e sono stati fatti investimenti considerevoli in AD DS locale. |

## <a name="where-can-i-learn-more"></a>Altre informazioni
Sono disponibili moltissime risorse utili online, che forniscono tutte le informazioni necessarie su Azure AD. Gli articoli seguenti forniscono informazioni introduttive:

* [Abilitazione della directory per la gestione ibrida con Azure AD Connect](active-directory-aadconnect.md)
* [Sicurezza aggiuntiva per un mondo sempre connesso](../multi-factor-authentication/multi-factor-authentication.md)
* [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](active-directory-saas-app-provisioning.md)
* [Introduzione ad Azure AD Report](active-directory-reporting-getting-started.md)
* [Gestire le password da qualsiasi posizione](active-directory-passwords-update-your-own-password.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)
* [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](active-directory-saas-app-provisioning.md)
* [Come fornire l'accesso remoto sicuro alle applicazioni locali](active-directory-application-proxy-get-started.md)
* [Gestione dell'accesso alle risorse tramite i gruppi di Azure Active Directory](active-directory-manage-groups.md)
* [Che cosa sono le licenze di Microsoft Azure Active Directory?](active-directory-licensing-whatis-azure-portal.md)
* [Come individuare app cloud non autorizzate usate nell'organizzazione](active-directory-cloudappdiscovery-whatis.md)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver compreso i concetti di identità di Azure e le opzioni disponibili, è possibile usare le risorse seguenti per avviare l'implementazione dell'opzione selezionata:

[Altre informazioni sulle soluzioni per la gestione delle identità ibride di Azure](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution)

[Altre informazioni in un ambiente Azure di prova](https://aka.ms/aad-poc)

[Distribuire Azure AD nell'ambiente di produzione](https://aka.ms/aad-onboard)
