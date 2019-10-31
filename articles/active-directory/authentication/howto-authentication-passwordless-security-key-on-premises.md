---
title: Chiave di sicurezza senza password accedi a risorse locali (anteprima)-Azure Active Directory
description: Abilitare la chiave di sicurezza senza password accedi alle risorse locali (anteprima)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42c42bb1e76d854723c8a88e9c3e2c104464beb0
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73164861"
---
# <a name="enable-passwordless-security-key-sign-in-to-on-premises-resources-preview"></a>Abilitare la chiave di sicurezza senza password accedi alle risorse locali (anteprima)

Questo documento è incentrato sull'abilitazione dell'autenticazione senza password per le risorse locali per ambienti con **Azure ad aggiunti** e **ibridi Azure ad** dispositivi Windows 10. Questa funzionalità offre un Single Sign-On semplice (SSO) alle risorse locali usando chiavi di sicurezza compatibili con Microsoft.

|     |
| --- |
| Le chiavi di sicurezza di FIDO2 sono una funzionalità di anteprima pubblica di Azure Active Directory. Per altre informazioni sulle funzionalità in anteprima, vedere [Condizioni Supplementari per l'Utilizzo delle Anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="sso-to-on-premises-resources-using-fido2-keys"></a>Da SSO a risorse locali con chiavi di FIDO2

Azure Active Directory (AD) può emettere ticket di concessione ticket (TGT) Kerberos per uno o più domini Active Directory. Questa funzionalità consente agli utenti di accedere a Windows con credenziali moderne come le chiavi di sicurezza FIDO2 e di accedere alle risorse tradizionali basate su Active Directory. I ticket di servizio e l'autorizzazione Kerberos continueranno a essere controllati dai controller di dominio Active Directory locali.

Nel Active Directory locale verrà creato un Azure AD oggetto server Kerberos, che verrà quindi pubblicato in modo sicuro in Azure Active Directory. L'oggetto non è associato ad alcun server fisico. Si tratta semplicemente di una risorsa che può essere utilizzata da Azure Active Directory per generare ticket di concessione ticket (TGT) Kerberos per la Dominio di Active Directory.

![Recupero di TGT e PRT da Azure AD e servizi di dominio Active Directory](./media/howto-authentication-passwordless-on-premises/fido2-tgt-exchange-process.png)

1. L'utente accede al dispositivo Windows 10 con una chiave di sicurezza FIDO2 ed esegue l'autenticazione per Azure AD.
1. Azure AD controlla la directory di una chiave del server Kerberos corrispondente al dominio AD locale dell'utente.
   1. Azure AD genera un TGT Kerberos per il dominio AD locale dell'utente. Il TGT include solo il SID dell'utente. In TGT non sono inclusi dati di autorizzazione.
1. Il TGT viene restituito al client insieme al relativo Azure AD PRT (Primary Refresh token).
1. Il computer client contatta un controller di dominio di AD locale e commercia i TGT parziali per un TGT completamente formato.
1. Il computer client dispone ora di un Azure AD PRT e di un completo Active Directory TGT e può accedere alle risorse cloud e locali.

## <a name="requirements"></a>Requisiti

Prima di completare la procedura descritta in questo articolo, le organizzazioni devono completare la procedura descritta nell'articolo [abilitare la chiave di sicurezza senza password per i dispositivi Windows 10 (anteprima)](howto-authentication-passwordless-security-key.md) .

Le organizzazioni devono soddisfare i requisiti software seguenti.

- I dispositivi devono eseguire Windows 10 Insider Build 18945 o versione successiva
- Eseguire l'aggiornamento alla versione più recente di [Azure ad Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect)
- Controller di dominio Windows Server 2016/2019 con patch completa per gestire il carico della richiesta di autenticazione locale.

### <a name="supported-scenarios"></a>Scenari supportati

- Lo scenario supporta il Single Sign-On (SSO) per entrambi:
   - Risorse cloud come Office 365 e altre applicazioni abilitate per SAML.
   - Risorse locali e autenticazione integrata di Windows per siti Web, inclusi siti Web e siti di SharePoint che richiedono l'autenticazione IIS e/o risorse che usano l'autenticazione NTLM.

### <a name="unsupported-scenarios"></a>Scenari non supportati

Gli scenari seguenti non sono supportati:

- La distribuzione di Windows Server Active Directory Domain Services (AD DS) aggiunta a un dominio (solo dispositivi locali) **non è supportata**.
- Gli scenari RDP, VDI e Citrix **non sono supportati** con la chiave di sicurezza.
- S/MIME **non è supportato** con la chiave di sicurezza.
- "Esegui come" **non è supportato** con la chiave di sicurezza.
- L'accesso a un server con la chiave di sicurezza **non è supportato**.

## <a name="create-kerberos-server-object"></a>Crea oggetto server Kerberos

Gli amministratori utilizzeranno gli strumenti di PowerShell dal server Azure AD Connect per creare un Azure AD oggetto server Kerberos nella propria directory locale. Sarà necessario eseguire questi passaggi in ogni dominio e insieme di strutture dell'organizzazione che contengono Azure AD utenti.

1. Eseguire l'aggiornamento alla versione più recente di Azure AD Connect. Le istruzioni presuppongono che sia già stato configurato Azure AD Connect per supportare l'ambiente ibrido.
1. Nel server Azure AD Connect aprire un prompt dei comandi di PowerShell con privilegi elevati e passare a C:\Programmi\Microsoft Azure Active Directory Connect\AzureADKerberos\
1. Eseguire i comandi di PowerShell seguenti per creare una nuova Azure AD oggetto server Kerberos sia nel dominio Active Directory locale che in Azure Active Directory tenant.

> [!NOTE]
> Sostituire "contoso.corp.com" nell'esempio seguente con il nome di dominio Active Directory locale.

```PowerShell
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

È possibile visualizzare e verificare il Azure AD server Kerberos appena creato usando il comando seguente. 

```PowerShell
Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

Tramite questo comando vengono restituite le proprietà del server Azure AD Kerberos. È possibile esaminare le proprietà per verificare che tutti gli elementi siano in ordine corretto.

| Proprietà | Description |
| --- | --- |
| ID | ID univoco dell'oggetto controller di dominio di Active Directory. Questo ID viene talvolta definito "slot" o "ID ramo". |
| DomainDnsName | Nome di dominio DNS del Dominio di Active Directory. |
| ComputerAccount | Oggetto account computer del Azure AD oggetto server Kerberos (il controller di dominio). |
| AccountUtente | Oggetto account utente disabilitato che include la chiave di crittografia del server TGT Azure AD Kerberos. Il DN di questo account sarà: <br> CN = krbtgt_AzureAD, CN = Users, < domain-DN > |
| Versione della versione | La versione chiave della chiave di crittografia TGT del server Kerberos Azure AD. La versione viene assegnata al momento della creazione della chiave. La versione viene quindi incrementata ogni volta che la chiave viene ruotata. Gli incrementi sono basati sui metadati di replica e saranno probabilmente maggiori di uno. Ad esempio, la versione iniziale della versione può essere 192272. La prima volta che la chiave viene ruotata, la versione può passare a 212621. L'aspetto importante da verificare è che la versione della versione per l'oggetto locale e la CloudKeyVersion per l'oggetto Cloud siano le stesse. |
| KeyUpdatedOn | Data e ora in cui la chiave di crittografia TGT Azure AD del server Kerberos è stata aggiornata o creata. |
| KeyUpdatedFrom | Il controller di dominio in cui è stato eseguito l'ultimo aggiornamento della chiave di crittografia del server Azure AD Kerberos TGT. |
| CloudId | ID dall'oggetto Azure AD. Deve corrispondere all'ID precedente. |
| CloudDomainDnsName | DomainDnsName dall'oggetto Azure AD. Deve corrispondere al DomainDnsName sopra riportato. |
| CloudKeyVersion | La versione dell'oggetto Azure AD. Deve corrispondere alla versione precedente. |
| CloudKeyUpdatedOn | KeyUpdatedOn dall'oggetto Azure AD. Deve corrispondere al KeyUpdatedOn sopra riportato. |

### <a name="rotating-the-azure-ad-kerberos-server-key"></a>Rotazione della chiave del server Kerberos Azure AD

Le chiavi di KRBTGT della crittografia del server Kerberos Azure AD devono essere ruotate a intervalli regolari. È consigliabile seguire la stessa pianificazione usata per ruotare tutte le altre chiavi di krbtgt del controller di Dominio di Active Directory.

> [!WARNING]
> Esistono altri strumenti che potrebbero ruotare le chiavi di krbtgt. Tuttavia, è necessario usare gli strumenti indicati in questo documento per ruotare le chiavi di krbtgt del server Azure AD Kerberos. Ciò garantisce che le chiavi vengano aggiornate sia in AD locale che in Azure AD.

```PowerShell
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred -RotateServerKey
```

### <a name="removing-the-azure-ad-kerberos-server"></a>Rimozione del server Azure AD Kerberos

Se si desidera ripristinare lo scenario e rimuovere il server Azure AD Kerberos da Active Directory locale e Azure Active Directory, eseguire il comando seguente.  

```PowerShell
Remove-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="multi-forest-and-multi-domain-scenarios"></a>Scenari con più foreste e più domini

L'oggetto server Azure AD Kerberos è rappresentato in Azure AD come oggetto KerberosDomain. Ogni dominio di Active Directory locale è rappresentato come un singolo oggetto KerberosDomain in Azure AD.

Ad esempio, l'organizzazione dispone di una foresta Active Directory con due domini, contoso.com e fabrikam.com. Se si sceglie di consentire Azure AD di emettere ticket di concessione ticket (TGT) Kerberos per l'intera foresta, in Azure AD saranno presenti due oggetti KerberosDomain. Un oggetto KerberosDomain per contoso.com e uno per fabrikam.com. Se si dispone di più foreste Active Directory, sarà presente un oggetto KerberosDomain per ogni dominio in ogni foresta.

È necessario eseguire i passaggi descritti in [creare un oggetto server Kerberos](#create-kerberos-server-object) in ogni dominio e insieme di strutture dell'organizzazione che contengono Azure ad utenti.

## <a name="known-behavior"></a>Comportamento noto

L'accesso con FIDO verrà bloccato se la password è scaduta. Si prevede che l'utente possa reimpostare la password prima di poter accedere usando FIDO.

## <a name="troubleshooting-and-feedback"></a>Risoluzione dei problemi e commenti e suggerimenti

Per condividere commenti e suggerimenti o riscontrare problemi durante l'anteprima di questa funzionalità, è possibile condividerla tramite l'app hub di feedback di Windows.

1. Avviare **feedback Hub** e assicurarsi di aver eseguito l'accesso.
1. Inviare commenti e suggerimenti nella categorizzazione seguente:
   1. Categoria: sicurezza e privacy
   1. Sottocategoria: FIDO
1. Per acquisire i log, usare l'opzione: **ricrea il problema**

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="does-this-work-in-my-on-premises-environment"></a>Questa operazione viene eseguita nell'ambiente locale?

Questa funzionalità non funziona per un ambiente di Active Directory Domain Services (AD DS) locale.

### <a name="my-organization-requires-two-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>L'organizzazione richiede l'autenticazione a due fattori per accedere alle risorse, cosa è possibile fare per supportare questo requisito?

Le chiavi di sicurezza sono costituite da diversi fattori di forma. Contattare il produttore del dispositivo di interesse per discutere il modo in cui i dispositivi possono essere abilitati con un PIN o biometrico come secondo fattore.

### <a name="can-admins-set-up-security-keys"></a>Gli amministratori possono configurare le chiavi di sicurezza?

Questa funzionalità è stata utilizzata per la disponibilità generale (GA).

### <a name="where-can-i-go-to-find-compliant-security-keys"></a>Dove è possibile trovare le chiavi di sicurezza conformi?

[Chiavi di sicurezza FIDO2](concept-authentication-passwordless.md#fido2-security-keys)

### <a name="what-do-i-do-if-i-lose-my-security-key"></a>Cosa fare se si perde la chiave di sicurezza?

È possibile rimuovere le chiavi dalla portale di Azure, passando alla pagina delle informazioni di sicurezza e rimuovendo la chiave di sicurezza.

### <a name="im-not-able-to-use-fido-immediately-after-i-create-a-hybrid-azure-ad-joined-machine"></a>Non è possibile usare FIDO immediatamente dopo aver creato un computer ibrido Azure AD aggiunto

Se si installa un computer ibrido Azure AD aggiunto, dopo l'aggiunta a un dominio e il riavvio è necessario accedere con una password e attendere la sincronizzazione dei criteri prima di poter usare FIDO per l'accesso.

- Verificare lo stato corrente digitando `dsregcmd /status` in una finestra di comando e verificare che sia `AzureAdJoined` che `DomainJoined` mostrino `YES`.
- Questo ritardo è un limite noto per i dispositivi aggiunti a un dominio e non per la specifica di FIDO.

### <a name="im-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-fido-and-get-a-credential-prompt"></a>Non è possibile ottenere l'accesso SSO alla risorsa di rete NTLM dopo aver eseguito l'accesso con FIDO e aver ricevuto una richiesta di credenziali

Assicurarsi che venga applicata una patch a un numero sufficiente di controller di dominio per rispondere al tempo per soddisfare la richiesta di risorse. Per verificare se è possibile visualizzare un controller di dominio che esegue la funzionalità, controllare l'output di nltest/dsgetdc: contoso/keylist/KDC.

## <a name="next-steps"></a>Passaggi successivi

[Scopri di più su password](concept-authentication-passwordless.md)
