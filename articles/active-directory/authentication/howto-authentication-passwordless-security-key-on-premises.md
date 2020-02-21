---
title: Accesso alla chiave di sicurezza senza password per le risorse locali (anteprima)-Azure Active Directory
description: Informazioni su come abilitare la chiave di sicurezza senza password accesso alle risorse locali usando Azure Active Directory (anteprima)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e28403d905a25e9e792b3b1f31b79c39cd7728b
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2020
ms.locfileid: "77522093"
---
# <a name="enable-passwordless-security-key-sign-in-to-on-premises-resources-with-azure-active-directory-preview"></a>Abilitare la chiave di sicurezza senza password accesso alle risorse locali con Azure Active Directory (anteprima)

Questo documento è incentrato sull'abilitazione dell'autenticazione senza password per le risorse locali per ambienti con **Azure ad aggiunti** e **ibridi Azure ad** dispositivi Windows 10. Questa funzionalità offre un Single Sign-On semplice (SSO) alle risorse locali usando chiavi di sicurezza compatibili con Microsoft.

|     |
| --- |
| Le chiavi di sicurezza di FIDO2 sono una funzionalità di anteprima pubblica di Azure Active Directory. Per ulteriori informazioni sulle anteprime, vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="sso-to-on-premises-resources-using-fido2-keys"></a>Da SSO a risorse locali con chiavi di FIDO2

Azure Active Directory (AD) può emettere ticket di concessione ticket (TGT) Kerberos per uno o più domini Active Directory. Questa funzionalità consente agli utenti di accedere a Windows con credenziali moderne come le chiavi di sicurezza FIDO2 e di accedere alle risorse tradizionali basate su Active Directory. I ticket e l'autorizzazione del servizio Kerberos continuano a essere controllati dai controller di dominio Active Directory locali.

Nel Active Directory locale viene creato un Azure AD oggetto server Kerberos, che viene quindi pubblicato in modo sicuro in Azure Active Directory. L'oggetto non è associato ad alcun server fisico. Si tratta semplicemente di una risorsa che può essere usata da Azure Active Directory per generare TGT Kerberos per la Dominio di Active Directory.

![Ricezione di un ticket di concessione ticket (TGT) da Azure AD e servizi di dominio Active Directory](./media/howto-authentication-passwordless-on-premises/fido2-ticket-granting-ticket-exchange-process.png)

1. L'utente accede al dispositivo Windows 10 con una chiave di sicurezza FIDO2 ed esegue l'autenticazione per Azure AD.
1. Azure AD controlla la directory di una chiave del server Kerberos corrispondente al dominio AD locale dell'utente.
   1. Azure AD genera un TGT Kerberos per il dominio AD locale dell'utente. Il TGT include solo il SID dell'utente. In TGT non sono inclusi dati di autorizzazione.
1. Il TGT viene restituito al client insieme al relativo Azure AD PRT (Primary Refresh token).
1. Il computer client contatta un controller di dominio di AD locale e commercia i TGT parziali per un TGT completamente formato.
1. Il computer client dispone ora di un Azure AD PRT e di un completo Active Directory TGT e può accedere alle risorse cloud e locali.

## <a name="requirements"></a>Requisiti

Prima di completare la procedura descritta in questo articolo, le organizzazioni devono completare i passaggi per [abilitare la firma della chiave di sicurezza senza password nei dispositivi Windows 10 (anteprima)](howto-authentication-passwordless-security-key.md) .

Le organizzazioni devono inoltre soddisfare i requisiti software seguenti.

- I dispositivi devono eseguire Windows 10 Insider Build 18945 o versione successiva.
- È necessario avere la versione 1.4.32.0 o successiva di [Azure ad Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect).
- Nei controller di dominio di Windows Server devono essere installate le patch seguenti:
    - Per Windows Server 2016- https://support.microsoft.com/help/4534307/windows-10-update-kb4534307
    - Per Windows Server 2019- https://support.microsoft.com/help/4534321/windows-10-update-kb4534321

### <a name="supported-scenarios"></a>Scenari supportati

Lo scenario supporta Single Sign-On (SSO) in entrambi gli scenari seguenti:

- Per le risorse cloud come Office 365 e altre applicazioni abilitate per SAML.
- Per le risorse locali e l'autenticazione integrata di Windows nei siti Web. Le risorse possono includere siti Web e siti di SharePoint che richiedono l'autenticazione IIS e/o risorse che usano l'autenticazione NTLM.

### <a name="unsupported-scenarios"></a>Scenari non supportati

Gli scenari seguenti non sono supportati:

- Distribuzione di Windows Server Active Directory Domain Services (AD DS) aggiunta a un dominio (solo dispositivi locali).
- Scenari RDP, VDI e Citrix con una chiave di sicurezza.
- S/MIME con una chiave di sicurezza.
- "Esegui come" utilizzando una chiave di sicurezza.
- Accedere a un server usando la chiave di sicurezza.

