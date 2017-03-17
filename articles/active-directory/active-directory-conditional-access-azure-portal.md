---
title: Accesso condizionale di Azure Active Directory | Documentazione Microsoft
description: Usare il controllo di accesso condizionale di Azure Active Directory per controllare condizioni specifiche durante il processo di autenticazione per l&quot;accesso alle applicazioni.
services: active-directory
keywords: accesso condizionale alle app, accesso condizionale con Azure AD, accesso sicuro alle risorse aziendali, criteri di accesso condizionale
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/06/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 7c28fda22a08ea40b15cf69351e1b0aff6bd0a95
ms.openlocfilehash: 62804019a8c2c5e719c36021ee04cbd20e03dd05
ms.lasthandoff: 03/07/2017


---
# <a name="conditional-access-in-azure-active-directory---preview"></a>Accesso condizionale in Azure Active Directory: anteprima

> [!div class="op_single_selector"]
> * [Portale di Azure](active-directory-conditional-access-azure-portal.md)
> * [Portale di Azure classico](active-directory-conditional-access.md)


Il comportamento illustrato in questo argomento è attualmente in [anteprima](active-directory-preview-explainer.md).

In un mondo in cui i dispositivi mobili e il cloud hanno sempre più importanza, Azure Active Directory consente ovunque l'accesso Single Sign-On a dispositivi, app e servizi. Con il proliferare dei dispositivi (inclusi i dispositivi BYOD), del lavoro al di fuori delle reti aziendali e delle app SaaS di terze parti, i professionisti IT hanno due obiettivi opposti:

- Fare in modo che gli utenti finali siano produttivi sempre e ovunque
- Proteggere gli asset aziendali in qualsiasi momento

Per aumentare la produttività, Azure Active Directory consente agli utenti di accedere agli asset aziendali in molti modi diversi. Con la gestione degli accessi alle applicazioni, Azure Active Directory consente di assicurarsi che solo *le persone giuste* possano accedere alle applicazioni. Ma come comportarsi se si vuole più controllo sul modo in cui le persone giuste accedono alle risorse in determinate condizioni? O nei casi in cui si vuole impedire anche alle *persone giuste* l'accesso a determinate app? Ad esempio, può non essere un problema se le persone giuste accedono a determinate app da una rete attendibile, ma può essere opportuno evitare che accedano a tali app da una rete considerata non attendibile. Per risolvere questi dubbi, è possibile usare l'accesso condizionale.

L'accesso condizionale è una funzionalità di Azure Active Directory che consente di applicare controlli sull'accesso alle app nel proprio ambiente in base a specifiche condizioni. Con i controlli, è possibile vincolare l'accesso a requisiti aggiuntivi o bloccarlo. L'implementazione dell'accesso condizionale è basata su criteri. Un approccio basato su criteri semplifica l'esperienza di configurazione perché risponde a esigenze specifiche nell'ambito dei requisiti di accesso.  

In genere, per definire i requisiti di accesso, si usano istruzioni basate sullo schema seguente:

![Controllo](./media/active-directory-conditional-access-azure-portal/10.png)

Se si sostituiscono le due occorrenze di "*questo*" con le informazioni reali, si ottiene un esempio di istruzione dei criteri con cui probabilmente si ha familiarità:

*Quando i terzisti cercano di accedere alle app per cloud da reti non considerate attendibili, bloccare l'accesso.*

L'istruzione dei criteri precedente mette in evidenza l'efficacia dell'accesso condizionale. Se da un lato si può consentire ai terzisti l'accesso di base alle app per cloud (**chi**), con l'accesso condizionale si possono anche definire le condizioni in cui l'accesso è possibile (**come**).

Nell'ambito dell'accesso condizionale di Azure Active Directory:

- "**Quando accade questo**" è l'**istruzione della condizione**
- "**Fare questo**" sono i **controlli**

![Controllo](./media/active-directory-conditional-access-azure-portal/11.png)

La combinazione di un'istruzione della condizione con i controlli rappresenta un criterio di accesso condizionale.

![Controllo](./media/active-directory-conditional-access-azure-portal/12.png)


## <a name="controls"></a>Controlli

In un criterio di accesso condizionale i controlli definiscono che cosa deve accadere dopo che un'istruzione della condizione è stata soddisfatta.  
Con i controlli, è possibile bloccare o consentire l'accesso con requisiti aggiuntivi.
Quando si configura un criterio che consente l'accesso, è necessario selezionare almeno un requisito.   

### <a name="grant-controls"></a>Controlli di concessione
L'implementazione corrente di Azure Active Directory consente di configurare i requisiti dei controlli di concessione seguenti:

