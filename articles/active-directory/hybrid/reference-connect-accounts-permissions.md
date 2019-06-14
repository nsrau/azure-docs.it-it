---
title: 'Azure AD Connect: account e autorizzazioni | Microsoft Docs'
description: Questo argomento descrive gli account usati e creati nonché le autorizzazioni necessarie.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.reviewer: cychua
ms.assetid: b93e595b-354a-479d-85ec-a95553dd9cc2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 466b1aadb84bc92981b9adf1b1affa69f5f2ec25
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64919178"
---
# <a name="azure-ad-connect-accounts-and-permissions"></a>Azure AD Connect: Account e autorizzazioni

## <a name="accounts-used-for-azure-ad-connect"></a>Account usati per Azure AD Connect

![Panoramica degli account](media/reference-connect-accounts-permissions/account5.png)

Azure AD Connect usa 3 account per sincronizzare le informazioni da locale o da Windows Server Active Directory ad Azure Active Directory.  Questi account sono:

- **Account del connettore di AD DS**:     usato per leggere e scrivere informazioni in Windows Server Active Directory

- **Account del servizio ADSync**:      usato per eseguire il servizio di sincronizzazione e accedere al database SQL

- **Account del connettore di Azure AD**:     usato per scrivere informazioni in Azure AD

Oltre a questi tre account usati per eseguire Azure AD Connect, per installare Azure AD Connect sono necessari anche gli account aggiuntivi seguenti.  Si tratta di:

- **Account amministratore locale**: l'amministratore che sta installando Azure AD Connect e possiede autorizzazioni di amministratore locale per il computer.

- **Account amministratore dell'organizzazione Active Directory Domain Services**: usato facoltativamente per creare l'"account del connettore di AD DS" sopra.

- **Account Administrator globale di AD Azure**:  usato per creare l'account del connettore di Azure AD e configurare Azure AD.

- **Account SA di SQL (facoltativo)** :     usato per creare il database ADSync, quando si usa la versione completa di SQL Server.  SQL Server può essere locale o remoto per l'installazione di Azure AD Connect.  Questo account può essere lo stesso dell'amministratore dell'organizzazione.  L'amministratore SQL può ora effettuare il provisioning del database fuori banda e quindi l'amministratore di Azure AD Connect può eseguire l'installazione con diritti di proprietario del database.  Per altre informazioni, vedere [Installare Azure AD Connect usando le autorizzazioni di amministratore con delega SQL](how-to-connect-install-sql-delegation.md)

## <a name="installing-azure-ad-connect"></a>Installazione di Azure AD Connect
L'installazione guidata di Azure AD Connect offre due percorsi diversi:

* Impostazioni rapide: sono necessari più privilegi.  È possibile eseguire la configurazione con facilità, senza che sia necessario creare utenti o configurare autorizzazioni.
* Impostazioni personalizzate: sono disponibili più opzioni e scelte. In alcune situazioni è tuttavia necessario assicurarsi manualmente di disporre delle autorizzazioni corrette.



## <a name="express-settings-installation"></a>Installazione mediante le impostazioni rapide
In Impostazioni rapide, l'installazione guidata richiede quanto segue:

  - Le credenziali amministratore dell'organizzazione AD DS
  - Le credenziali amministratore globale di Azure AD

### <a name="ad-ds-enterprise-admin-credentials"></a>Le credenziali amministratore dell'organizzazione AD DS
L'account amministratore dell'organizzazione AD DS è usato per configurare Active Directory locale. Queste credenziali vengono usate solo durante l'installazione e non dopo il completamento di quest'ultima. L'amministratore dell'organizzazione, non l'amministratore di dominio, deve garantire che le autorizzazioni in Active Directory possano essere impostate in tutti i domini.

Se si esegue l'aggiornamento da DirSync, le credenziali di amministratore dell'organizzazione di Servizi di dominio Active Directory vengono usate per reimpostare la password per l'account usato da DirSync. Sono necessarie anche le credenziali di amministratore globale di Azure AD.

### <a name="azure-ad-global-admin-credentials"></a>Credenziali di amministratore globale di Azure AD
Queste credenziali vengono usate solo durante l'installazione e non dopo il completamento di quest'ultima. Vengono usate per creare l'account del connettore di Azure AD usato per sincronizzare le modifiche apportate ad Azure AD. e per abilitare la sincronizzazione come funzionalità in Azure AD.

