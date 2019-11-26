---
title: Implementare la sincronizzazione dell'hash delle password con il servizio di sincronizzazione Azure AD Connect | Microsoft Docs
description: Contiene informazioni sul funzionamento e sulla configurazione della sincronizzazione dell'hash delle password.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 05f16c3e-9d23-45dc-afca-3d0fa9dbf501
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/02/2019
ms.subservice: hybrid
ms.author: billmath
search.appverid:
- MET150
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d5ca62bc032c12c568e2b8065630dcd8b687513
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483106"
---
# <a name="implement-password-hash-synchronization-with-azure-ad-connect-sync"></a>Implementare la sincronizzazione dell'hash delle password con il servizio di sincronizzazione Azure AD Connect
Questo articolo contiene le informazioni necessarie per sincronizzare le password utente da un'istanza di Active Directory locale a un'istanza di Azure Active Directory (Azure AD) basata sul cloud.

## <a name="how-password-hash-synchronization-works"></a>Funzionamento della sincronizzazione dell'hash delle password
Il servizio di dominio Active Directory archivia le password come rappresentazioni con valore hash delle password utente reali. Un valore hash è il risultato di una funzione matematica unidirezionale, chiamata *algoritmo di hash*. Non esiste un metodo per ripristinare la versione in testo normale di una password dal risultato di una funzione unidirezionale. 

Per sincronizzare la password, il servizio di sincronizzazione Azure AD Connect estrae l'hash della password dall'istanza di Active Directory locale. All'hash della password viene applicata un'elaborazione di sicurezza aggiuntiva prima della sincronizzazione con il servizio di autenticazione di Azure Active Directory. Le password vengono sincronizzate per ogni singolo utente e in ordine cronologico.

Il flusso di dati del processo di sincronizzazione hash delle password è simile alla sincronizzazione dei dati utente. Le password vengono tuttavia sincronizzate con una frequenza maggiore rispetto alla finestra di sincronizzazione standard della directory per altri attributi. Il processo di sincronizzazione dell'hash delle password viene eseguito ogni 2 minuti. Non è possibile modificare la frequenza del processo. Se si sincronizza una password, la password sincronizzata sovrascrive quella esistente nel cloud.

Quando si abilita la funzionalità di sincronizzazione dell'hash delle password per la prima volta, viene eseguita una sincronizzazione iniziale delle password di tutti gli utenti inclusi nell'ambito. Non è possibile definire in modo esplicito un subset di password utente da sincronizzare. However, if there are multiple connectors, it is possible to disable password hash sync for some connectors but not others using the [Set-ADSyncAADPasswordSyncConfiguration](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant) cmdlet.

Quando si modifica una password locale, la password aggiornata viene sincronizzata, spesso in pochi minuti.
In caso di sincronizzazioni non riuscite, la funzionalità di sincronizzazione dell'hash delle password esegue automaticamente nuovi tentativi di sincronizzazione. Se si verifica un errore durante un tentativo di sincronizzazione di una password, viene registrato un errore nel visualizzatore eventi.

La sincronizzazione di una password non ha alcun impatto sull'utente attualmente connesso.
Se si modifica una password sincronizzata dopo aver eseguito l'accesso a un servizio cloud, la sessione corrente del servizio cloud non rileva immediatamente la modifica. Tuttavia, quando il servizio cloud richiede di nuovo l'autenticazione, è necessario specificare la nuova password.

Un utente deve immettere le proprie credenziali aziendali una seconda volta per l'autenticazione in Azure AD, indipendentemente dal fatto che abbia effettuato l'accesso alla rete aziendale. Gli effetti di questo criterio possono essere ridotti al minimo se al momento dell'accesso l'utente seleziona la casella di controllo Mantieni l'accesso. La selezione di questa opzione imposta un cookie di sessione che permette di ignorare l'autenticazione per 180 giorni. Il comportamento dell'opzione Mantieni l'accesso può essere abilitato o disabilitato dall'amministratore di Azure AD. È anche possibile ridurre le richieste di password attivando l'[accesso Seamless SSO](how-to-connect-sso.md) che consente agli utenti di accedere automaticamente dai dispositivi di proprietà dell'azienda connessi alla rete aziendale.

> [!NOTE]
> La sincronizzazione delle password è supportata solo per l'utente del tipo di oggetto in Active Directory. Non è supportata per il tipo di oggetto iNetOrgPerson.

### <a name="detailed-description-of-how-password-hash-synchronization-works"></a>Descrizione dettagliata del funzionamento della sincronizzazione dell'hash delle password