![Controllo](./media/active-directory-conditional-access-azure-portal/05.png)

- **Autenticazione a più fattori**: è possibile richiedere l'autenticazione avanzata tramite l'autenticazione a più fattori. I provider possono usare Azure Multi-Factor Authentication o un provider di autenticazione a più fattori locale, in combinazione con Active Directory Federation Services (AD FS). L'uso dell'autenticazione a più fattori assicura la protezione delle risorse nel caso in cui un utente non autorizzato abbia avuto l'accesso alle credenziali di un utente valido.

- **Dispositivo conforme**: è possibile impostare criteri di accesso condizionale a livello di dispositivo. È possibile impostare un criterio per consentire l'accesso alle risorse dell'organizzazione soltanto ai computer conformi o registrati in un'applicazione di gestione di dispositivi mobili. Ad esempio, è possibile usare Intune per verificare la conformità dei dispositivi e quindi segnalarla a Azure AD affinché ne tenga conto durante l'accesso alle applicazioni. Per istruzioni dettagliate su come usare Intune per proteggere le applicazioni e i dati, vedere Proteggere le applicazioni e i dati con Microsoft Intune. È inoltre possibile usare Intune per applicare la protezione dei dati per i dispositivi smarriti o rubati. Per altre informazioni, vedere Proteggere i dati con la cancellazione completa o selettiva tramite Microsoft Intune.

- **Dispositivo aggiunto a un dominio**: è possibile richiedere che il dispositivo usato per connettersi ad Azure Active Directory sia un dispositivo aggiunto a un dominio. Questo criterio si applica ai computer desktop, portatili o tablet aziendali Windows. Per altre informazioni su come configurare la registrazione automatica dei dispositivi aggiunti a un dominio con Azure AD, vedere [Registrazione automatica dei dispositivi con Azure Active Directory per i dispositivi Windows aggiunti a un dominio](active-directory-conditional-access-automatic-device-registration.md).

Se in un criterio di accesso condizionale è selezionato più di un requisito, è anche possibile configurare i requisiti per applicarli. È possibile scegliere di richiedere tutti i tutti i controlli selezionati o solo uno.

![Controllo](./media/active-directory-conditional-access-azure-portal/06.png)

### <a name="session-controls"></a>Controlli di sessione
I controlli di sessione consentono di limitare esperienza in un'app cloud. Questi controlli sono imposti dalle app cloud e si basano sulle informazioni aggiuntive relative alla sessione fornite da Azure AD all'app.

![Controllo](./media/active-directory-conditional-access-azure-portal/session-control-pic.png)

#### <a name="use-app-enforced-restrictions"></a>Usa restrizioni imposte dalle app
È possibile usare questo controllo per chiedere ad Azure AD di passare le informazioni sul dispositivo all'app cloud, che in questo modo può sapere se l'utente proviene da un dispositivo conforme o da un dispositivo aggiunto a un dominio. Questo controllo è attualmente supportato solo con SharePoint come app cloud. SharePoint usa le informazioni sul dispositivo per offrire agli utenti un'esperienza completa o limitata, a seconda dello stato del dispositivo.
Per altre informazioni su come richiedere l'accesso limitato con SharePoint, vedere [qui](https://aka.ms/spolimitedaccessdocs).

## <a name="condition-statement"></a>Istruzione della condizione

La sezione precedente ha illustrato le opzioni supportate per bloccare o limitare l'accesso alle risorse sotto forma di controlli. In un criterio di accesso condizionale si definiscono i criteri che devono essere soddisfatti per i controlli da applicare sotto forma di istruzione della condizione.  

È possibile includere le assegnazioni seguenti nell'istruzione della condizione:

![Controllo](./media/active-directory-conditional-access-azure-portal/07.png)


- **Chi**: in molti casi è necessario applicare i controlli a uno specifico set di utenti. In un'istruzione della condizione è possibile definire questo set selezionando gli utenti e gruppi a cui si applica il criterio. Se necessario, è anche possibile escludere in modo esplicito un set di utenti dal criterio esentandoli.  
Selezionando utenti e gruppi, si definisce l'ambito degli utenti a cui si applica il criterio.    

    ![Controllo](./media/active-directory-conditional-access-azure-portal/08.png)



- **Che cosa**: di solito nel proprio ambiente vengono eseguite alcune app che, dal punto di vista della protezione, richiedono più attenzione di altre, ad esempio, app che hanno accesso a dati sensibili.
Selezionando le app per cloud, si definisce l'ambito delle app per cloud a cui si applica il criterio. Se necessario, è anche possibile escludere in modo esplicito un set di app dal criterio.

    ![Controllo](./media/active-directory-conditional-access-azure-portal/09.png)


