---
title: "Comprendere le identità di Azure | Microsoft Docs"
description: "Ottenere una conoscenza di base dei termini, dei concetti e delle raccomandazioni relativi alle soluzioni di gestione delle identità di Microsoft Azure, per migliorare il processo decisionale in termini di governance delle identità per la propria organizzazione."
keywords: 
author: jeffgilb
manager: femila
ms.date: 5/12/2017
ms.topic: article
ms.prod: 
ms.service: azure
ms.technology: 
ms.assetid: 
ms.reviewer: jsnow
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 17c4dc6a72328b613f31407aff8b6c9eacd70d9a
ms.openlocfilehash: 8d550a9c2a6d2f28687aefc3b9db97ca3c68eb82
ms.contentlocale: it-it
ms.lasthandoff: 05/16/2017


---
# <a name="understand-azure-identity-solutions"></a>Informazioni sulle soluzioni di gestione delle identità di Azure
Microsoft Azure Active Directory (Azure AD) è una soluzione cloud completa per la gestione delle identità e degli accessi ad alta disponibilità, che riunisce servizi di directory di base, governance delle identità avanzata e gestione dell'accesso alle applicazioni. Azure AD [abilita il Single Sign On (SSO)](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-sso) su oltre 1.000 applicazioni commerciali e personalizzate pre-integrate presenti nella [galleria delle applicazioni di Azure AD](https://azure.microsoft.com/marketplace/active-directory/all/), incluse quelle usate quotidianamente come Office 365, Salesforce.com, Box, ServiceNow e Workday.

Nella sua forma più semplice, l'identità di Azure è fornita da una singola directory di Azure AD che viene associata automaticamente a una sottoscrizione di Azure al momento della sua creazione. Come servizio di gestione delle identità di Azure, Azure AD offre quindi tutte le funzioni di gestione delle identità e controllo di accesso per le risorse basate su cloud, ad esempio utenti, applicazioni e gruppi, per un singolo tenant (organizzazione), come illustrato nel diagramma seguente:

![Azure Active Directory](./media/understand-azure-identity-solutions/azure-ad.png)

Microsoft Azure offre diverse modalità per sfruttare l'identità come servizio (IDaaS), con livelli variabili di complessità concepiti per soddisfare le esigenze delle singole organizzazioni. Il resto di questo articolo aiuta a comprendere la terminologia e i concetti che è necessario conoscere e fornisce indicazioni per facilitare la scelta tra le opzioni di identità di Azure disponibili.

## <a name="terms-to-know"></a>Termini chiave

Prima di poter intraprendere una decisione relativa al servizio di gestione delle identità di Azure per l'organizzazione, è necessario avere una conoscenza di base dei termini comunemente usati a proposito dei servizi di gestione delle identità di Azure.

