---
title: Accesso alla chiave di sicurezza senza password per le risorse locali (anteprima) - Azure Active Directory
description: Informazioni su come abilitare l'accesso alla chiave di sicurezza senza password per le risorse locali usando Azure Active Directory (anteprima)Learn how to enable passwordless security key sign-in to on-premises resources using Azure Active Directory (preview)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/09/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 181e8192170cd7394d6817edd655f4e8257b48a4
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654033"
---
# <a name="enable-passwordless-security-key-sign-in-to-on-premises-resources-with-azure-active-directory-preview"></a>Abilitare l'accesso alla chiave di sicurezza senza password per le risorse locali con Azure Active Directory (anteprima)Enable passwordless security key sign-in to on-premises resources with Azure Active Directory (preview)

Questo documento è incentrato sull'abilitazione dell'autenticazione senza password alle risorse locali per gli ambienti con dispositivi Windows 10 **aggiunti ad Azure AD** e **ibridi** di Azure AD. Questa funzionalità offre l'accesso Single Sign-On (SSO) trasparente alle risorse locali utilizzando chiavi di sicurezza compatibili con Microsoft.This functionality provides seamless single sign-on (SSO) to on-premises resources using Microsoft-compatible security keys.

|     |
| --- |
| FIDO2 security keys are a public preview feature of Azure Active Directory. Per altre informazioni sulle anteprime, vedere Condizioni per [l'utilizzo supplementari per le anteprime di Microsoft AzureFor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) more information about previews, see Supplemental Terms of Use for Microsoft Azure Previews|
|     |

## <a name="sso-to-on-premises-resources-using-fido2-keys"></a>SSO alle risorse locali usando le chiavi FIDO2

Azure Active Directory (AD) può rilasciare ticket di concessione ticket Kerberos (TGR) per uno o più domini di Active Directory. Questa funzionalità consente agli utenti di accedere a Windows con credenziali moderne come le chiavi di protezione FIDO2 e accedere alle risorse tradizionali basate su Active Directory. I ticket di servizio Kerberos e l'autorizzazione continuano a essere controllati dai controller di dominio Active Directory locali.

Un oggetto server Kerberos di Azure AD viene creato in Active Directory locale e quindi pubblicato in modo sicuro in Azure Active Directory.An Azure AD Kerberos Server object is created in your on-premises Active Directory and then securely published to Azure Active Directory. L'oggetto non è associato ad alcun server fisico. Si tratta semplicemente di una risorsa che può essere usata da Azure Active Directory per generare TGR Kerberos per il dominio di Active Directory.

![Ottenere un ticket di concessione ticket (TGT) da Azure AD e Servizi di dominio Active DirectoryGetting a ticket Granting Ticket (TGT) from Azure AD and AD DS](./media/howto-authentication-passwordless-on-premises/fido2-ticket-granting-ticket-exchange-process.png)

1. L'utente accede al proprio dispositivo Windows 10 con una chiave di sicurezza FIDO2 ed esegue l'autenticazione in Azure AD.
1. Azure AD verifica la presenza di una chiave server Kerberos corrispondente al dominio AD locale dell'utente.
   1. Azure AD genera un TGT Kerberos per il dominio AD locale dell'utente. Il TGT include solo il SID dell'utente. Nessun dato di autorizzazione è incluso nel TGT.
1. Il TGT viene restituito al client insieme al relativo token di aggiornamento primario (PRT) di Azure AD.
1. Il computer client contatta un controller di dominio AD locale e scambia il TGT parziale per un TGT completamente formato.
1. Il computer client dispone ora di un'azure AD PRT e di un TGT completo di Active Directory e può accedere alle risorse cloud e locali.

## <a name="requirements"></a>Requisiti

Le organizzazioni devono completare la procedura per abilitare la chiave di sicurezza senza password per i [dispositivi Windows 10 (anteprima)](howto-authentication-passwordless-security-key.md) prima di completare i passaggi descritti in questo articolo.

Le organizzazioni devono inoltre soddisfare i seguenti requisiti software.

- I dispositivi devono eseguire Windows 10 Insider Build 18945 o versione più recente.
- È necessario disporre della versione 1.4.32.0 o successiva di [Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect).
  - Per altre informazioni sulle opzioni di autenticazione ibrida di Azure AD disponibili, vedere Scegliere il metodo di [autenticazione corretto per la soluzione](../../security/fundamentals/choose-ad-authn.md) di identità ibrida di Azure Active Directory e Selezionare il tipo di installazione da usare per Azure AD Connect.For more information on the available Azure AD hybrid authentication options, see Choose the right authentication method for your Azure Active Directory hybrid identity solution and Select which installation type to use for Azure AD [Connect.](../hybrid/how-to-connect-install-select-installation.md)
- Nei controller di dominio di Windows Server devono essere installate le seguenti patch:
    - Per Windows Server 2016 -https://support.microsoft.com/help/4534307/windows-10-update-kb4534307
    - Per Windows Server 2019 -https://support.microsoft.com/help/4534321/windows-10-update-kb4534321

### <a name="supported-scenarios"></a>Scenari supportati

Lo scenario supporta Single Sign-On (SSO) in entrambi gli scenari seguenti:

- Per le risorse cloud come Office 365 e altre applicazioni abilitate SAML.
- Per le risorse locali e l'autenticazione integrata di Windows nei siti Web. Le risorse possono includere siti Web e siti di SharePoint che richiedono l'autenticazione IIS e/o risorse che utilizzano l'autenticazione NTLM.

### <a name="unsupported-scenarios"></a>Scenari non supportati

Gli scenari seguenti non sono supportati:The following scenarios aren't supported:

- Distribuzione di Servizi di dominio Active Directory di Windows Server aggiunto al dominio (solo dispositivi locali).
- RDP, VDI e Citrix utilizzando una chiave di sicurezza.
- S/MIME utilizzando una chiave di protezione.
- "Esegui come" utilizzando una chiave di sicurezza.
- Accedere a un server utilizzando la chiave di protezione.

## <a name="create-kerberos-server-object"></a>Creare un oggetto server Kerberos

Gli amministratori usano gli strumenti di PowerShell dal server Azure AD Connect per creare un oggetto server Kerberos di Azure AD nella directory locale. Eseguire i passaggi seguenti in ogni dominio e foresta dell'organizzazione che contengono utenti di Azure AD:Run the following steps in each domain and forest in your organization that contain Azure AD users:

1. Eseguire l'aggiornamento alla versione più recente di Azure AD Connect.Upgrade to the latest version of Azure AD Connect. Le istruzioni presuppongono che Azure AD Connect sia già stato configurato per supportare l'ambiente ibrido.
1. Nel server Azure AD Connect aprire un prompt di PowerShell con privilegi elevati e passare a`C:\Program Files\Microsoft Azure Active Directory Connect\AzureADKerberos\`
1. Eseguire i comandi di PowerShell seguenti per creare un nuovo oggetto server Kerberos di Azure AD sia nel dominio di Active Directory locale che nel tenant di Azure Active Directory.

> [!NOTE]
> Sostituire `contoso.corp.com` nell'esempio seguente con il nome di dominio di Active Directory locale.

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

### <a name="viewing-and-verifying-the-azure-ad-kerberos-server"></a>Visualizzazione e verifica del server Kerberos di Azure AD

È possibile visualizzare e verificare il server Kerberos di Azure AD appena creato usando il comando seguente:You can view and verify the newly created Azure AD Kerberos Server using the following command:

```powerShell
Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

Questo comando restituisce le proprietà del server Kerberos di Azure AD. È possibile rivedere le proprietà per verificare che tutto sia in ordine.

| Proprietà | Descrizione |
| --- | --- |
| ID | ID univoco dell'oggetto controller di dominio Active Directory. Questo ID viene talvolta definito "slot" o "ID ramo". |
| DomainDnsName | Il nome di dominio DNS del dominio di Active Directory. |
| ComputerAccount | L'oggetto account computer dell'oggetto Azure AD Kerberos Server (il controller di dominio). |
| UserAccount | Oggetto account utente disabilitato che contiene la chiave di crittografia TGT del server Kerberos di Azure AD. Il DN di questo account è`CN=krbtgt_AzureAD,CN=Users,<Domain-DN>` |
| KeyVersion | La versione con chiave della chiave di crittografia TGT del server Kerberos di Azure AD. La versione viene assegnata al momento della creazione della chiave. La versione viene quindi incrementata ogni volta che la chiave viene ruotata. Gli incrementi sono basati sui metadati della replica e probabilmente maggiori di uno. Ad esempio, il *KeyVersion* iniziale potrebbe essere *192272*. La prima volta che la chiave viene ruotata, la versione potrebbe passare a *212621*. La cosa importante da verificare è che *il KeyVersion* per l'oggetto locale e *CloudKeyVersion* per l'oggetto cloud sono gli stessi. |
| KeyUpdatedOn | Data e ora in cui la chiave di crittografia TGT del server Kerberos di Azure AD è stata aggiornata o creata. |
| KeyUpdatedFrom | Controller di dominio in cui è stata aggiornata la chiave di crittografia TGT del server Kerberos di Azure AD. |
| CloudId | ID dell'oggetto Azure AD. Deve corrispondere all'ID precedente. |
| CloudDomainDnsName (nome di dominio CloudDomainDns)CloudDomain | *DomainDnsName* dall'oggetto Azure AD. Deve corrispondere al *DomainDnsName* precedente. |
| CloudKeyVersion | Il *KeyVersion* dall'oggetto Azure AD. Deve corrispondere a *KeyVersion* precedente. |
| CloudKeyUpdatedOn | Il *KeyUpdatedOn* dall'oggetto Azure AD. Deve corrispondere al *KeyUpdatedOn* precedente. |

### <a name="rotating-the-azure-ad-kerberos-server-key"></a>Rotazione della chiave del server Kerberos di Azure AD

Le chiavi krbtgt di crittografia del server Kerberos di Azure AD devono essere ruotate regolarmente. Si consiglia di seguire la stessa pianificazione utilizzata per ruotare tutte le altre chiavi krbtgt di Controller di dominio Active Directory.

> [!WARNING]
> Esistono altri strumenti che potrebbero ruotare le chiavi krbtgt, tuttavia, è necessario utilizzare gli strumenti menzionati in questo documento per ruotare le chiavi krbtgt del server Kerberos di Azure AD. Ciò garantisce che le chiavi vengano aggiornate sia in Active Directory locale che in Azure AD.

```powerShell
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred -RotateServerKey
```

### <a name="removing-the-azure-ad-kerberos-server"></a>Rimozione del server Kerberos di Azure ADRemoving the Azure AD Kerberos Server

Se si vuole ripristinare lo scenario e rimuovere il server Kerberos di Azure AD da Active Directory locale e Azure Active Directory, eseguire il comando seguente:

```powerShell
Remove-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="multi-forest-and-multi-domain-scenarios"></a>Scenari con più foreste e più dominiMulti-forest and multi-domain scenarios

L'oggetto server Kerberos di Azure AD è rappresentato in Azure AD come oggetto *KerberosDomain.The* Azure AD Kerberos server object is represented in Azure AD as a KerberosDomain object. Ogni dominio di Active Directory locale è rappresentato come un singolo oggetto *KerberosDomain* in Azure AD.

Ad esempio, l'organizzazione dispone di una `contoso.com` `fabrikam.com`foresta di Active Directory con due domini e . Se si sceglie di consentire ad Azure AD di rilasciare TGT Kerberos per l'intera foresta, sono disponibili due oggetti *KerberosDomain* in Azure AD. Un oggetto *KerberosDomain* per `contoso.com` `fabrikam.com`e uno per . Se si dispone di più foreste di Active Directory, è presente un oggetto *KerberosDomain* per ogni dominio in ogni foresta.

È necessario eseguire la procedura per creare un [oggetto server Kerberos](#create-kerberos-server-object) in ogni dominio e foresta dell'organizzazione che contengono utenti di Azure AD.

## <a name="known-behavior"></a>Comportamento noto

L'accesso con FIDO è bloccato se la password è scaduta. L'aspettativa è che l'utente reimposti la password prima di poter accedere utilizzando FIDO.

## <a name="troubleshooting-and-feedback"></a>Risoluzione dei problemi e feedback

Se vuoi condividere commenti e suggerimenti o riscontrare problemi durante l'anteprima di questa funzionalità, condividila tramite l'app Hub di Feedback di Windows seguendo la procedura seguente:

1. Avvia **Hub di Feedback** e assicurati di aver effettuato l'accesso.
1. Inviare commenti e suggerimenti nella seguente categorizzazione:
   - Categoria: Sicurezza e Privacy
   - Sottocategoria: FIDO
1. Per acquisire i log, utilizzare l'opzione **Ricrea il problema**

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="does-this-work-in-my-on-premises-environment"></a>Funziona nel mio ambiente locale?

Questa funzionalità non funziona per un ambiente di servizi di dominio Active Directory (AD DS) locale puro.

### <a name="my-organization-requires-two-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>L'organizzazione richiede l'autenticazione a due fattori per accedere alle risorse. Cosa posso fare per sostenere questo requisito?

Le chiavi di sicurezza sono disponibili in una varietà di fattori di forma. Contattare il produttore del dispositivo di interesse per discutere come i dispositivi possono essere abilitati con un PIN o biometrico come secondo fattore.

### <a name="can-admins-set-up-security-keys"></a>Gli amministratori possono configurare le chiavi di sicurezza?

Stiamo lavorando su questa funzionalità per la disponibilità generale (GA) di questa funzionalità.

### <a name="where-can-i-go-to-find-compliant-security-keys"></a>Dove posso trovare le chiavi di sicurezza conformi?

[Chiavi di protezione FIDO2](concept-authentication-passwordless.md#fido2-security-keys)

### <a name="what-do-i-do-if-i-lose-my-security-key"></a>Cosa devo fare se perdo la chiave di sicurezza?

È possibile rimuovere le chiavi dal portale di Azure passando alla pagina **Informazioni** di sicurezza e rimuovendola.

### <a name="im-not-able-to-use-fido-immediately-after-i-create-a-hybrid-azure-ad-joined-machine"></a>Non è possibile usare FIDO subito dopo aver creato un computer ibrido aggiunto ad Azure AD

Se si esegue l'installazione pulita di un computer aggiunto ad Azure AD ibrido, dopo il processo di aggiunta e riavvio del dominio è necessario accedere con una password e attendere la sincronizzazione dei criteri prima di poter usare FIDO per l'accesso.

- Controllare lo stato `dsregcmd /status` corrente digitando in una finestra di comando e verificare che sia *AzureAdJoined* che *DomainJoined* mostrino *YES*.
- Questo ritardo è una limitazione nota per i dispositivi aggiunti al dominio e non è specifico di FIDO.

### <a name="im-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-fido-and-get-a-credential-prompt"></a>Non riesco a ottenere SSO alla risorsa di rete NTLM dopo l'accesso con FIDO e ottenere una richiesta di credenziali

Assicurarsi che un numero sufficiente di controller di dominio sia patchato per rispondere in tempo per soddisfare la richiesta di risorse. Per verificare se è possibile visualizzare un controller di `nltest /dsgetdc:contoso /keylist /kdc`dominio che esegue la funzionalità, esaminare l'output di .

## <a name="next-steps"></a>Passaggi successivi

[Ulteriori informazioni su passwordless](concept-authentication-passwordless.md)
