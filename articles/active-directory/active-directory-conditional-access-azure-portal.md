---
title: Accesso condizionale di Azure Active Directory | Microsoft Docs
description: Usare il controllo di accesso condizionale di Azure Active Directory per controllare condizioni specifiche durante il processo di autenticazione per l'accesso alle applicazioni.
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
ms.date: 09/20/2017
ms.author: markvi
ms.reviewer: calebb
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 9424f43db504964ba5ea3cbd84f305d1a6697208
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---
# <a name="conditional-access-in-azure-active-directory"></a>Accesso condizionale in Azure Active Directory

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

Sono disponibili due tipi di controlli: 

- **Controlli concessione**: questi controlli determinano se un utente può completare o meno l'autenticazione e raggiungere la risorsa a cui sta tentando di accedere. Se si dispone di più controlli selezionati, è possibile configurare se sono tutti obbligatori quando vengono elaborati i criteri.
L'implementazione corrente di Azure Active Directory consente di configurare i requisiti dei controlli di concessione seguenti:

    ![Controllo](./media/active-directory-conditional-access-azure-portal/73.png)

- **Controlli della sessione**: questi controlli consentono di limitare l'esperienza all'interno di un'app cloud. Questi controlli sono imposti dalle app cloud e si basano sulle informazioni aggiuntive relative alla sessione fornite da Azure AD all'app.

    ![Controllo](./media/active-directory-conditional-access-azure-portal/31.png)


