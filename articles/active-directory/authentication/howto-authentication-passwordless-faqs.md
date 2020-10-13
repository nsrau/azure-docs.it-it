---
title: Domande frequenti sulla distribuzione di chiavi di sicurezza ibride per FIDO2-Azure Active Directory
description: Informazioni sulle domande frequenti sull'accesso con chiave di sicurezza ibrido FIDO2 senza password con Azure Active Directory (anteprima)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 08/19/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 16e232cedb13dc246bf7a568adfad401c1fe3eb8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89236655"
---
# <a name="deployment-frequently-asked-questions-faqs-for-hybrid-fido2-security-keys-in-azure-ad-preview"></a>Domande frequenti sulla distribuzione per le chiavi di sicurezza ibrido per FIDO2 in Azure AD (anteprima)

Questo articolo illustra le domande frequenti sulla distribuzione per i dispositivi ibridi Azure AD aggiunti e l'accesso senza password alle risorse locali. Con questa funzionalità senza password, è possibile abilitare l'autenticazione Azure AD nei dispositivi Windows 10 per i dispositivi ibridi Azure AD aggiunti usando le chiavi di sicurezza FIDO2. Gli utenti possono accedere a Windows sui propri dispositivi con credenziali moderne come le chiavi di FIDO2 e accedere alle risorse basate su Active Directory Domain Services tradizionali (AD DS) con un'esperienza di Single Sign-On semplice (SSO) alle risorse locali.

Sono supportati gli scenari seguenti per gli utenti in un ambiente ibrido:

* Accedere a dispositivi ibridi Azure AD aggiunti usando le chiavi di sicurezza FIDO2 e ottenere l'accesso SSO alle risorse locali.
* Accedere a Azure AD dispositivi aggiunti usando le chiavi di sicurezza FIDO2 e ottenere l'accesso SSO alle risorse locali.

Per iniziare a usare le chiavi di sicurezza di FIDO2 e l'accesso ibrido alle risorse locali, vedere gli articoli seguenti:

* [Chiavi di sicurezza FIDO2 senza password](howto-authentication-passwordless-security-key.md)
* [Accesso senza password in Windows 10](howto-authentication-passwordless-security-key-windows.md)
* [Accesso senza password in locale](howto-authentication-passwordless-security-key-on-premises.md)

> [!NOTE]
> Le chiavi di sicurezza di FIDO2 sono una funzionalità di anteprima pubblica di Azure Active Directory. Per altre informazioni sulle anteprime, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="security-keys"></a>Chiavi di sicurezza

