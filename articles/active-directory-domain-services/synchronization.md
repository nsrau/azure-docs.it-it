---
title: Funzionamento della sincronizzazione in Servizi di dominio Azure AD Documenti Microsoft
description: Informazioni sul funzionamento del processo di sincronizzazione per oggetti e credenziali da un tenant di Azure AD o da un ambiente Servizi di dominio Active Directory locale a un dominio gestito di Servizi di dominio Azure Active Directory.Learn how the synchronization process works for objects and credentials from an Azure AD tenant or on-premises Active Directory Domain Services environment to an Azure Active Directory Domain Services managed domain.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 57cbf436-fc1d-4bab-b991-7d25b6e987ef
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: 7e0e904b182a57a51b5d76f0acebc13bce5902b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944426"
---
# <a name="how-objects-and-credentials-are-synchronized-in-an-azure-ad-domain-services-managed-domain"></a>Modalità di sincronizzazione di oggetti e credenziali in un dominio gestito di Servizi di dominio Azure ADHow objects and credentials are synchronized in an Azure AD Domain Services managed domain

Gli oggetti e le credenziali in un dominio gestito di Servizi di dominio Active Directory di Azure possono essere creati localmente all'interno del dominio o sincronizzati da un tenant di Azure Active Directory (Azure AD). Quando si distribuisce Azure AD DS per la prima volta, viene configurata una sincronizzazione unidirezionale automatica che ha iniziato a replicare gli oggetti da Azure AD. Questa sincronizzazione unidirezionale continua a essere eseguita in background per mantenere aggiornato il dominio gestito di Servizi di dominio Active Directory di Azure con eventuali modifiche di Azure AD. Non viene eseguita alcuna sincronizzazione da Servizi di dominio Active Directory di Azure ad Azure AD.

In a hybrid environment, objects and credentials from an on-premises AD DS domain can be synchronized to Azure AD using Azure AD Connect. Dopo che tali oggetti sono stati sincronizzati correttamente con Azure AD, la sincronizzazione automatica in background rende tali oggetti e credenziali disponibili per le applicazioni che usano il dominio gestito di Servizi di dominio Active Directory di Azure.Once those objects are successfully synchronized to Azure AD, the automatic background sync then makes those objects and credentials available to applications using the Azure AD DS managed domain.

Il diagramma seguente illustra il funzionamento della sincronizzazione tra Servizi di dominio Active Directory di Azure, Azure AD e un ambiente di Servizi di dominio Active Directory locale facoltativo:The following diagram illustrates how synchronization works between Azure AD DS, Azure AD, and an optional on-premises Ad DS environment:

