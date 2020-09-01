---
title: Problemi noti e risoluzione dei problemi per le chiavi di sicurezza ibrido per FIDO2-Azure Active Directory
description: Informazioni su alcuni problemi noti e su come risolvere i problemi di accesso alle chiavi di sicurezza ibrido senza password con Azure Active Directory (anteprima)
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
ms.openlocfilehash: ecab82e43bff6c0d1d83c9c1cdc38cafd809e277
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89236663"
---
# <a name="troubleshooting-for-hybrid-deployments-of-fido2-security-keys-in-azure-ad-preview"></a>Risoluzione dei problemi per le distribuzioni ibride di chiavi di sicurezza FIDO2 in Azure AD (anteprima)

Questo articolo illustra le domande frequenti per i dispositivi ibridi Azure AD aggiunti e l'accesso senza password alle risorse locali. Con questa funzionalità senza password, è possibile abilitare l'autenticazione Azure AD nei dispositivi Windows 10 per i dispositivi ibridi Azure AD aggiunti usando le chiavi di sicurezza FIDO2. Gli utenti possono accedere a Windows sui propri dispositivi con credenziali moderne come le chiavi di FIDO2 e accedere alle risorse basate su Active Directory Domain Services tradizionali (AD DS) con un'esperienza di Single Sign-On semplice (SSO) alle risorse locali.

Sono supportati gli scenari seguenti per gli utenti in un ambiente ibrido:

* Accedere a dispositivi ibridi Azure AD aggiunti usando le chiavi di sicurezza FIDO2 e ottenere l'accesso SSO alle risorse locali.
* Accedere a Azure AD dispositivi aggiunti usando le chiavi di sicurezza FIDO2 e ottenere l'accesso SSO alle risorse locali.

Per iniziare a usare le chiavi di sicurezza di FIDO2 e l'accesso ibrido alle risorse locali, vedere gli articoli seguenti:

* [Chiavi di sicurezza non password](howto-authentication-passwordless-security-key.md)
* [Accesso senza password in Windows 10](howto-authentication-passwordless-security-key-windows.md)
* [Password in locale](howto-authentication-passwordless-security-key-on-premises.md)

> [!NOTE]
> Le chiavi di sicurezza di FIDO2 sono una funzionalità di anteprima pubblica di Azure Active Directory. Per altre informazioni sulle anteprime, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="known-issues"></a>Problemi noti