### <a name="ad-ds-connector-account-required-permissions-for-express-settings"></a>Autorizzazioni necessarie per l'account del connettore di AD DS per le impostazioni rapide
L'account del connettore di Active Directory Domain Services è creato per la lettura e la scrittura in AD di Windows Server e ha le autorizzazioni seguenti se creato tramite impostazioni rapide:

| Autorizzazione | Usato per |
| --- | --- |
| <li>Replica modifiche directory</li><li>Replica modifiche directory - Tutto |Sincronizzazione dell'hash delle password |
| Lettura/scrittura di tutte le proprietà - Utente |Importazione ed Exchange ibrido |
| Lettura/scrittura di tutte le proprietà - iNetOrgPerson |Importazione ed Exchange ibrido |
| Lettura/scrittura di tutte le proprietà - Gruppo |Importazione ed Exchange ibrido |
| Lettura/scrittura di tutte le proprietà - Contatto |Importazione ed Exchange ibrido |
| Reimposta password |Preparazione per l'abilitazione del writeback delle password |

### <a name="express-installation-wizard-summary"></a>Riepilogo della procedura guidata di installazione rapida

![Installazione rapida](./media/reference-connect-accounts-permissions/express.png)

Di seguito è riportato un riepilogo delle pagine della procedura guidata di installazione rapida, le credenziali raccolte e il relativo uso.

| Pagina della procedura guidata | Credenziali raccolte | Autorizzazioni necessarie | Utilizzo |
| --- | --- | --- | --- |
| N/D |Utente che esegue l'installazione guidata |Amministratore del server locale |<li>Crea l'account di servizio ADSync usato per eseguire il servizio di sincronizzazione. |
| Connessione ad Azure AD |Credenziali di directory di Azure AD |Ruolo di amministratore in Azure AD |<li>Abilitazione della sincronizzazione nella directory di Azure AD.</li>  <li>Creazione dell'account del connettore di Azure AD usato per le operazioni di sincronizzazione ricorrenti in Azure AD.</li> |
| Connettersi ad AD DS |Credenziali Active Directory locali |Membro del gruppo Enterprise Admins (EA) in Active Directory |<li>Crea l'account del connettore di Active Directory Domain Services in Active Directory e concede le relative autorizzazioni. L'account creato viene usato per leggere e scrivere informazioni di directory durante la sincronizzazione.</li> |


## <a name="custom-installation-settings"></a>Impostazioni di installazione personalizzata

Con l'installazione personalizzata la procedura guidata offre più opzioni e scelte. 

### <a name="custom-installation-wizard-summary"></a>Riepilogo della procedura guidata di installazione personalizzata

Di seguito è riportato un riepilogo delle pagine della procedura guidata di installazione personalizzata, le credenziali raccolte e il relativo uso.

![Installazione rapida](./media/reference-connect-accounts-permissions/customize.png)

| Pagina della procedura guidata | Credenziali raccolte | Autorizzazioni necessarie | Utilizzo |
| --- | --- | --- | --- |
| N/D |Utente che esegue l'installazione guidata |<li>Amministratore del server locale</li><li>Se si usa una versione completa di SQL Server, l'utente deve essere un amministratore di sistema in SQL</li> |Per impostazione predefinita, crea l'account locale usato come account del servizio del motore di sincronizzazione. L'account viene creato solo quando l'amministratore non specifica un account determinato. |
| Installazione dei servizi di sincronizzazione, opzione Account di servizio |Active Directory o credenziali dell'account utente locale |Autorizzazioni utente concesse mediante l'installazione guidata |Se l'amministratore specifica un account, quest'ultimo viene usato come account del servizio di sincronizzazione. |
| Connessione ad Azure AD |Credenziali di directory di Azure AD |Ruolo di amministratore in Azure AD |<li>Abilitazione della sincronizzazione nella directory di Azure AD.</li>  <li>Creazione dell'account del connettore di Azure AD usato per le operazioni di sincronizzazione ricorrenti in Azure AD.</li> |
| Connessione delle directory |Credenziali Active Directory locali per ogni foresta connessa ad Azure AD |Le autorizzazioni variano in base alle funzionalità abilitate e sono disponibili in Creare l'account del connettore di Active Directory Domain Services |L'account viene usato per leggere e scrivere informazioni di directory durante la sincronizzazione. |
| Server ADFS |Per ogni server nell'elenco, la procedura guidata raccoglie le credenziali quando le credenziali di accesso dell'utente che esegue la procedura guidata non sono sufficienti per la connessione |Amministratore di dominio |Installazione e configurazione del ruolo del server ADFS. |
| Server Proxy applicazione Web |Per ogni server nell'elenco, la procedura guidata raccoglie le credenziali quando le credenziali di accesso dell'utente che esegue la procedura guidata non sono sufficienti per la connessione |Amministratore locale nel computer di destinazione |Installazione e configurazione del ruolo del server WAP. |
| Credenziali di attendibilità del proxy |Credenziali di attendibilità del servizio federativo (le credenziali che sono utilizzate dal proxy per richiedere un certificato di attendibilità da FS) |Account di dominio che è un amministratore locale del server ADFS |Registrazione iniziale del certificato di attendibilità di FS-WAP. |
| Pagina Account del servizio ADFS, "Utilizzare un'opzione account utente di dominio" |Credenziali dell'account utente di Active Directory |Utente di dominio |L'account utente di Azure AD vengono fornite le cui credenziali viene usato come account di accesso del servizio AD FS. |