- **Come**: purché l'accesso alle app avvenga in condizioni controllabili, non è necessario imporre altri controlli sull'accesso degli utenti alle app per cloud. Le cose tuttavia cambiano se l'accesso alle app per cloud viene eseguito, ad esempio, da reti non considerate attendibili o da dispositivi non conformi. In un'istruzione della condizione è possibile definire alcune condizioni di accesso con requisiti aggiuntivi per l'esecuzione dell'accesso alle app.

    ![Condizioni](./media/active-directory-conditional-access-azure-portal/01.png)


## <a name="conditions"></a>Condizioni

Nell'implementazione corrente di Azure Active Directory è possibile definire condizioni per le aree seguenti:


- **Piattaforme del dispositivo**: la piattaforma del dispositivo è caratterizzata dal sistema operativo in esecuzione nel dispositivo (Android, iOS, Windows Phone, Windows). È possibile definire le piattaforme del dispositivo incluse, ma anche quelle escluse da un criterio.  
Per usare le piattaforme del dispositivo, impostare prima i controlli di configurazione su **Sì** e quindi selezionare tutte oppure una o più piattaforme del dispositivo a cui il criterio si applica. Se si selezionano singole piattaforme del dispositivo, il criterio ha effetto solo su queste piattaforme. In questo caso, gli accessi alle altre piattaforme supportate non sono interessati dal criterio.

    ![Condizioni](./media/active-directory-conditional-access-azure-portal/02.png)

- **Località**: la località è identificata dall'indirizzo IP del client usato per connettersi ad Azure Active Directory. Questa condizione richiede familiarità con gli IP attendibili. Indirizzi IP attendibili è una funzionalità di Multi-Factor Authentication che consente di definire intervalli di indirizzi IP attendibili che rappresentano la Intranet locale dell'organizzazione. Quando si configurano le condizioni di una località, Indirizzi IP attendibili consente di distinguere le connessioni stabilite dalla rete dell'organizzazione da quelle stabilite da tutte le altre località. Per altre informazioni, vedere [Indirizzi IP attendibili](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips).  
È possibile includere tutte le località o tutti gli IP attendibili ed escludere tutti gli IP attendibili.

    ![Condizioni](./media/active-directory-conditional-access-azure-portal/03.png)


- **App client**: l'app client può essere, a livello generale, l'app (Web browser, app per dispositivi mobili, client per desktop) usata per connettersi ad Azure Active Directory oppure è possibile selezionare in modo specifico Exchange Active Sync.  
L'autenticazione legacy fa riferimento ai client che usano l'autenticazione di base, ad esempio i client Office meno recenti che non usano la moderna autenticazione. L'accesso condizionale non è attualmente supportato con l'autenticazione legacy.

    ![Condizioni](./media/active-directory-conditional-access-azure-portal/04.png)


## <a name="what-you-should-know"></a>Informazioni utili

### <a name="do-i-need-to-assign-a-user-to-my-policy"></a>È necessario assegnare un utente al criterio?

Quando si configura un criterio di accesso condizionale, è consigliabile assegnargli almeno un gruppo. Un criterio di accesso condizionale a cui non sono assegnati utenti e gruppi non viene mai attivato.

Quando si vogliono assegnare più utenti e gruppi a un criterio, è consigliabile iniziare assegnando un solo utente o gruppo e quindi testare la configurazione. Se il criterio funziona come previsto, sarà quindi possibile aggiungergli altre assegnazioni.  


### <a name="how-are-assignments-evaluated"></a>Come vengono valutate le assegnazioni?

Tutte le assegnazioni vengono collegate logicamente con l'operatore **AND**. Se è configurata più di un'assegnazione, per attivare un criterio, devono essere soddisfatte tutte le assegnazioni.  

Se è necessario configurare una condizione della località che si applica a tutte le connessioni stabilite dall'esterno della rete dell'organizzazione, è possibile:

- Includere **Tutte le località**
- Escludere **Tutti gli indirizzi IP attendibili**

### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>Che cosa accade se sono configurati criteri nel portale di Azure classico e nel portale di Azure?  
Entrambi i criteri vengono applicati da Azure Active Directory e l'utente ottiene l'accesso solo quando tutti i requisiti vengono soddisfatti.

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>Che cosa accade se sono presenti criteri nel portale di Intune Silverlight e nel portale di Azure?
Entrambi i criteri vengono applicati da Azure Active Directory e l'utente ottiene l'accesso solo quando tutti i requisiti vengono soddisfatti.

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>Che cosa accade se sono configurati più criteri per lo stesso utente?  
Per ogni accesso, Azure Active Directory valuta tutti i criteri e verifica che tutti i requisiti vengano soddisfatti prima di concedere l'accesso all'utente.


