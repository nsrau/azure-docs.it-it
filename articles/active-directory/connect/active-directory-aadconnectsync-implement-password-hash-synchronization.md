---
title: Implementare la sincronizzazione dell'hash delle password con il servizio di sincronizzazione Azure AD Connect | Microsoft Docs
description: Contiene informazioni sul funzionamento e sulla configurazione della sincronizzazione dell'hash delle password.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 05f16c3e-9d23-45dc-afca-3d0fa9dbf501
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: af9a2e47a5e91d9b32a787f8f40ba06b5a1596e7
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2018
ms.locfileid: "39265047"
---
# <a name="implement-password-hash-synchronization-with-azure-ad-connect-sync"></a>Implementare la sincronizzazione dell'hash delle password con il servizio di sincronizzazione Azure AD Connect
Questo articolo contiene le informazioni necessarie per sincronizzare le password utente da un'istanza di Active Directory locale a un'istanza di Azure Active Directory (Azure AD) basata sul cloud.

## <a name="what-is-password-hash-synchronization"></a>Informazioni sulla sincronizzazione dell'hash delle password
La probabilità di restare bloccati e di non poter portare a termine il proprio lavoro per aver dimenticato una password è proporzionale al numero di password diverse che è necessario ricordare. Più password si devono ricordare, maggiore sarà la probabilità di dimenticarne una. Le domande e le chiamate relative alla reimpostazione della password e ad altri problemi correlati impegnano la maggior parte delle risorse di supporto tecnico.

La sincronizzazione dell'hash delle password è una funzionalità usata per sincronizzare le password utente da un'istanza di Active Directory locale a un'istanza di Azure AD basata sul cloud.
Usare questa funzionalità per accedere a servizi di Azure AD come Office 365, Microsoft Intune, CRM Online e Azure Active Directory Domain Services. Per accedere al servizio, è necessario usare la stessa password usata per accedere all'istanza di Active Directory locale.