La sezione seguente illustra in dettaglio il funzionamento della sincronizzazione hash delle password tra Active Directory e Azure AD.

![Flusso dettagliato della sincronizzazione password](./media/how-to-connect-password-hash-synchronization/arch3b.png)

1. Ogni due minuti, l'agente di sincronizzazione hash delle password nel server AD Connect richiede gli hash delle password archiviate (attributo unicodePwd) da un controller di dominio.  La richiesta si avvale del protocollo di replica standard [MS-DRSR](https://msdn.microsoft.com/library/cc228086.aspx) usato per sincronizzare i dati tra i controller di dominio. L'account di servizio deve disporre delle autorizzazioni di Azure AD Replica modifiche directory e Replica modifiche directory - Tutto (concesse per impostazione predefinita in fase di installazione) per ottenere gli hash delle password.
2. Prima dell'invio, il controller di dominio crittografa l'hash della password MD4 tramite una chiave corrispondente a un hash [MD5](https://www.rfc-editor.org/rfc/rfc1321.txt) della chiave di sessione RPC e a un valore salt. Invia quindi il risultato dell'agente di sincronizzazione dell'hash delle password tramite RPC. Il controller di dominio passa inoltre il valore salt all'agente di sincronizzazione usando il protocollo di replica del controller di dominio, in modo che l'agente sia in grado di decrittografare la busta.
3. Dopo aver ottenuto la busta crittografata, l'agente di sincronizzazione dell'hash delle password usa [MD5CryptoServiceProvider](https://msdn.microsoft.com/library/System.Security.Cryptography.MD5CryptoServiceProvider.aspx) e il valore salt per generare una chiave per decrittografare i dati ricevuti riportandoli nel formato MD4 originale. L'agente di sincronizzazione hash delle password non ha mai accesso alla password non crittografata. L'uso di MD5 da parte dell'agente di sincronizzazione dell'hash delle password è esclusivamente per la compatibilità del protocollo di replica con il controller di dominio e viene usato solo in locale tra il controller di dominio e l'agente di sincronizzazione dell'hash delle password.
4. L'agente di sincronizzazione dell'hash delle password espande l'hash della password binario a 16 byte in 64 byte convertendo per prima cosa l'hash in una stringa esadecimale a 32 byte e quindi riconvertendo questa stessa stringa in formato binario con la codifica UTF-16.
5. L'agente di sincronizzazione dell'hash delle password aggiunge un valore salt per utente lungo 10 byte al file binario a 64 byte per proteggere ulteriormente l'hash originale.
6. L'agente di sincronizzazione dell'hash delle password combina quindi l'hash MD4 con il valore salt per utente e li inserisce nella funzione [PBKDF2](https://www.ietf.org/rfc/rfc2898.txt). Vengono usate 1000 iterazioni dell'algoritmo di hash con chiave [HMAC-SHA256](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx). 
7. L'agente di sincronizzazione dell'hash delle password concatena all'hash a 32 byte risultante sia il valore salt per utente sia il numero di iterazioni SHA256 (per l'uso con Azure AD) e quindi trasmette la stringa da Azure AD Connect ad Azure AD tramite SSL.</br> 
8. Quando un utente prova ad accedere ad Azure AD e immette la password, la password viene eseguita tramite lo stesso processo MD4 + valore salt + PBKDF2 + HMAC-SHA256. Se l'hash risultante corrisponde all'hash archiviato in Azure AD, l'utente ha immesso la password corretta e viene autenticato.

> [!NOTE]
> L'hash MD4 originale non viene trasmesso ad Azure AD. Viene invece trasmesso l'hash SHA256 dell'hash MD4 originale. Di conseguenza, se si riesce a ottenere l'hash archiviato in Azure AD, questo non può essere usato in un attacco di tipo Pass-the-Hash locale.

### <a name="security-considerations"></a>Considerazioni sulla sicurezza

Quando si sincronizzano le password, la versione in testo normale della password non viene esposta alla funzionalità di sincronizzazione dell'hash delle password, né ad Azure AD o a qualsiasi servizio associato.

L'autenticazione utente viene eseguita in Azure AD e non nell'istanza di Active Directory dell'organizzazione. I dati delle password SHA256 archiviati in Azure AD (un hash dell'hash MD4 originale) sono più sicuri di quelli archiviati in Active Directory. Inoltre, dal momento che questo hash SHA256 non può essere decrittografato, non potrà essere ripresentato nell'ambiente Active Directory dell'organizzazione come una password utente valida in un attacco di tipo Pass-the-Hash.

### <a name="password-policy-considerations"></a>Considerazioni relative ai criteri password

