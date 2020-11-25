---
title: Condizione di posizione nell'accesso condizionale Azure Active Directory
description: Informazioni su come usare la condizione della posizione per controllare l'accesso alle app cloud in base al percorso di rete dell'utente.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: 888ed2fa24b82c0dda3361df1c63bb802e58f5fe
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95904104"
---
# <a name="using-the-location-condition-in-a-conditional-access-policy"></a>Uso della condizione location in un criterio di accesso condizionale 

Come illustrato nell' [articolo introduttivo](overview.md) , i criteri di accesso condizionale sono alla base di un'istruzione if-then che combina i segnali, per prendere decisioni e applicare i criteri dell'organizzazione. Uno di questi segnali che possono essere incorporati nel processo decisionale è il percorso di rete.

![Segnale condizionale concettuale oltre a decisione per l'applicazione di criteri](./media/location-condition/conditional-access-signal-decision-enforcement.png)

Le organizzazioni possono usare questo percorso di rete per attività comuni come: 

- Necessità dell'autenticazione a più fattori per gli utenti che accedono a un servizio quando non sono connessi alla rete aziendale.
- Blocco dell'accesso per gli utenti che accedono a un servizio da specifici paesi o aree geografiche.

Il percorso di rete è determinato dall'indirizzo IP pubblico fornito da un client per Azure Active Directory. Per impostazione predefinita, i criteri di accesso condizionale si applicano a tutti gli indirizzi IPv4 e IPv6. 

> [!TIP]
> Gli intervalli IPv6 sono supportati solo nell'interfaccia **[location denominata (anteprima)](#preview-features)** . 

## <a name="named-locations"></a>Posizioni specifiche

Le località sono indicate nel portale di Azure in **Azure Active Directory**  >  **sicurezza**  >  **accesso condizionale**  >  **località denominate**. Questi percorsi di rete denominati possono includere percorsi come intervalli di rete della sede centrale dell'organizzazione, intervalli di reti VPN o intervalli che si desidera bloccare. 

![Località denominate nel portale di Azure](./media/location-condition/new-named-location.png)

Per configurare un percorso, è necessario specificare almeno un **nome** e l'intervallo di indirizzi IP. 

Il numero di località denominate che è possibile configurare è limitato dalle dimensioni dell'oggetto correlato in Azure AD. È possibile configurare i percorsi in base alle limitazioni seguenti:

- Una località denominata con un massimo di 1200 intervalli IPv4.
- Un massimo di 90 posizioni specifiche, ognuna con un intervallo IP assegnato.

> [!TIP]
> Gli intervalli IPv6 sono supportati solo nell'interfaccia **[location denominata (anteprima)](#preview-features)** . 

### <a name="trusted-locations"></a>Percorsi attendibili

Quando si crea un percorso di rete, un amministratore ha la possibilità di contrassegnare un percorso come percorso attendibile. 

![Percorsi attendibili nella portale di Azure](./media/location-condition/new-trusted-location.png)

Questa opzione può includere i criteri di accesso condizionale in cui è possibile, ad esempio, richiedere la registrazione per l'autenticazione a più fattori da un percorso di rete attendibile. Inoltre, il calcolo del rischio è Azure AD Identity Protection, abbassando il rischio di accesso degli utenti quando provengono da una località contrassegnata come attendibile.

### <a name="countries-and-regions"></a>Paesi e aree geografiche

Alcune organizzazioni possono scegliere di definire interi paesi o aree geografiche per i confini IP come località denominate per i criteri di accesso condizionale. Questi percorsi possono essere usati quando si blocca il traffico non necessario quando si conoscono utenti validi che non proverranno mai da una località come la Corea del Nord. Questi mapping dell'indirizzo IP al paese vengono aggiornati periodicamente. 

> [!NOTE]
> Non è possibile eseguire il mapping degli intervalli di indirizzi IPv6 ai paesi. Solo gli indirizzi IPv4 vengono mappati ai paesi.

![Creare una nuova località basata su paese o area geografica nella portale di Azure](./media/location-condition/new-named-location-country-region.png)

#### <a name="include-unknown-areas"></a>Includi aree sconosciute

Alcuni indirizzi IP non sono mappati a un paese o a un'area specifica, inclusi tutti gli indirizzi IPv6. Per acquisire questi percorsi IP, selezionare la casella **Includi aree sconosciute** durante la definizione di un percorso. Questa opzione consente di scegliere se questi indirizzi IP devono essere inclusi nella posizione specifica. Usare questa impostazione quando i criteri che usano la posizione specifica devono essere applicati a posizioni sconosciute.

### <a name="configure-mfa-trusted-ips"></a>Configurare gli indirizzi IP attendibili a più fattori

È inoltre possibile configurare gli intervalli di indirizzi IP che rappresentano la Intranet locale dell'organizzazione nelle [impostazioni del servizio di autenticazione a più fattori](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx). Questa funzionalità consente di configurare fino a 50 intervalli di indirizzi IP. Gli intervalli di indirizzi IP sono in formato CIDR. Per altre informazioni, vedere [indirizzi IP attendibili](../authentication/howto-mfa-mfasettings.md#trusted-ips).  

Se sono stati configurati indirizzi IP attendibili, vengono visualizzati come **indirizzi IP attendibili** a più fattori nell'elenco dei percorsi per la condizione di posizione.

### <a name="skipping-multi-factor-authentication"></a>Ignorare l'autenticazione a più fattori

Nella pagina delle impostazioni del servizio di autenticazione a più fattori, è possibile identificare gli utenti della rete Intranet aziendale selezionando **Ignora l'autenticazione a più fattori per le richieste provenienti da utenti federati nella Intranet**. Questa impostazione indica che l'attestazione della rete aziendale interna, rilasciata da AD FS, deve essere attendibile e usata per identificare l'utente come appartenente alla rete aziendale. Per altre informazioni, vedere [abilitare la funzionalità indirizzi IP attendibili tramite l'accesso condizionale](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access).

