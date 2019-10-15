---
title: Che cos'è la condizione della posizione in Active Directory accesso condizionale di Azure? | Microsoft Docs
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
ms.openlocfilehash: bd62cda209a8ac95a41fa271ce3a96001a3b4811
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164785"
---
# <a name="what-is-the-location-condition-in-azure-active-directory-conditional-access"></a>Che cos'è la condizione della posizione in Active Directory accesso condizionale di Azure? 

Con [accesso condizionale di Azure Active Directory (Azure AD)](../active-directory-conditional-access-azure-portal.md), è possibile controllare gli utenti come autorizzati possono accedere alle App cloud. La condizione della posizione di un criterio di accesso condizionale consente di associare le impostazioni di controlli di accesso ai percorsi di rete degli utenti.

Questo articolo fornisce le informazioni necessarie per configurare la condizione della posizione.

## <a name="locations"></a>Località

Azure AD Abilita single sign-on ai dispositivi, App e servizi da qualsiasi posizione nella rete internet pubblica. Con la condizione della posizione, è possibile controllare l'accesso alle app cloud in base al percorso di rete dell'utente. Casi d'uso comuni per la condizione della posizione:

- Necessità dell'autenticazione a più fattori per gli utenti che accedono a un servizio quando non sono connessi alla rete aziendale.
- Blocco dell'accesso per gli utenti che accedono a un servizio da specifici paesi o aree geografiche.

Un percorso è un'etichetta per un percorso di rete che rappresenta una posizione specifica o multi-factor authentication, gli indirizzi IP attendibili.

## <a name="named-locations"></a>Posizioni specifiche

Con le località denominate, è possibile creare raggruppamenti logici di intervalli di indirizzi IP o paesi e aree geografiche.

È possibile accedere a specifiche posizioni il **Gestisci** sezione della pagina di accesso condizionale.

![Località denominate nell'accesso condizionale](./media/location-condition/02.png)

Una posizione specifica ha le caratteristiche seguenti:

![Creare un nuovo percorso](./media/location-condition/42.png)

- **Nome**: nome visualizzato di una posizione specifica.
- **Intervalli IP**: uno o più intervalli di indirizzi IPv4 in formato CIDR. Specifica un intervallo di indirizzi IPv6 non è supportata.

   > [!NOTE]
   > IPv6 indirizzo rangess attualmente non è possibile includere in una posizione denominata. Gli intervalli IPv6 questo measn non possono essere esclusa da un criterio di accesso condizionale.

- **Contrassegna come posizione attendibile**: flag che è possibile impostare per una posizione specifica per indicare un percorso attendibile. In genere, i percorsi attendibili sono aree della rete controllate dal reparto IT. Oltre all'accesso condizionale, posizioni attendibili sono usate anche dai report di sicurezza di Azure Identity Protection e Azure AD per ridurre [falsi positivi](../reports-monitoring/concept-risk-events.md#impossible-travel-to-atypical-locations-1).
- **Paesi/aree geografiche**: questa opzione consente di selezionare uno o più paesi o aree geografiche per definire una posizione specifica.
- **Includi aree sconosciute** -alcuni indirizzi IP non sono mappati a un paese specifico. Questa opzione consente di scegliere se questi indirizzi IP devono essere inclusi nella posizione specifica. Usare questa impostazione quando i criteri che usano la posizione specifica devono essere applicati a posizioni sconosciute.

Il numero di località denominate che è possibile configurare è limitato dalle dimensioni dell'oggetto correlato in Azure AD. È possibile configurare i percorsi in base alle limitazioni seguenti:

- Una posizione specifica con un massimo di 1200 intervalli IP.
- Un massimo di 90 posizioni specifiche, ognuna con un intervallo IP assegnato.

Criteri di accesso condizionale si applicano al traffico IPv4 e IPv6. Località denominate attualmente non consentire gli intervalli IPv6 da configurare. Questa limitazione comporta le situazioni seguenti:

- Criteri di accesso condizionale non possono essere assegnati a specifici intervalli IPv6
- Criteri di accesso condizionale non è possibile escludere specifici intervalli IPV6

Se un criterio è configurato per applicare in "Qualsiasi percorso", verrà applicata al traffico IPv4 e IPv6. Località denominate configurate per paesi e aree geografiche specificato supporta solo indirizzi IPv4. Il traffico IPv6 è solo se è selezionata l'opzione per "Includi aree sconosciute" inclusa.

## <a name="trusted-ips"></a>Indirizzi IP attendibili

È inoltre possibile configurare gli intervalli di indirizzi IP che rappresentano la Intranet locale dell'organizzazione nelle [impostazioni del servizio di autenticazione a più fattori](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx). Questa funzionalità consente di configurare fino a 50 intervalli di indirizzi IP. Gli intervalli di indirizzi IP sono in formato CIDR. Per altre informazioni, vedere [gli indirizzi IP attendibili](../authentication/howto-mfa-mfasettings.md#trusted-ips).  

Se si dispone di indirizzi IP attendibili configurati, vengono visualizzati come **indirizzi IP attendibili MFA** nell'elenco di percorsi per la condizione della posizione.

### <a name="skipping-multi-factor-authentication"></a>Ignorare l'autenticazione a più fattori

Nella pagina delle impostazioni del servizio di autenticazione a più fattori, è possibile identificare gli utenti della rete Intranet aziendale selezionando **Ignora l'autenticazione a più fattori per le richieste provenienti da utenti federati nella Intranet**. Questa impostazione indica che l'attestazione della rete aziendale interna, rilasciata da AD FS, deve essere attendibile e usata per identificare l'utente come appartenente alla rete aziendale. Per altre informazioni, vedere [abilitare la funzionalità indirizzi IP attendibili usando l'accesso condizionale](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access).

