---
title: Condizioni della posizione nell'accesso condizionale di Azure Active Directory | Microsoft Docs
description: Informazioni su come usare la condizione della posizione per controllare l'accesso alle app cloud in base al percorso di rete dell'utente.
services: active-directory
keywords: accesso condizionale alle app, accesso condizionale con Azure AD, accesso sicuro alle risorse aziendali, criteri di accesso condizionale
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/01/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: c9712cf0cf20bbcfc089eb18896370f9e02eb571
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2018
---
# <a name="location-conditions-in-azure-active-directory-conditional-access"></a>Condizioni della posizione nell'accesso condizionale di Azure Active Directory 

Con l'[accesso condizionale di Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md) è possibile controllare il modo in cui gli utenti autorizzati possono accedere alle app cloud. La condizione della posizione dei criteri dei accesso condizionale consente di associare le impostazioni dei controlli dell'accesso ai percorsi di rete degli utenti.

Questo articolo fornisce le informazioni necessarie per configurare la condizione della posizione. 

## <a name="locations"></a>Località

Azure AD consente l'accesso Single Sign-On ai dispositivi, alle app e ai servizi da qualsiasi posizione della rete pubblica Internet. Con la condizione della posizione, è possibile controllare l'accesso alle app cloud in base al percorso di rete dell'utente. Casi d'uso comuni per la condizione della posizione:

- Necessità dell'autenticazione a più fattori per gli utenti che accedono a un servizio quando non sono connessi alla rete aziendale  

- Blocco dell'accesso per gli utenti che accedono a un servizio da specifici paesi o aree geografiche. 

Una posizione è un'etichetta per un percorso di rete che rappresenta una posizione specifica o gli indirizzi IP attendibili per l'autenticazione a più fattori.


## <a name="named-locations"></a>Posizioni specifiche 

Con le posizioni specifiche è possibile creare raggruppamenti logici di intervalli di indirizzi IP, paesi e aree geografiche. 

 Una posizione specifica ha le caratteristiche seguenti:

![Località](./media/active-directory-conditional-access-locations/42.png)

- **Nome**: nome visualizzato di una posizione specifica.

- **Intervalli IP**: uno o più intervalli di indirizzi IP in formato CIDR.

- **Contrassegna come posizione attendibile**: flag che è possibile impostare per una posizione specifica per indicare un percorso attendibile. In genere, i percorsi attendibili sono aree della rete controllate dal reparto IT. Oltre che per l'accesso condizionale, le posizioni specifiche attendibili vengono usate anche da Azure AD Identity Protection e dai report di sicurezza di Azure AD per ridurre il numero di [falsi positivi](active-directory-reporting-risk-events.md#impossible-travel-to-atypical-locations-1).

- **Paesi/aree geografiche**: questa opzione consente di selezionare uno o più paesi o aree geografiche per definire una posizione specifica. 

- **Includi aree sconosciute**: alcuni indirizzi IP non sono associati a un paese specifico. Questa opzione consente di scegliere se questi indirizzi IP devono essere inclusi nella posizione specifica. È possibile controllare quando applicare i criteri che usano la posizione specifica alle posizioni sconosciute.

Il numero di località denominate che è possibile configurare è limitato dalle dimensioni dell'oggetto correlato in Azure AD. È possibile configurare:

- Una posizione specifica con un massimo di 1200 intervalli IP.

- Un massimo di 90 posizioni specifiche, ognuna con un intervallo IP assegnato.




## <a name="trusted-ips"></a>Indirizzi IP attendibili

È inoltre possibile configurare gli intervalli di indirizzi IP che rappresentano la Intranet locale dell'organizzazione nelle [impostazioni del servizio di autenticazione a più fattori](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx). Questa funzionalità consente di configurare fino a 50 intervalli di indirizzi IP. Gli intervalli di indirizzi IP sono in formato CIDR. Per altre informazioni, vedere [IP attendibili](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips).  

Se si dispone di indirizzi IP attendibili configurati, vengono visualizzati come **Indirizzi IP attendibili MFA** nell'elenco dei percorsi per la condizione della posizione.   

### <a name="skipping-multi-factor-authentication"></a>Ignorare l'autenticazione a più fattori

