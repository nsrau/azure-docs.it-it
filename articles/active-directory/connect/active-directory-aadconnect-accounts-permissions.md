---
title: Account e autorizzazioni di Azure AD Connect | Documentazione Microsoft
description: "Questo argomento descrive gli account usati e creati nonché le autorizzazioni necessarie."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: b93e595b-354a-479d-85ec-a95553dd9cc2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: b2baa9ea093a36cadb2251bbd1f4390552d8ec0e


---
# <a name="azure-ad-connect-accounts-and-permissions"></a>Azure AD Connect: account e autorizzazioni
L'installazione guidata di Azure AD Connect offre due percorsi diversi:

* Mediante le impostazioni rapide la procedura guidata richiede più privilegi per semplificare la definizione della configurazione, senza richiedere agli utenti di creare o configurare separatamente le autorizzazioni.
* Mediante le impostazioni personalizzate, la procedura guidata offre un maggior numero di scelte e opzioni, ma esistono alcune situazioni in cui è necessario assicurarsi manualmente di disporre delle autorizzazioni corrette.

## <a name="related-documentation"></a>Documentazione correlata
Se non è stata letta la documentazione in [Integrazione delle identità locali con Azure Active Directory](../active-directory-aadconnect.md), la tabella seguente fornisce collegamenti ad argomenti correlati.

| Argomento |
| --- | --- |
| Eseguire l'installazione con le Impostazioni rapide |
| Eseguire l'installazione mediante le impostazioni personalizzate |
| Aggiornamento da DirSync |

## <a name="express-settings-installation"></a>Installazione mediante le impostazioni rapide
Nell'installazione guidata mediante le impostazioni rapide vengono richieste le credenziali di amministratore dell'organizzazione di Servizi di dominio Active Directory, in modo che l'istanza locale di Active Directory possa essere configurata con le autorizzazioni richieste per Azure AD Connect. Se si esegue l'aggiornamento da DirSync, le credenziali di amministratore dell'organizzazione di Servizi di dominio Active Directory vengono usate per reimpostare la password per l'account usato da DirSync. Sono necessarie anche le credenziali di amministratore globale di Azure AD.

