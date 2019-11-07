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
ms.openlocfilehash: 1d8caafe312c123a9d572e9a5f4c5cf64a05f7ea
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721051"
---
# <a name="implement-password-hash-synchronization-with-azure-ad-connect-sync"></a>Implementare la sincronizzazione dell'hash delle password con il servizio di sincronizzazione Azure AD Connect
Questo articolo contiene le informazioni necessarie per sincronizzare le password utente da un'istanza di Active Directory locale a un'istanza di Azure Active Directory (Azure AD) basata sul cloud.

## <a name="how-password-hash-synchronization-works"></a>Funzionamento della sincronizzazione dell'hash delle password
Il servizio di dominio Active Directory archivia le password come rappresentazioni con valore hash delle password utente reali. Un valore hash è il risultato di una funzione matematica unidirezionale, chiamata *algoritmo di hash*. Non esiste un metodo per ripristinare la versione in testo normale di una password dal risultato di una funzione unidirezionale. 

Per sincronizzare la password, il servizio di sincronizzazione Azure AD Connect estrae l'hash della password dall'istanza di Active Directory locale. All'hash della password viene applicata un'elaborazione di sicurezza aggiuntiva prima della sincronizzazione con il servizio di autenticazione di Azure Active Directory. Le password vengono sincronizzate per ogni singolo utente e in ordine cronologico.

Il flusso di dati del processo di sincronizzazione hash delle password è simile alla sincronizzazione dei dati utente. Le password vengono tuttavia sincronizzate con una frequenza maggiore rispetto alla finestra di sincronizzazione standard della directory per altri attributi. Il processo di sincronizzazione dell'hash delle password viene eseguito ogni 2 minuti. Non è possibile modificare la frequenza del processo. Se si sincronizza una password, la password sincronizzata sovrascrive quella esistente nel cloud.

Quando si abilita la funzionalità di sincronizzazione dell'hash delle password per la prima volta, viene eseguita una sincronizzazione iniziale delle password di tutti gli utenti inclusi nell'ambito. Non è possibile definire in modo esplicito un subset di password utente da sincronizzare. Tuttavia, se sono presenti più connettori, è possibile disabilitare la sincronizzazione dell'hash delle password per alcuni connettori, ma non altri, usando il cmdlet [set-ADSyncAADPasswordSyncConfiguration](https://docs.microsoft.com/en-us/azure/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant) .

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

### <a name="security-considerations"></a>Considerazioni relative alla sicurezza

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

Se un utente rientra nell'ambito della sincronizzazione dell'hash delle password, per impostazione predefinita la password dell'account cloud viene impostata in modo da *non scadere mai*.

È possibile continuare ad accedere ai servizi cloud usando una password sincronizzata che è in realtà scaduta nell'ambiente locale. La password cloud viene aggiornata alla modifica successiva della password nell'ambiente locale.

##### <a name="public-preview-of-the-enforcecloudpasswordpolicyforpasswordsyncedusers-feature"></a>Anteprima pubblica della funzionalità *EnforceCloudPasswordPolicyForPasswordSyncedUsers*

Se sono presenti utenti sincronizzati che interagiscono solo con Azure AD servizi integrati e devono essere conformi ai criteri di scadenza delle password, è possibile forzarli a conformarsi ai criteri di scadenza della password Azure AD abilitando il  *Funzionalità EnforceCloudPasswordPolicyForPasswordSyncedUsers* .

Quando *EnforceCloudPasswordPolicyForPasswordSyncedUsers* è disabilitato (impostazione predefinita), Azure ad Connect imposta l'attributo PasswordPolicies degli utenti sincronizzati su "DisablePasswordExpiration". Questa operazione viene eseguita ogni volta che la password di un utente viene sincronizzata e indica Azure AD di ignorare i criteri di scadenza della password cloud per tale utente. È possibile controllare il valore dell'attributo usando il modulo Azure AD PowerShell con il comando seguente:

`(Get-AzureADUser -objectID <User Object ID>).passwordpolicies`


Per abilitare la funzionalità EnforceCloudPasswordPolicyForPasswordSyncedUsers, eseguire il comando seguente usando il modulo MSOnline di PowerShell:

`Set-MsolDirSyncFeature -Feature EnforceCloudPasswordPolicyForPasswordSyncedUsers  $true`

Una volta abilitata, Azure AD non passa a ogni utente sincronizzato per rimuovere il valore `DisablePasswordExpiration` dall'attributo PasswordPolicies. Al contrario, il valore viene impostato su `None` durante la sincronizzazione della password successiva per ogni utente alla successiva modifica della password in AD locale.  

È consigliabile abilitare EnforceCloudPasswordPolicyForPasswordSyncedUsers, prima di abilitare la sincronizzazione dell'hash delle password, in modo che la sincronizzazione iniziale degli hash delle password non aggiunga il valore `DisablePasswordExpiration` all'attributo PasswordPolicies per gli utenti.

I criteri predefiniti per le password Azure AD richiedono agli utenti di modificare le password ogni 90 giorni. Se il criterio in Active Directory è anche di 90 giorni, i due criteri devono corrispondere. Tuttavia, se i criteri di Active Directory non sono 90 giorni, è possibile aggiornare i criteri di Azure AD password in modo che corrispondano usando il comando di PowerShell Set-MsolPasswordPolicy.

Azure AD supporta un criterio di scadenza della password separato per ogni dominio registrato.

Avvertenza: se sono presenti account sincronizzati che devono avere password non in scadenza in Azure AD, è necessario aggiungere in modo esplicito il valore `DisablePasswordExpiration` all'attributo PasswordPolicies dell'oggetto utente in Azure AD.  È possibile eseguire questa operazione eseguendo il comando seguente.

`Set-AzureADUser -ObjectID <User Object ID> -PasswordPolicies "DisablePasswordExpiration"`

> [!NOTE]
> Questa funzionalità è disponibile in anteprima pubblica in questo momento.

#### <a name="public-preview-of-synchronizing-temporary-passwords-and-force-password-on-next-logon"></a>Anteprima pubblica della sincronizzazione delle password temporanee e "forza la password all'accesso successivo"

È tipico forzare un utente a modificare la password durante il primo accesso, soprattutto dopo la reimpostazione della password amministratore.  È comunemente nota come impostazione di una password "temporanea" e viene completata selezionando il flag "utente necessario modificare la password al successivo accesso" per un oggetto utente in Active Directory (AD).
  
La funzionalità relativa alle password temporanee consente di garantire che il trasferimento della proprietà delle credenziali venga completato al primo utilizzo, per ridurre al minimo la durata del tempo in cui più persone hanno la conoscenza di tale credenziale.

Per supportare password temporanee in Azure AD per gli utenti sincronizzati, è possibile abilitare la funzionalità *ForcePasswordResetOnLogonFeature* eseguendo il comando seguente nel server Azure ad Connect, sostituendo <AAD Connector Name> con il nome del connettore specifico di ambiente:

`Set-ADSyncAADCompanyFeature -ConnectorName "<AAD Connector name>" -ForcePasswordResetOnLogonFeature $true`

Per determinare il nome del connettore, è possibile usare il comando seguente:

`(Get-ADSyncConnector | where{$_.ListName -eq "Windows Azure Active Directory (Microsoft)"}).Name`

Avvertenza: se si impone a un utente di modificare la password all'accesso successivo, è necessario modificare la password nello stesso momento.  AD Connect non preleverà il flag di modifica Force password da solo, ma è supplementare per la modifica della password rilevata che si verifica durante la sincronizzazione degli hash delle password.

> [!CAUTION]
> Se non si Abilita la reimpostazione della password self-service (SSPR) in Azure AD gli utenti avranno un'esperienza confusa quando reimpostano la password in Azure AD e quindi tenteranno di accedere Active Directory con la nuova password, perché la nuova password non è valida in Active Directory . È consigliabile usare questa funzionalità solo quando SSPR e il writeback delle password sono abilitati nel tenant.

> [!NOTE]
> Questa funzionalità è disponibile in anteprima pubblica in questo momento.

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

## <a name="password-hash-sync-process-for-azure-ad-domain-services"></a>Processo di sincronizzazione dell'hash delle password per Azure AD Domain Services