Dopo aver selezionato questa opzione, inclusa la posizione specifica **indirizzi IP attendibili MFA** verranno applicate a tutti i criteri con questa opzione è selezionata.

Per le applicazioni per dispositivi mobili e desktop, che hanno lunga durata della sessione, l'accesso condizionale viene rivalutato periodicamente. Il valore predefinito è una volta ogni ora. Quando l'attestazione della rete aziendale interna viene emessa solo al momento dell'autenticazione iniziale, Azure AD potrebbe non disporre di un elenco di intervalli IP attendibili. In questo caso, è più difficile determinare se l'utente sia ancora presente nella rete aziendale:

1. Verificare se l'indirizzo IP dell'utente è in uno degli intervalli di indirizzi IP attendibili.
2. Verificare se i primi tre ottetti dell'indirizzo IP dell'utente corrispondono i primi tre ottetti dell'indirizzo IP dell'autenticazione iniziale. L'indirizzo IP viene confrontato con l'iniziale l'autenticazione quando l'attestazione della rete azienda interna è stata emessa originariamente e la posizione dell'utente è stata convalidata.

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

Criteri di accesso condizionale vengono valutati quando:

- Un utente accede inizialmente a un'app Web, un'applicazione per dispositivi mobili o un'applicazione desktop.
- Un'applicazione desktop o per dispositivo mobili che usa l'autenticazione moderna usa un token di aggiornamento per acquisire un nuovo token di accesso. Per impostazione predefinita questo controllo è una sola volta con cadenza oraria.

Indica che questo controllo per dispositivi mobili e applicazioni desktop con l'autenticazione moderna, una modifica nella posizione viene rilevata entro un'ora di modifica del percorso di rete. Per le applicazioni desktop e per dispositivi mobili che non usano l'autenticazione moderna, questo criterio viene applicato a ogni richiesta di token. La frequenza della richiesta può variare in base all'applicazione. Analogamente, per le applicazioni Web il criterio viene applicato all'accesso iniziale e rimane valido per l'intera durata della sessione dell'applicazione Web interessata. A causa delle differenze a livello di durata delle sessioni delle applicazioni, anche il tempo tra le valutazioni dei criteri può variare. Il criterio viene applicato ogni volta che l'applicazione richiede un nuovo token di accesso.

Per impostazione predefinita, Azure AD rilascia un token su base oraria. Se si esce dalla rete aziendale, entro un'ora i criteri vengono applicati per le applicazioni che usano l'autenticazione moderna.

### <a name="user-ip-address"></a>Indirizzo IP utente

L'indirizzo IP usato nella valutazione dei criteri è l'indirizzo IP pubblico dell'utente. Per i dispositivi in una rete privata, questo indirizzo IP non è l'indirizzo IP del client del dispositivo dell'utente sulla intranet, l'indirizzo usato dalla rete per la connessione a internet pubblico.

> [!WARNING]
> Se il dispositivo ha solo un indirizzo IPv6, configurare la condizione di percorso non è supportata.

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>Caricamento e download in blocco delle posizioni specifiche

Quando si creano o si aggiornano le posizioni specifiche, per gli aggiornamenti in blocco è possibile caricare o scaricare un file CSV con gli intervalli di indirizzi IP. Con un'operazione di caricamento si sostituiscono gli intervalli di indirizzi IP nell'elenco con quelli del file. Ogni riga del file contiene un intervallo di indirizzi IP in formato CIDR.

### <a name="cloud-proxies-and-vpns"></a>Proxy cloud e VPN

Quando si usa un proxy ospitato nel cloud o una soluzione VPN, l'indirizzo IP usato da Azure AD durante la valutazione dei criteri è l'indirizzo IP del proxy. L'intestazione X-Forwarded-For (XFF) che contiene l'indirizzo IP pubblico dell'utente non viene usata perché non è certo che provenga da un'origine attendibile, pertanto potrebbe essere un metodo per falsificare un indirizzo IP.

Quando è presente un proxy cloud, è possibile usare criteri che richiedono un dispositivo aggiunto al dominio o l'attestazione della rete aziendale intera di AD FS.

### <a name="api-support-and-powershell"></a>Supporto dell'API e PowerShell

API e PowerShell non è ancora supportata per le posizioni specifiche o per i criteri di accesso condizionale.

## <a name="next-steps"></a>Passaggi successivi

- Se si desidera sapere come configurare un criterio di accesso condizionale, vedere [Richiedi autenticazione a più fattori per App specifiche con Azure Active Directory l'accesso condizionale](app-based-mfa.md).
- Se si è pronti per configurare i criteri di accesso condizionale per l'ambiente, vedere la [procedure consigliate per l'accesso condizionale in Azure Active Directory](best-practices.md).