Per altre informazioni, vedere [Controls in Azure Active Directory Conditional Access](active-directory-conditional-access-controls.md) (Controlli nell'accesso condizionale in Azure Active Directory).


## <a name="condition-statement"></a>Istruzione della condizione

La sezione precedente ha illustrato le opzioni supportate per bloccare o limitare l'accesso alle risorse sotto forma di controlli. In un criterio di accesso condizionale si definiscono i criteri che devono essere soddisfatti per i controlli da applicare sotto forma di istruzione della condizione.  

È possibile includere le assegnazioni seguenti nell'istruzione della condizione:

![Controllo](./media/active-directory-conditional-access-azure-portal/07.png)


### <a name="who"></a>Chi?

Quando si configura un criterio di accesso condizionale, è necessario selezionare gli utenti o i gruppi a cui il criterio si applica. In molti casi è necessario applicare i controlli a uno specifico set di utenti. In un'istruzione della condizione è possibile definire questo set selezionando gli utenti e gruppi necessari a cui si applica il criterio. Se necessario, è anche possibile escludere in modo esplicito un set di utenti dal criterio esentandoli.  

![Controllo](./media/active-directory-conditional-access-azure-portal/08.png)



### <a name="what"></a>Cosa?

Quando si configura un criterio di accesso condizionale, è necessario selezionare le app cloud a cui il criterio si applica.
Di solito nel proprio ambiente sono presenti alcune app che, dal punto di vista della protezione, richiedono più attenzione di altre, ad esempio, app che hanno accesso a dati sensibili.
Selezionando le app per cloud, si definisce l'ambito delle app per cloud a cui si applica il criterio. Se necessario, è anche possibile escludere in modo esplicito un set di app dal criterio.

![Controllo](./media/active-directory-conditional-access-azure-portal/09.png)

Per un elenco completo delle app cloud che è possibile usare nel criterio di accesso condizionale, vedere [Documentazione tecnica sull'accesso condizionale di Azure Active Directory](active-directory-conditional-access-technical-reference.md#cloud-apps-assignments).

### <a name="how"></a>Come?

Purché l'accesso alle app avvenga in condizioni controllabili, non è necessario imporre altri controlli sull'accesso degli utenti alle app per cloud. Le cose tuttavia cambiano se l'accesso alle app per cloud viene eseguito, ad esempio, da reti non considerate attendibili o da dispositivi non conformi. In un'istruzione della condizione è possibile definire alcune condizioni di accesso con requisiti aggiuntivi per l'esecuzione dell'accesso alle app.

![Condizioni](./media/active-directory-conditional-access-azure-portal/01.png)


## <a name="conditions"></a>Condizioni

Nell'implementazione corrente di Azure Active Directory è possibile definire condizioni per le aree seguenti:

- Rischio di accesso
- Piattaforme del dispositivo
- Località
- App client
- Tempo

![Condizioni](./media/active-directory-conditional-access-azure-portal/01.png)

### <a name="sign-in-risk"></a>Rischio di accesso

Un rischio di accesso è un oggetto usato da Azure Active Directory per tenere traccia della probabilità che un tentativo di accesso non sia eseguito dal proprietario legittimo di un account utente. In questo oggetto la probabilità (alta, media o bassa) viene archiviata sotto forma di attributo denominato [livello di rischio di accesso](active-directory-reporting-risk-events.md#risk-level). L'oggetto viene generato durante l'accesso di un utente se vengono rilevati rischi di accesso da Azure Active Directory. Per altre informazioni, vedere [Accessi a rischio](active-directory-identityprotection.md#risky-sign-ins).  
È possibile usare il livello di rischio di accesso calcolato come condizione nei criteri di accesso condizionale. 

![Condizioni](./media/active-directory-conditional-access-azure-portal/22.png)

### <a name="device-platforms"></a>Piattaforme del dispositivo

Le piattaforme del dispositivo sono caratterizzate dal sistema operativo in esecuzione sul dispositivo:

- Android
- iOS
- Windows Phone
- Windows
- macOS (anteprima). 

![Condizioni](./media/active-directory-conditional-access-azure-portal/02.png)

È possibile definire le piattaforme del dispositivo incluse, ma anche quelle escluse da un criterio.  
Per usare le piattaforme del dispositivo, impostare prima i controlli di configurazione su **Sì** e quindi selezionare tutte oppure una o più piattaforme del dispositivo a cui il criterio si applica. Se si selezionano singole piattaforme del dispositivo, il criterio ha effetto solo su queste piattaforme. In questo caso, gli accessi alle altre piattaforme supportate non sono interessati dal criterio.


### <a name="locations"></a>Località

Le località consentono di definire le condizioni che dipendono dal punto in cui è stato avviato un tentativo di connessione. Nell'elenco delle località le voci sono suddivise tra **località denominate** e **indirizzi IP attendibili MFA**.  

**Località denominate** è una funzionalità di Azure Active Directory che consente di definire etichette per le località da cui sono stati eseguiti tentativi di connessione. Per definire una località, è possibile configurare un intervallo di indirizzi IP oppure selezionare un paese o un'area geografica.  

![Condizioni](./media/active-directory-conditional-access-azure-portal/42.png)

È possibile anche contrassegnare una località denominata come località attendibile. Per i criteri di accesso condizionale, la località attendibile è un'altra opzione di filtro che consente di selezionare *Tutte le posizioni attendibili* nella condizione delle località.
Le località denominate sono importanti anche nel contesto del rilevamento di [eventi di rischio](active-directory-reporting-risk-events.md) per ridurre il numero di falsi positivi per eventi di rischio di tipo Trasferimento impossibile a posizioni atipiche. 

Il numero di località denominate che è possibile configurare è limitato dalle dimensioni dell'oggetto correlato in Azure AD. È possibile configurare:
 
 - Una località denominata con un massimo di 500 intervalli IP
 - Un massimo di 60 località denominate (anteprima), ognuna con un intervallo IP assegnato 

Per altre informazioni, vedere [Località denominate in Azure Active Directory](active-directory-named-locations.md).


Gli **IP attendibili MFA** sono una funzionalità di Multi-Factor Authentication che consente di definire intervalli di indirizzi IP attendibili che rappresentano la Intranet locale dell'organizzazione. Quando si configura la condizione di una località, Indirizzi IP attendibili consente di distinguere le connessioni stabilite dalla rete dell'organizzazione da quelle stabilite da tutte le altre località. Per altre informazioni, vedere [IP attendibili](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips).  

Nei criteri di accesso condizionale, è possibile:

- Includere
    - Qualsiasi località
    - Tutte le località attendibili
    - Le località selezionate
- Escludere
    - Tutte le località attendibili
    - Le località selezionate
     
![Condizioni](./media/active-directory-conditional-access-azure-portal/03.png)


### <a name="client-apps"></a>App client

L'app client può essere, a livello generale, l'app (Web browser, app per dispositivi mobili, client desktop) usata per la connessione ad Azure Active Directory oppure è possibile selezionare in modo specifico Exchange Active Sync.  
L'autenticazione legacy fa riferimento ai client che usano l'autenticazione di base, ad esempio i client Office meno recenti che non usano la moderna autenticazione. L'accesso condizionale non è attualmente supportato con l'autenticazione legacy.

![Condizioni](./media/active-directory-conditional-access-azure-portal/04.png)


Per un elenco completo delle app client che è possibile usare nel criterio di accesso condizionale, vedere [Documentazione tecnica sull'accesso condizionale di Azure Active Directory](active-directory-conditional-access-technical-reference.md#client-apps-condition).




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

Molti clienti Intune usano l'accesso condizionale per assicurarsi che solo i dispositivi attendibili possano accedere a Office 365. Di conseguenza i dispositivi mobili vengono registrati con Intune e soddisfano i requisiti dei criteri di conformità e i PC Windows vengono aggiunti e un dominio locale. Un importante miglioramento consiste nel fatto che non è necessario impostare lo stesso criterio per ogni servizio di Office 365.  Quando si crea un nuovo criterio, configurare le app per cloud in modo da includere ogni app di O365 da proteggere con l'accesso condizionale.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come configurare un criterio di accesso condizionale, vedere [Get started with conditional access in Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md) (Introduzione all'accesso condizionale in Azure Active Directory).

- Se si è pronti per configurare i criteri di accesso condizionale per l'ambiente in uso, vedere il [Procedure consigliate per l'accesso condizionale in Azure Active Directory](active-directory-conditional-access-best-practices.md). 