### <a name="create-the-ad-ds-connector-account"></a>Creare l'account del connettore di AD DS

>[!IMPORTANT]
>Con la build **1.1.880.0** (rilasciata nel mese di agosto 2018) è stato introdotto un nuovo modulo di PowerShell denominato ADSyncConfig.psm1, che include una raccolta di cmdlet che consentono di configurare le autorizzazioni di Active Directory corrette per l'account del connettore di Azure AD DS.
>
>Per altre informazioni, vedere [Azure AD Connect: configurare l'autorizzazione dell'account del connettore di Active Directory Domain Services](how-to-connect-configure-ad-ds-connector-account.md)

L'account specificato nella pagina **Connessione delle directory** deve essere presente in Active Directory prima dell'installazione.  Azure AD Connect versione 1.1.524.0 e successive con l'opzione per consentire alla procedura guidata di Azure AD Connect di creare l'**account del connettore di AD DS** usato per connettersi ad Active Directory.  

È necessario che siano disponibili anche le autorizzazioni necessarie. L'installazione guidata non verifica che le autorizzazioni e gli eventuali problemi vengano rilevati solo durante la sincronizzazione.

Le autorizzazioni necessarie dipendono dalle funzionalità facoltative abilitate. Se si dispone di più domini, le autorizzazioni devono essere concesse per tutti i domini nella foresta. Se queste funzionalità non vengono abilitate, sono sufficienti le autorizzazioni dell' **utente di dominio** predefinite.