## <a name="create-kerberos-server-object"></a>Crea oggetto server Kerberos

Gli amministratori usano gli strumenti di PowerShell dal server Azure AD Connect per creare un oggetto server Azure AD Kerberos nella propria directory locale. Eseguire i passaggi seguenti in ogni dominio e insieme di strutture dell'organizzazione che contengono Azure AD utenti:

1. Eseguire l'aggiornamento alla versione più recente di Azure AD Connect. Le istruzioni presuppongono che sia già stato configurato Azure AD Connect per supportare l'ambiente ibrido.
1. Nel server Azure AD Connect aprire un prompt di PowerShell con privilegi elevati e passare a `C:\Program Files\Microsoft Azure Active Directory Connect\AzureADKerberos\`
1. Eseguire i comandi di PowerShell seguenti per creare una nuova Azure AD oggetto server Kerberos sia nel dominio Active Directory locale che in Azure Active Directory tenant.

> [!NOTE]
> Sostituire `contoso.corp.com` nell'esempio seguente con il nome di dominio Active Directory locale.

```powerShell
Import-Module ".\AzureAdKerberos.psd1"

# Specify the on-premises Active Directory domain. A new Azure AD
# Kerberos Server object will be created in this Active Directory domain.
$domain = "contoso.corp.com"

# Enter an Azure Active Directory global administrator username and password.
$cloudCred = Get-Credential

# Enter a domain administrator username and password.
$domainCred = Get-Credential

# Create the new Azure AD Kerberos Server object in Active Directory
# and then publish it to Azure Active Directory.
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="viewing-and-verifying-the-azure-ad-kerberos-server"></a>Visualizzazione e verifica del Azure AD server Kerberos

È possibile visualizzare e verificare il Azure AD server Kerberos appena creato usando il comando seguente:

```powerShell
Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

Questo comando restituisce le proprietà del server Azure AD Kerberos. È possibile esaminare le proprietà per verificare che tutti gli elementi siano in ordine corretto.

| Proprietà | Descrizione |
| --- | --- |
| ID | ID univoco dell'oggetto DC di servizi di dominio Active Directory. Questo ID viene talvolta definito "slot" o "ID ramo". |
| DomainDnsName | Nome di dominio DNS del Dominio di Active Directory. |
| ComputerAccount | Oggetto account computer del Azure AD oggetto server Kerberos (il controller di dominio). |
| UserAccount | Oggetto account utente disabilitato che include la chiave di crittografia del server TGT Azure AD Kerberos. Il DN di questo account è `CN=krbtgt_AzureAD,CN=Users,<Domain-DN>` |
| Versione della versione | La versione chiave della chiave di crittografia TGT del server Kerberos Azure AD. La versione viene assegnata al momento della creazione della chiave. La versione viene quindi incrementata ogni volta che la chiave viene ruotata. Gli incrementi sono basati sui metadati di replica e probabilmente maggiori di uno. Ad esempio, la versione iniziale della *versione* può essere *192272*. La prima volta che la chiave viene ruotata, la versione può passare a *212621*. L'aspetto importante da verificare è che la *versione della versione* per l'oggetto locale e la *CloudKeyVersion* per l'oggetto Cloud siano le stesse. |
| KeyUpdatedOn | Data e ora in cui è stata aggiornata o creata la chiave di crittografia TGT del server Kerberos Azure AD. |
| KeyUpdatedFrom | Il controller di dominio in cui è stato eseguito l'ultimo aggiornamento della chiave di crittografia del server Azure AD Kerberos TGT. |
| CloudId | ID dall'oggetto Azure AD. Deve corrispondere all'ID precedente. |
| CloudDomainDnsName | *DomainDnsName* dall'oggetto Azure ad. Deve corrispondere al *DomainDnsName* sopra riportato. |
| CloudKeyVersion | La *versione* dell'oggetto Azure ad. Deve corrispondere alla *versione* precedente. |
| CloudKeyUpdatedOn | *KeyUpdatedOn* dall'oggetto Azure ad. Deve corrispondere al *KeyUpdatedOn* sopra riportato. |

### <a name="rotating-the-azure-ad-kerberos-server-key"></a>Rotazione della chiave del server Kerberos Azure AD

Le chiavi di KRBTGT della crittografia del server Kerberos Azure AD devono essere ruotate a intervalli regolari. È consigliabile seguire la stessa pianificazione usata per ruotare tutte le altre chiavi di krbtgt del controller di Dominio di Active Directory.

> [!WARNING]
> Esistono altri strumenti che potrebbero ruotare le chiavi di krbtgt. Tuttavia, è necessario usare gli strumenti indicati in questo documento per ruotare le chiavi di krbtgt del server Azure AD Kerberos. Ciò garantisce che le chiavi vengano aggiornate sia in AD locale che in Azure AD.

```powerShell
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred -RotateServerKey
```

### <a name="removing-the-azure-ad-kerberos-server"></a>Rimozione del server Azure AD Kerberos

Se si vuole ripristinare lo scenario e rimuovere il server Azure AD Kerberos da Active Directory locale e Azure Active Directory, eseguire il comando seguente:

```powerShell
Remove-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="multi-forest-and-multi-domain-scenarios"></a>Scenari con più foreste e più domini

