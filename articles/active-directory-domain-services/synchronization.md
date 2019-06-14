---
title: 'Azure Active Directory Domain Services: Sincronizzazione nei domini gestiti | Microsoft Docs'
description: Informazioni sulla sincronizzazione in un dominio gestito di Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 57cbf436-fc1d-4bab-b991-7d25b6e987ef
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: mstephen
ms.openlocfilehash: 295a991e610e76971413a2abdba1e2fcc5f9eba6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66246690"
---
# <a name="synchronization-in-an-azure-ad-domain-services-managed-domain"></a>Sincronizzazione in un dominio gestito di Azure Active Directory Domain Services
Il diagramma seguente illustra il funzionamento della sincronizzazione nei domini gestiti di Azure Active Directory Domain Services.

![Sincronizzazione in Azure AD Domain Services](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-your-on-premises-directory-to-your-azure-ad-tenant"></a>Sincronizzazione dalla directory locale al tenant di Azure AD
Il servizio di sincronizzazione Azure AD Connect viene usato per sincronizzare account utente, appartenenza a gruppi e hash delle credenziali nel tenant di Azure AD. Vengono sincronizzati gli attributi degli account utente, come l'UPN e l'ID di sicurezza (SID). Se si usa Azure Active Directory Domain Services, anche gli hash delle credenziali legacy richiesti per l'autenticazione NTLM e Kerberos vengono sincronizzati nel tenant di Azure AD.

Se si configura il writeback, le modifiche apportate alla directory di Azure AD vengono sincronizzate nell'istanza locale di Active Directory. Ad esempio, se si modifica la password usando le funzionalità di modifica delle password self-service di Azure AD, la password modificata viene aggiornata nel dominio locale di Active Directory.

> [!NOTE]
> Usare sempre la versione più recente di Azure AD Connect per ottenere le correzioni per tutti i bug noti.
>
>

## <a name="synchronization-from-your-azure-ad-tenant-to-your-managed-domain"></a>Sincronizzazione dal tenant di Azure AD al dominio gestito
Account utente, appartenenza a gruppi e hash delle credenziali vengono sincronizzati dal tenant di Azure AD al dominio gestito di Azure Active Directory Domain Services. Questo processo di sincronizzazione è automatico, non è quindi necessario configurarlo, monitorarlo o gestirlo. La sincronizzazione iniziale può richiedere da alcune ore a un paio di giorni a seconda del numero di oggetti presenti nella directory di Azure AD. Dopo che la sincronizzazione iniziale è stata completata, occorrono circa 20-30 minuti perché le modifiche apportate in Azure AD siano aggiornate nel dominio gestito. Questo intervallo di sincronizzazione si applica a modifiche della password o a modifiche agli attributi apportati in Azure AD.

Il processo di sincronizzazione è anche di natura unidirezionale. Il dominio gestito è in gran parte di sola lettura, ad eccezione delle eventuali unità organizzative personalizzate create. Non è quindi possibile apportare modifiche agli attributi utente, alle password utente o all'appartenenza a gruppi all'interno del dominio gestito. Di conseguenza, non viene eseguita alcuna sincronizzazione inversa delle modifiche dal dominio gestito al tenant di Azure AD.

## <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>Sincronizzazione da un ambiente locale a più foreste
Molte organizzazioni hanno un'infrastruttura di identità locale piuttosto complessa composta da più foreste account. Azure AD Connect supporta la sincronizzazione di utenti, gruppi e hash delle credenziali da ambienti a più foreste nel tenant di Azure AD.

Il tenant di Azure AD è invece uno spazio dei nomi piatto molto più semplice. Per consentire agli utenti di accedere in modo affidabile ad applicazioni protette da Azure AD, risolvere i conflitti UPN tra gli account utente nelle varie foreste. Il dominio gestito di Azure Active Directory Domain Services somiglia molto al tenant di Azure AD. Nel dominio gestito viene visualizzata una struttura di unità organizzative piatta. Tutti i gruppi e account utente vengono archiviati all'interno del contenitore "AADDC Users", nonostante la sincronizzazione da foreste o domini locali diversi. Anche se in locale è stata configurata una struttura gerarchica di unità organizzative, il dominio gestito presenta comunque una semplice struttura di unità organizzative piatta.

## <a name="exclusions---what-isnt-synchronized-to-your-managed-domain"></a>Esclusioni: cosa non viene sincronizzato nel dominio gestito
Gli oggetti o attributi indicati di seguito non vengono sincronizzati nel tenant di Azure AD o nel dominio gestito:

* **Attributi esclusi:** è possibile scegliere di escludere determinati attributi dalla sincronizzazione nel tenant di Azure AD dal dominio locale usando Azure AD Connect. Gli attributi esclusi non sono disponibili nel dominio gestito.
* **Criteri di gruppo:** i criteri di gruppo configurati nel dominio locale non vengono sincronizzati nel dominio gestito.
* **Condivisione Sysvol:** il contenuto della condivisione SYSVOL nel dominio locale non viene sincronizzato nel dominio gestito.
* **Oggetti computer:** gli oggetti computer per i computer aggiunti al dominio locale non vengono sincronizzati nel dominio gestito. Tali computer non hanno una relazione di trust con il dominio gestito e appartengono solo al dominio locale. Nel dominio gestito sono presenti oggetti computer solo per i computer aggiunti in modo esplicito al dominio gestito.
* **Attributi SidHistory per utenti e gruppi:** gli ID di sicurezza (SID) dell'utente primario e del gruppo primario dal dominio locale vengono sincronizzati nel dominio gestito. Tuttavia, gli attributi SidHistory esistenti per utenti e gruppi non vengono sincronizzati dal dominio locale nel dominio gestito.
* **Strutture di unità organizzative (OU):** le unità organizzative definite nel dominio locale non vengono sincronizzate nel dominio gestito. Nel dominio gestito sono presenti due unità organizzative incorporate. Per impostazione predefinita, il dominio gestito presenta una struttura di unità organizzative piatta. È tuttavia possibile scegliere di [creare un'unità organizzativa personalizzata nel dominio gestito](create-ou.md).

## <a name="how-specific-attributes-are-synchronized-to-your-managed-domain"></a>Sincronizzazione di attributi specifici nel dominio gestito
La tabella seguente riporta alcuni attributi comuni e il modo in cui vengono sincronizzati nel dominio gestito.

| Attributo nel dominio gestito | `Source` | Note |
|:--- |:--- |:--- |
| UPN |Attributo UPN dell'utente nel tenant di Azure AD |L'attributo UPN del tenant di Azure AD viene sincronizzato così com'è nel dominio gestito. Il modo più affidabile per accedere al dominio gestito è quindi l'uso dell'UPN. |
| SAMAccountName |Attributo mailNickname dell'utente nel tenant di Azure AD o generato automaticamente |L'attributo SAMAccountName è originato dall'attributo mailNickname nel tenant di Azure AD. Se più account utente hanno lo stesso attributo mailNickname, l'attributo SAMAccountName viene generato automaticamente. Se l'attributo mailNickname o il prefisso UPN dell'utente ha una lunghezza superiore a 20 caratteri, l'attributo SAMAccountName viene generato automaticamente per soddisfare il limite di 20 caratteri relativo agli attributi SAMAccountName. |
| Password |Password utente del tenant di Azure AD |Gli hash delle credenziali necessari per l'autenticazione NTLM o Kerberos, anche detti credenziali supplementari, vengono sincronizzati dal tenant di Azure AD. Se il tenant di Azure AD è un tenant sincronizzato, tali credenziali vengono originate dal dominio locale. |
| SID utente/gruppo primario |Generato automaticamente |L'ID di sicurezza primario per gli account di gruppo o gli account utente viene generato automaticamente nel dominio gestito. Questo attributo non corrisponde all'ID di sicurezza utente/gruppo primario dell'oggetto nel dominio Active Directory locale. La mancata corrispondenza è dovuta alla differenza di spazio dei nomi dell'ID di sicurezza tra il dominio gestito e il dominio locale. |
| Cronologia SID per utenti e gruppi |SID utente/gruppo primario locale |L'attributo SidHistory per utenti e gruppi nel dominio gestito viene impostato in modo che corrisponda all'ID di sicurezza utente o gruppo primario nel dominio locale. Questa funzionalità consente di semplificare il potenziamento e lo spostamento delle applicazioni locali nel dominio gestito, perché elimina la necessità di creare un nuovo elenco di controllo di accesso delle risorse. |

> [!NOTE]
> **Accedere al dominio gestito usando il formato UPN:** per alcuni account utente nel dominio gestito l'attributo SAMAccountName può essere generato automaticamente. Se più utenti hanno lo stesso attributo mailNickname o sono presenti utenti con un prefisso UPN troppo lungo, l'attributo SAMAccountName per questi utenti potrebbe essere generato automaticamente. Il formato SAMAccountName, ad esempio "CONTOSO100\joeuser", non è quindi un modo sempre affidabile per accedere al dominio. L'attributo SAMAccountName autogenerato dell'utente potrebbe infatti essere diverso dal relativo prefisso UPN. Per accedere al dominio gestito in modo affidabile, usare il formato UPN, ad esempio 'joeuser@contoso100.com'.

### <a name="attribute-mapping-for-user-accounts"></a>Mapping degli attributi per gli account utente
La tabella seguente mostra come vengono sincronizzati attributi specifici per gli oggetti utente nel tenant di Azure AD con gli attributi corrispondenti nel dominio gestito.

| Attributo utente nel tenant di Azure AD | Attributo utente nel dominio gestito |
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
| objectId |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| passwordPolicies |userAccountControl (imposta o cancella il bit DONT_EXPIRE_PASSWORD) |
| physicalDeliveryOfficeName |physicalDeliveryOfficeName |
| postalCode |postalCode |
| preferredLanguage |preferredLanguage |
| stato |st |
| streetAddress |streetAddress |
| surname |sn |
| telephoneNumber |telephoneNumber |
| userPrincipalName |userPrincipalName |

### <a name="attribute-mapping-for-groups"></a>Mapping degli attributi per i gruppi
La tabella seguente mostra come vengono sincronizzati attributi specifici per gli oggetti gruppo nel tenant di Azure AD con gli attributi corrispondenti nel dominio gestito.

| Attributo di gruppo nel tenant di Azure AD | Attributo di gruppo nel dominio gestito |
|:--- |:--- |
| displayName |displayName |
| displayName |SAMAccountName (a volte può essere generato automaticamente) |
| mail |mail |
| mailNickname |msDS-AzureADMailNickname |
| objectId |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| securityEnabled |groupType |

## <a name="password-hash-synchronization-and-security-considerations"></a>Considerazioni sulla sicurezza e la sincronizzazione dell'hash delle password
Quando si abilita Azure AD Domain Services, la directory di Azure AD genera e archivia gli hash delle password in formati compatibili con NTLM e Kerberos. 

Per gli account utente del cloud esistenti, poiché Azure AD non archivia mai password non crittografate, questi hash non possono essere generati automaticamente. Pertanto, Microsoft richiede che [gli utenti del cloud reimpostino o modifichino le proprie password](active-directory-ds-getting-started-password-sync.md) affinché gli hash delle password siano generati e archiviati in Azure AD. Per qualsiasi account utente del cloud creato in Azure AD dopo l'abilitazione di Azure AD Domain Services, gli hash delle password sono generati e archiviati in formati compatibili con NTLM e Kerberos. 

Per gli account utente sincronizzati da AD locale mediante la sincronizzazione di Azure AD Connect, è necessario [configurare Azure AD Connect per la sincronizzazione degli hash delle password in formati compatibili con NTLM e Kerberos](active-directory-ds-getting-started-password-sync-synced-tenant.md).

Gli hash delle password compatibili con NTLM e Kerberos sono sempre archiviati in modo crittografato in Azure AD. Questi hash sono crittografati in maniera tale che solo Azure AD Domain Services possa accedere alle chiavi di decrittografia. Nessun altro servizio o componente in Azure AD ha accesso alle chiavi di decrittografia. Le chiavi di crittografia sono univoche a livello di tenant di Azure AD. Azure AD Domain Services sincronizza gli hash delle password nei controller di dominio per il dominio gestito. Questi hash delle password sono archiviati e protetti in questi controller di dominio analogamente a come le password sono archiviate e protette nei controller di dominio di AD in Windows Server. I dischi per questi controller di dominio gestiti sono crittografati a dati inattivi.

## <a name="objects-that-are-not-synchronized-to-your-azure-ad-tenant-from-your-managed-domain"></a>Oggetti che non vengono sincronizzati nel tenant di Azure AD dal dominio gestito
Come descritto in una sezione precedente di questo articolo, non viene eseguita alcuna sincronizzazione dal dominio gestito al tenant di Azure AD. È tuttavia possibile scegliere di [creare un'unità organizzativa personalizzata](create-ou.md) nel dominio gestito. All'interno delle queste unità organizzative personalizzate è possibile creare anche altre unità organizzative, utenti, gruppi o account di servizio. Gli oggetti creati all'interno di unità organizzative personalizzate non vengono sincronizzati nel tenant di Azure AD, ma sono disponibili per l'uso unicamente all'interno del dominio gestito. Tali oggetti non sono quindi visibili tramite i cmdlet di PowerShell per Azure AD, l'API Graph di Azure AD o l'interfaccia utente di gestione di Azure AD.

## <a name="related-content"></a>Contenuto correlato
* [Funzionalità - Servizi di dominio Azure AD](active-directory-ds-features.md)
* [Scenari di distribuzione - Servizi di dominio Azure AD](scenarios.md)
* [Considerazioni sulla rete per Azure AD Domain Services](network-considerations.md)
* [Introduzione ad Azure AD Domain Services](create-instance.md)