| Funzionalità | Autorizzazioni |
| --- | --- |
| funzionalità ms-DS-ConsistencyGuid |Autorizzazioni di scrittura per l'attributo ms-DS-ConsistencyGuid documentato in [Concetti di progettazione - Uso di ms-DS-ConsistencyGuid come sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). | 
| Sincronizzazione dell'hash delle password |<li>Replica modifiche directory</li>  <li>Replica modifiche directory - Tutto |
| Distribuzione ibrida di Exchange |Autorizzazioni di scrittura per gli attributi documentati in [Writeback della distribuzione ibrida Exchange](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) per utenti, gruppi e contatti. |
| Cartelle pubbliche della posta di Exchange |Autorizzazioni di lettura per gli attributi documentati in [Cartelle pubbliche della posta di Exchange](reference-connect-sync-attributes-synchronized.md#exchange-mail-public-folder) per le cartelle pubbliche. | 
| writeback delle password |Autorizzazioni di scrittura per gli attributi documentati in [Introduzione alla gestione delle password](../authentication/howto-sspr-writeback.md) per gli utenti. |
| Writeback dispositivi |Autorizzazioni concesse con uno script di PowerShell come descritto in [Writeback dei dispositivi](how-to-connect-device-writeback.md). |
| Writeback dei gruppi |Consente il writeback dei **gruppi di Office 365** in una foresta in cui è installato Exchange.  Per altre informazioni, vedere [Writeback dei gruppi](how-to-connect-preview.md#group-writeback).|

## <a name="upgrade"></a>Aggiornamento
Quando si aggiorna da una versione di Azure AD Connect a una nuova versione, è necessario avere le autorizzazioni seguenti:

>[!IMPORTANT]
>A partire dalla build 1.1.484, in Azure AD Connect è stato introdotto un bug di regressione che richiede autorizzazioni sysadmin per aggiornare il database SQL.  Questo bug è stato corretto nella build 1.1.647.  Se si esegue l'aggiornamento a questa build, saranno necessarie autorizzazioni sysadmin.  Le autorizzazioni dbo non sono sufficienti.  Se si tenta di eseguire l'aggiornamento di Azure AD Connect senza disporre delle autorizzazioni sysadmin, l'aggiornamento avrà esito negativo e Azure AD Connect non funzionerà più correttamente in seguito.  Microsoft è al corrente di questo problema e sta lavorando a una soluzione.


| Entità | Autorizzazioni necessarie | Usato per |
| --- | --- | --- |
| Utente che esegue l'installazione guidata |Amministratore del server locale |Aggiornare i file binari. |
| Utente che esegue l'installazione guidata |Membro di ADSyncAdmins |Apportare modifiche alle regole di sincronizzazione e ad altre configurazioni. |
| Utente che esegue l'installazione guidata |Se si usa un server SQL completo: DBO (o simile) del database del motore di sincronizzazione |Apportare modifiche a livello di database, ad esempio l'aggiornamento di tabelle con nuove colonne. |

## <a name="more-about-the-created-accounts"></a>Altre informazioni sugli account creati
### <a name="ad-ds-connector-account"></a>Account del connettore di AD DS
Se si usano le impostazioni rapide, viene creato un account in Active Directory usato per la sincronizzazione. L'account creato si trova nel dominio radice della foresta nel contenitore degli utenti e il relativo nome con prefisso è **MSOL_** . L'account viene creato con una password lunga e complessa priva di scadenza. Se nel dominio sono presenti criteri di password, assicurarsi che per tale account siano consentite password lunghe e complesse.

![Account AD](./media/reference-connect-accounts-permissions/adsyncserviceaccount.png)

Se si usano le impostazioni personalizzate, l'utente è responsabile della creazione dell'account prima di avviare l'installazione.  Consultare Creare l'account del connettore di Active Directory Domain Services.

### <a name="adsync-service-account"></a>Account del servizio ADSync
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

| | DB locale</br>Express | DB/SQL locale</br>Personalizzato | SQL remoto</br>Personalizzato |
| --- | --- | --- | --- |
| **computer autonomo o di gruppo di lavoro** | Non supportate | **VSA**</br>Account locale (2008)</br>Account locale |  Non supportate |
| **computer aggiunto a un dominio** | **VSA**</br>Account locale (2008) | **VSA**</br>Account locale (2008)</br>Account locale</br>Account di dominio</br>sMSA, gMSA | **gMSA**</br>Account di dominio |
| **Controller di dominio** | **Account di dominio** | *gMSA*</br>**Account di dominio**</br>sMSA| *gMSA*</br>**Account di dominio**|

#### <a name="virtual-service-account"></a>Account del servizio virtuale
Un account del servizio virtuale è un tipo speciale di account che non ha una password ed è gestito da Windows.

![VSA](./media/reference-connect-accounts-permissions/aadsyncvsa.png)

L'account del servizio virtuale deve essere usato con scenari in cui il motore di sincronizzazione e SQL sono sullo stesso server. Se però si usa un server SQL remoto, è consigliabile usare un account del servizio gestito del gruppo.

Questa funzionalità richiede Windows Server 2008 R2 o versioni successive. Se si installa Azure AD Connect in Windows Server 2008, l'installazione ritorna all'uso di un [account utente](#user-account).

#### <a name="group-managed-service-account"></a>Account del servizio gestito del gruppo
Se si usa un server SQL remoto, è consigliabile usare un **account del servizio gestito del gruppo**. Per altre informazioni su come preparare Active Directory per l'account del servizio gestito del gruppo, vedere la [panoramica sugli account del servizio gestito del gruppo](https://technet.microsoft.com/library/hh831782.aspx).

Per usare questa opzione, nella pagina [Installazione dei componenti necessari](how-to-connect-install-custom.md#install-required-components) selezionare **Usa un account del servizio esistente** e quindi **Account del servizio gestito**.  
![VSA](./media/reference-connect-accounts-permissions/serviceaccount.png)  
L'opzione è supportata anche per l'uso di un [account del servizio gestito autonomo](https://technet.microsoft.com/library/dd548356.aspx). Tuttavia l'opzione può essere usata solo nel computer locale e non offre alcun vantaggio se viene usata con un account del servizio virtuale predefinito.

Questa funzionalità richiede Windows Server 2012 o versioni successive. Se è necessario usare un sistema operativo precedente e SQL remoto, usare un [account utente](#user-account).

#### <a name="user-account"></a>Account utente
Mediante l'installazione guidata viene creato un account di servizio locale (a meno che non si specifichi l'account da usare nelle impostazioni personalizzate). L'account, preceduto da **AAD_** , viene usato per l'esecuzione del servizio di sincronizzazione effettivo. Se si installa Azure AD Connect in un Controller di dominio, l'account viene creato nel dominio. L'account del servizio **AAD_** deve essere presente nel dominio se:
   - si usa un server remoto che esegue SQL Server
   - si usa un proxy che richiede l'autenticazione

![Account del servizio di sincronizzazione](./media/reference-connect-accounts-permissions/syncserviceaccount.png)

L'account viene creato con una password lunga e complessa priva di scadenza.

Questo account viene usato per archiviare in modo sicuro le password per gli altri account. Le password di questi altri account vengono archiviate crittografate nel database. Le chiavi private per le chiavi di crittografia sono protette tramite la crittografia a chiave segreta dei servizi di crittografia con Data Protection API (DPAPI) di Windows.

Se si usa una versione completa di SQL Server, l'account del servizio corrisponde al DBO del database creato per il motore di sincronizzazione. Il servizio non funzionerà come previsto con tutte le altre autorizzazioni. Viene inoltre creato l'accesso a SQL.

L'account concede inoltre le autorizzazioni a file, chiavi del Registro di sistema e altri oggetti correlati al motore di sincronizzazione.

### <a name="azure-ad-connector-account"></a>Account del connettore di Azure AD
In Azure AD viene creato un account per l'uso con il servizio di sincronizzazione. Questo account può essere identificato in base al relativo nome visualizzato.

![Account AD](./media/reference-connect-accounts-permissions/aadsyncserviceaccount2.png)

Il nome del server in cui viene usato l'account può essere identificato nella seconda parte del nome utente. Nell'immagine, il nome del server è DC1. Se si dispone di server di gestione temporanea, ogni server avrà il proprio account.

L'account viene creato con una password lunga e complessa priva di scadenza. Viene concesso un ruolo speciale per gli **account di sincronizzazione della directory** che dispone solo delle autorizzazioni per eseguire attività di sincronizzazione della directory. Questo ruolo predefinito speciale non può essere concesso al di fuori della procedura guidata di Azure AD Connect. Il portale di Azure mostra questo account con il ruolo **Utente**.

In Azure AD esiste un limite di 20 account del servizio di sincronizzazione. Per ottenere l'elenco degli account del servizio Azure AD esistenti in Azure AD, eseguire il cmdlet di PowerShell per Azure AD seguente:`Get-AzureADDirectoryRole | where {$_.DisplayName -eq "Directory Synchronization Accounts"} | Get-AzureADDirectoryRoleMember`

Per rimuovere gli account del servizio Azure AD inutilizzati, eseguire il cmdlet di PowerShell per Azure AD seguente:`Remove-AzureADUser -ObjectId <ObjectId-of-the-account-you-wish-to-remove>`

>[!NOTE]
>Prima di poter usare i comandi di PowerShell precedenti è necessario installare il [Azure Active Directory PowerShell per il modulo di Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0#installing-the-azure-ad-module) e connettersi all'istanza di Azure AD usando [Connect-AzureAD](https://docs.microsoft.com/powershell/module/azuread/connect-azuread?view=azureadps-2.0)

Per altre informazioni su come gestire o reimpostare la password per l'account Azure AD Connector vedere [gestire l'account di Azure AD Connect](how-to-connect-azureadaccount.md)

## <a name="related-documentation"></a>Documentazione correlata
Se non è stata letta la documentazione in [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md), la tabella seguente fornisce collegamenti ad argomenti correlati.

|Argomento |Collegamento|  
| --- | --- |
|Scaricare Azure AD Connect | [Scaricare Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771)|
|Eseguire l'installazione con le Impostazioni rapide | [Installazione rapida di Azure AD Connect](how-to-connect-install-express.md)|
|Eseguire l'installazione mediante le impostazioni personalizzate | [Installazione personalizzata di Azure AD Connect](./how-to-connect-install-custom.md)|
|Aggiornamento da DirSync | [Aggiornamento dallo strumento di sincronizzazione di Azure AD (DirSync)](how-to-dirsync-upgrade-get-started.md)|
|Dopo l'installazione | [Verificare l'installazione e assegnare le licenze](how-to-connect-post-installation.md)|

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md).