|Termini chiave| Descrizione|
|-----|-----|
|Sottoscrizione di Azure |Le sottoscrizioni vengono usate per effettuare i pagamenti relativi ai servizi cloud di Azure e sono in genere collegate a una carta di credito. È possibile disporre di più sottoscrizioni, ma può essere difficile condividere le risorse tra le sottoscrizioni.|
|Tenant di Azure | Un tenant di Azure AD rappresenta un'unica organizzazione. Si tratta di un'istanza attendibile e dedicata di Azure AD che l'organizzazione riceve al momento della sottoscrizione di un servizio cloud Microsoft, ad esempio Azure, Intune o Office 365 e che diventa di sua proprietà. I tenant possano accedere ai servizi in un ambiente dedicato (tenant singolo) o in un ambiente condiviso con altre organizzazioni (multi-tenant).|
|Directory di Azure AD | Ogni tenant di Azure è dotato di una directory di Azure AD dedicata e attendibile che contiene gli utenti, i gruppi e le applicazioni del tenant. Viene usata per eseguire le funzioni di gestione degli accessi e delle identità relative alle risorse del tenant. Poiché viene eseguito automaticamente il provisioning di un'unica directory di Azure AD per rappresentare l'organizzazione quando si effettua l'iscrizione a un servizio cloud di Microsoft, come Azure, Microsoft Intune o Office 365, talvolta si noterà che i termini *tenant*, *Azure AD* e *directory di Azure AD* vengono usati in modo intercambiabile. |
|Dominio personalizzato | Quando si registra per la prima volta una sottoscrizione a un servizio cloud Microsoft, per il tenant (organizzazione) verrà creato un nome di dominio *. onmicrosoft.com*. Tuttavia, la maggior parte delle organizzazioni dispongono di uno o più nomi di dominio impegnati nello svolgimento delle proprie attività di business e che gli utenti finali usano per accedere alle risorse aziendali. È possibile aggiungere il nome di dominio personalizzato ad Azure AD, in modo che il nome di dominio sia familiare agli utenti, ad esempio  *alice@contoso.com*  anziché  *alice@contoso.onmicrosoft.com* . |
|Account Azure AD | Si tratta delle identità create tramite Azure AD o un altro servizio cloud Microsoft, ad esempio Office 365. Sono archiviate in un tenant di Azure AD e accessibili da una qualsiasi delle sottoscrizioni di servizi cloud dell'organizzazione. |
|Amministratore della sottoscrizione di Azure| L'amministratore account è la persona che ha eseguito l'accesso o ha acquistato la sottoscrizione di Azure. È autorizzato ad accedere al [Centro account](https://account.windowsazure.com/Home/Index) ed eseguire diverse attività di gestione, ad esempio creare e annullare sottoscrizioni, modificare la fatturazione per una sottoscrizione oppure modificare l'amministratore del servizio. |
|Amministrazione globale di Azure AD | Gli amministratori globali di Azure AD hanno accesso completo a tutte le funzionalità amministrative di Azure AD. Per impostazione predefinita, la persona che registra la sottoscrizione al servizio cloud Microsoft diventa automaticamente un amministratore globale. È possibile avere più amministratori globali; solo gli amministratori globali possono assegnare gli [altri ruoli di amministratore](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) agli utenti. |
|Account Microsoft | Account Microsoft (creati dall'utente per uso personale): garantiscono l'accesso a tutti i prodotti e ai servizi cloud Microsoft destinati agli utenti privati, ad esempio Outlook (Hotmail), Messenger, OneDrive, MSN, Xbox LIVE o Office 365. Queste identità vengono create e archiviate nel sistema di account delle identità degli utenti gestito da Microsoft.|
|Account aziendali o dell'istituto di istruzione | Sono rilasciati da un amministratore per uso aziendale/accademico e garantiscono l'accesso a tutti i servizi cloud Microsoft per grandi aziende, ad esempio Azure, Intune o Office 365.|


## <a name="concepts-to-understand"></a>Concetti chiave

Dopo aver appreso la terminologia di base relativa alle identità, per poter prendere decisioni informate sul servizio di gestione delle identità di Azure è necessario comprendere i concetti di base seguenti.

|Concetto chiave |Descrizione|
|-----|-----|
|[Associare le sottoscrizioni di Azure ad Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-how-subscriptions-associated-directory) |Ogni sottoscrizione di Azure ha una relazione di trust con una directory di Azure AD, necessaria per autenticare gli utenti, i servizi e i dispositivi. *Più sottoscrizioni possono considerare attendibile la stessa directory di Azure AD, ma una sottoscrizione considererà attendibile solo una singola directory di Azure AD*. Questa relazione di trust è diversa dalla relazione tra la sottoscrizione e le altre risorse in Azure, ad esempio siti Web, database e così via, le quali sono da considerarsi più come risorse figlio di una sottoscrizione. Se la sottoscrizione scade, non sarà più possibile accedere alle risorse associate alla sottoscrizione diversa da Azure AD. Tuttavia, la directory Azure AD rimane in Azure, quindi sarà possibile associarvi un'altra sottoscrizione e continuare a gestire le risorse del tenant.|
|[Come funzionano le licenze di Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-get-started-azure-portal) | Quando si acquista o si attiva Enterprise Mobility Suite, Azure AD Premium o Azure AD Basic, la directory viene aggiornata con la sottoscrizione, inclusi il relativo periodo di validità e le licenze prepagate. Quando la sottoscrizione è attiva, le funzionalità del servizio possono essere gestite dagli amministratori globali di Azure AD e usate dagli utenti dotati di licenza. Le informazioni relative alla sottoscrizione, tra cui il numero di licenze disponibili o assegnate, sono disponibili nel portale di Azure nel pannello **Licenze** >  **di Azure Active Directory**. Questa è anche la posizione migliore per gestire le assegnazioni delle licenze.|
|[Controllo di accesso basato sui ruoli nel portale di Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is)|Il controllo di accesso in base al ruolo aiuta nella gestione specifica degli accessi per le risorse di Azure. Un numero eccessivo di autorizzazioni può esporre un account agli attacchi. Un numero di autorizzazioni insufficiente ostacola l'efficienza del lavoro dei dipendenti. Il modello di controllo di accesso in base al ruolo consente di assegnare ai dipendenti le esatte autorizzazioni necessarie in base ai tre ruoli di base validi per tutti i gruppi di risorse: proprietario, collaboratore, lettore. È inoltre possibile creare fino a 2.000 [ruoli Controllo degli accessi in base al ruolo](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles) per soddisfare esigenze specifiche. |
|[Identità ibrida](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)|L'identità ibrida viene ottenuta tramite l'integrazione di AD DS locale con Azure AD tramite [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect). Ciò consente quindi di fornire agli utenti un'identità comune per le applicazioni di Office 365, Azure e delle app locali o e SaaS integrate con Azure AD. Con l'identità ibrida è possibile estendere in modo efficace l'ambiente locale al cloud per la gestione dell'accesso e delle identità.|

### <a name="the-difference-between-windows-server-ad-ds-and-azure-ad"></a>Differenza tra Windows Server AD DS e Azure AD
Se si ha già familiarità con Windows Server Active Directory Domain Services (AD DS), introdotto per la prima volta in Windows 2000 Server, è probabile che si conoscano già i concetti di base di un servizio di gestione delle identità. È tuttavia importante comprendere che Azure AD non è solo un controller di dominio nel cloud. Si tratta invece di un metodo innovativo che offre identità come servizio (IDaaS) in Azure, e richiede un approccio totalmente nuovo per adottare appieno le funzionalità basate su cloud e proteggere l'organizzazione dalle minacce attuali. I reparti IT hanno protetto per anni il proprio perimetro di sicurezza usando AD DS, ma le aziende odierne, senza più confini perimetrali e impegnate a supportare le esigenze di gestione delle identità di dipendenti, clienti e partner, esigono nuovi piani di controllo. Azure AD è la risposta a questa esigenza.

La protezione si è spostata oltre il firewall aziendale, ed è oggi nel cloud, dove Azure AD protegge le risorse aziendali e l'accesso fornendo agli utenti un'identità comune, sia locale che cloud. Assicura così agli utenti la flessibilità per accedere in modo sicuro alle applicazioni necessarie per svolgere il proprio lavoro da qualsiasi dispositivo nonché i controlli di protezione dei dati basati sui rischi, supportati da funzionalità di machine learning e di reporting dettagliato, indispensabili all'IT per garantire la protezione dei dati aziendali.

### <a name="extend-office-365-management-and-security-capabilities"></a>Estendere le funzionalità di gestione e protezione di Office 365
Siete già utenti di Office 365? È possibile accelerare la trasformazione digitale estendendo le funzionalità di sicurezza e gestione integrata di Office 365 con Azure AD per proteggere l'accesso a tutte le risorse, agevolando una produttività senza ostacoli e sicura all'intera forza lavoro. Quando si usano le funzionalità di Azure AD, oltre a quelle incluse in Office 365, è possibile proteggere l'intero portfolio di applicazioni con un'identità che consenta l'accesso SSO a tutte le applicazioni. Le funzionalità di accesso condizionale sono ampliabili non solo in base allo stato del dispositivo, ma in base all'utente, alla posizione, all'applicazione e anche al rischio. Le funzionalità multi-factor authentication (MFA) sono in grado di offrire ulteriore protezione quando necessario. Aggiungono capacità di supervisione dei privilegi utente e forniscono l'accesso amministrativo su richiesta, just-in-time. Grazie alla funzionalità self-service offerte da Azure AD, ad esempio reimpostazione delle password dimenticate, richieste di accesso all'applicazione, creazione e gestione dei gruppi gli utenti saranno più produttivi e invieranno meno ticket di supporto tecnico.

> [!TIP]
> Per altre informazioni sull'uso della gestione delle identità di Azure AD con Office 365 [Richiedere l'e-book](https://info.microsoft.com/Extend-Office-365-security-with-EMS.html).

## <a name="microsoft-identity-solutions"></a>Soluzioni Microsoft per la gestione delle identità

Microsoft Azure offre diverse modalità per la gestione delle identità degli utenti, sia che queste vengano gestite completamente in locale, solo nel cloud, o anche in punti intermedi. Tali opzioni includono AD DS "fai da te" in Azure, Azure Active Directory (Azure AD), Soluzione ibrida di gestione delle identità e Azure AD Domain Services.

Le aziende con un footprint ridotto nel cloud possono usare **AD DS "fai da te" in Azure**. Questa opzione supporta numerosi scenari con Windows Server AD DS, particolarmente adatti per la distribuzione di macchine virtuali (VM) in Azure. È possibile creare una macchina virtuale di Azure come controller di dominio in esecuzione in un data center lontano connesso alla rete remota. Da qui, la macchina virtuale può supportare le richieste di autenticazione provenienti dagli utenti remoti e migliorare le prestazioni di autenticazione. Questa opzione è ideale anche come soluzione alternativa relativamente conveniente ai costosi siti di ripristino di emergenza, poiché consente di ospitare in Azure un numero ridotto di controller di dominio e una singola rete virtuale. È poi possibile distribuire un'applicazione in Azure, ad esempio SharePoint, che richiede AD DS di Windows Server, ma non ha dipendenze dalla rete locale o dall'istanza di Active Directory di Windows Server aziendale. In questo caso, è possibile la distribuzione di una foresta isolata in Azure per soddisfare requisiti della farm del server SharePoint. È anche supportata la distribuzione di applicazioni di rete che richiedono la connettività alla rete locale e all'istanza di Active Directory locale.

**Azure Active Directory (Azure AD)** Azure Active Directory (Azure AD) è una soluzione cloud globale e autonoma per la gestione delle identità e dell'accesso che offre un solido set di funzionalità per gestire utenti e gruppi. Il servizio aiuta a proteggere l'accesso ad applicazioni locali e cloud, tra cui i servizi Web Microsoft come Office 365 e molte applicazioni SaaS (Software as a Service) non Microsoft. Azure AD è disponibile in tre edizioni: gratuita, Basic e Premium. Azure AD migliora l'efficacia organizzativa ed estende la sicurezza oltre il firewall perimetrale, promuovendo l'adozione di un nuovo piano di controllo protetto grazie alle funzionalità di machine learning e sicurezza avanzata di Azure.

Invece di scegliere tra le soluzioni di gestione delle identità basate su cloud o in locale, molti CIO lungimiranti e capaci di anticipare il percorso a lungo termine della propria azienda, estendono le directory locali nel cloud adottando soluzioni **ibride per la gestione delle identità**. Queste soluzioni offrono una gestione autenticamente globale delle identità e degli accessi, incentivando l'accesso sicuro e produttivo alle applicazioni necessarie agli utenti per svolgere il proprio lavoro, ora e nell'immediato futuro.

> [!TIP]
> Per altre informazioni su come i CIO hanno integrato Azure Active Directory nelle proprie strategie IT, scaricare la [Guida per CIO ad Azure Active Directory](https://aka.ms/AzureADCIOGuide).

Infine, **Azure AD Domain Services** rappresenta un'opzione cloud per usare AD DS per il controllo semplificato della configurazione delle VM di Azure e per soddisfare i requisiti di gestione delle identità locali per le attività di sviluppo e test delle applicazioni di rete. Azure AD Domain Services non è un metodo per spostare l'infrastruttura AD DS locale nelle VM di Azure gestite da Azure AD Domain Services. Al contrario, le VM di Azure nei domini gestiti dovrebbero essere usate per supportare lo sviluppo, il test e lo spostamento nel cloud delle applicazioni locali che richiedono i metodi di autenticazione AD DS.

## <a name="common-scenarios-and-recommendations"></a>Scenari comuni e raccomandazioni

Di seguito sono riportati alcuni scenari comuni di gestione delle identità e degli accessi con raccomandazioni riguardo all'opzione di gestione delle identità di Azure più appropriata per ciascuno di essi.

  |Scenario di gestione delle identità| Raccomandazione|
  |-----|-----|
  |La mia azienda deve supportare un numero ridotto di macchine virtuali in Azure, ma ha già investito molto in Active Directory (AD DS) locale.|La soluzione ["fai da te" AD DS](https://msdn.microsoft.com/library/azure/jj156090.aspx) è l'opzione ideale per usare le macchine virtuali di Azure quando è necessario supportare un numero minimo di macchine virtuali e sono stati fatti investimenti considrevoli in AD DS locale. |
  |La mia azienda è nata nel cloud e non ha effettuato alcun investimento in soluzioni di gestione delle identità locali.| [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) è la scelta ideale per le aziende con ambenti esclusivamente cloud senza investimenti in locale.|
  |La mia organizzazione ha effettuato grandi investimenti in Windows Server Active Directory, ma ora intende estendere la gestione delle identità al cloud.| La soluzione di gestione delle identità di Azure più diffusa è l'[identità ibrida](https://docs.microsoft.com/azure/active-directory/active-directory-hybrid-identity-design-considerations-overview). Se sono già stati effettuati investimenti in AD DS locale, Azure AD Connect consente di estendere facilmente le identità nel cloud.|
  |Ho esigenza di una configurazione semplificata della VM di Azure e di pochi controlli per soddisfare le esigenze di identità locali per attività di sviluppo e test delle applicazioni.|[Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) è un'ottima scelta se è necessario usare AD DS per il controllo di configurazioni semplificate di VM di Azure o se si intende sviluppare o migrare applicazioni locali legacy e compatibili con la directory nel cloud.|

## <a name="next-steps"></a>Passaggi successivi

Dopo aver compreso i concetti di identità di Azure e le opzioni disponibili, è possibile usare le risorse seguenti per avviare l'implementazione dell'opzione selezionata:

[Verificare la comprensione delle soluzioni di identità di ](https://aka.ms/aad-understand-quiz)

[Altre informazioni in un ambiente Azure di prova](https://aka.ms/aad-poc)

[Distribuire Azure AD nell'ambiente di produzione](https://aka.ms/aad-onboard)