L'oggetto server Azure AD Kerberos è rappresentato in Azure AD come oggetto *KerberosDomain* . Ogni dominio di Active Directory locale è rappresentato come un singolo oggetto *KerberosDomain* in Azure ad.

Ad esempio, l'organizzazione dispone di una foresta Active Directory con due domini, `contoso.com` e `fabrikam.com`. Se si sceglie di consentire Azure AD di emettere TGT Kerberos per l'intera foresta, in Azure AD sono presenti due oggetti *KerberosDomain* . Un oggetto *KerberosDomain* per `contoso.com`e uno per `fabrikam.com`. Se si dispone di più foreste Active Directory, esiste un oggetto *KerberosDomain* per ogni dominio in ogni foresta.

È necessario eseguire i passaggi per [creare un oggetto server Kerberos](#create-kerberos-server-object) in ogni dominio e foresta dell'organizzazione che contiene Azure ad utenti.

## <a name="known-behavior"></a>Comportamento noto

L'accesso con FIDO viene bloccato se la password è scaduta. Si prevede che l'utente possa reimpostare la password prima di poter accedere usando FIDO.

## <a name="troubleshooting-and-feedback"></a>Risoluzione dei problemi e commenti e suggerimenti

Per condividere commenti e suggerimenti o riscontrare problemi durante l'anteprima di questa funzionalità, condividere tramite l'app Windows Feedback hub usando la procedura seguente:

1. Avviare **feedback Hub** e assicurarsi di aver eseguito l'accesso.
1. Inviare commenti e suggerimenti nella categorizzazione seguente:
   - Categoria: sicurezza e privacy
   - Sottocategoria: FIDO
1. Per acquisire i log, utilizzare l'opzione per **ricreare il problema**

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="does-this-work-in-my-on-premises-environment"></a>Questa operazione viene eseguita nell'ambiente locale?

Questa funzionalità non funziona per un ambiente di Active Directory Domain Services locale puro (AD DS).

### <a name="my-organization-requires-two-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>L'organizzazione richiede l'autenticazione a due fattori per accedere alle risorse. Cosa è possibile fare per supportare questo requisito?

Le chiavi di sicurezza sono costituite da diversi fattori di forma. Contattare il produttore del dispositivo di interesse per discutere il modo in cui i dispositivi possono essere abilitati con un PIN o biometrico come secondo fattore.

### <a name="can-admins-set-up-security-keys"></a>Gli amministratori possono configurare le chiavi di sicurezza?

Questa funzionalità è stata utilizzata per la disponibilità generale (GA).

### <a name="where-can-i-go-to-find-compliant-security-keys"></a>Dove è possibile trovare le chiavi di sicurezza conformi?

[Chiavi di sicurezza FIDO2](concept-authentication-passwordless.md#fido2-security-keys)

### <a name="what-do-i-do-if-i-lose-my-security-key"></a>Cosa fare se si perde la chiave di sicurezza?

È possibile rimuovere le chiavi dal portale di Azure passando alla pagina delle **informazioni di sicurezza** e rimuovendo la chiave di sicurezza.

### <a name="im-not-able-to-use-fido-immediately-after-i-create-a-hybrid-azure-ad-joined-machine"></a>Non è possibile usare FIDO immediatamente dopo aver creato un computer ibrido Azure AD aggiunto

Se si installa un computer ibrido Azure AD aggiunto, dopo il processo di aggiunta e riavvio del dominio è necessario accedere con una password e attendere la sincronizzazione dei criteri prima di poter usare FIDO per l'accesso.

- Verificare lo stato corrente digitando `dsregcmd /status` in una finestra di comando e verificare che sia *AzureAdJoined* che *DomainJoined* mostrino *Sì*.
- Questo ritardo è un limite noto per i dispositivi aggiunti a un dominio e non è specifico di FIDO.

### <a name="im-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-fido-and-get-a-credential-prompt"></a>Non è possibile ottenere l'accesso SSO alla risorsa di rete NTLM dopo aver eseguito l'accesso con FIDO e aver ricevuto una richiesta di credenziali

Assicurarsi che venga applicata una patch a un numero sufficiente di controller di dominio per rispondere al tempo per soddisfare la richiesta di risorse. Per verificare se è possibile visualizzare un controller di dominio che esegue la funzionalità, esaminare l'output del `nltest /dsgetdc:contoso /keylist /kdc`.

## <a name="next-steps"></a>Passaggi successivi

[Scopri di più su password](concept-authentication-passwordless.md)