| Pagina della procedura guidata | Credenziali raccolte | Autorizzazioni necessarie | Utilizzo |
| --- | --- | --- | --- |
| N/D |Utente che esegue l'installazione guidata |Amministratore del server locale |<li>Creazione dell'account locale usato come [account del servizio del motore di sincronizzazione](#azure-ad-connect-sync-service-account). |
| Connessione ad Azure AD |Credenziali di directory di Azure AD |Ruolo di amministratore in Azure AD |<li>Abilitazione della sincronizzazione nella directory di Azure AD.</li>  <li>Creazione dell'[account Azure AD](#azure-ad-service-account) utilizzato per le operazioni di sincronizzazione ricorrenti in Azure AD.</li> |
| Connettersi ad AD DS |Credenziali Active Directory locali |Membro del gruppo Enterprise Admins (EA) in Active Directory |<li>Creazione di un [account](#active-directory-account) in Active Directory e concessione delle relative autorizzazioni. L'account creato viene usato per leggere e scrivere informazioni di directory durante la sincronizzazione.</li> |

### <a name="enterprise-admin-credentials"></a>Credenziali di amministratore dell'organizzazione
Queste credenziali vengono usate solo durante l'installazione e dopo il completamento di quest'ultima. È l'amministratore dell'organizzazione, non l'amministratore di dominio, a garantire che le autorizzazioni in Active Directory possano essere impostate in tutti i domini.

### <a name="global-admin-credentials"></a>Credenziali di amministratore globale
Queste credenziali vengono usate solo durante l'installazione e non dopo il completamento di quest'ultima. Vengono usate per creare l'[account Azure AD](#azure-ad-service-account) utile per sincronizzare le modifiche apportate ad Azure AD. e per abilitare la sincronizzazione come funzionalità in Azure AD.

### <a name="permissions-for-the-created-ad-ds-account-for-express-settings"></a>Autorizzazioni per gli account creati di Servizi di dominio Active Directory per le impostazioni rapide
L' [account](#active-directory-account) creato per la lettura e la scrittura in Servizi di dominio Active Directory ha le autorizzazioni seguenti se creato tramite impostazioni rapide:

| Autorizzazione | Usato per |
| --- | --- |
| <li>Replica modifiche directory</li><li>Replica modifiche directory - Tutto |Sincronizzazione delle password |
| Lettura/scrittura di tutte le proprietà - Utente |Importazione ed Exchange ibrido |
| Lettura/scrittura di tutte le proprietà - iNetOrgPerson |Importazione ed Exchange ibrido |
| Lettura/scrittura di tutte le proprietà - Gruppo |Importazione ed Exchange ibrido |
| Lettura/scrittura di tutte le proprietà - Contatto |Importazione ed Exchange ibrido |
| Reimpostazione delle password |Preparazione per l'abilitazione del writeback delle password |

## <a name="custom-settings-installation"></a>Installazione mediante le impostazioni personalizzate
Quando si usano le impostazioni personalizzate, l'account usato per connettersi ad Active Directory deve essere creato prima dell'installazione. Per le autorizzazioni da concedere a questo account, vedere [Creare l'account di Servizi di dominio Active Directory](#create-the-ad-ds-account).

| Pagina della procedura guidata | Credenziali raccolte | Autorizzazioni necessarie | Utilizzo |
| --- | --- | --- | --- |
| N/D |Utente che esegue l'installazione guidata |<li>Amministratore del server locale</li><li>Se si usa una versione completa di SQL Server, l'utente deve essere un amministratore di sistema in SQL</li> |Per impostazione predefinita, crea l'account locale usato come [account del servizio del motore di sincronizzazione](#azure-ad-connect-sync-service-account). L'account viene creato solo quando l'amministratore non specifica un account determinato. |
| Installazione dei servizi di sincronizzazione, opzione Account di servizio |Active Directory o credenziali dell'account utente locale |Autorizzazioni utente concesse mediante l'installazione guidata |Se l'amministratore specifica un account, quest'ultimo viene usato come account del servizio di sincronizzazione. |
| Connessione ad Azure AD |Credenziali di directory di Azure AD |Ruolo di amministratore in Azure AD |<li>Abilitazione della sincronizzazione nella directory di Azure AD.</li>  <li>Creazione dell'[account Azure AD](#azure-ad-service-account) utilizzato per le operazioni di sincronizzazione ricorrenti in Azure AD.</li> |
| Connessione delle directory |Credenziali Active Directory locali per ogni foresta connessa ad Azure AD |Le autorizzazioni variano in base alle funzionalità attivate e sono disponibili in [Creare l'account di Servizi di dominio Active Directory](#create-the-ad-ds-account) |L'account viene usato per leggere e scrivere informazioni di directory durante la sincronizzazione. |
| Server ADFS |Per ogni server nell'elenco, la procedura guidata raccoglie le credenziali, quando le credenziali di accesso dell'utente che esegue la procedura guidata non sono sufficienti per la connessione |Amministratore di dominio |Installazione e configurazione del ruolo del server ADFS. |
| Server Proxy applicazione Web |Per ogni server nell'elenco, la procedura guidata raccoglie le credenziali, quando le credenziali di accesso dell'utente che esegue la procedura guidata non sono sufficienti per la connessione |Amministratore locale nel computer di destinazione |Installazione e configurazione del ruolo del server WAP. |
| Credenziali di attendibilità del proxy |Credenziali di attendibilità del servizio federativo (le credenziali che sono utilizzate dal proxy per richiedere un certificato di attendibilità da FS) |Account di dominio che è un amministratore locale del server ADFS |Registrazione iniziale del certificato di attendibilità di FS-WAP. |
| Pagina Account del servizio ADFS, "Utilizzare un'opzione account utente di dominio" |Credenziali dell'account utente di Active Directory |Utente di dominio |L'account utente di Active Directory di cui vengono fornite le credenziali viene usato come account di accesso del servizio AD FS. |

### <a name="create-the-ad-ds-account"></a>Creare l'account di Servizi di dominio Active Directory
Quando si installa Azure AD Connect, l'account specificato nella pagina **Connessione delle directory** deve essere presente in Active Directory e deve disporre delle autorizzazioni concesse necessarie. L'installazione guidata non verifica che le autorizzazioni e gli eventuali problemi vengano rilevati solo durante la sincronizzazione.

Le autorizzazioni necessarie dipendono dalle funzionalità facoltative abilitate. Se si dispone di più domini, le autorizzazioni devono essere concesse per tutti i domini nella foresta. Se queste funzionalità non vengono abilitate, sono sufficienti le autorizzazioni dell' **utente di dominio** predefinite.

| Funzionalità | Autorizzazioni |
| --- | --- |
| Sincronizzazione delle password |<li>Replica modifiche directory</li>  <li>Replica modifiche directory - Tutto |
| Distribuzione ibrida di Exchange |Autorizzazioni di scrittura per gli attributi documentati in [Writeback della distribuzione ibrida Exchange](../active-directory-aadconnectsync-attributes-synchronized.md#exchange-hybrid-writeback) per utenti, gruppi e contatti. |
| Writeback delle password |Autorizzazioni di scrittura per gli attributi documentati in [Introduzione alla gestione delle password](../active-directory-passwords-getting-started.md#step-4-set-up-the-appropriate-active-directory-permissions) per gli utenti. |
| Writeback dispositivi |Autorizzazioni concesse con uno script di PowerShell come descritto in [Writeback dei dispositivi](../active-directory-aadconnect-feature-device-writeback.md). |
| Writeback dei gruppi |Lettura, creazione, aggiornamento ed eliminazione di oggetti di gruppo nell'unità organizzativa in cui devono trovarsi i gruppi di distribuzione. |

## <a name="upgrade"></a>Aggiornamento
Quando si aggiorna da una versione di Azure AD Connect a una nuova versione, è necessario avere le autorizzazioni seguenti:

| Entità | Autorizzazioni necessarie | Utilizzo |
| --- | --- | --- |
| Utente che esegue l'installazione guidata |Amministratore del server locale |Aggiornare i file binari. |
| Utente che esegue l'installazione guidata |Membro di ADSyncAdmins |Apportare modifiche alle regole di sincronizzazione e ad altre configurazioni. |
| Utente che esegue l'installazione guidata |Se si utilizza un server SQL completo: DBO (o simile) del database del motore di sincronizzazione |Apportare modifiche a livello di database, ad esempio l'aggiornamento di tabelle con nuove colonne. |

## <a name="more-about-the-created-accounts"></a>Altre informazioni sugli account creati
### <a name="active-directory-account"></a>Account Active Directory
Se si usano le impostazioni rapide, viene creato un account in Active Directory usato per la sincronizzazione. L'account creato si trova nel dominio radice della foresta nel contenitore degli utenti e il relativo nome con prefisso è **MSOL_**. L'account viene creato con una password lunga e complessa priva di scadenza. Se nel dominio sono presenti criteri di password, assicurarsi che per tale account siano consentite password lunghe e complesse.

![Account AD](./media/active-directory-aadconnect-accounts-permissions/adsyncserviceaccount.png)

### <a name="azure-ad-connect-sync-service-accounts"></a>Account del servizio di sincronizzazione Azure AD Connect
Mediante l'installazione guidata viene creato un account di servizio locale (a meno che non si specifichi l'account da usare nelle impostazioni personalizzate). L'account, preceduto da **AAD_**, viene usato per l'esecuzione del servizio di sincronizzazione effettivo. Se si installa Azure AD Connect in un Controller di dominio, l'account viene creato nel dominio. Se si usa un server remoto che esegue SQL Server o un proxy che richiede l'autenticazione, l'account del servizio **AAD_** deve trovarsi nel dominio.

![Account del servizio di sincronizzazione](./media/active-directory-aadconnect-accounts-permissions/syncserviceaccount.png)

L'account viene creato con una password lunga e complessa priva di scadenza.

Questo account viene usato per archiviare in modo sicuro le password per gli altri account. Le password di questi altri account vengono archiviate crittografate nel database. Le chiavi private per le chiavi di crittografia sono protette tramite la crittografia a chiave segreta dei servizi di crittografia con Data Protection API (DPAPI) di Windows. Non reimpostare la password nell'account del servizio, perché Windows elimina le chiavi di crittografia per motivi di sicurezza.

Se si usa una versione completa di SQL Server, l'account del servizio corrisponde al DBO del database creato per il motore di sincronizzazione. Il servizio non funzionerà come previsto con tutte le altre autorizzazioni. Viene inoltre creato l'accesso a SQL.

L'account concede inoltre le autorizzazioni a file, chiavi del Registro di sistema e altri oggetti correlati al motore di sincronizzazione.

### <a name="azure-ad-service-account"></a>Account del servizio Azure AD
In Azure AD viene creato un account per l'uso con il servizio di sincronizzazione. Questo account può essere identificato in base al relativo nome visualizzato.

![Account AD](./media/active-directory-aadconnect-accounts-permissions/aadsyncserviceaccount.png)

Il nome del server in cui viene usato l'account può essere identificato nella seconda parte del nome utente. Nell'immagine, il nome del server è FABRIKAMCON. Se si dispone di server di gestione temporanea, ogni server avrà il proprio account. In Azure AD esiste un limite di 10 account del servizio di sincronizzazione.

L'account del servizio viene creato con una password lunga e complessa priva di scadenza. Viene concesso un ruolo speciale per gli **account di sincronizzazione della directory** che dispone solo delle autorizzazioni per eseguire attività di sincronizzazione della directory. Questo specifico ruolo integrato non può essere concesso all'esterno della procedura di Azure AD Connect e il portale di Azure mostra questo account con il ruolo **utente**.

![Ruolo dell'account AD](./media/active-directory-aadconnect-accounts-permissions/aadsyncserviceaccountrole.png)

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](../active-directory-aadconnect.md).




<!--HONumber=Nov16_HO3-->