L'abilitazione della sincronizzazione dell'hash delle password influisce su due tipi di criteri password:

* Criteri di complessità delle password
* Criteri di scadenza delle password

#### <a name="password-complexity-policy"></a>Criteri di complessità delle password

Quando la sincronizzazione dell'hash delle password è abilitata, i criteri di complessità delle password presenti nell'istanza di Active Directory locale sostituiscono i criteri di complessità definiti nel cloud per gli utenti sincronizzati. È possibile usare tutte le password valide dell'istanza di Active Directory locale per accedere ai servizi Azure AD.

> [!NOTE]
> Le password degli utenti create direttamente nel cloud restano soggette ai criteri password definiti nel cloud.

#### <a name="password-expiration-policy"></a>Criteri di scadenza delle password

If a user is in the scope of password hash synchronization, by default the cloud account password is set to *Never Expire*.

È possibile continuare ad accedere ai servizi cloud usando una password sincronizzata che è in realtà scaduta nell'ambiente locale. La password cloud viene aggiornata alla modifica successiva della password nell'ambiente locale.

##### <a name="public-preview-of-the-enforcecloudpasswordpolicyforpasswordsyncedusers-feature"></a>Public preview of the *EnforceCloudPasswordPolicyForPasswordSyncedUsers* feature

If there are synchronized users that only interact with Azure AD integrated services and must also comply with a password expiration policy, you can force them to comply with your Azure AD password expiration policy by enabling the *EnforceCloudPasswordPolicyForPasswordSyncedUsers* feature.

When *EnforceCloudPasswordPolicyForPasswordSyncedUsers* is disabled (which is the default setting), Azure AD Connect sets the PasswordPolicies attribute of synchronized users to "DisablePasswordExpiration". This is done every time a user's password is synchronized and instructs Azure AD to ignore the cloud password expiration policy for that user. You can check the value of the attribute using the Azure AD PowerShell module with the following command:

`(Get-AzureADUser -objectID <User Object ID>).passwordpolicies`


To enable the EnforceCloudPasswordPolicyForPasswordSyncedUsers feature, run the following command using the MSOnline PowerShell module:

`Set-MsolDirSyncFeature -Feature EnforceCloudPasswordPolicyForPasswordSyncedUsers -Enable $true`

Once enabled, Azure AD does not go to each synchronized user to remove the `DisablePasswordExpiration` value from the PasswordPolicies attribute. Instead, the value is set to `None` during the next password sync for each user when they next change their password in on-premises AD.  

It is recommended to enable EnforceCloudPasswordPolicyForPasswordSyncedUsers, prior to enabling password hash sync, so that the initial sync of password hashes does not add the `DisablePasswordExpiration` value to the PasswordPolicies attribute for the users.

The default Azure AD password policy requires users to change their passwords every 90 days. If your policy in AD is also 90 days, the two policies should match. However, if the AD policy is not 90 days, you can update the Azure AD password policy to match by using the Set-MsolPasswordPolicy PowerShell command.

Azure AD supports a separate password expiration policy per registered domain.

Caveat: If there are synchronized accounts that need to have non-expiring passwords in Azure AD, you must explicitly add the `DisablePasswordExpiration` value to the PasswordPolicies attribute of the user object in Azure AD.  You can do this by running the following command.

`Set-AzureADUser -ObjectID <User Object ID> -PasswordPolicies "DisablePasswordExpiration"`

> [!NOTE]
> This feature is in Public Preview right now.

#### <a name="public-preview-of-synchronizing-temporary-passwords-and-force-password-on-next-logon"></a>Public Preview of synchronizing temporary passwords and "Force Password on Next Logon"

It is typical to force a user to change their password during their first logon, especially after an admin password reset occurs.  It is commonly known as setting a "temporary" password and is completed by checking the "User must change password at next logon" flag on a user object in Active Directory (AD).
  
The temporary password functionality helps to ensure that the transfer of ownership of the credential is completed on first use, to minimize the duration of time in which more than one individual has knowledge of that credential.

To support temporary passwords in Azure AD for synchronized users, you can enable the *ForcePasswordResetOnLogonFeature* feature, by running the following command on your Azure AD Connect server, replacing <AAD Connector Name> with the connector name specific to your environment:

`Set-ADSyncAADCompanyFeature -ConnectorName "<AAD Connector name>" -ForcePasswordResetOnLogonFeature $true`

You can use the following command to determine the connector name:

`(Get-ADSyncConnector | where{$_.ListName -eq "Windows Azure Active Directory (Microsoft)"}).Name`

