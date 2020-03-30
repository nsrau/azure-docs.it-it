---
title: 'Azure AD Connect: msExchUserHoldPolicies e cloudMsExchUserHoldPolicies Documenti Microsoft'
description: In questo argomento viene descritto il comportamento degli attributi degli attributi msExchUserHoldPolicies e cloudMsExchUserHoldPolicies
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/23/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4c637a01825616334cda8faa594efd08f29de8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74213083"
---
# <a name="azure-ad-connect---msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>Azure AD Connect - msExchUserHoldPolicies e cloudMsExchUserHoldPolicies
Nel documento di riferimento seguente vengono descritti questi attributi utilizzati da Exchange e il modo corretto per modificare le regole di sincronizzazione predefinite.

## <a name="what-are-msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>Che cosa sono msExchUserHoldPolicies e cloudMsExchUserHoldPolicies?
Sono disponibili due tipi di [blocchi](https://docs.microsoft.com/Exchange/policy-and-compliance/holds/holds?view=exchserver-2019) per Exchange Server: conservazione per controversia legale e archiviazione sul posto. Quando conservazione per controversia legale è abilitato, tutti gli elementi della cassetta postale vengono messi in attesa.  Un'archiviazione sul posto viene utilizzata per mantenere solo gli elementi che soddisfano i criteri di una query di ricerca definita utilizzando lo strumento eDiscovery sul posto.

Gli attributi MsExchUserHoldPolcies e cloudMsExchUserHoldPolicies consentono ad Active Directory locale e Azure AD di determinare quali utenti sono in attesa a seconda che utilizzino Exchange locale o Exchange online.

## <a name="msexchuserholdpolicies-synchronization-flow"></a>Flusso di sincronizzazione di msExchUserHoldPolicies
Per impostazione predefinita MsExchUserHoldPolcies viene sincronizzato da Azure AD Connect direttamente con l'attributo msExchUserHoldPolicies nel metaverse e quindi all'attributo msExchUserHoldPolices in Azure AD

Le tabelle seguenti descrivono il flusso:The following tables describe the flow:

In ingresso da Active Directory locale:

|Attributo di Active Directory|Nome attributo|Tipo di flusso|Attributo metaverse|Regola di sincronizzazione|
|-----|-----|-----|-----|-----|
|Active Directory locale|msExchUserHoldPolicies|Connessione diretta|MsExchUserHoldPolices|In da Active Directory - Scambio utenti|

In uscita in uscita in Azure AD:Outbound to Azure AD:

|Attributo metaverse|Nome attributo|Tipo di flusso|Attributo di Azure AD|Regola di sincronizzazione|
|-----|-----|-----|-----|-----|
|Azure Active Directory|msExchUserHoldPolicies|Connessione diretta|msExchUserHoldPolicies|Uscita in AAD – UserExchangeOnline|

## <a name="cloudmsexchuserholdpolicies-synchronization-flow"></a>Flusso di sincronizzazione cloudMsExchUserHoldPolicies
Per impostazione predefinita, cloudMsExchUserHoldPolicies viene sincronizzato da Azure AD Connect direttamente all'attributo cloudMsExchUserHoldPolicies nel metaverse. Quindi, se msExchUserHoldPolices non è null nel metaverse, l'attributo in flusso in Active Directory.

Le tabelle seguenti descrivono il flusso:The following tables describe the flow:

In ingresso da Azure AD:Inbound from Azure AD:

|Attributo di Active Directory|Nome attributo|Tipo di flusso|Attributo metaverse|Regola di sincronizzazione|
|-----|-----|-----|-----|-----|
|Active Directory locale|CloudMsExchUserHoldPolicies|Connessione diretta|CloudMsExchUserHoldPolicies|In da AAD - Scambio utenti|

In uscita in Active Directory locale:

|Attributo metaverse|Nome attributo|Tipo di flusso|Attributo di Azure AD|Regola di sincronizzazione|
|-----|-----|-----|-----|-----|
|Azure Active Directory|CloudMsExchUserHoldPolicies|IF(NON NULL)|msExchUserHoldPolicies|Uscita per Active Directory - UserExchangeOnline|

## <a name="information-on-the-attribute-behavior"></a>Informazioni sul comportamento dell'attributo
msExchangeUserHoldPolicies è un singolo attributo di autorità.  Un singolo attributo authority può essere impostato su un oggetto (in questo caso, oggetto utente) nella directory locale o nella directory cloud.  Le regole di inizio dell'autorità stabiliscono che se l'attributo viene sincronizzato dall'ambiente locale, Azure AD non sarà autorizzato ad aggiornare questo attributo.

Per consentire agli utenti di impostare un criterio di conservazione su un oggetto utente nel cloud, viene utilizzato l'attributo cloudMSExchangeUserHoldPolicies. Questo attributo viene usato perché Azure AD non può impostare msExchangeUserHoldPolicies direttamente in base alle regole illustrate in precedenza.  Questo attributo verrà quindi sincronizzato nuovamente nella directory locale se msExchangeUserHoldPolicies non è null e sostituisce il valore corrente di msExchangeUserHoldPolicies.

In determinate circostanze, ad esempio, se entrambi sono stati modificati in locale e in Azure allo stesso tempo, ciò potrebbe causare alcuni problemi.  

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md).