* [Gli utenti non riescono ad accedere usando le chiavi di sicurezza di FIDO2 perché Windows Hello è troppo veloce e è il meccanismo di accesso predefinito](#users-are-unable-to-sign-in-using-fido2-security-keys-as-windows-hello-face-is-too-quick-and-is-the-default-sign-in-mechanism)
* [Gli utenti non sono in grado di usare le chiavi di sicurezza di FIDO2 subito dopo la creazione di un computer ibrido Azure AD aggiunto](#users-arent-able-to-use-fido2-security-keys-immediately-after-they-create-a-hybrid-azure-ad-joined-machine)
* [Gli utenti non sono in grado di ottenere l'accesso SSO alla risorsa di rete NTLM dopo l'accesso con una chiave di sicurezza FIDO2 e la ricezione di una richiesta di credenziali](#users-are-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-a-fido2-security-key-and-receiving-a-credential-prompt)

### <a name="users-are-unable-to-sign-in-using-fido2-security-keys-as-windows-hello-face-is-too-quick-and-is-the-default-sign-in-mechanism"></a>Gli utenti non riescono ad accedere usando le chiavi di sicurezza di FIDO2 perché Windows Hello è troppo veloce e è il meccanismo di accesso predefinito

Windows Hello Smile è la soluzione ideale per un dispositivo in cui è registrato un utente. Le chiavi di sicurezza di FIDO2 sono destinate all'uso nei dispositivi condivisi o in cui la registrazione di Windows Hello for business è una barriera.

Se Windows Hello-Face impedisce agli utenti di provare lo scenario di accesso alla chiave di sicurezza FIDO2, gli utenti possono disattivare l'accesso Hello-Face rimuovendo la registrazione del viso in **impostazioni > opzioni di accesso**.

### <a name="users-arent-able-to-use-fido2-security-keys-immediately-after-they-create-a-hybrid-azure-ad-joined-machine"></a>Gli utenti non sono in grado di usare le chiavi di sicurezza di FIDO2 subito dopo la creazione di un computer ibrido Azure AD aggiunto

Dopo il processo di aggiunta e riavvio del dominio in un'installazione pulita di un computer ibrido Azure AD aggiunto, è necessario eseguire l'accesso con una password e attendere che i criteri vengano sincronizzati prima di poter usare una chiave di sicurezza FIDO2 per eseguire l'accesso.

Questo comportamento è un limite noto per i dispositivi aggiunti a un dominio e non è specifico per le chiavi di sicurezza FIDO2.

Per controllare lo stato corrente, usare il `dsregcmd /status` comando. Verificare che sia *AzureAdJoined* che *DomainJoined* mostrino *Sì*.

### <a name="users-are-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-a-fido2-security-key-and-receiving-a-credential-prompt"></a>Gli utenti non sono in grado di ottenere l'accesso SSO alla risorsa di rete NTLM dopo l'accesso con una chiave di sicurezza FIDO2 e la ricezione di una richiesta di credenziali

Assicurarsi che sia stata applicata una patch per un numero sufficiente di controller di dominio per rispondere al tempo per soddisfare la richiesta di risorse. Per verificare se è possibile visualizzare un server in cui è in esecuzione la funzionalità, esaminare l'output di `nltest /dsgetdc:<dc name> /keylist /kdc`

Se si è in grado di visualizzare un controller di dominio con questa funzionalità, è possibile che la password dell'utente sia cambiata dopo l'accesso o che si sia verificato un altro problema. Raccogliere i log come descritto in dettaglio nella sezione seguente per il debug del team di supporto Microsoft.

## <a name="troubleshoot"></a>Risolvere problemi

Sono disponibili due aree per la risoluzione dei problemi relativi ai [client della finestra](#windows-client-issues)o ai [problemi di distribuzione](#deployment-issues).

### <a name="windows-client-issues"></a>Problemi relativi ai client Windows

Per raccogliere dati che consentono di risolvere i problemi relativi all'accesso a Windows o alle risorse locali dai dispositivi Windows 10, seguire questa procedura:

1. Aprire l'app **Hub di feedback** . Verificare che il nome sia in basso a sinistra nell'app, quindi selezionare **Crea un nuovo elemento di feedback**.

    Per il tipo di elemento feedback, scegliere *problema*.

1. Selezionare la categoria *sicurezza e privacy* , quindi la sottocategoria *fido* .
1. Consente di impostare la casella di controllo *Invia file allegati e diagnostica a Microsoft insieme ai commenti e suggerimenti*.
1. Selezionare *ricrea problemi*, quindi *Avvia acquisizione*.
1. Bloccare e sbloccare il computer con la chiave di sicurezza FIDO2. Se il problema si verifica, provare a sbloccare con altre credenziali.
1. Tornare a **Hub feedback**, selezionare **Arresta acquisizione**e inviare il feedback.
1. Passare alla pagina *Commenti* e suggerimenti, quindi alla scheda *commenti e suggerimenti* . Selezionare il feedback inviato di recente.
1. Selezionare il pulsante *Condividi* nell'angolo in alto a destra per ottenere un collegamento al feedback. Includere questo collegamento quando si apre un caso di supporto oppure rispondere al progettista assegnato a un caso di supporto esistente.

Vengono raccolti i registri eventi e le informazioni sulla chiave del registro di sistema seguenti:

**Chiavi del Registro di sistema**

* *HKEY_LOCAL_MACHINE \SOFTWARE\Policies\Microsoft\FIDO [ \* ]*
* *HKEY_LOCAL_MACHINE \SOFTWARE\Policies\Microsoft\PasswordForWork \* [ \* ]*
* *HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\Policies\PasswordForWork \* [ \* ]*

**Informazioni di diagnostica**

* Dump del kernel attivo
* Raccogli informazioni sul pacchetto AppX
* File di contesto UIF

**Log eventi**

* *%SystemRoot%\System32\winevt\Logs\Microsoft-Windows-AAD%40Operational.evtx*
* *%SystemRoot%\System32\winevt\Logs\Microsoft-Windows-WebAuthN%40Operational.evtx*
* *%SystemRoot%\System32\winevt\Logs\Microsoft-Windows-HelloForBusiness%40Operational.evtx*

### <a name="deployment-issues"></a>Problemi di distribuzione

Per risolvere i problemi relativi alla distribuzione del server Azure AD Kerberos, usare il nuovo modulo di PowerShell incluso con Azure AD Connect.

#### <a name="viewing-the-logs"></a>Visualizzazione dei log

I cmdlet di PowerShell del server Azure AD Kerberos utilizzano la stessa registrazione della procedura guidata Azure AD Connect standard. Per visualizzare informazioni o dettagli sull'errore dai cmdlet, attenersi alla procedura seguente:

1. Nel server Azure AD Connect passare a `C:\ProgramData\AADConnect\` . Per impostazione predefinita questa cartella è nascosta.
1. Aprire e visualizzare il file più recente che `trace-*.log` si trova nella directory.

#### <a name="viewing-the-azure-ad-kerberos-server-objects"></a>Visualizzazione degli oggetti del server Azure AD Kerberos

Per visualizzare gli oggetti del server Azure AD Kerberos e verificare che siano in un ordine corretto, completare i passaggi seguenti:

1. Nel server Azure AD Connect aprire PowerShell e passare a `C:\Program Files\Microsoft Azure Active Directory Connect\AzureADKerberos\`
1. Eseguire i comandi di PowerShell seguenti per visualizzare il Azure AD server Kerberos sia da Azure AD che da servizi di dominio Active Directory locale.

    Sostituire *Corp.contoso.com* con il nome del dominio di servizi di dominio Active Directory locale.

    ```powershell
    Import-Module ".\AzureAdKerberos.psd1"

    # Specify the on-premises AD DS domain.
    $domain = "corp.contoso.com"

    # Enter an Azure Active Directory Global Admin username and password.
    $cloudCred = Get-Credential

    # Enter a Domain Admin username and password.
    $domainCred = Get-Credential

    # Get the Azure AD Kerberos Server Object
    Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential
    $domainCred
    ```

Il comando restituisce le proprietà del server Azure AD Kerberos da Azure AD e servizi di dominio Active Directory locali. Esaminare le proprietà per verificare che tutti gli elementi siano in ordine corretto. Usare la tabella seguente per verificare le proprietà.

Il primo set di proprietà è costituito dagli oggetti dell'ambiente Servizi di dominio Active Directory locale. La seconda metà (le proprietà che iniziano con * Cloud * *) si trovano dall'oggetto server Kerberos in Azure AD:

| Proprietà           | Descrizione  |
|--------------------|--------------|
| ID                 | *ID* univoco dell'oggetto controller di dominio di servizi di dominio Active Directory. |
| DomainDnsName      | Nome di dominio DNS del dominio di servizi di dominio Active Directory. |
| ComputerAccount    | Oggetto account computer del Azure AD oggetto server Kerberos (il controller di dominio). |
| UserAccount        | Oggetto account utente disabilitato che include la chiave di crittografia del server TGT Azure AD Kerberos. Il DN di questo account è *CN = krbtgt_AzureAD, CN = Users, <Domain-DN>* |
| Versione della versione         | La versione chiave della chiave di crittografia TGT del server Kerberos Azure AD. La versione viene assegnata al momento della creazione della chiave. La versione viene quindi incrementata ogni volta che la chiave viene ruotata. Gli incrementi sono basati sui metadati di replica e saranno probabilmente maggiori di uno.<br /><br /> Ad esempio, la versione iniziale della *versione* può essere *192272*. La prima volta che la chiave viene ruotata, la versione può passare a *212621*.<br /><br /> L'aspetto importante da verificare è che la *versione della versione* per l'oggetto locale e la *CloudKeyVersion* per l'oggetto Cloud siano le stesse. |
| KeyUpdatedOn       | Data e ora in cui è stata aggiornata o creata la chiave di crittografia TGT del server Kerberos Azure AD. |
| KeyUpdatedFrom     | Il controller di dominio in cui è stato eseguito l'ultimo aggiornamento della chiave di crittografia del server Azure AD Kerberos TGT. |
| CloudId            | *ID* dall'oggetto Azure ad. Deve corrispondere all' *ID* precedente. |
| CloudDomainDnsName | *DomainDnsName* dall'oggetto Azure ad. Deve corrispondere al *DomainDnsName* sopra riportato. |
| CloudKeyVersion    | La *versione* dell'oggetto Azure ad. Deve corrispondere alla *versione* precedente. |
| CloudKeyUpdatedOn  | *KeyUpdatedOn* dall'oggetto Azure ad. Deve corrispondere al *KeyUpdatedOn* sopra riportato. |

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare le chiavi di sicurezza di FIDO2 e l'accesso ibrido alle risorse locali, vedere gli articoli seguenti:

* [Chiavi di sicurezza FIDO2 senza password](howto-authentication-passwordless-security-key.md)
* [Accesso senza password in Windows 10](howto-authentication-passwordless-security-key-windows.md)
* [Accesso senza password in locale](howto-authentication-passwordless-security-key-on-premises.md)
