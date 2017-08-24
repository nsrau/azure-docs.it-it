---
title: Account e autorizzazioni di Azure AD Connect | Documentazione Microsoft
description: "Questo argomento descrive gli account usati e creati nonché le autorizzazioni necessarie."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.reviewer: cychua
ms.assetid: b93e595b-354a-479d-85ec-a95553dd9cc2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: billmath
ms.translationtype: HT
ms.sourcegitcommit: 54774252780bd4c7627681d805f498909f171857
ms.openlocfilehash: fdd90721b6823c20c1ff27383769bfff24e80eae
ms.contentlocale: it-it
ms.lasthandoff: 07/27/2017

---
# <a name="azure-ad-connect-accounts-and-permissions"></a>Azure AD Connect: account e autorizzazioni
L'installazione guidata di Azure AD Connect offre due percorsi diversi:

* Impostazioni rapide: sono necessari più privilegi.  È possibile eseguire la configurazione con facilità, senza che sia necessario creare utenti o configurare autorizzazioni.
* Impostazioni personalizzate: sono disponibili più opzioni e scelte. In alcune situazioni è tuttavia necessario assicurarsi manualmente di disporre delle autorizzazioni corrette.

## <a name="related-documentation"></a>Documentazione correlata
Se non è stata letta la documentazione in [Integrazione delle identità locali con Azure Active Directory](../active-directory-aadconnect.md), la tabella seguente fornisce collegamenti ad argomenti correlati.

|Argomento |Collegamento|  
| --- | --- |
|Scaricare Azure AD Connect | [Scaricare Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771)|
|Eseguire l'installazione con le Impostazioni rapide | [Installazione rapida di Azure AD Connect](./active-directory-aadconnect-get-started-express.md)|
|Eseguire l'installazione mediante le impostazioni personalizzate | [Installazione personalizzata di Azure AD Connect](./active-directory-aadconnect-get-started-custom.md)|
|Aggiornamento da DirSync | [Aggiornamento dallo strumento di sincronizzazione di Azure AD (DirSync)](./active-directory-aadconnect-dirsync-upgrade-get-started.md)|
|Dopo l'installazione | [Verificare l'installazione e assegnare le licenze ](active-directory-aadconnect-whats-next.md)|

## <a name="express-settings-installation"></a>Installazione mediante le impostazioni rapide
In Impostazioni rapide vengono richieste le credenziali di amministratore dell'organizzazione di Active Directory Domain Services.  Si tratta quindi dell'istanza locale di Active Directory che è possibile configurare con le autorizzazioni necessarie per Azure AD Connect. Se si esegue l'aggiornamento da DirSync, le credenziali di amministratore dell'organizzazione di Servizi di dominio Active Directory vengono usate per reimpostare la password per l'account usato da DirSync. Sono necessarie anche le credenziali di amministratore globale di Azure AD.

