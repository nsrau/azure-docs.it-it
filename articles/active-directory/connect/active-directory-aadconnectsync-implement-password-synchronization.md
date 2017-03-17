---
title: Implementazione della sincronizzazione password con il servizio di sincronizzazione Azure AD Connect | Documentazione Microsoft
description: Fornisce informazioni sul funzionamento e sull&quot;abilitazione della sincronizzazione password.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 05f16c3e-9d23-45dc-afca-3d0fa9dbf501
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/13/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 64b6447608ecdd9bdd2b307f4bff2cae43a4b13f
ms.openlocfilehash: cff066ff2943443749ee8eb2ef71c7ca93bb829c
ms.lasthandoff: 03/01/2017


---
# <a name="implementing-password-synchronization-with-azure-ad-connect-sync"></a>Implementazione della sincronizzazione password con il servizio di sincronizzazione Azure AD Connect
Questo argomento contiene le indicazioni necessarie per sincronizzare le password utente da un'istanza di Active Directory (AD) locale a un'istanza di Azure Active Directory basata sul cloud (Azure AD).

## <a name="what-is-password-synchronization"></a>Informazioni sulla sincronizzazione password
La probabilità di rimanere bloccati e non poter portare a termine il proprio lavoro perché si ha dimenticato una password è proporzionale al numero di password diverse che si devono ricordare. Più password si devono ricordare, maggiore sarà la probabilità di dimenticarne una. Le domande e le chiamate relative alla reimpostazione della password e ad altri problemi correlati impegnano la maggior parte delle risorse di supporto tecnico.

La sincronizzazione password è una funzionalità che permette di sincronizzare le password utente da un'istanza di Active Directory locale a un'istanza di Azure Active Directory (Azure AD) basata sul cloud.
Questa funzionalità consente di accedere ai servizi Azure Active Directory, come Office 365, Microsoft Intune, CRM Online e Servizi di dominio Azure AD, usando la stessa password che si usa per accedere all'istanza locale di Active Directory.

