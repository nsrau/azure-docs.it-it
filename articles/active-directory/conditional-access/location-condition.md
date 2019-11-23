---
title: Location condition in Azure Active Directory Conditional Access
description: Informazioni su come usare la condizione della posizione per controllare l'accesso alle app cloud in base al percorso di rete dell'utente.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.workload: identity
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 915675af1e646f2cb77e36c0018ed372ff9496fc
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74380305"
---
# <a name="what-is-the-location-condition-in-azure-active-directory-conditional-access"></a>What is the location condition in Azure Active Directory Conditional Access? 

With [Azure Active Directory (Azure AD) Conditional Access](../active-directory-conditional-access-azure-portal.md), you can control how authorized users can access your cloud apps. The location condition of a Conditional Access policy enables you to tie access controls settings to the network locations of your users.

Questo articolo fornisce le informazioni necessarie per configurare la condizione della posizione.

## <a name="locations"></a>Località

Azure AD enables single sign-on to devices, apps, and services from anywhere on the public internet. Con la condizione della posizione, è possibile controllare l'accesso alle app cloud in base al percorso di rete dell'utente. Casi d'uso comuni per la condizione della posizione:

- Necessità dell'autenticazione a più fattori per gli utenti che accedono a un servizio quando non sono connessi alla rete aziendale.
- Blocco dell'accesso per gli utenti che accedono a un servizio da specifici paesi o aree geografiche.

A location is a label for a network location that either represents a named location or multi-factor authentication Trusted IPs.

## <a name="named-locations"></a>Posizioni specifiche

With named locations, you can create logical groupings of IP address ranges or countries and regions.

You can access your named locations in the **Manage** section of the Conditional Access page.

![Named locations in Conditional Access](./media/location-condition/02.png)

Una posizione specifica ha le caratteristiche seguenti:

![Create a new named location](./media/location-condition/42.png)

- **Nome**: nome visualizzato di una posizione specifica.
- **Intervalli IP**: uno o più intervalli di indirizzi IPv4 in formato CIDR. Specifying an IPv6 address range is not supported.

   > [!NOTE]
   > IPv6 address ranges cannot currently be included in a named location. This means IPv6 ranges cannot be excluded from a Conditional Access policy.