Dopo aver selezionato questa opzione, inclusa la località denominata autenticazione a più fattori **indirizzi IP attendibili** verrà applicata a tutti i criteri con questa opzione selezionata.

Per le applicazioni per dispositivi mobili e desktop, che hanno durate di sessione di lunga durata, l'accesso condizionale viene periodicamente rivalutato. Il valore predefinito è una volta ogni ora. Quando l'attestazione della rete aziendale interna viene emessa solo al momento dell'autenticazione iniziale, Azure AD potrebbe non disporre di un elenco di intervalli IP attendibili. In questo caso, è più difficile determinare se l'utente sia ancora presente nella rete aziendale:

1. Verificare se l'indirizzo IP dell'utente è in uno degli intervalli di indirizzi IP attendibili.
1. Controllare se i primi tre ottetti dell'indirizzo IP dell'utente corrispondono ai primi tre ottetti dell'indirizzo IP dell'autenticazione iniziale. L'indirizzo IP viene confrontato con l'autenticazione iniziale quando l'attestazione della rete aziendale interna è stata originariamente rilasciata e la posizione utente è stata convalidata.

Se entrambi i passaggi danno esito negativo, l'utente non viene più considerato come su un indirizzo IP attendibile.

## <a name="preview-features"></a>Funzionalità di anteprima

Oltre alla funzionalità di località denominata disponibile a livello generale, esiste anche una località denominata (anteprima). È possibile accedere all'anteprima del percorso denominato usando il banner nella parte superiore del pannello della località denominata corrente.