![Cos'è Azure AD Connect](./media/active-directory-aadconnectsync-implement-password-synchronization/arch1.png)

Poiché riduce il numero di password necessarie agli utenti a una sola, la sincronizzazione password consente di:

* Migliorare la produttività degli utenti
* Ridurre i costi del supporto tecnico  

Se si decide di usare la [**federazione con AD FS**](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect), è anche possibile abilitare la sincronizzazione password come backup in caso di errore dell'infrastruttura AD FS.

La sincronizzazione password è un'estensione della funzionalità di sincronizzazione della directory implementata dal relativo servizio Azure AD Connect. Per usare la sincronizzazione password nel proprio ambiente, è necessario:

* Installare Azure AD Connect  
* Configurare la sincronizzazione delle directory tra l'istanza di Active Directory locale e Azure Active Directory
* Abilitare la sincronizzazione password

Per altre informazioni, vedere [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)

> [!NOTE]
> Per altre informazioni dettagliate sui Servizi di dominio Active Directory configurati per FIPS e per la sincronizzazione password, vedere [Sincronizzazione password e FIPS](#password-synchronization-and-fips)
>
>

## <a name="how-password-synchronization-works"></a>Funzionamento della sincronizzazione password
Servizi di dominio Active Directory archivia le password come rappresentazione del valore hash della password utente effettiva. Un valore hash è il risultato di una funzione matematica unidirezionale, detta "*algoritmo hash*". Non esiste un metodo per ripristinare la versione in testo normale di una password dal risultato di una funzione unidirezionale. Non è possibile usare l'hash della password per accedere alla rete locale.

Per sincronizzare la password, il servizio di sincronizzazione Azure AD Connect estrae l'hash della password dall'istanza di Active Directory locale. All'hash della password viene applicata un'elaborazione di sicurezza aggiuntiva prima della sincronizzazione con il servizio di autenticazione di Azure Active Directory. Le password vengono sincronizzate per ogni singolo utente e in ordine cronologico.

L'effettivo flusso di dati del processo di sincronizzazione password è simile alla sincronizzazione degli dati utente come il nome visualizzato o gli indirizzi di posta elettronica. Le password vengono tuttavia sincronizzate con una frequenza maggiore rispetto alla finestra di sincronizzazione standard della directory per altri attributi. Il processo di sincronizzazione password viene eseguito ogni 2 minuti. Non è possibile modificare la frequenza del processo. Se si sincronizza una password, la password sincronizzata sovrascrive quella esistente nel cloud.

Quando si abilita la funzionalità di sincronizzazione password per la prima volta, viene eseguita una sincronizzazione iniziale delle password di tutti gli utenti inclusi nell'ambito. Non è possibile definire in modo esplicito un subset delle password utente da sincronizzare.

Quando si modifica una password locale, la password aggiornata viene sincronizzata, spesso in pochi minuti.
In caso di sincronizzazioni non riuscite, la funzionalità di sincronizzazione password esegue anche automaticamente nuovi tentativi di sincronizzazione. Se si verifica un errore durante un tentativo di sincronizzazione di una password, viene registrato un errore nel visualizzatore eventi.

La sincronizzazione di una password non incide in alcun modo sull'utente attualmente connesso.
Se si modifica una password sincronizzata dopo aver eseguito l'accesso a un servizio cloud, la sessione corrente del servizio cloud non è immediatamente interessata. Tuttavia, quando il servizio cloud richiede di nuovo l'autenticazione, è necessario specificare la nuova password.

> [!NOTE]
> La sincronizzazione delle password è supportata solo per l'utente del tipo di oggetto in Active Directory. Non è supportata per il tipo di oggetto iNetOrgPerson.
>
>

### <a name="how-password-synchronization-works-with-azure-ad-domain-services"></a>Funzionamento della sincronizzazione password con Servizi di dominio Azure AD
È possibile usare la funzionalità di sincronizzazione password per sincronizzare le password locali con [Servizi di dominio Azure AD](../../active-directory-domain-services/active-directory-ds-overview.md). Questo scenario consente a Servizi di dominio Azure AD di autenticare gli utenti nel cloud con tutti i metodi disponibili nell'istanza di Active Directory locale. L'esperienza di questo scenario è simile all'uso dell'Utilità di migrazione ad Active Directory (ADMT) in un ambiente locale.

### <a name="security-considerations"></a>Considerazioni relative alla sicurezza
Quando si sincronizzano le password, la versione in testo normale della password non viene esposta alla funzionalità di sincronizzazione password, né ad Azure AD o a qualsiasi servizio associato.

Nell'istanza locale di Active Directory non è prevista neppure l'archiviazione della password in un formato con crittografia reversibile. Per la trasmissione tra Active Directory locale e Azure Active Directory viene usato un digest dell'hash della password di Active Directory. Il digest dell'hash della password non può essere usato per accedere alle risorse nell'ambiente locale.

### <a name="password-policy-considerations"></a>Considerazioni relative ai criteri password
L'abilitazione della sincronizzazione password influisce su due tipi di criteri password:

1. Criteri di complessità delle password
2. Criteri di scadenza delle password

**Password complexity policy**  
Quando si abilita la sincronizzazione password, i criteri di complessità delle password presenti nell'istanza locale di Active Directory sostituiscono i criteri di complessità definiti nel cloud per gli utenti sincronizzati. È possibile usare tutte le password valide di Active Directory locale per accedere ai servizi Azure AD.

> [!NOTE]
> Le password degli utenti create direttamente nel cloud restano soggette ai criteri password definiti nel cloud.
>
>

**Password expiration policy**  
Se un utente è incluso nell'ambito della sincronizzazione password, la password dell'account cloud viene impostata su "*Non scade mai*".
È possibile continuare ad accedere ai servizi cloud usando una password sincronizzata che è in realtà scaduta nell'ambiente locale. La password cloud viene aggiornata alla modifica successiva della password nell'ambiente locale.

### <a name="overwriting-synchronized-passwords"></a>Sostituzione delle password sincronizzate
Un amministratore può reimpostare manualmente la password usando Windows PowerShell.

In tal caso, la password sincronizzata viene sostituita dalla nuova password, a cui si applicano tutti i criteri password definiti nel cloud.

Se si modifica nuovamente la password locale, la nuova password viene sincronizzata nel cloud e sostituisce la password aggiornata manualmente.

## <a name="enabling-password-synchronization"></a>Abilitazione della sincronizzazione password
Se si usano le **Impostazioni rapide**quando si installa Azure AD Connect, la sincronizzazione password viene abilitata automaticamente. Per altre informazioni dettagliate, vedere [Introduzione alle impostazioni rapide per Azure AD Connect](active-directory-aadconnect-get-started-express.md).

Se si usano le impostazioni personalizzate quando si installa Azure AD Connect, la sincronizzazione password viene abilitata nella pagina di accesso dell'utente. Per altre informazioni dettagliate, vedere [Installazione personalizzata di Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

![Abilitazione della sincronizzazione password](./media/active-directory-aadconnectsync-implement-password-synchronization/usersignin.png)

### <a name="password-synchronization-and-fips"></a>Sincronizzazione password e FIPS
Se il server è stato bloccato in conformità allo standard FIPS (Federal Information Processing Standard), MD5 è stato disabilitato.

**Per abilitare MD5 per la sincronizzazione password, seguire questa procedura:**

1. Passare a **%programfiles%\Azure AD Sync\Bin**.
2. Aprire **miiserver.exe.config**.
3. Passare al nodo **configuration/runtime** alla fine del file.
4. Aggiungere il nodo seguente: `<enforceFIPSPolicy enabled="false"/>`
5. Salvare le modifiche.

Per riferimento, questo elemento di cattura mostra come apparirà:

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

Per informazioni sulla sicurezza e su FIPS, vedere il post di blog relativo alla [conformità di FIPS, della crittografia e della sincronizzazione password di ADD](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/)

## <a name="troubleshooting-password-synchronization"></a>Risoluzione dei problemi di sincronizzazione password
Se si verificano problemi con la sincronizzazione delle password, vedere [Risolvere i problemi di sincronizzazione delle password](active-directory-aadconnectsync-troubleshoot-password-synchronization.md).

## <a name="next-steps"></a>Passaggi successivi
* [Servizio di sincronizzazione Azure AD Connect: Personalizzazione delle opzioni di sincronizzazione](active-directory-aadconnectsync-whatis.md)
* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)