Se si usa Azure AD Domain Services per fornire l'autenticazione legacy per le applicazioni e i servizi che devono usare Keberos, LDAP o NTLM, alcuni processi aggiuntivi fanno parte del flusso di sincronizzazione dell'hash delle password. Azure AD Connect usa il processo aggiuntivo seguente per sincronizzare gli hash delle password con Azure AD da usare nei Azure AD Domain Services:

> [!IMPORTANT]
> Azure AD Connect sincronizza solo gli hash delle password legacy quando si Abilita Azure AD DS per il tenant di Azure AD. La procedura seguente non viene usata se si usa solo Azure AD Connect per sincronizzare un ambiente Active Directory Domain Services locale con Azure AD.
>
> Se le applicazioni legacy non usano l'autenticazione NTLM o binding semplici LDAP, è consigliabile disabilitare la sincronizzazione dell'hash delle password NTLM per Azure AD DS. Per altre informazioni, vedere disabilitare i pacchetti di [crittografia vulnerabili e la sincronizzazione degli hash delle credenziali NTLM](../../active-directory-domain-services/secure-your-domain.md).

1. Azure AD Connect recupera la chiave pubblica per l'istanza del tenant di Azure AD Domain Services.
1. Quando un utente modifica la password, il controller di dominio locale archivia il risultato della modifica della password (hash) in due attributi:
    * *unicodePwd* per l'hash della password NTLM.
    * *supplementalCredentials* per l'hash della password Kerberos.
1. Azure AD Connect rileva le modifiche della password tramite il canale di replica della directory (le modifiche dell'attributo che devono essere replicate in altri controller di dominio).
1. Per ogni utente la cui password è stata modificata, Azure AD Connect esegue i passaggi seguenti:
    * Genera una chiave simmetrica casuale AES a 256 bit.
    * Genera un vettore di inizializzazione casuale necessario per il primo ciclo di crittografia.
    * Estrae gli hash delle password Kerberos dagli attributi *supplementalCredentials* .
    * Verifica l'impostazione di *SyncNtlmPasswords* di configurazione della sicurezza del Azure ad Domain Services.
        * Se questa impostazione è disabilitata, genera un hash NTLM casuale ad alta entropia (diverso dalla password dell'utente). Questo hash viene quindi combinato con gli hash della password Kerberos esatti dall'attributo *supplementalCrendetials* in una struttura di dati.
        * Se abilitata, combina il valore dell'attributo *unicodePwd* con gli hash della password Kerberos estratti dall'attributo *supplementalCredentials* in una struttura di dati.
    * Crittografa la singola struttura di dati utilizzando la chiave simmetrica AES.
    * Crittografa la chiave simmetrica AES usando la chiave pubblica Azure AD Domain Services del tenant.
1. Azure AD Connect trasmette la chiave simmetrica AES crittografata, la struttura dei dati crittografati contenente gli hash delle password e il vettore di inizializzazione per Azure AD.
1. Azure AD archivia la chiave simmetrica AES crittografata, la struttura dei dati crittografati e il vettore di inizializzazione per l'utente.
1. Azure AD inserisce la chiave simmetrica AES crittografata, la struttura dei dati crittografati e il vettore di inizializzazione usando un meccanismo di sincronizzazione interno su una sessione HTTP crittografata per Azure AD Domain Services.
1. Azure AD Domain Services recupera la chiave privata per l'istanza del tenant da Azure Key Vault.
1. Per ogni set di dati crittografato (che rappresenta la modifica della password di un singolo utente), Azure AD Domain Services esegue quindi i passaggi seguenti:
    * Usa la chiave privata per decrittografare la chiave simmetrica AES.
    * Usa la chiave simmetrica AES con il vettore di inizializzazione per decrittografare la struttura dei dati crittografati che contiene gli hash delle password.
    * Scrive gli hash della password Kerberos ricevuti dal controller di dominio Azure AD Domain Services. Gli hash vengono salvati nell'attributo *supplementalCredentials* dell'oggetto utente crittografato con la chiave pubblica del controller di dominio Azure ad Domain Services.
    * Azure AD Domain Services scrive l'hash della password NTLM ricevuto per il controller di dominio Azure AD Domain Services. L'hash viene salvato nell'attributo *unicodePwd* dell'oggetto utente crittografato con la chiave pubblica del controller di dominio Azure ad Domain Services.

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