Caveat:  Forcing a user to change their password on next logon requires a password change at the same time.  AD Connect will not pick up the force password change flag by itself, it is supplemental to the detected password change that occurs during password hash sync.

> [!CAUTION]
> If you do not enable Self-service Password Reset (SSPR) in Azure AD users will have a confusing experience when they reset their password in Azure AD and then attempt to sign in in Active Directory with the new password, as the new password isn’t valid in Active Directory. You should only use this feature when SSPR and Password Writeback is enabled on the tenant.

> [!NOTE]
> This feature is in Public Preview right now.

#### <a name="account-expiration"></a>Scadenza dell'account

Se l'organizzazione usa l'attributo accountExpires nella gestione degli account utente, tenere presente che questo attributo non viene sincronizzato con Azure AD. Di conseguenza, un account Active Directory scaduto in un ambiente configurato per la sincronizzazione dell'hash delle password continuerà a essere attivo in Azure AD. Se l'account è scaduto, è consigliabile fare in modo che un'azione del flusso di lavoro attivi uno script di PowerShell per disabilitare l'account Azure AD dell'utente (usare il cmdlet [Set-AzureADUser](https://docs.microsoft.com/powershell/module/azuread/set-azureaduser?view=azureadps-2.0)). Al contrario, quando l'account è attivato, è necessario attivare anche l'istanza di Azure AD.

### <a name="overwrite-synchronized-passwords"></a>Sovrascrivere password sincronizzate

Un amministratore può reimpostare manualmente la password usando Windows PowerShell.

In questo caso, la password sincronizzata viene sostituita dalla nuova password, a cui si applicano tutti i criteri password definiti nel cloud.

Se si modifica di nuovo la password locale, la nuova password viene sincronizzata nel cloud e sostituisce la password aggiornata manualmente.

La sincronizzazione di una password non ha alcun impatto sull'utente di Azure connesso. Se si modifica una password sincronizzata dopo aver eseguito l'accesso a un servizio cloud, questa operazione non ha un impatto immediato sulla sessione corrente del servizio cloud. L'opzione Mantieni l'accesso estende la durata in base a questa differenza. Quando il servizio cloud richiede di nuovo l'autenticazione, è necessario specificare la nuova password.

### <a name="additional-advantages"></a>Vantaggi aggiuntivi

- In genere, la sincronizzazione dell'hash delle password è più semplice da implementare rispetto a un servizio federativo. Non richiede altri server e permette di eliminare la dipendenza da un servizio federativo a disponibilità elevata per l'autenticazione degli utenti.
- La sincronizzazione dell'hash delle password può anche essere abilitata in aggiunta alla federazione. Può inoltre essere usata come fallback in caso di interruzione del servizio federativo.

## <a name="password-hash-sync-process-for-azure-ad-domain-services"></a>Password hash sync process for Azure AD Domain Services

If you use Azure AD Domain Services to provide legacy authentication for applications and services that need to use Keberos, LDAP, or NTLM, some additional processes are part of the password hash synchronization flow. Azure AD Connect uses the additional following process to synchronize password hashes to Azure AD for use in Azure AD Domain Services:

> [!IMPORTANT]
> Azure AD Connect should only be installed and configured for synchronization with on-premises AD DS environments. It's not supported to install Azure AD Connect in an Azure AD DS managed domain to synchronize objects back to Azure AD.
>
> Azure AD Connect only synchronizes legacy password hashes when you enable Azure AD DS for your Azure AD tenant. The following steps aren't used if you only use Azure AD Connect to synchronize an on-premises AD DS environment with Azure AD.
>
> If your legacy applications don't use NTLM authentication or LDAP simple binds, we recommend that you disable NTLM password hash synchronization for Azure AD DS. For more information, see [Disable weak cipher suites and NTLM credential hash synchronization](../../active-directory-domain-services/secure-your-domain.md).

1. Azure AD Connect retrieves the public key for the tenant's instance of Azure AD Domain Services.
1. When a user changes their password, the on-premises domain controller stores the result of the password change (hashes) in two attributes:
    * *unicodePwd* for the NTLM password hash.
    * *supplementalCredentials* for the Kerberos password hash.