Nella pagina delle impostazioni del servizio di autenticazione a più fattori, è possibile identificare gli utenti della rete Intranet aziendale selezionando **Ignora l'autenticazione a più fattori per le richieste provenienti da utenti federati nella Intranet**. Questa impostazione indica che l'attestazione della rete aziendale interna, rilasciata da AD FS, deve essere attendibile e usata per identificare l'utente come appartenente alla rete aziendale. Per altre informazioni, vedere [Abilitare la funzionalità degli indirizzi IP attendibili tramite l'accesso condizionale](../multi-factor-authentication/multi-factor-authentication-whats-next.md#enable-the-trusted-ips-feature-by-using-conditional-access).

Dopo aver selezionato questa opzione, inclusa la posizione specifica, gli **Indirizzi IP attendibili MFA** verranno applicati a tutti i criteri con l'opzione selezionata.

Per le applicazioni desktop e per dispositivi mobili, con cicli di vita di sessione prolungati, l'accesso condizionale viene rivalutato periodicamente. Il valore predefinito è una volta ogni ora. Quando l'attestazione della rete aziendale interna è emessa solo al momento dell'autenticazione iniziale, Azure AD potrebbe non disporre di un elenco di intervalli IP attendibili. In questo caso, è più difficile determinare se l'utente sia ancora presente nella rete aziendale:

1. Verificare se l'indirizzo IP dell'utente è in uno degli intervalli di indirizzi IP attendibili.

2. Controllare se i primi tre ottetti dell'indirizzo IP dell'utente corrispondono ai primi 3 ottetti dell'indirizzo IP dell'autenticazione iniziale. L'indirizzo IP viene confrontato con l'autenticazione iniziale perché questa indica quando l'attestazione della rete aziendale interna è stata emessa originariamente e la posizione dell'utente è stata convalidata.

Se entrambi i passaggi danno esito negativo, l'utente non viene più considerato come su un indirizzo IP attendibile.



## <a name="location-condition-configuration"></a>Configurazione della condizione della posizione

Quando si configura la condizione per la posizione, è possibile distinguere tra:

- Qualsiasi località 
- Tutte le località attendibili
- Le località selezionate

![Località](./media/active-directory-conditional-access-locations/01.png)

### <a name="any-location"></a>Qualsiasi località

Per impostazione predefinita, la selezione di **Tutte le località** fa in modo che i criteri vengano applicati a tutti gli indirizzi IP, cioè qualsiasi indirizzo su Internet. Questa impostazione non è limitata agli indirizzi IP configurati come posizione specifica. Quando si seleziona **Tutte le località**, è comunque possibile escludere percorsi specifici dai criteri. Ad esempio, è possibile applicare dei criteri a tutte le posizioni tranne che ai percorsi attendibili per impostare l'ambito per tutte le posizioni ad eccezione della rete aziendale.

### <a name="all-trusted-locations"></a>Tutte le località attendibili

Questa opzione si applica a:

- Tutte le posizioni che sono state contrassegnate come attendibili
- Gli **indirizzi IP attendibili MFA** (se configurati)


### <a name="selected-locations"></a>Le località selezionate

Con questa opzione è possibile selezionare una o più posizioni specifiche. Per applicare criteri con questa impostazione, un utente deve connettersi da una delle posizioni selezionate. Quando si fa clic su **Seleziona** si apre il controllo di selezione delle reti denominate che mostra l'elenco delle reti denominate. L'elenco indica anche se il percorso di rete è stato contrassegnato come attendibile. La posizione specifica denominata **Indirizzi IP attendibili MFA** viene usata per includere le impostazioni IP che possono essere configurate nella pagina di impostazione del servizio di autenticazione a più fattori.

## <a name="what-you-should-know"></a>Informazioni utili

### <a name="when-is-a-location-evaluated"></a>Quando viene valutata una posizione?

I criteri di accesso condizionale vengono valutati quando: 

- Un utente accede inizialmente a un'app Web, un'applicazione per dispositivi mobili o un'applicazione desktop. 

- Un'applicazione desktop o per dispositivo mobili che usa l'autenticazione moderna usa un token di aggiornamento per acquisire un nuovo token di accesso. Il valore predefinito è una volta ogni ora. 

Per le applicazioni per dispositivi mobili e le applicazioni desktop che usano l'autenticazione moderna, ciò significa che una modifica del percorso viene rilevata entro un'ora dalla modifica del percorso di rete. Per le applicazioni desktop e per dispositivi mobili che non usano l'autenticazione moderna, questo criterio viene applicato a ogni richiesta di token. La frequenza della richiesta può variare in base all'applicazione. Analogamente, per le applicazioni Web il criterio viene applicato all'accesso iniziale e rimane valido per l'intera durata della sessione dell'applicazione Web interessata. A causa delle differenze a livello di durata delle sessioni delle applicazioni, anche il tempo tra le valutazioni dei criteri può variare. Il criterio viene applicato ogni volta che l'applicazione richiede un nuovo token di accesso.


Per impostazione predefinita, Azure AD rilascia un token su base oraria. Se si esce dalla rete aziendale, entro un'ora i criteri vengono applicati per le applicazioni che usano l'autenticazione moderna.


### <a name="user-ip-address"></a>Indirizzo IP utente

L'indirizzo IP usato nella valutazione dei criteri è l'indirizzo IP pubblico dell'utente. Per i dispositivi su una rete privata, non si tratta dell'indirizzo IP del client del dispositivo dell'utente sulla Intranet, ma dell'indirizzo usato dalla rete per connettersi a Internet. 

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>Caricamento e download in blocco delle posizioni specifiche

Quando si creano o si aggiornano le posizioni specifiche, per gli aggiornamenti in blocco è possibile caricare o scaricare un file CSV con gli intervalli di indirizzi IP. Con un'operazione di caricamento si sostituiscono gli intervalli di indirizzi IP nell'elenco con quelli del file. Ogni riga del file contiene un intervallo di indirizzi IP in formato CIDR. 


### <a name="cloud-proxies-and-vpns"></a>Proxy cloud e VPN 

Quando si usa un proxy ospitato nel cloud o una soluzione VPN, l'indirizzo IP usato da Azure AD durante la valutazione dei criteri è l'indirizzo IP del proxy. L'intestazione X-Forwarded-For (XFF) che contiene l'indirizzo IP pubblico degli utenti non viene usato perché non è certo che provenga da una fonte attendibile, pertanto potrebbe essere un metodo per falsificare un indirizzo IP. 

Quando è presente un proxy cloud, è possibile usare criteri che richiedono un dispositivo aggiunto al dominio o l'attestazione della rete aziendale intera di AD FS.



### <a name="api-support-and-powershell"></a>Supporto dell'API e PowerShell 

L'API e PowerShell non sono ancora supportati per le posizioni specifiche o per i criteri di accesso condizionale.





## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come configurare un criterio di accesso condizionale, vedere [Get started with conditional access in Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md) (Introduzione all'accesso condizionale in Azure Active Directory).

- Se si è pronti per configurare i criteri di accesso condizionale per l'ambiente in uso, vedere il [Procedure consigliate per l'accesso condizionale in Azure Active Directory](active-directory-conditional-access-best-practices.md). 