* [L'organizzazione richiede l'autenticazione a due fattori per accedere alle risorse. Cosa è possibile fare per supportare questo requisito?](#my-organization-requires-multi-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement)
* [Dove è possibile trovare chiavi di sicurezza FIDO2 conformi?](#where-can-i-find-compliant-fido2-security-keys)
* [Cosa fare se si perde la chiave di sicurezza?](#what-if-i-lose-my-security-key)
* [Come vengono protetti i dati nella chiave di sicurezza FIDO2?](#how-is-the-data-protected-on-the-fido2-security-key)
* [Come funziona la registrazione delle chiavi di sicurezza FIDO2?](#how-does-the-registering-of-fido2-security-keys-work)
* [Per gli amministratori è possibile effettuare il provisioning delle chiavi direttamente per gli utenti?](#is-there-a-way-for-admins-to-provision-the-keys-for-the-users-directly)

### <a name="my-organization-requires-multi-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>L'organizzazione richiede la funzionalità di autenticazione a più fattori per accedere alle risorse. Cosa è possibile fare per supportare questo requisito?

Le chiavi di sicurezza di FIDO2 sono costituite da diversi fattori di forma. Contattare il produttore del dispositivo di interesse per discutere il modo in cui i dispositivi possono essere abilitati con un PIN o biometrico come secondo fattore. Per un elenco dei provider supportati, vedere [provider di chiavi di sicurezza FIDO2](concept-authentication-passwordless.md#fido2-security-key-providers).

### <a name="where-can-i-find-compliant-fido2-security-keys"></a>Dove è possibile trovare chiavi di sicurezza FIDO2 conformi?

Per un elenco dei provider supportati, vedere [provider di chiavi di sicurezza FIDO2](concept-authentication-passwordless.md#fido2-security-key-providers).

### <a name="what-if-i-lose-my-security-key"></a>Che cosa succede se si perde la chiave di sicurezza?

È possibile rimuovere le chiavi nella portale di Azure passando alla pagina delle **informazioni di sicurezza** e rimuovendo la chiave di sicurezza FIDO2.

### <a name="how-is-the-data-protected-on-the-fido2-security-key"></a>Come vengono protetti i dati nella chiave di sicurezza FIDO2?

Le chiavi di sicurezza di FIDO2 hanno enclave sicure che proteggono le chiavi private archiviate su di essi. Una chiave di sicurezza FIDO2 dispone anche di proprietà anti-hammering incorporate, come in Windows Hello, in cui non è possibile estrarre la chiave privata.

### <a name="how-does-the-registering-of-fido2-security-keys-work"></a>Come funziona la registrazione delle chiavi di sicurezza FIDO2?

Per altre informazioni su come registrare e usare le chiavi di sicurezza di FIDO2, vedere [abilitare l'accesso con chiave di sicurezza senza password](howto-authentication-passwordless-security-key.md).

### <a name="is-there-a-way-for-admins-to-provision-the-keys-for-the-users-directly"></a>Per gli amministratori è possibile effettuare il provisioning delle chiavi direttamente per gli utenti?

No, non al momento.

## <a name="prerequisites"></a>Prerequisiti

* [Questa funzionalità funziona se non esiste una connettività Internet?](#does-this-feature-work-if-theres-no-internet-connectivity)
* [Quali sono gli endpoint specifici che devono essere aperti per Azure AD?](#what-are-the-specific-end-points-that-are-required-to-be-open-to-azure-ad)
* [Ricerca per categorie identificare il tipo di aggiunta al dominio (Azure AD aggiunto o ibrido Azure AD aggiunto) per il dispositivo Windows 10?](#how-do-i-identify-the-domain-join-type-azure-ad-joined-or-hybrid-azure-ad-joined-for-my-windows-10-device)
* [Qual è il suggerimento sul numero di controller di dominio che devono essere corretti?](#whats-the-recommendation-on-the-number-of-dcs-that-should-be-patched)
* [È possibile distribuire il provider di credenziali FIDO2 in un dispositivo solo locale?](#can-i-deploy-the-fido2-credential-provider-on-an-on-premises-only-device)
* [L'accesso alla chiave di sicurezza di FIDO2 non funziona per l'amministratore del dominio o altri account con privilegi elevati. Perché?](#fido2-security-key-sign-in-isnt-working-for-my-domain-admin-or-other-high-privilege-accounts-why)

### <a name="does-this-feature-work-if-theres-no-internet-connectivity"></a>Questa funzionalità funziona se non esiste una connettività Internet?

La connettività Internet è un prerequisito per l'abilitazione di questa funzionalità. La prima volta che un utente accede usando le chiavi di sicurezza di FIDO2, deve disporre di connettività Internet. Per gli eventi di accesso successivi, l'accesso memorizzato nella cache dovrebbe funzionare e consentire all'utente di eseguire l'autenticazione senza connettività Internet.

Per un'esperienza coerente, assicurarsi che i dispositivi dispongano di accesso a Internet e di visibilità ai controller di dominio.

### <a name="what-are-the-specific-end-points-that-are-required-to-be-open-to-azure-ad"></a>Quali sono gli endpoint specifici che devono essere aperti per Azure AD?

Per la registrazione e l'autenticazione sono necessari gli endpoint seguenti:

* **. microsoftonline.com*
* **. microsoftonline-p.com*
* **. msauth.net*
* **. msauthimages.net*
* **. msecnd.net*
* **. msftauth.net*
* **. msftauthimages.net*
* **. phonefactor.net*
* *enterpriseregistration.windows.net*
* *management.azure.com*
* *policykeyservice.dc.ad.msft.net*
* *secure.aadcdn.microsoftonline-p.com*

Per un elenco completo degli endpoint necessari per usare i prodotti Microsoft online, vedere [URL e intervalli di indirizzi IP per Office 365](/microsoft-365/enterprise/urls-and-ip-address-ranges).

### <a name="how-do-i-identify-the-domain-join-type-azure-ad-joined-or-hybrid-azure-ad-joined-for-my-windows-10-device"></a>Ricerca per categorie identificare il tipo di aggiunta al dominio (Azure AD aggiunto o ibrido Azure AD aggiunto) per il dispositivo Windows 10?

Per verificare se il dispositivo client Windows 10 ha il tipo di aggiunta al dominio corretto, usare il comando seguente:

```console
Dsregcmd/status
```

L'output di esempio seguente mostra che il dispositivo è Azure AD Unito come *AzureADJoined* è impostato su *Sì*:

```output
+---------------------+
| Device State        |
+---------------------+

AzureADJoined: YES
EnterpriseJoined: NO
DomainedJoined: NO
```

L'output di esempio seguente mostra che il dispositivo è ibrido Azure AD Unito come *DomainedJoined* è impostato anche su *Sì*. Viene inoltre visualizzato *DomainName* :

```output
+---------------------+
| Device State        |
+---------------------+

AzureADJoined: YES
EnterpriseJoined: NO
DomainedJoined: YES
DomainName: CONTOSO
```

In un controller di dominio Windows Server 2016 o 2019, verificare che siano applicate le patch seguenti. Se necessario, eseguire Windows Update per installarli:

* Windows Server 2016- [KB4534307](https://support.microsoft.com/help/4534307/windows-10-update-kb4534307)
* Windows Server 2019- [KB4534321](https://support.microsoft.com/help/4534321/windows-10-update-kb4534321)

Da un dispositivo client, eseguire il comando seguente per verificare la connettività a un controller di dominio appropriato con le patch installate:

```console
nltest /dsgetdc:<domain> /keylist /kdc
```

### <a name="whats-the-recommendation-on-the-number-of-dcs-that-should-be-patched"></a>Qual è il suggerimento sul numero di controller di dominio che devono essere corretti?

È consigliabile applicare patch alla maggior parte dei controller di dominio Windows Server 2016 o 2019 con la patch per assicurarsi che possano gestire il carico delle richieste di autenticazione dell'organizzazione.

In un controller di dominio Windows Server 2016 o 2019, verificare che siano applicate le patch seguenti. Se necessario, eseguire Windows Update per installarli:

* Windows Server 2016- [KB4534307](https://support.microsoft.com/help/4534307/windows-10-update-kb4534307)
* Windows Server 2019- [KB4534321](https://support.microsoft.com/help/4534321/windows-10-update-kb4534321)

### <a name="can-i-deploy-the-fido2-credential-provider-on-an-on-premises-only-device"></a>È possibile distribuire il provider di credenziali FIDO2 in un dispositivo solo locale?

No, questa funzionalità non è supportata per il dispositivo solo locale. Il provider di credenziali FIDO2 non viene visualizzato.

### <a name="fido2-security-key-sign-in-isnt-working-for-my-domain-admin-or-other-high-privilege-accounts-why"></a>L'accesso alla chiave di sicurezza di FIDO2 non funziona per l'amministratore del dominio o altri account con privilegi elevati. Perché?

Il criterio di sicurezza predefinito non concede Azure AD autorizzazione per firmare gli account con privilegi elevati in risorse locali.

Per sbloccare gli account, utilizzare **Active Directory utenti e computer** per modificare la proprietà *msDS-NeverRevealGroup* dell' *oggetto computer Azure ad Kerberos (CN = AzureADKerberos, OU = Domain Controllers, \<domain-DN> )*.

## <a name="under-the-hood"></a>dietro le quinte

* [In che modo Azure AD Kerberos è collegato all'ambiente Active Directory Domain Services locale?](#how-is-azure-ad-kerberos-linked-to-my-on-premises-active-directory-domain-services-environment)
* [Dove è possibile visualizzare gli oggetti server Kerberos creati in Active Directory e pubblicati in Azure AD?](#where-can-i-view-these-kerberos-server-objects-that-are-created-in-ad-ds-and-published-in-azure-ad)
* [Perché non è possibile registrare la chiave pubblica in servizi di dominio Active Directory in locale, quindi non esiste alcuna dipendenza da Internet?](#why-cant-we-have-the-public-key-registered-to-on-premises-ad-ds-so-there-is-no-dependency-on-the-internet)
* [Come vengono ruotate le chiavi nell'oggetto server Kerberos?](#how-are-the-keys-rotated-on-the-kerberos-server-object)
* [Perché è necessario Azure AD Connect? Le informazioni vengono scritte in servizi di dominio Active Directory da Azure AD?](#why-do-we-need-azure-ad-connect-does-it-write-any-info-back-to-ad-ds-from-azure-ad)
* [Qual è l'aspetto della richiesta/risposta HTTP durante la richiesta di PRT + TGT parziale?](#what-does-the-http-requestresponse-look-like-when-requesting-prt-partial-tgt)

### <a name="how-is-azure-ad-kerberos-linked-to-my-on-premises-active-directory-domain-services-environment"></a>In che modo Azure AD Kerberos è collegato all'ambiente Active Directory Domain Services locale?

Sono disponibili due parti: l'ambiente di servizi di dominio Active Directory locale e il tenant Azure AD.

**Active Directory Domain Services (AD DS)**

Il Azure AD server Kerberos è rappresentato in un ambiente AD DS locale come oggetto controller di dominio (DC). Questo oggetto controller di dominio è costituito da più oggetti:

* *CN = AzureADKerberos, OU = controller di dominio,\<domain-DN>*
    
    Oggetto *computer* che rappresenta un controller di dominio Read-Only (RODC) in servizi di dominio Active Directory. Nessun computer associato a questo oggetto. Si tratta invece di una rappresentazione logica di un controller di dominio.

* *CN = krbtgt_AzureAD, CN = Users,\<domain-DN>*

    Oggetto *utente* che rappresenta una chiave di crittografia del ticket di concessione ticket Kerberos (TGT) di RODC.

* *CN = 900274c4-b7d2-43c8-90EE-00a9f650e335, CN = AzureAD, CN = System,\<domain-DN>*

    Oggetto *serviceConnectionPoint* che archivia i metadati relativi agli oggetti Server Kerberos Azure ad. Gli strumenti di amministrazione utilizzano questo oggetto per identificare e individuare gli oggetti server Kerberos Azure AD.

**Azure Active Directory**

Il Azure AD server Kerberos è rappresentato in Azure AD come oggetto *KerberosDomain* . Ogni ambiente di servizi di dominio Active Directory locale è rappresentato come un singolo oggetto *KerberosDomain* nel tenant Azure ad.

Ad esempio, è possibile disporre di una foresta di servizi di dominio Active Directory con due domini, ad esempio *contoso.com* e *Fabrikam.com*. Se si consente Azure AD di emettere ticket di concessione ticket Kerberos (TGT) per l'intera foresta, sono presenti due oggetti *KerberosDomain* in Azure ad-one per *contoso.com* e uno per *Fabrikam.com*.

Se si dispone di più foreste di servizi di dominio Active Directory, è necessario disporre di un oggetto *KerberosDomain* per ogni dominio in ogni foresta.

### <a name="where-can-i-view-these-kerberos-server-objects-that-are-created-in-ad-ds-and-published-in-azure-ad"></a>Dove è possibile visualizzare gli oggetti server Kerberos creati in servizi di dominio Active Directory e pubblicati in Azure AD?

Per visualizzare tutti gli oggetti, usare i cmdlet di PowerShell del server Azure AD Kerberos inclusi nella versione più recente di Azure AD Connect.

Per ulteriori informazioni, incluse le istruzioni su come visualizzare gli oggetti, vedere [creare oggetti server Kerberos](howto-authentication-passwordless-security-key-on-premises.md#create-kerberos-server-object).

### <a name="why-cant-we-have-the-public-key-registered-to-on-premises-ad-ds-so-there-is-no-dependency-on-the-internet"></a>Perché non è possibile registrare la chiave pubblica in servizi di dominio Active Directory in locale, quindi non esiste alcuna dipendenza da Internet?

Abbiamo ricevuto commenti sulla complessità del modello di distribuzione per Windows Hello for business, quindi volevo semplificare il modello di distribuzione senza dover usare i certificati e l'infrastruttura a chiave pubblica (FIDO2 non usa certificati).

### <a name="how-are-the-keys-rotated-on-the-kerberos-server-object"></a>Come vengono ruotate le chiavi nell'oggetto server Kerberos?

Analogamente a qualsiasi altro controller di dominio, le chiavi di crittografia del server *krbtgt* Azure ad Kerberos devono essere ruotate a intervalli regolari. È consigliabile seguire la stessa pianificazione usata per ruotare tutte le altre chiavi di *krbtgt* di servizi di dominio Active Directory.

> [!NOTE]
> Sebbene siano disponibili altri strumenti per ruotare le chiavi di *krbtgt* , è necessario [usare i cmdlet di PowerShell per ruotare le chiavi *krbtgt* ](howto-authentication-passwordless-security-key-on-premises.md#rotating-the-azure-ad-kerberos-server-key) del server Azure ad Kerberos. Questo metodo assicura che le chiavi vengano aggiornate sia nell'ambiente di servizi di dominio Active Directory locale sia in Azure AD.

### <a name="why-do-we-need-azure-ad-connect-does-it-write-any-info-back-to-ad-ds-from-azure-ad"></a>Perché è necessario Azure AD Connect? Le informazioni vengono scritte in servizi di dominio Active Directory da Azure AD?

Azure AD Connect non esegue la scrittura di informazioni da Azure AD a servizi di dominio Active Directory. L'utilità include il modulo PowerShell per creare l'oggetto server Kerberos in servizi di dominio Active Directory e pubblicarlo in Azure AD.

### <a name="what-does-the-http-requestresponse-look-like-when-requesting-prt-partial-tgt"></a>Qual è l'aspetto della richiesta/risposta HTTP durante la richiesta di PRT + TGT parziale?

La richiesta HTTP è una richiesta standard PRT (Primary Refresh token). Questa richiesta di PRT include un'attestazione che indica che è necessario un ticket di concessione ticket (TGT) Kerberos.

| Attestazione | Valore | Descrizione                             |
|-------|-------|-----------------------------------------|
| TGT   | true  | Attestazione indica che il client necessita di un TGT. |

Azure AD combina la chiave client crittografata e il buffer dei messaggi nella risposta PRT come proprietà aggiuntive. Il payload viene crittografato usando la chiave di sessione del dispositivo Azure AD.

| Campo              | Type   | Descrizione  |
|--------------------|--------|--------------|
| tgt_client_key     | string | Chiave client con codifica Base64 (segreto). Questa chiave è il segreto client usato per proteggere TGT. In questo scenario senza password, il segreto client viene generato dal server come parte di ogni richiesta TGT e quindi restituito al client nella risposta. |
| tgt_key_type       | INT    | Il tipo di chiave di servizi di dominio Active Directory locale utilizzato sia per la chiave client che per la chiave di sessione Kerberos inclusa nella KERB_MESSAGE_BUFFER. |
| tgt_message_buffer | string | KERB_MESSAGE_BUFFER con codifica Base64. |

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare le chiavi di sicurezza di FIDO2 e l'accesso ibrido alle risorse locali, vedere gli articoli seguenti:

* [Chiavi di sicurezza FIDO2 senza password](howto-authentication-passwordless-security-key.md)
* [Accesso senza password in Windows 10](howto-authentication-passwordless-security-key-windows.md)
* [Accesso senza password in locale](howto-authentication-passwordless-security-key-on-premises.md)