1. Azure AD Connect detects password changes through the directory replication channel (attribute changes needing to replicate to other domain controllers).
1. For each user whose password has changed, Azure AD Connect performs the following steps:
    * Generates a random AES 256-bit symmetric key.
    * Generates a random initialization vector needed for the first round of encryption.
    * Extracts Kerberos password hashes from the *supplementalCredentials* attributes.
    * Checks the Azure AD Domain Services security configuration *SyncNtlmPasswords* setting.
        * If this setting is disabled, generates a random, high-entropy NTLM hash (different from the user's password). This hash is then combined with the exacted Kerberos password hashes from the *supplementalCrendetials* attribute into one data structure.
        * If enabled, combines the value of the *unicodePwd* attribute with the extracted Kerberos password hashes from the *supplementalCredentials* attribute into one data structure.
    * Encrypts the single data structure using the AES symmetric key.
    * Encrypts the AES symmetric key using the tenant's Azure AD Domain Services public key.
1. Azure AD Connect transmits the encrypted AES symmetric key, the encrypted data structure containing the password hashes, and the initialization vector to Azure AD.
1. Azure AD stores the encrypted AES symmetric key, the encrypted data structure, and the initialization vector for the user.
1. Azure AD pushes the encrypted AES symmetric key, the encrypted data structure, and the initialization vector using an internal synchronization mechanism over an encrypted HTTP session to Azure AD Domain Services.
1. Azure AD Domain Services retrieves the private key for the tenant's instance from Azure Key vault.
1. For each encrypted set of data (representing a single user's password change), Azure AD Domain Services then performs the following steps:
    * Uses its private key to decrypt the AES symmetric key.
    * Uses the AES symmetric key with the initialization vector to decrypt the encrypted data structure that contains the password hashes.
    * Writes the Kerberos password hashes it receives to the Azure AD Domain Services domain controller. The hashes are saved into the user object's *supplementalCredentials* attribute that is encrypted to the Azure AD Domain Services domain controller's public key.
    * Azure AD Domain Services writes the NTLM password hash it received to the Azure AD Domain Services domain controller. The hash is saved into the user object's *unicodePwd* attribute that is encrypted to the Azure AD Domain Services domain controller's public key.

## <a name="enable-password-hash-synchronization"></a>Abilitare la sincronizzazione dell'hash delle password

>[!IMPORTANT]
>Se si esegue la migrazione da AD FS (o da altre tecnologia federative) per la sincronizzazione dell'hash delle password, si consiglia vivamente di seguire la guida per la distribuzione dettagliata pubblicata [qui](https://aka.ms/adfstophsdpdownload).

Quando si installa Azure AD Connect usando l'opzione **Impostazioni rapide**, la sincronizzazione dell'hash delle password viene abilitata automaticamente. Per altre informazioni, vedere [Introduzione alle impostazioni rapide per Azure AD Connect](how-to-connect-install-express.md).

Se si usano impostazioni personalizzate quando si installa Azure AD Connect, la sincronizzazione dell'hash delle password è disponibile nella pagina di accesso dell'utente. Per altre informazioni, vedere [Installazione personalizzata di Azure AD Connect](how-to-connect-install-custom.md).

![Abilitazione della sincronizzazione dell'hash delle password](./media/how-to-connect-password-hash-synchronization/usersignin2.png)

### <a name="password-hash-synchronization-and-fips"></a>Sincronizzazione dell'hash delle password e FIPS
Se il server è stato bloccato in conformità allo standard FIPS (Federal Information Processing Standard), MD5 è disabilitato.

**Per abilitare MD5 per la sincronizzazione dell'hash delle password, seguire questa procedura:**

1. Passare a %programmi%\Azure AD Sync\Bin.
2. Aprire miiserver.exe.config.
3. Passare al nodo configuration/runtime alla fine del file.
4. Aggiungere il nodo seguente: `<enforceFIPSPolicy enabled="false"/>`
5. Salvare le modifiche.

Come riferimento, il frammento di codice dovrà essere simile al seguente:

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

Per informazioni sulla sicurezza e su FIPS, vedere [Azure AD password hash sync, encryption, and FIPS compliance](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/) (Sincronizzazione password, crittografia e conformità FIPS di Azure AD).

## <a name="troubleshoot-password-hash-synchronization"></a>Risolvere i problemi di sincronizzazione dell'hash delle password
In caso di problemi di sincronizzazione dell'hash delle password, vedere [Risolvere i problemi di sincronizzazione dell'hash delle password](tshoot-connect-password-hash-synchronization.md).

## <a name="next-steps"></a>Passaggi successivi
* [Servizio di sincronizzazione Azure AD Connect: personalizzazione delle opzioni di sincronizzazione](how-to-connect-sync-whatis.md)
* [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md)
* [Ottenere un piano di distribuzione dettagliato per la migrazione dal file system distribuito di Azure alla sincronizzazione dell'hash delle password](https://aka.ms/authenticationDeploymentPlan)