![Panoramica della sincronizzazione per un dominio gestito di Servizi di dominio Azure ADSynchronization overview for an Azure AD Domain Services managed domain](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-azure-ad-to-azure-ad-ds"></a>Sincronizzazione da Azure AD ad Azure AD DSSynchronization from Azure AD to Azure AD DS

Gli account utente, le appartenenze ai gruppi e gli hashe delle credenziali vengono sincronizzati in un modo da Azure AD ad Azure AD DS. Questo processo di sincronizzazione è automatico, Non è necessario configurare, monitorare o gestire questo processo di sincronizzazione. La sincronizzazione iniziale può richiedere da alcune ore a un paio di giorni, a seconda del numero di oggetti nella directory di Azure AD. Al termine della sincronizzazione iniziale, le modifiche apportate in Azure AD, ad esempio le modifiche di password o attributi, vengono sincronizzate automaticamente con Servizi di dominio Active Directory di Azure.After the initial synchronization is complete, changes that are made in Azure AD, such as password or attribute changes, are then automatically synchronized to Azure AD DS.

Il processo di sincronizzazione è un modo / unidirezionale in base alla progettazione. Non esiste la sincronizzazione inversa delle modifiche da Servizi di dominio Active Directory di Azure ad Azure AD. Un dominio gestito di Azure AD DS è in gran parte di sola lettura, ad eccezione delle sole personalizzate che è possibile creare. Non è possibile apportare modifiche agli attributi utente, alle password utente o alle appartenenze ai gruppi all'interno di un dominio gestito di Servizi di dominio Active Directory di Azure.You can't make changes to user attributes, user passwords, or group memberships within an Azure AD DS managed domain.

## <a name="attribute-synchronization-and-mapping-to-azure-ad-ds"></a>Sincronizzazione e mapping degli attributi a Azure AD DS

Nella tabella seguente sono elencati alcuni attributi comuni e la modalità di sincronizzazione con Azure AD DS.

| Attributo in Azure AD DSAttribute in Azure AD DS | Source (Sorgente) | Note |
|:--- |:--- |:--- |
| UPN | Attributo *UPN* dell'utente nel tenant di Azure ADUser's UPN attribute in Azure AD tenant | The UPN attribute from the Azure AD tenant is synchronized as-is to Azure AD DS. Il modo più affidabile per accedere a un dominio gestito di Azure AD DS consiste nell'usare l'UPN. |
| SAMAccountName | Attributo *mailNickname* dell'utente nel tenant di Azure AD o generato automaticamenteUser's mailNickname attribute in Azure AD tenant or autogenerated | L'attributo *SAMAccountName* proviene dall'attributo *mailNickname* nel tenant di Azure AD. Se più account utente hanno lo stesso attributo *mailNickname,* *SAMAccountName* viene generato automaticamente. Se il prefisso *mailNickname* o *UPN* dell'utente è più lungo di 20 caratteri, *SAMAccountName* viene generato automaticamente per soddisfare il limite di 20 caratteri per gli attributi *SAMAccountName.* |
| Password | Password dell'utente dal tenant di Azure ADUser's password from the Azure AD tenant | Gli errori delle password legacy necessari per l'autenticazione NTLM o Kerberos vengono sincronizzati dal tenant di Azure AD. Se il tenant di Azure AD è configurato per la sincronizzazione ibrida tramite Azure AD Connect, questi hash delle password provengono dall'ambiente di Servizi di dominio Active Directory locale. |
| SID utente/gruppo primario | Generato automaticamente | Il SID primario per gli account utente/gruppo viene generato automaticamente in Azure AD DS. Questo attributo non corrisponde al SID utente/gruppo primario dell'oggetto in un ambiente Servizi di dominio Active Directory locale. Questa mancata corrispondenza è dovuto al fatto che il dominio gestito di Servizi di dominio Active Directory di Azure ha uno spazio dei nomi SID diverso da quello del dominio di Servizi di dominio Active Directory locale. |
| Cronologia SID per utenti e gruppi | SID utente/gruppo primario locale | L'attributo *SidHistory* per utenti e gruppi in Servizi di dominio Active Directory di Azure è impostato in modo che corrisponda all'utente primario corrispondente o al SID di gruppo in un ambiente di Servizi di dominio Active Directory locale. Questa funzionalità consente di semplificare lo lift-and-shift delle applicazioni locali in Azure AD DS, in quanto non è necessario riaccendere le risorse. |

> [!TIP]
> **Accedere al dominio gestito utilizzando il formato UPN** L'attributo *SAMAccountName,* ad esempio , può essere generato automaticamente per alcuni account utente in un dominio gestito di Servizi di dominio Active Directory di Azure.The SAMAccountName attribute, such `AADDSCONTOSO\driley`as , may be auto-generated for some user accounts in an Azure AD DS managed domain. *SAMAccountName* generato automaticamente dagli utenti può differire dal prefisso UPN, quindi non è sempre un modo affidabile per accedere.
>
> Ad esempio, se più utenti hanno lo stesso attributo *mailNickname* o gli utenti hanno prefissi UPN eccessivamente lunghi, il *SAMAccountName* per questi utenti potrebbe essere generato automaticamente. Usare il formato UPN, ad `driley@aaddscontoso.com`esempio , per accedere in modo affidabile a un dominio gestito di Azure AD DS.

### <a name="attribute-mapping-for-user-accounts"></a>Mapping degli attributi per gli account utente

La tabella seguente illustra come gli attributi specifici per gli oggetti utente in Azure AD vengono sincronizzati con gli attributi corrispondenti in Servizi di dominio Active Directory di Azure.The following table illustrates how specific attributes for user objects in Azure AD are synchronized to corresponding attributes in Azure AD DS.

| Attributo utente in Azure ADUser attribute in Azure AD | Attributo utente in Azure AD DSUser attribute in Azure AD DS |
|:--- |:--- |
| accountEnabled |userAccountControl (imposta o cancella il bit ACCOUNT_DISABLED) |
| city |l |
| country |co |
| department |department |
| displayName |displayName |
| facsimileTelephoneNumber |facsimileTelephoneNumber |
| givenName |givenName |
| jobTitle |title |
| mail |mail |
| mailNickname |msDS-AzureADMailNickname |
| mailNickname |SAMAccountName (a volte può essere generato automaticamente) |
| mobile |mobile |
| objectid |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| passwordPolicies |userAccountControl (imposta o cancella il bit DONT_EXPIRE_PASSWORD) |
| physicalDeliveryOfficeName |physicalDeliveryOfficeName |
| postalCode |postalCode |
| preferredLanguage |preferredLanguage |
| state |st |
| streetAddress |streetAddress |
| surname |sn |
| telephoneNumber |telephoneNumber |
| userPrincipalName |userPrincipalName |

### <a name="attribute-mapping-for-groups"></a>Mapping degli attributi per i gruppi

La tabella seguente illustra come gli attributi specifici per gli oggetti gruppo in Azure AD vengono sincronizzati con gli attributi corrispondenti in Servizi di dominio Active Directory di Azure.The following table illustrates how specific attributes for group objects in Azure AD are synchronized to corresponding attributes in Azure AD DS.

| Attributo di gruppo in Azure ADGroup attribute in Azure AD | Attributo di gruppo in Azure AD DSGroup attribute in Azure AD DS |
|:--- |:--- |
| displayName |displayName |
| displayName |SAMAccountName (a volte può essere generato automaticamente) |
| mail |mail |
| mailNickname |msDS-AzureADMailNickname |
| objectid |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| securityEnabled |groupType |

## <a name="synchronization-from-on-premises-ad-ds-to-azure-ad-and-azure-ad-ds"></a>Sincronizzazione da Servizi di dominio Active Directory locale ad Azure AD e Azure AD DS

Azure AD Connect viene usato per sincronizzare gli account utente, le appartenenze ai gruppi e gli hashe sè delle credenziali da un ambiente di Servizi di dominio Active Directory locale ad Azure AD. Gli attributi degli account utente, ad esempio l'UPN e l'identificatore di protezione (SID) locale, vengono sincronizzati. Per accedere usando Azure AD DS, gli errori delle password legacy necessari per l'autenticazione NTLM e Kerberos vengono sincronizzati anche con Azure AD.

> [!IMPORTANT]
> Azure AD Connect deve essere installato e configurato solo per la sincronizzazione con gli ambienti AD DS locali. Non è supportata l'installazione di Azure AD Connect in un dominio gestito di Azure AD DS per sincronizzare gli oggetti con Azure AD.

Se si configura il writeback, le modifiche di Azure AD vengono sincronizzate nell'ambiente locale di Servizi di dominio Active Directory. Ad esempio, se un utente modifica la password usando la gestione delle password self-service di Azure AD, la password viene aggiornata nuovamente nell'ambiente locale di Servizi di dominio Active Directory.For example, if a user changes their password using Azure AD self-service password management, the password is updated back in the on-premises AD DS environment.

> [!NOTE]
> Usare sempre la versione più recente di Azure AD Connect per ottenere le correzioni per tutti i bug noti.

### <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>Sincronizzazione da un ambiente locale a più foreste

Molte organizzazioni dispongono di un ambiente di Servizi di dominio Active Directory locale piuttosto complesso che include più foreste. Azure AD Connect supporta la sincronizzazione di utenti, gruppi e credenziali da ambienti a più foreste ad Azure AD.

Azure AD ha uno spazio dei nomi molto più semplice e semplice. Per consentire agli utenti di accedere in modo affidabile ad applicazioni protette da Azure AD, risolvere i conflitti UPN tra gli account utente nelle varie foreste. I domini gestiti di Servizi di dominio Active Directory di Azure usano una struttura di unità organizzative flat, simile ad Azure AD. Tutti gli account utente e i gruppi vengono archiviati nel contenitore *Utenti AADDC,* nonostante siano sincronizzati da domini o foreste locali diversi, anche se è stata configurata una struttura gerarchica di unità organizzative locale. Il dominio gestito di Azure AD DS appiattisce qualsiasi struttura gerarchica di unità organizzative.

Come descritto in precedenza, non esiste alcuna sincronizzazione da Servizi di dominio Active Directory di Azure ad Azure AD. È possibile [creare un'unità organizzativa personalizzata](create-ou.md) in Servizi di dominio Active Directory di Azure e quindi account utente, gruppi o di servizio all'interno di tali unità organizzative personalizzate. Nessuno degli oggetti creati nelle aree utente personalizzate viene sincronizzato con Azure AD. Questi oggetti sono disponibili solo all'interno del dominio gestito di Azure AD DS e non sono visibili usando i cmdlet di Azure AD PowerShell, l'API Microsoft Graph o l'interfaccia utente di gestione di Azure AD.

## <a name="what-isnt-synchronized-to-azure-ad-ds"></a>Elementi non sincronizzati con Azure AD DS

Gli oggetti o gli attributi seguenti non vengono sincronizzati da un ambiente DiS di Servizi di dominio Active Directory locale ad Azure AD o Azure AD DS:The following objects or attributes aren't synchronized from an on-premises AD DS environment to Azure AD or Azure AD DS:

* **Attributi esclusi:** È possibile scegliere di escludere determinati attributi dalla sincronizzazione in Azure AD da un ambiente di Servizi di dominio Active Directory locale usando Azure AD Connect.You can choose to exclude certain attributes from synchronizing to Azure AD from an on-premises AD DS environment using Azure AD Connect. Questi attributi esclusi non sono quindi disponibili in Servizi di dominio Active Directory di Azure.These excluded attributes aren't then available in Azure AD DS.
* **Criteri di gruppo:** I criteri di gruppo configurati in un ambiente di Servizi di dominio Active Directory locale non vengono sincronizzati con Servizi di dominio Active Directory di Azure.Group Policies configured in an on-premises AD DS environment aren't synchronized to Azure AD DS.
* **Cartella Sysvol:** Il contenuto della cartella *Sysvol* in un ambiente di Dominio Active Directory locale non viene sincronizzato con Servizi di dominio Active Directory di Azure.
* **Oggetti computer:** Computer objects for computers joined to an on-premises AD DS environment aren't synchronized to Azure AD DS. Questi computer non hanno una relazione di trust con il dominio gestito di Servizi di dominio Active Directory di Azure e appartengono solo all'ambiente di Servizi di dominio Active Directory locale. In Servizi di dominio Active Directory di Azure vengono visualizzati solo gli oggetti computer per i computer con aggiunta esplicita a un componente di dominio al dominio gestito.
* **Attributi SidHistory per utenti e gruppi:** I SID dell'utente primario e del gruppo primario da un ambiente Servizi di dominio Active Directory locale vengono sincronizzati con Servizi di dominio Active Directory di Azure.The primary user and primary group SIDs from an on-premises AD DS environment are synchronized to Azure AD DS. Tuttavia, gli attributi *SidHistory* esistenti per utenti e gruppi non vengono sincronizzati dall'ambiente di Servizi di dominio Active Directory locale ad Azure AD DS.
* **Strutture unità organizzative (OU):** Le unità organizzative definite in un ambiente di Servizi di dominio Active Directory locale non vengono sincronizzate con Servizi di dominio Active Directory di Azure.Organizational Units defined in an on-premises AD DS environment don't synchronize to Azure AD DS. Esistono due opzioni di gestione delle sole predefinite in Azure AD DS, una per gli utenti e una per i computer. Il dominio gestito di Azure AD DS ha una struttura di unità organizzative semplice. È possibile scegliere [di creare un'unità organizzativa personalizzata nel dominio gestito.](create-ou.md)

## <a name="password-hash-synchronization-and-security-considerations"></a>Considerazioni sulla sicurezza e la sincronizzazione dell'hash delle password

Quando si abilita Azure AD DS, sono necessari gli iseo delle password legacy per l'autenticazione NTLM e Kerberos.When you enable Azure AD DS, legacy password hashes for NTLM - Kerberos authentication are required. Azure AD non archivia password non crittografate, pertanto questi errori non possono essere generati automaticamente per gli account utente esistenti. Una volta generato e archiviato, gli hash delle password compatibili con NTLM e Kerberos vengono sempre archiviati in modo crittografato in Azure AD.

Le chiavi di crittografia sono univoche per ogni tenant di Azure AD. Questi hash sono crittografati in modo che solo Azure AD DS abbia accesso alle chiavi di decrittografia. Nessun altro servizio o componente in Azure AD ha accesso alle chiavi di decrittografia.

Gli hashe delle password legacy vengono quindi sincronizzati da Azure AD nei controller di dominio per un dominio gestito di Servizi di dominio Active Directory di Azure.Legacy password hashes are then synchronized from Azure AD into the domain controllers for an Azure AD DS managed domain. I dischi per questi controller di dominio gestiti in Azure AD DS vengono crittografati inattivi. Questi isemi con password vengono archiviati e protetti in questi controller di dominio in modo simile alla modalità di archiviazione e protezione delle password in un ambiente di Servizi di dominio Active Directory locale.

Per gli ambienti Azure AD solo cloud, [gli utenti devono reimpostare/modificare la password](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) affinché gli errori di password necessari vengano generati e archiviati in Azure AD. Per qualsiasi account utente del cloud creato in Azure AD dopo l'abilitazione di Azure AD Domain Services, gli hash delle password sono generati e archiviati in formati compatibili con NTLM e Kerberos. Questi nuovi account non devono reimpostare o modificare la password per generare gli insiemi di password legacy.

Per gli account utente ibridi sincronizzati dall'ambiente di Servizi di dominio Active Directory locale tramite Azure AD Connect, è necessario [configurare Azure AD Connect per sincronizzare gli hashe delle password nei formati compatibili NTLM e Kerberos.](tutorial-configure-password-hash-sync.md)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle specifiche della sincronizzazione delle password, vedere Funzionamento della [sincronizzazione dell'hash delle password con Azure AD Connect.For](../active-directory/hybrid/how-to-connect-password-hash-synchronization.md?context=/azure/active-directory-domain-services/context/azure-ad-ds-context)more information on the specifics of password synchronization, see How password hash synchronization works with Azure AD Connect .

Per iniziare a usare Servizi di dominio Active Directory di Azure, [creare un dominio gestito.](tutorial-create-instance.md)
