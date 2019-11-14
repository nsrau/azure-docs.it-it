---
title: Qual è la condizione di posizione in Azure Active Directory l'accesso condizionale? | Documentazione di Microsoft
description: Informazioni su come usare la condizione della posizione per controllare l'accesso alle app cloud in base al percorso di rete dell'utente.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.workload: identity
ms.date: 04/12/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 799a994e1351f62fac9f5a07060658cea60c9274
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74065741"
---
# <a name="what-is-the-location-condition-in-azure-active-directory-conditional-access"></a>Qual è la condizione di posizione in Azure Active Directory l'accesso condizionale? 

Con [l'accesso condizionale Azure Active Directory (Azure ad)](../active-directory-conditional-access-azure-portal.md), è possibile controllare il modo in cui gli utenti autorizzati possono accedere alle app cloud. La condizione di percorso di un criterio di accesso condizionale consente di associare le impostazioni dei controlli di accesso ai percorsi di rete degli utenti.

Questo articolo fornisce le informazioni necessarie per configurare la condizione della posizione.

## <a name="locations"></a>Località

Azure AD abilita Single Sign-On a dispositivi, app e servizi da qualsiasi posizione sulla rete Internet pubblica. Con la condizione della posizione, è possibile controllare l'accesso alle app cloud in base al percorso di rete dell'utente. Casi d'uso comuni per la condizione della posizione:

- Necessità dell'autenticazione a più fattori per gli utenti che accedono a un servizio quando non sono connessi alla rete aziendale.
- Blocco dell'accesso per gli utenti che accedono a un servizio da specifici paesi o aree geografiche.

Un percorso è un'etichetta per un percorso di rete che rappresenta una località denominata o indirizzi IP attendibili di autenticazione a più fattori.

## <a name="named-locations"></a>Posizioni specifiche

Con le località denominate, è possibile creare raggruppamenti logici di intervalli di indirizzi IP o paesi e aree geografiche.

È possibile accedere alle località denominate nella sezione **Gestisci** della pagina accesso condizionale.

![Località denominate nell'accesso condizionale](./media/location-condition/02.png)

Una posizione specifica ha le caratteristiche seguenti:

![Crea una nuova posizione denominata](./media/location-condition/42.png)

- **Nome**: nome visualizzato di una posizione specifica.
- **Intervalli IP**: uno o più intervalli di indirizzi IPv4 in formato CIDR. La specifica di un intervallo di indirizzi IPv6 non è supportata.

   > [!NOTE]
   > Gli intervalli di indirizzi IPv6 attualmente non possono essere inclusi in una posizione denominata. Questo significa che gli intervalli IPv6 non possono essere esclusi da un criterio di accesso condizionale.

- **Contrassegna come posizione attendibile**: flag che è possibile impostare per una posizione specifica per indicare un percorso attendibile. In genere, i percorsi attendibili sono aree della rete controllate dal reparto IT. Oltre all'accesso condizionale, le località denominate attendibili vengono usate anche da Azure Identity Protection e Azure AD report di sicurezza per ridurre i [falsi positivi](../reports-monitoring/concept-risk-events.md#impossible-travel-to-atypical-locations-1).
- **Paesi/aree geografiche**: questa opzione consente di selezionare uno o più paesi o aree geografiche per definire una posizione specifica.
- **Includi aree sconosciute** : alcuni indirizzi IP non sono mappati a un paese o a un'area specifica. Questa opzione consente di scegliere se questi indirizzi IP devono essere inclusi nella posizione specifica. Usare questa impostazione quando i criteri che usano la posizione specifica devono essere applicati a posizioni sconosciute.

Il numero di località denominate che è possibile configurare è limitato dalle dimensioni dell'oggetto correlato in Azure AD. È possibile configurare i percorsi in base alle limitazioni seguenti:

- Una posizione specifica con un massimo di 1200 intervalli IP.
- Un massimo di 90 posizioni specifiche, ognuna con un intervallo IP assegnato.

I criteri di accesso condizionale si applicano al traffico IPv4 e IPv6. Le località attualmente denominate non consentono la configurazione degli intervalli IPv6. Questa limitazione causa le situazioni seguenti:

- I criteri di accesso condizionale non possono essere assegnati a intervalli IPv6 specifici
- I criteri di accesso condizionale non possono escludere intervalli IPV6 specifici

Se i criteri sono configurati per essere applicati a "qualsiasi percorso", verranno applicati al traffico IPv4 e IPv6. Località denominate configurate per paesi e aree geografiche specificato supporta solo indirizzi IPv4. Il traffico IPv6 è incluso solo se è stata selezionata l'opzione "Includi aree sconosciute".

## <a name="trusted-ips"></a>Provider di identità attendibili

È inoltre possibile configurare gli intervalli di indirizzi IP che rappresentano la Intranet locale dell'organizzazione nelle [impostazioni del servizio di autenticazione a più fattori](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx). Questa funzionalità consente di configurare fino a 50 intervalli di indirizzi IP. Gli intervalli di indirizzi IP sono in formato CIDR. Per altre informazioni, vedere [indirizzi IP attendibili](../authentication/howto-mfa-mfasettings.md#trusted-ips).  

Se sono stati configurati indirizzi IP attendibili, vengono visualizzati come **indirizzi IP attendibili** a più fattori nell'elenco dei percorsi per la condizione di posizione.

### <a name="skipping-multi-factor-authentication"></a>Ignorare l'autenticazione a più fattori

Nella pagina delle impostazioni del servizio di autenticazione a più fattori, è possibile identificare gli utenti della rete Intranet aziendale selezionando **Ignora l'autenticazione a più fattori per le richieste provenienti da utenti federati nella Intranet**. Questa impostazione indica che l'attestazione della rete aziendale interna, rilasciata da AD FS, deve essere attendibile e usata per identificare l'utente come appartenente alla rete aziendale. Per altre informazioni, vedere [abilitare la funzionalità indirizzi IP attendibili tramite l'accesso condizionale](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access).

Dopo aver selezionato questa opzione, inclusa la località denominata autenticazione a più fattori **indirizzi IP attendibili** verrà applicata a tutti i criteri con questa opzione selezionata.

Per le applicazioni per dispositivi mobili e desktop, che hanno durate di sessione di lunga durata, l'accesso condizionale viene periodicamente rivalutato. Il valore predefinito è una volta ogni ora. Quando l'attestazione della rete aziendale interna viene emessa solo al momento dell'autenticazione iniziale, Azure AD potrebbe non disporre di un elenco di intervalli IP attendibili. In questo caso, è più difficile determinare se l'utente sia ancora presente nella rete aziendale:

1. Verificare se l'indirizzo IP dell'utente è in uno degli intervalli di indirizzi IP attendibili.
2. Controllare se i primi tre ottetti dell'indirizzo IP dell'utente corrispondono ai primi tre ottetti dell'indirizzo IP dell'autenticazione iniziale. L'indirizzo IP viene confrontato con l'autenticazione iniziale quando l'attestazione della rete aziendale interna è stata originariamente rilasciata e la posizione utente è stata convalidata.

Se entrambi i passaggi danno esito negativo, l'utente non viene più considerato come su un indirizzo IP attendibile.

## <a name="location-condition-configuration"></a>Configurazione della condizione della posizione

Quando si configura la condizione per la posizione, è possibile distinguere tra:

- Qualsiasi località
- Tutte le località attendibili
- Le località selezionate

![Configurazione della condizione della posizione](./media/location-condition/01.png)

### <a name="any-location"></a>Qualsiasi località

Per impostazione predefinita, la selezione di **Tutte le località** fa in modo che i criteri vengano applicati a tutti gli indirizzi IP, cioè qualsiasi indirizzo su Internet. Questa impostazione non è limitata agli indirizzi IP configurati come posizione specifica. Quando si seleziona **Tutte le località**, è comunque possibile escludere percorsi specifici dai criteri. Ad esempio, è possibile applicare dei criteri a tutte le posizioni tranne che ai percorsi attendibili per impostare l'ambito per tutte le posizioni ad eccezione della rete aziendale.

### <a name="all-trusted-locations"></a>Tutte le località attendibili

Questa opzione si applica a:

- Tutte le posizioni che sono state contrassegnate come attendibili
- Gli **indirizzi IP attendibili MFA** (se configurati)

### <a name="selected-locations"></a>Le località selezionate

Con questa opzione è possibile selezionare una o più posizioni specifiche. Per applicare criteri con questa impostazione, un utente deve connettersi da una delle posizioni selezionate. Quando si fa clic su **Seleziona**, si apre il controllo di selezione delle reti denominate che mostra l'elenco delle reti denominate. L'elenco indica anche se il percorso di rete è stato contrassegnato come attendibile. La posizione specifica denominata **Indirizzi IP attendibili MFA** viene usata per includere le impostazioni IP che possono essere configurate nella pagina di impostazione del servizio di autenticazione a più fattori.

## <a name="what-you-should-know"></a>Informazioni utili

### <a name="when-is-a-location-evaluated"></a>Quando viene valutata una posizione?

I criteri di accesso condizionale vengono valutati quando:

- Un utente accede inizialmente a un'app Web, un'applicazione per dispositivi mobili o un'applicazione desktop.
- Un'applicazione desktop o per dispositivo mobili che usa l'autenticazione moderna usa un token di aggiornamento per acquisire un nuovo token di accesso. Per impostazione predefinita, questo controllo è una volta all'ora.

Questo controllo significa che per le applicazioni desktop e per dispositivi mobili che usano l'autenticazione moderna, viene rilevata una modifica nella posizione entro un'ora dalla modifica del percorso di rete. Per le applicazioni desktop e per dispositivi mobili che non usano l'autenticazione moderna, questo criterio viene applicato a ogni richiesta di token. La frequenza della richiesta può variare in base all'applicazione. Analogamente, per le applicazioni Web il criterio viene applicato all'accesso iniziale e rimane valido per l'intera durata della sessione dell'applicazione Web interessata. A causa delle differenze a livello di durata delle sessioni delle applicazioni, anche il tempo tra le valutazioni dei criteri può variare. Il criterio viene applicato ogni volta che l'applicazione richiede un nuovo token di accesso.

Per impostazione predefinita, Azure AD rilascia un token su base oraria. Se si esce dalla rete aziendale, entro un'ora i criteri vengono applicati per le applicazioni che usano l'autenticazione moderna.

### <a name="user-ip-address"></a>Indirizzo IP utente

L'indirizzo IP usato nella valutazione dei criteri è l'indirizzo IP pubblico dell'utente. Per i dispositivi su una rete privata, questo indirizzo IP non è l'IP client del dispositivo dell'utente nella Intranet, ma è l'indirizzo usato dalla rete per connettersi alla rete Internet pubblica.

> [!WARNING]
> Se il dispositivo dispone solo di un indirizzo IPv6, la configurazione della condizione di posizione non è supportata.

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>Caricamento e download in blocco delle posizioni specifiche

Quando si creano o si aggiornano le posizioni specifiche, per gli aggiornamenti in blocco è possibile caricare o scaricare un file CSV con gli intervalli di indirizzi IP. Con un'operazione di caricamento si sostituiscono gli intervalli di indirizzi IP nell'elenco con quelli del file. Ogni riga del file contiene un intervallo di indirizzi IP in formato CIDR.

### <a name="cloud-proxies-and-vpns"></a>Proxy cloud e VPN

Quando si usa un proxy ospitato nel cloud o una soluzione VPN, l'indirizzo IP usato da Azure AD durante la valutazione dei criteri è l'indirizzo IP del proxy. L'intestazione X-Forwarded-For (XFF) che contiene l'indirizzo IP pubblico dell'utente non viene usata perché non è certo che provenga da un'origine attendibile, pertanto potrebbe essere un metodo per falsificare un indirizzo IP.

Quando è presente un proxy cloud, è possibile usare criteri che richiedono un dispositivo aggiunto al dominio o l'attestazione della rete aziendale intera di AD FS.

### <a name="api-support-and-powershell"></a>Supporto dell'API e PowerShell

API e PowerShell non sono ancora supportati per le località denominate o per i criteri di accesso condizionale.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come configurare criteri di accesso condizionale, vedere [Richiedere MFA per app specifiche con l'accesso condizionale di Azure Active Directory](app-based-mfa.md).
- Se si è pronti per configurare i criteri di accesso condizionale per l'ambiente in uso, vedere le [procedure consigliate per l'accesso condizionale](best-practices.md) in Azure Active Directory.