- **Contrassegna come posizione attendibile**: flag che è possibile impostare per una posizione specifica per indicare un percorso attendibile. In genere, i percorsi attendibili sono aree della rete controllate dal reparto IT. In addition to Conditional Access, trusted named locations are also used by Azure Identity Protection and Azure AD security reports to reduce [false positives](../reports-monitoring/concept-risk-events.md#impossible-travel-to-atypical-locations-1).
- **Paesi/aree geografiche**: questa opzione consente di selezionare uno o più paesi o aree geografiche per definire una posizione specifica.
- **Include unknown areas** - Some IP addresses are not mapped to a specific country or region. Questa opzione consente di scegliere se questi indirizzi IP devono essere inclusi nella posizione specifica. Usare questa impostazione quando i criteri che usano la posizione specifica devono essere applicati a posizioni sconosciute.

Il numero di località denominate che è possibile configurare è limitato dalle dimensioni dell'oggetto correlato in Azure AD. You can configure locations based on of the following limitations:

- Una posizione specifica con un massimo di 1200 intervalli IP.
- Un massimo di 90 posizioni specifiche, ognuna con un intervallo IP assegnato.

Conditional Access policy applies to IPv4 and IPv6 traffic. Currently named locations do not allow IPv6 ranges to be configured. This limitation causes the following situations:

- Conditional Access policy cannot be targeted to specific IPv6 ranges
- Conditional Access policy cannot exclude specific IPV6 ranges

If a policy is configured to apply to “Any location”, it will apply to IPv4 and IPv6 traffic. Named locations configured for specified countries and regions only support IPv4 addresses. IPv6 traffic is only included if the option to “include unknown areas” selected.

## <a name="trusted-ips"></a>IP attendibili

È inoltre possibile configurare gli intervalli di indirizzi IP che rappresentano la Intranet locale dell'organizzazione nelle [impostazioni del servizio di autenticazione a più fattori](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx). Questa funzionalità consente di configurare fino a 50 intervalli di indirizzi IP. Gli intervalli di indirizzi IP sono in formato CIDR. For more information, see [Trusted IPs](../authentication/howto-mfa-mfasettings.md#trusted-ips).  

If you have Trusted IPs configured, they show up as **MFA Trusted IPS** in the list of locations for the location condition.

### <a name="skipping-multi-factor-authentication"></a>Ignorare l'autenticazione a più fattori

Nella pagina delle impostazioni del servizio di autenticazione a più fattori, è possibile identificare gli utenti della rete Intranet aziendale selezionando **Ignora l'autenticazione a più fattori per le richieste provenienti da utenti federati nella Intranet**. Questa impostazione indica che l'attestazione della rete aziendale interna, rilasciata da AD FS, deve essere attendibile e usata per identificare l'utente come appartenente alla rete aziendale. For more information, see [Enable the Trusted IPs feature by using Conditional Access](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access).

After checking this option, including the named location **MFA Trusted IPS** will apply to any policies with this option selected.

For mobile and desktop applications, which have long lived session lifetimes, Conditional Access is periodically reevaluated. Il valore predefinito è una volta ogni ora. Quando l'attestazione della rete aziendale interna viene emessa solo al momento dell'autenticazione iniziale, Azure AD potrebbe non disporre di un elenco di intervalli IP attendibili. In questo caso, è più difficile determinare se l'utente sia ancora presente nella rete aziendale:

1. Verificare se l'indirizzo IP dell'utente è in uno degli intervalli di indirizzi IP attendibili.
2. Check whether the first three octets of the user’s IP address match the first three octets of the IP address of the initial authentication. The IP address is compared with the initial authentication when the inside corporate network claim was originally issued and the user location was validated.

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

Conditional Access policies are evaluated when:

- Un utente accede inizialmente a un'app Web, un'applicazione per dispositivi mobili o un'applicazione desktop.
- Un'applicazione desktop o per dispositivo mobili che usa l'autenticazione moderna usa un token di aggiornamento per acquisire un nuovo token di accesso. By default this check is once an hour.

This check means for mobile and desktop applications using modern authentication, a change in location would be detected within an hour of changing the network location. Per le applicazioni desktop e per dispositivi mobili che non usano l'autenticazione moderna, questo criterio viene applicato a ogni richiesta di token. La frequenza della richiesta può variare in base all'applicazione. Analogamente, per le applicazioni Web il criterio viene applicato all'accesso iniziale e rimane valido per l'intera durata della sessione dell'applicazione Web interessata. A causa delle differenze a livello di durata delle sessioni delle applicazioni, anche il tempo tra le valutazioni dei criteri può variare. Il criterio viene applicato ogni volta che l'applicazione richiede un nuovo token di accesso.

Per impostazione predefinita, Azure AD rilascia un token su base oraria. Se si esce dalla rete aziendale, entro un'ora i criteri vengono applicati per le applicazioni che usano l'autenticazione moderna.

### <a name="user-ip-address"></a>Indirizzo IP utente

L'indirizzo IP usato nella valutazione dei criteri è l'indirizzo IP pubblico dell'utente. For devices on a private network, this IP address is not the client IP of the user’s device on the intranet, it is the address used by the network to connect to the public internet.

> [!WARNING]
> If your device has only an IPv6 address, configuring the location condition is not supported.

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>Caricamento e download in blocco delle posizioni specifiche

Quando si creano o si aggiornano le posizioni specifiche, per gli aggiornamenti in blocco è possibile caricare o scaricare un file CSV con gli intervalli di indirizzi IP. Con un'operazione di caricamento si sostituiscono gli intervalli di indirizzi IP nell'elenco con quelli del file. Ogni riga del file contiene un intervallo di indirizzi IP in formato CIDR.

### <a name="cloud-proxies-and-vpns"></a>Proxy cloud e VPN

Quando si usa un proxy ospitato nel cloud o una soluzione VPN, l'indirizzo IP usato da Azure AD durante la valutazione dei criteri è l'indirizzo IP del proxy. L'intestazione X-Forwarded-For (XFF) che contiene l'indirizzo IP pubblico dell'utente non viene usata perché non è certo che provenga da un'origine attendibile, pertanto potrebbe essere un metodo per falsificare un indirizzo IP.

Quando è presente un proxy cloud, è possibile usare criteri che richiedono un dispositivo aggiunto al dominio o l'attestazione della rete aziendale intera di AD FS.

### <a name="api-support-and-powershell"></a>Supporto dell'API e PowerShell

API and PowerShell is not yet supported for named locations, or for Conditional Access policies.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come configurare criteri di accesso condizionale, vedere [Richiedere MFA per app specifiche con l'accesso condizionale di Azure Active Directory](app-based-mfa.md).
- Se si è pronti per configurare i criteri di accesso condizionale per l'ambiente in uso, vedere le [procedure consigliate per l'accesso condizionale](best-practices.md) in Azure Active Directory.