![Prova l'anteprima dei percorsi denominati](./media/location-condition/preview-features.png)

Con l'anteprima della località denominata, è possibile

- Configurare fino a 195 località denominate
- Configurare fino a 2000 intervalli IP per località denominata
- Configurare gli indirizzi IPv6 insieme agli indirizzi IPv4

Sono stati anche aggiunti altri controlli per ridurre la modifica della configurazione non consentita.

- Non è più possibile configurare gli intervalli di indirizzi IP privati
- Il numero di indirizzi IP che è possibile includere in un intervallo è limitato. Quando si configura un intervallo IP, saranno consentite solo le maschere CIDR maggiori di/8.

Con l'anteprima sono ora disponibili due opzioni di creazione: 

- **Località paesi**
- **Percorso degli intervalli IP**

> [!NOTE]
> Non è possibile eseguire il mapping degli intervalli di indirizzi IPv6 ai paesi. Solo gli indirizzi IPv4 vengono mappati ai paesi.

![Interfaccia di anteprima località denominate](./media/location-condition/named-location-preview.png)

## <a name="location-condition-in-policy"></a>Condizione del percorso nei criteri

Quando si configura la condizione per la posizione, è possibile distinguere tra:

- Tutti i percorsi
- Tutte le località attendibili
- Le località selezionate

### <a name="any-location"></a>Tutti i percorsi

Per impostazione predefinita, la selezione di **Tutte le località** fa in modo che i criteri vengano applicati a tutti gli indirizzi IP, cioè qualsiasi indirizzo su Internet. Questa impostazione non è limitata agli indirizzi IP configurati come posizione specifica. Quando si seleziona **Tutte le località**, è comunque possibile escludere percorsi specifici dai criteri. Ad esempio, è possibile applicare dei criteri a tutte le posizioni tranne che ai percorsi attendibili per impostare l'ambito per tutte le posizioni ad eccezione della rete aziendale.

### <a name="all-trusted-locations"></a>Tutte le località attendibili

Questa opzione si applica a:

- Tutte le posizioni che sono state contrassegnate come attendibili
- Gli **indirizzi IP attendibili MFA** (se configurati)

### <a name="selected-locations"></a>Le località selezionate

Con questa opzione è possibile selezionare una o più posizioni specifiche. Per applicare criteri con questa impostazione, un utente deve connettersi da una delle posizioni selezionate. Quando si fa clic su **Seleziona**, si apre il controllo di selezione delle reti denominate che mostra l'elenco delle reti denominate. L'elenco indica anche se il percorso di rete è stato contrassegnato come attendibile. La posizione specifica denominata **Indirizzi IP attendibili MFA** viene usata per includere le impostazioni IP che possono essere configurate nella pagina di impostazione del servizio di autenticazione a più fattori.

## <a name="ipv6-traffic"></a>Traffico IPv6

Per impostazione predefinita, i criteri di accesso condizionale si applicano a tutto il traffico IPv6. Con l' [anteprima del percorso denominato](#preview-features), è possibile escludere intervalli di indirizzi IPv6 specifici da un criterio di accesso condizionale. Questa opzione è utile nei casi in cui non si desidera che i criteri vengano applicati per intervalli IPv6 specifici. Ad esempio, se si desidera non applicare un criterio per gli utilizzi nella rete aziendale e la rete aziendale è ospitata in intervalli IPv6 pubblici.  

### <a name="when-will-my-tenant-have-ipv6-traffic"></a>Quando il tenant avrà il traffico IPv6?

Azure Active Directory (Azure AD) attualmente non supporta connessioni di rete dirette che utilizzano IPv6. Tuttavia, in alcuni casi il traffico di autenticazione viene inoltrato tramite un altro servizio. In questi casi, l'indirizzo IPv6 verrà usato durante la valutazione dei criteri.

La maggior parte del traffico IPv6 che viene inoltrato a Azure AD deriva da Microsoft Exchange Online. Se disponibile, Exchange preferisce le connessioni IPv6. **Quindi, se si dispone di criteri di accesso condizionale per Exchange, che sono stati configurati per intervalli IPv4 specifici, è necessario assicurarsi di aver aggiunto anche gli intervalli IPv6 delle organizzazioni.** L'inclusione degli intervalli IPv6 provocherà un comportamento imprevisto per i due casi seguenti:

- Quando si usa un client di posta elettronica per connettersi a Exchange Online con l'autenticazione legacy, Azure AD possibile che riceva un indirizzo IPv6. La richiesta di autenticazione iniziale passa a Exchange e viene quindi inoltrata tramite proxy a Azure AD.
- Quando Outlook Accesso Web (OWA) viene usato nel browser, verificherà periodicamente che tutti i criteri di accesso condizionale continuino a essere soddisfatti. Questo controllo viene usato per rilevare i casi in cui è possibile che un utente sia stato spostato da un indirizzo IP consentito a una nuova posizione, ad esempio la caffetteria giù dalla strada. In questo caso, se viene utilizzato un indirizzo IPv6 e se l'indirizzo IPv6 non è compreso in un intervallo configurato, è possibile che la sessione dell'utente sia interrotta e che venga reindirizzata a Azure AD per eseguire di nuovo l'autenticazione. 

Questi sono i motivi più comuni per cui potrebbe essere necessario configurare gli intervalli IPv6 nelle località denominate. Inoltre, se si usa Azure reti virtuali, si disporrà di traffico proveniente da un indirizzo IPv6. Se il traffico VNet è bloccato da un criterio di accesso condizionale, controllare il log di accesso Azure AD. Una volta identificato il traffico, è possibile ottenere l'indirizzo IPv6 usato ed escluderlo dal criterio. 

> [!NOTE]
> Se si desidera specificare un intervallo di CIDR IP per un singolo indirizzo, applicare la maschera di bit/128. Se si dice l'indirizzo IPv6 2607: FB90: b27a: 6f69: f8d5: dea0: FB39:74A e si vuole escludere tale indirizzo singolo come intervallo, usare 2607: FB90: b27a: 6f69: f8d5: dea0: FB39:74a/128.

### <a name="identifying-ipv6-traffic-in-the-azure-ad-sign-in-activity-reports"></a>Identificazione del traffico IPv6 nei report delle attività di accesso Azure AD

Per individuare il traffico IPv6 nel tenant, è possibile passare ai [report dell'attività di accesso Azure ad](../reports-monitoring/concept-sign-ins.md). Dopo aver aperto il report attività, aggiungere la colonna "indirizzo IP". Questa colonna consente di identificare il traffico IPv6.

È anche possibile trovare l'indirizzo IP del client facendo clic su una riga nel report, quindi passando alla scheda "posizione" nei dettagli dell'attività di accesso. 

## <a name="what-you-should-know"></a>Informazioni importanti

### <a name="when-is-a-location-evaluated"></a>Quando viene valutata una posizione?

I criteri di accesso condizionale vengono valutati quando:

- Un utente accede inizialmente a un'app Web, un'applicazione per dispositivi mobili o un'applicazione desktop.
- Un'applicazione desktop o per dispositivo mobili che usa l'autenticazione moderna usa un token di aggiornamento per acquisire un nuovo token di accesso. Per impostazione predefinita, questo controllo è una volta all'ora.

Questo controllo significa che per le applicazioni desktop e per dispositivi mobili che usano l'autenticazione moderna, viene rilevata una modifica nella posizione entro un'ora dalla modifica del percorso di rete. Per le applicazioni desktop e per dispositivi mobili che non usano l'autenticazione moderna, questo criterio viene applicato a ogni richiesta di token. La frequenza della richiesta può variare in base all'applicazione. Analogamente, per le applicazioni Web il criterio viene applicato all'accesso iniziale e rimane valido per l'intera durata della sessione dell'applicazione Web interessata. A causa delle differenze a livello di durata delle sessioni delle applicazioni, anche il tempo tra le valutazioni dei criteri può variare. Il criterio viene applicato ogni volta che l'applicazione richiede un nuovo token di accesso.

Per impostazione predefinita, Azure AD rilascia un token su base oraria. Se si esce dalla rete aziendale, entro un'ora i criteri vengono applicati per le applicazioni che usano l'autenticazione moderna.

### <a name="user-ip-address"></a>Indirizzo IP utente

L'indirizzo IP usato nella valutazione dei criteri è l'indirizzo IP pubblico dell'utente. Per i dispositivi su una rete privata, questo indirizzo IP non è l'IP client del dispositivo dell'utente nella Intranet, ma è l'indirizzo usato dalla rete per connettersi alla rete Internet pubblica.

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>Caricamento e download in blocco delle posizioni specifiche

Quando si creano o si aggiornano le posizioni specifiche, per gli aggiornamenti in blocco è possibile caricare o scaricare un file CSV con gli intervalli di indirizzi IP. Un caricamento sostituisce gli intervalli IP nell'elenco con tali intervalli dal file. Ogni riga del file contiene un intervallo di indirizzi IP in formato CIDR.

### <a name="cloud-proxies-and-vpns"></a>Proxy cloud e VPN

Quando si usa un proxy ospitato nel cloud o una soluzione VPN, l'indirizzo IP usato da Azure AD durante la valutazione dei criteri è l'indirizzo IP del proxy. L'intestazione X-Forwarded-For (XFF) che contiene l'indirizzo IP pubblico dell'utente non viene usata perché non è certo che provenga da un'origine attendibile, pertanto potrebbe essere un metodo per falsificare un indirizzo IP.

Quando è presente un proxy cloud, è possibile usare un criterio usato per richiedere un dispositivo ibrido Azure AD aggiunto o l'attestazione corpnet interna da AD FS.

### <a name="api-support-and-powershell"></a>Supporto dell'API e PowerShell

È disponibile una versione di anteprima del API Graph per le località denominate. per altre informazioni, vedere l' [API namedLocation](/graph/api/resources/namedlocation?view=graph-rest-beta).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come configurare i criteri di accesso condizionale, vedere l'articolo [creazione di un criterio di accesso condizionale](concept-conditional-access-policies.md).
- Cerchi un criterio di esempio che usa la condizione location? Vedere l'articolo relativo all' [accesso condizionale: bloccare l'accesso in base alla località](howto-conditional-access-policy-location.md)