| Pagina della procedura guidata | Credenziali raccolte | Autorizzazioni necessarie | Utilizzo |
| --- | --- | --- | --- |
| N/D |Utente che esegue l'installazione guidata |Amministratore del server locale |<li>Creazione dell'account locale usato come [account del servizio del motore di sincronizzazione](#azure-ad-connect-sync-service-account). |
| Connessione ad Azure AD |Credenziali di directory di Azure AD |Ruolo di amministratore in Azure AD |<li>Abilitazione della sincronizzazione nella directory di Azure AD.</li>  <li>Creazione dell'[account Azure AD](#azure-ad-service-account) utilizzato per le operazioni di sincronizzazione ricorrenti in Azure AD.</li> |
| Connettersi ad AD DS |Credenziali Active Directory locali |Membro del gruppo Enterprise Admins (EA) in Active Directory |<li>Creazione di un [account](#active-directory-account) in Active Directory e concessione delle relative autorizzazioni. L'account creato viene usato per leggere e scrivere informazioni di directory durante la sincronizzazione.</li> |

### <a name="enterprise-admin-credentials"></a>Credenziali di amministratore dell'organizzazione
Queste credenziali vengono usate solo durante l'installazione e non dopo il completamento di quest'ultima. L'amministratore dell'organizzazione, non l'amministratore di dominio, deve garantire che le autorizzazioni in Active Directory possano essere impostate in tutti i domini.

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
Azure AD Connect versione 1.1.524.0 e successive con l'opzione per consentire alla procedura guidata di Azure AD Connect di creare l'account usato per connettersi ad Active Directory.  Con le versioni precedenti era necessario creare l'account prima dell'installazione. Per le autorizzazioni da concedere a questo account, vedere [Creare l'account di Servizi di dominio Active Directory](#create-the-ad-ds-account). 

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
L'account specificato nella pagina **Connessione delle directory** deve essere presente in Active Directory prima dell'installazione.  È necessario che siano disponibili anche le autorizzazioni necessarie. L'installazione guidata non verifica che le autorizzazioni e gli eventuali problemi vengano rilevati solo durante la sincronizzazione.

Le autorizzazioni necessarie dipendono dalle funzionalità facoltative abilitate. Se si dispone di più domini, le autorizzazioni devono essere concesse per tutti i domini nella foresta. Se queste funzionalità non vengono abilitate, sono sufficienti le autorizzazioni dell' **utente di dominio** predefinite.

| Funzionalità | Autorizzazioni |
| --- | --- |
| Funzionalità msDS-ConsistencyGuid |Autorizzazioni di scrittura per l'attributo msDS-ConsistencyGuid documentato in [Concetti di progettazione - Uso di msDS-ConsistencyGuid come sourceAnchor](active-directory-aadconnect-design-concepts.md#using-msds-consistencyguid-as-sourceanchor). | 
| Sincronizzazione delle password |<li>Replica modifiche directory</li>  <li>Replica modifiche directory - Tutto |
| Distribuzione ibrida di Exchange |Autorizzazioni di scrittura per gli attributi documentati in [Writeback della distribuzione ibrida Exchange](active-directory-aadconnectsync-attributes-synchronized.md#exchange-hybrid-writeback) per utenti, gruppi e contatti. |
| Cartelle pubbliche della posta di Exchange |Autorizzazioni di lettura per gli attributi documentati in [Cartelle pubbliche della posta di Exchange](active-directory-aadconnectsync-attributes-synchronized.md#exchange-mail-public-folder) per le cartelle pubbliche. | 
| Writeback delle password |Autorizzazioni di scrittura per gli attributi documentati in [Introduzione alla gestione delle password](../active-directory-passwords-writeback.md) per gli utenti. |
| Writeback dispositivi |Autorizzazioni concesse con uno script di PowerShell come descritto in [Writeback dei dispositivi](active-directory-aadconnect-feature-device-writeback.md). |
| Writeback dei gruppi |Lettura, creazione, aggiornamento ed eliminazione di oggetti di gruppo per i **gruppi di Office 365** sincronizzati.  Per altre informazioni, vedere [Writeback dei gruppi](active-directory-aadconnect-feature-preview.md#group-writeback).|

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

Se si usano le impostazioni personalizzate, l'utente è responsabile della creazione dell'account prima di avviare l'installazione.

### <a name="azure-ad-connect-sync-service-account"></a>Account del servizio di sincronizzazione Azure AD Connect
Il servizio di sincronizzazione può essere eseguito con account diversi, ad esempio con un **account del servizio virtuale** (VSA), un **account del servizio gestito del gruppo** (gMSA/sMSA) o un normale account utente. Le opzioni supportate sono state modificate con il rilascio di aprile 2017 di Connect se si esegue una nuova installazione. Se si esegue un aggiornamento da una versione precedente di Azure AD Connect, tali opzioni non sono disponibili.

| Tipo di account | Opzione di installazione | Descrizione |
| --- | --- | --- |
| [Account del servizio virtuale](#virtual-service-account) | Rapida e personalizzata, aprile 2017 e versioni successive | Questa è l'opzione usata per tutte le installazioni rapide, ad eccezione delle installazioni in un controller di dominio. Per l'installazione personalizzata è l'opzione predefinita, a meno che non si usi un'altra opzione. |
| [Account del servizio gestito del gruppo](#group-managed-service-account) | Personalizzata, aprile 2017 e versioni successive | Se si usa un server SQL remoto, è consigliabile usare un account del servizio gestito del gruppo. |
| [Account utente](#user-account) | Rapida e personalizzata, aprile 2017 e versioni successive | Un account utente con il prefisso AAD_ viene creato durante l'installazione solo se è installato in Windows Server 2008 e in un controller di dominio. |
| [Account utente](#user-account) | Rapida e personalizzata, marzo 2017 e versioni precedenti | Durante l'installazione viene creato un account locale con prefisso AAD_. Se si usa l'installazione personalizzata, è possibile specificare un altro account. |

Se si usa Connect con una build di marzo 2017 o precedente, non reimpostare la password nell'account del servizio poiché Windows elimina le chiavi di crittografia per motivi di sicurezza. Non è possibile modificare l'account impostandone un altro senza reinstallare Azure AD Connect. Se si esegue l'aggiornamento a una build di aprile 2017 o successiva, è possibile modificare la password dell'account del servizio ma non l'account in uso.

> [!Important]
> È possibile impostare solo l'account del servizio durante la prima installazione. Non è supportata la modifica dell'account del servizio dopo il completamento dell'installazione.

La tabella seguente illustra le opzioni predefinite, consigliate e supportate per l'account del servizio di sincronizzazione.

Legenda:

- Il **grassetto** indica l'opzione predefinita e nella maggior parte dei casi l'opzione consigliata.
- Il *corsivo* indica l'opzione consigliata quando non è l'opzione predefinita.
- 2008: opzione predefinita se installata in Windows Server 2008
- Non in grassetto: opzione supportata
- Account locale: account utente locale sul server
- Account di dominio: account utente di dominio
- sMSA: [account del servizio gestito autonomo](https://technet.microsoft.com/library/dd548356.aspx)
- gMSA: [account del servizio gestito del gruppo](https://technet.microsoft.com/library/hh831782.aspx)

| | DB locale</br>Express | DB/SQL locale</br>Personalizzate | SQL remoto</br>Personalizzate |
| --- | --- | --- | --- |
| **computer autonomo o di gruppo di lavoro** | Non supportate | **VSA**</br>Account locale (2008)</br>Account locale |  Non supportate |
| **computer aggiunto a un dominio** | **VSA**</br>Account locale (2008) | **VSA**</br>Account locale (2008)</br>Account locale</br>Account di dominio</br>sMSA, gMSA | **gMSA**</br>Account di dominio |
| **Controller di dominio** | **Account di dominio** | *gMSA*</br>**Account di dominio**</br>sMSA| *gMSA*</br>**Account di dominio**|

#### <a name="virtual-service-account"></a>Account del servizio virtuale
Un account del servizio virtuale è un tipo speciale di account che non ha una password ed è gestito da Windows.

![VSA](./media/active-directory-aadconnect-accounts-permissions/aadsyncvsa.png)

L'account del servizio virtuale deve essere usato con scenari in cui il motore di sincronizzazione e SQL sono sullo stesso server. Se però si usa un server SQL remoto, è consigliabile usare un [account del servizio gestito del gruppo](#managed-service-account).

Questa funzionalità richiede Windows Server 2008 R2 o versioni successive. Se si installa Azure AD Connect in Windows Server 2008, l'installazione ritorna all'uso di un [account utente](#user-account).

#### <a name="group-managed-service-account"></a>Account del servizio gestito del gruppo
Se si usa un server SQL remoto, è consigliabile usare un **account del servizio gestito del gruppo**. Per altre informazioni su come preparare Active Directory per l'account del servizio gestito del gruppo, vedere la [panoramica sugli account del servizio gestito del gruppo](https://technet.microsoft.com/library/hh831782.aspx).

Per usare questa opzione, nella pagina [Installazione dei componenti necessari](active-directory-aadconnect-get-started-custom.md#install-required-components) selezionare **Usa un account del servizio esistente** e quindi **Account del servizio gestito**.  
![VSA](./media/active-directory-aadconnect-accounts-permissions/serviceaccount.png)  
L'opzione è supportata anche per l'uso di un [account del servizio gestito autonomo](https://technet.microsoft.com/library/dd548356.aspx). Tuttavia l'opzione può essere usata solo nel computer locale e non offre alcun vantaggio se viene usata con un account del servizio virtuale predefinito.

Questa funzionalità richiede Windows Server 2012 o versioni successive. Se è necessario usare un sistema operativo precedente e SQL remoto, usare un [account utente](#user-account).

#### <a name="user-account"></a>Account utente
Mediante l'installazione guidata viene creato un account di servizio locale (a meno che non si specifichi l'account da usare nelle impostazioni personalizzate). L'account, preceduto da **AAD_**, viene usato per l'esecuzione del servizio di sincronizzazione effettivo. Se si installa Azure AD Connect in un Controller di dominio, l'account viene creato nel dominio. L'account del servizio **AAD_** deve essere presente nel dominio se:
   - si usa un server remoto che esegue SQL Server
   - si usa un proxy che richiede l'autenticazione

![Account del servizio di sincronizzazione](./media/active-directory-aadconnect-accounts-permissions/syncserviceaccount.png)

L'account viene creato con una password lunga e complessa priva di scadenza.

Questo account viene usato per archiviare in modo sicuro le password per gli altri account. Le password di questi altri account vengono archiviate crittografate nel database. Le chiavi private per le chiavi di crittografia sono protette tramite la crittografia a chiave segreta dei servizi di crittografia con Data Protection API (DPAPI) di Windows.

Se si usa una versione completa di SQL Server, l'account del servizio corrisponde al DBO del database creato per il motore di sincronizzazione. Il servizio non funzionerà come previsto con tutte le altre autorizzazioni. Viene inoltre creato l'accesso a SQL.

L'account concede inoltre le autorizzazioni a file, chiavi del Registro di sistema e altri oggetti correlati al motore di sincronizzazione.

### <a name="azure-ad-service-account"></a>Account del servizio Azure AD
In Azure AD viene creato un account per l'uso con il servizio di sincronizzazione. Questo account può essere identificato in base al relativo nome visualizzato.

![Account AD](./media/active-directory-aadconnect-accounts-permissions/aadsyncserviceaccount.png)

Il nome del server in cui viene usato l'account può essere identificato nella seconda parte del nome utente. Nell'immagine, il nome del server è FABRIKAMCON. Se si dispone di server di gestione temporanea, ogni server avrà il proprio account.

L'account del servizio viene creato con una password lunga e complessa priva di scadenza. Viene concesso un ruolo speciale per gli **account di sincronizzazione della directory** che dispone solo delle autorizzazioni per eseguire attività di sincronizzazione della directory. Questo ruolo predefinito speciale non può essere concesso al di fuori della procedura guidata di Azure AD Connect. Il portale di Azure mostra questo account con il ruolo **Utente**.

In Azure AD esiste un limite di 20 account del servizio di sincronizzazione. Per ottenere l'elenco degli account del servizio Azure AD esistenti in Azure AD, eseguire il cmdlet di PowerShell per Azure AD seguente:`Get-AzureADDirectoryRole | where {$_.DisplayName -eq "Directory Synchronization Accounts"} | Get-AzureADDirectoryRoleMember`

Per rimuovere gli account del servizio Azure AD inutilizzati, eseguire il cmdlet di PowerShell per Azure AD seguente:`Remove-AzureADUser -ObjectId <ObjectId-of-the-account-you-wish-to-remove>`

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](../active-directory-aadconnect.md).