![Cos'è Azure AD Connect](./media/active-directory-aadconnectsync-implement-password-hash-synchronization/arch1.png)

La riduzione del numero di password permette agli utenti di mantenerne una sola. La sincronizzazione dell'hash delle password permette di:

* Migliorare la produttività degli utenti.
* Ridurre i costi del supporto tecnico.  

Inoltre, se si decide di usare la [federazione con Active Directory Federation Services (AD FS)](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect), è possibile configurare facoltativamente la sincronizzazione dell'hash delle password come backup in caso di errore dell'infrastruttura di AD FS.

La sincronizzazione dell'hash delle password è un'estensione della funzionalità di sincronizzazione della directory implementata dal relativo servizio Azure AD Connect. Per usare la sincronizzazione dell'hash delle password nel proprio ambiente, è necessario:

* Installare Azure AD Connect.  
* Configurare la sincronizzazione della directory tra l'istanza di Active Directory locale e l'istanza di Azure Active Directory.
* Abilitare la sincronizzazione dell'hash delle password.

Per altri dettagli, vedere [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).

> [!NOTE]
> Per altre informazioni sulla configurazione di Azure Active Directory Domain Services per FIPS e la sincronizzazione dell'hash delle password, vedere "Sincronizzazione dell'hash delle password e FIPS" più avanti in questo articolo.
>
>

## <a name="how-password-hash-synchronization-works"></a>Funzionamento della sincronizzazione dell'hash delle password
Active Directory Domain Services archivia le password sotto forma di valore hash dell'effettiva password utente. Un valore hash è il risultato di una funzione matematica unidirezionale, chiamata *algoritmo di hash*. Non esiste un metodo per ripristinare la versione in testo normale di una password dal risultato di una funzione unidirezionale. Non è possibile usare l'hash della password per accedere alla rete locale.

Per sincronizzare la password, il servizio di sincronizzazione Azure AD Connect estrae l'hash della password dall'istanza di Active Directory locale. All'hash della password viene applicata un'elaborazione di sicurezza aggiuntiva prima della sincronizzazione con il servizio di autenticazione di Azure Active Directory. Le password vengono sincronizzate per ogni singolo utente e in ordine cronologico.

L'effettivo flusso di dati del processo di sincronizzazione dell'hash delle password è simile alla sincronizzazione dei dati utente come il nome visualizzato o gli indirizzi di posta elettronica. Le password vengono tuttavia sincronizzate con una frequenza maggiore rispetto alla finestra di sincronizzazione standard della directory per altri attributi. Il processo di sincronizzazione dell'hash delle password viene eseguito ogni 2 minuti. Non è possibile modificare la frequenza del processo. Se si sincronizza una password, la password sincronizzata sovrascrive quella esistente nel cloud.

Quando si abilita la funzionalità di sincronizzazione dell'hash delle password per la prima volta, viene eseguita una sincronizzazione iniziale delle password di tutti gli utenti inclusi nell'ambito. Non è possibile definire in modo esplicito un subset di password utente da sincronizzare.

Quando si modifica una password locale, la password aggiornata viene sincronizzata, spesso in pochi minuti.
In caso di sincronizzazioni non riuscite, la funzionalità di sincronizzazione dell'hash delle password esegue automaticamente nuovi tentativi di sincronizzazione. Se si verifica un errore durante un tentativo di sincronizzazione di una password, viene registrato un errore nel visualizzatore eventi.

La sincronizzazione di una password non ha alcun impatto sull'utente attualmente connesso.
Se si modifica una password sincronizzata dopo aver eseguito l'accesso a un servizio cloud, la sessione corrente del servizio cloud non è immediatamente interessata. Tuttavia, quando il servizio cloud richiede di nuovo l'autenticazione, è necessario specificare la nuova password.

Un utente deve immettere le proprie credenziali aziendali una seconda volta per l'autenticazione in Azure AD, indipendentemente dal fatto che abbia effettuato l'accesso alla rete aziendale. Questo modello può essere ridotto al minimo se al momento dell'accesso l'utente seleziona la casella di controllo Mantieni l'accesso. La selezione di questa opzione imposta un cookie di sessione che permette di ignorare l'autenticazione per 180 giorni. Il comportamento dell'opzione Mantieni l'accesso può essere abilitato o disabilitato dall'amministratore di Azure AD. Inoltre, è possibile ridurre le richieste di password attivando l'[accesso Single Sign-On facile](active-directory-aadconnect-sso.md) che consente agli utenti di eseguire l'accesso automaticamente dai dispositivi di proprietà dell'azienda connessi alla rete aziendale.

> [!NOTE]
> La sincronizzazione delle password è supportata solo per l'utente del tipo di oggetto in Active Directory. Non è supportata per il tipo di oggetto iNetOrgPerson.

### <a name="detailed-description-of-how-password-hash-synchronization-works"></a>Descrizione dettagliata del funzionamento della sincronizzazione dell'hash delle password
Di seguito viene descritto in dettaglio il funzionamento della sincronizzazione dell'hash delle password tra Active Directory e Azure AD.

![Flusso dettagliato della sincronizzazione password](./media/active-directory-aadconnectsync-implement-password-hash-synchronization/arch3.png)


1. Ogni due minuti, l'agente di sincronizzazione dell'hash delle password nel server di AD Connect richiede gli hash delle password archiviate (attributo unicodePwd) da un controller di dominio tramite il protocollo di replica [MS-DRSR](https://msdn.microsoft.com/library/cc228086.aspx) standard usato per sincronizzare i dati tra i controller di dominio. L'account di servizio deve disporre delle autorizzazioni di Azure AD Replica modifiche directory e Replica modifiche directory - Tutto (concesse per impostazione predefinita in fase di installazione) per ottenere gli hash delle password.
2. Prima dell'invio, il controller di dominio crittografa l'hash della password MD4 tramite una chiave corrispondente a un hash [MD5](http://www.rfc-editor.org/rfc/rfc1321.txt) della chiave di sessione RPC e a un valore salt. Invia quindi il risultato dell'agente di sincronizzazione dell'hash delle password tramite RPC. Il controller di dominio passa inoltre il valore salt all'agente di sincronizzazione usando il protocollo di replica del controller di dominio, in modo che l'agente sia in grado di decrittografare la busta.
3.  Dopo aver ottenuto la busta crittografata, l'agente di sincronizzazione dell'hash delle password usa [MD5CryptoServiceProvider](https://msdn.microsoft.com/library/System.Security.Cryptography.MD5CryptoServiceProvider.aspx) e il valore salt per generare una chiave per decrittografare i dati ricevuti riportandoli nel formato MD4 originale. In nessun momento l'agente di sincronizzazione dell'hash delle password ha accesso alla password non crittografata. L'uso di MD5 da parte dell'agente di sincronizzazione dell'hash delle password è esclusivamente per la compatibilità del protocollo di replica con il controller di dominio e viene usato solo in locale tra il controller di dominio e l'agente di sincronizzazione dell'hash delle password.
4.  L'agente di sincronizzazione dell'hash delle password espande l'hash della password binario a 16 byte in 64 byte convertendo per prima cosa l'hash in una stringa esadecimale a 32 byte e quindi riconvertendo questa stessa stringa in formato binario con la codifica UTF-16.
5.  L'agente di sincronizzazione dell'hash delle password aggiunge un valore salt per utente lungo 10 byte al file binario a 64 byte per proteggere ulteriormente l'hash originale.
6.  L'agente di sincronizzazione dell'hash delle password combina quindi l'hash MD4 con il valore salt per utente e li inserisce nella funzione [PBKDF2](https://www.ietf.org/rfc/rfc2898.txt). Vengono usate 1000 iterazioni dell'algoritmo di hash con chiave [HMAC-SHA256](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx). 
7.  L'agente di sincronizzazione dell'hash delle password concatena all'hash a 32 byte risultante sia il valore salt per utente sia il numero di iterazioni SHA256 (per l'uso con Azure AD) e quindi trasmette la stringa da Azure AD Connect ad Azure AD tramite SSL.</br> 
8.  Quando un utente prova ad accedere ad Azure AD e immette la password, la password viene eseguita tramite lo stesso processo MD4 + valore salt + PBKDF2 + HMAC-SHA256. Se l'hash risultante corrisponde all'hash archiviato in Azure AD, l'utente ha immesso la password corretta e viene autenticato. 

>[!Note] 
>L'hash MD4 originale non viene trasmesso ad Azure AD. Viene invece trasmesso l'hash SHA256 dell'hash MD4 originale. Di conseguenza, se si riesce a ottenere l'hash archiviato in Azure AD, questo non può essere usato in un attacco di tipo Pass-the-Hash locale.

### <a name="how-password-hash-synchronization-works-with-azure-active-directory-domain-services"></a>Funzionamento della sincronizzazione dell'hash delle password con Azure Active Directory Domain Services
È possibile usare la funzionalità di sincronizzazione dell'hash delle password anche per sincronizzare le password locali con [Azure Active Directory Domain Services](../../active-directory-domain-services/active-directory-ds-overview.md). In questo scenario l'istanza di Azure Active Directory Domain Services autentica gli utenti nel cloud con tutti i metodi disponibili nell'istanza di Active Directory locale. L'esperienza di questo scenario è simile all'uso dell'Utilità di migrazione ad Active Directory (ADMT) in un ambiente locale.

### <a name="security-considerations"></a>Considerazioni relative alla sicurezza
Quando si sincronizzano le password, la versione in testo normale della password non viene esposta alla funzionalità di sincronizzazione dell'hash delle password, né ad Azure AD o a qualsiasi servizio associato.

L'autenticazione utente viene eseguita in Azure AD e non nell'istanza di Active Directory dell'organizzazione. Se l'organizzazione teme che la trasmissione dei dati relativi alle password all'esterno dell'organizzazione possa creare problemi, tenere presente che i dati delle password SHA256 archiviati in Azure AD, ovvero un hash dell'hash MD4 originale, sono notevolmente più sicuri rispetto a quello che viene archiviato in Active Directory. Inoltre, dal momento che questo hash SHA256 non può essere decrittografato, non potrà essere ripresentato nell'ambiente Active Directory dell'organizzazione come una password utente valida in un attacco di tipo Pass-the-Hash.

### <a name="password-policy-considerations"></a>Considerazioni relative ai criteri password
L'abilitazione della sincronizzazione dell'hash delle password influisce su due tipi di criteri password:

* Criteri di complessità delle password
* Criteri di scadenza delle password

#### <a name="password-complexity-policy"></a>Criteri di complessità delle password  
Quando la sincronizzazione dell'hash delle password è abilitata, i criteri di complessità delle password presenti nell'istanza di Active Directory locale sostituiscono i criteri di complessità definiti nel cloud per gli utenti sincronizzati. È possibile usare tutte le password valide dell'istanza di Active Directory locale per accedere ai servizi Azure AD.

> [!NOTE]
> Le password degli utenti create direttamente nel cloud restano soggette ai criteri password definiti nel cloud.

#### <a name="password-expiration-policy"></a>Criteri di scadenza delle password  
Se un utente è incluso nell'ambito della sincronizzazione dell'hash delle password, la password dell'account cloud viene impostata su *Non scade mai*.

È possibile continuare ad accedere ai servizi cloud usando una password sincronizzata che è in realtà scaduta nell'ambiente locale. La password cloud viene aggiornata alla modifica successiva della password nell'ambiente locale.

#### <a name="account-expiration"></a>Scadenza dell'account
Se l'organizzazione usa l'attributo accountExpires come parte della gestione degli account utente, tenere presente che questo attributo non viene sincronizzato con Azure AD. Di conseguenza, un account Active Directory scaduto in un ambiente configurato per la sincronizzazione dell'hash delle password continuerà a essere attivo in Azure AD. Se l'account è scaduto, è consigliabile fare in modo che un'azione del flusso di lavoro attivi uno script di PowerShell per disabilitare l'account Azure AD dell'utente (usare il cmdlet [Set-AzureADUser](https://docs.microsoft.com/powershell/module/azuread/set-azureaduser?view=azureadps-2.0)). Al contrario, quando l'account è attivato, è necessario attivare anche l'istanza di Azure AD.

### <a name="overwrite-synchronized-passwords"></a>Sovrascrivere password sincronizzate
Un amministratore può reimpostare manualmente la password usando Windows PowerShell.

In questo caso, la password sincronizzata viene sostituita dalla nuova password, a cui si applicano tutti i criteri password definiti nel cloud.

Se si modifica di nuovo la password locale, la nuova password viene sincronizzata nel cloud e sostituisce la password aggiornata manualmente.

La sincronizzazione di una password non ha alcun impatto sull'utente di Azure connesso. Se si modifica una password sincronizzata dopo aver eseguito l'accesso a un servizio cloud, questa operazione non ha un impatto immediato sulla sessione corrente del servizio cloud. L'opzione Mantieni l'accesso estende la durata in base a questa differenza. Quando il servizio cloud richiede di nuovo l'autenticazione, è necessario specificare la nuova password.

### <a name="additional-advantages"></a>Vantaggi aggiuntivi

- In genere, la sincronizzazione dell'hash delle password è più semplice da implementare rispetto a un servizio federativo. Non richiede altri server e permette di eliminare la dipendenza da un servizio federativo a disponibilità elevata per l'autenticazione degli utenti.
- La sincronizzazione dell'hash delle password può anche essere abilitata in aggiunta alla federazione. Può inoltre essere usata come fallback in caso di interruzione del servizio federativo.

## <a name="enable-password-hash-synchronization"></a>Abilitare la sincronizzazione dell'hash delle password

>[!IMPORTANT]
>Se si esegue la migrazione da AD FS (o da altre tecnologia federative) per la sincronizzazione dell'hash delle password, si consiglia vivamente di seguire la guida per la distribuzione dettagliata pubblicata [qui](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Password%20Hash%20Synchronization.docx).

Quando si installa Azure AD Connect usando l'opzione **Impostazioni rapide**, la sincronizzazione dell'hash delle password viene abilitata automaticamente. Per altre informazioni dettagliate, vedere [Introduzione alle impostazioni rapide per Azure AD Connect](active-directory-aadconnect-get-started-express.md).

Se si usano impostazioni personalizzate quando si installa Azure AD Connect, la sincronizzazione dell'hash delle password è disponibile nella pagina di accesso dell'utente. Per altre informazioni dettagliate, vedere [Installazione personalizzata di Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

![Abilitazione della sincronizzazione dell'hash delle password](./media/active-directory-aadconnect-get-started-custom/usersignin2.png)

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

Per informazioni sulla sicurezza e su FIPS, vedere [AAD Password Sync, encryption and FIPS compliance](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/) (Sincronizzazione password, crittografia e conformità con FIPS di Azure AD).

## <a name="troubleshoot-password-hash-synchronization"></a>Risolvere i problemi di sincronizzazione dell'hash delle password
In caso di problemi di sincronizzazione dell'hash delle password, vedere [Risolvere i problemi di sincronizzazione dell'hash delle password](active-directory-aadconnectsync-troubleshoot-password-hash-synchronization.md).

## <a name="next-steps"></a>Passaggi successivi
* [Servizio di sincronizzazione Azure AD Connect: personalizzazione delle opzioni di sincronizzazione](active-directory-aadconnectsync-whatis.md)
* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)
* [Ottenere un piano di distribuzione dettagliato per la migrazione dal file system distribuito di Azure alla sincronizzazione dell'hash delle password](https://aka.ms/authenticationDeploymentPlan)