### <a name="does-conditional-access-work-with-exchange-activesync"></a>L'accesso condizionale funziona con Exchange ActiveSync?

No, non è possibile usare Exchange ActiveSync in un criterio di accesso condizionale in questa fase.


### <a name="what-happens-if-i-require-multi-factor-authentication-or-a-compliant-device"></a>Che cosa accade se si richiedi l'autenticazione a più fattori o un dispositivo conforme?

Attualmente, all'utente verrà chiesta l'autenticazione a più fattori indipendentemente dal dispositivo.


## <a name="what-you-should-avoid-doing"></a>Azioni da evitare

Il framework di accesso condizionale offre ottima flessibilità di configurazione. Tuttavia, questa flessibilità ideale comporta anche che è opportuno esaminare attentamente ogni criterio di configurazione prima che venga rilasciato, in modo da evitare risultati indesiderati. In questo contesto, è necessario prestare particolare attenzione alle assegnazioni che interessano set completi, ad esempio **tutti gli utenti/i gruppi/le applicazioni cloud**.

Nell'ambiente, è necessario evitare le seguenti configurazioni:


**Per tutti gli utenti e tutte le applicazioni cloud:**

- **Blocca accesso**: questa configurazione consente di bloccare l'intera organizzazione. Un'idea chiaramente non buona.

- **Richiedi un dispositivo conforme**: per gli utenti che non hanno ancora registrato i propri dispositivi, questo criterio blocca tutti gli accessi, tra cui l'accesso al portale di Intune. Se l'utente è amministratore senza un dispositivo registrato, questo criterio blocca l'accesso al portale di Azure per la modifica dei criteri.

- **Require domain join** (Richiedi aggiunta a dominio): se ancora non si dispone di un dispositivo aggiunto al dominio, questo criterio di blocco dell'accesso è anche in grado di bloccare l'accesso per tutti gli utenti nell'organizzazione.


**Per tutti gli utenti, tutte le applicazioni cloud e tutte le piattaforme per dispositivi:**

- **Blocca accesso**: questa configurazione consente di bloccare l'intera organizzazione. Un'idea chiaramente non buona.


## <a name="common-scenarios"></a>Scenari comuni

### <a name="requiring-multi-factor-authentication-for-apps"></a>Richiesta dell'autenticazione a più fattori per le app

Diversi ambienti hanno app che richiedono un livello di protezione maggiore delle altre,
ad esempio, app che hanno accesso a dati sensibili.
Per aggiungere un altro livello di protezione a queste app, è possibile configurare un criterio di accesso condizionale che richiede l'autenticazione a più fattori quando gli utenti accedono a queste app.


### <a name="requiring-multi-factor-authentication-for-access-from-networks-that-are-not-trusted"></a>Richiesta dell'autenticazione a più fattori per l'accesso da reti non considerate attendibili

Questo scenario è simile a quello precedente perché aggiunge un requisito per l'autenticazione a più fattori.
La differenza principale è tuttavia la condizione per questo requisito.  
Mentre l'elemento essenziale dello scenario precedente sono le app con accesso a dati sensibili, in questo scenario l'elemento essenziale sono le località attendibili.  
In altre parole, è possibile avere un requisito per l'autenticazione a più fattori se un'app è accessibile a un utente da una rete non considerata attendibile.


### <a name="only-trusted-devices-can-access-office-365-services"></a>Solo i dispositivi attendibili possono accedere ai servizi di Office 365

Se si usa Intune nell'ambiente, è possibile iniziare immediatamente a usare l'interfaccia del criterio di accesso condizionale nella console Azure.

Molti clienti Intune usano l'accesso condizionale per assicurarsi che solo i dispositivi attendibili possano accedere a Office 365. Di conseguenza i dispositivi mobili vengono registrati con Intune e soddisfano i requisiti dei criteri di conformità e i PC Windows vengono aggiunti e un dominio locale. Un importante miglioramento consiste nel fatto che non è necessario impostare lo stesso criterio per ogni servizio di Office 365.  Quando si crea un nuovo criterio, configurare le app per cloud per includere ogni app di O365 che si vuole proteggere con l'accesso condizionale.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come configurare un criterio di accesso condizionale, vedere [Get started with conditional access in Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md) (Introduzione all'accesso condizionale in Azure Active Directory).

