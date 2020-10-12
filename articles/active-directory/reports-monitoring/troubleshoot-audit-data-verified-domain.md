---
title: Risolvere i problemi relativi ai dati di controllo della modifica del dominio verificato | Microsoft Docs
description: Fornisce informazioni che verranno visualizzate nei log attività Azure Active Directory quando si modifica un dominio verificato dagli utenti.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/22/2020
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3c9ec3b1e96e47dbf46c6acb2c81147b614d069
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87117430"
---
# <a name="troubleshoot-audit-data-on-verified-domain-change"></a>Risoluzione dei problemi: controllare i dati sulla modifica del dominio verificato 


## <a name="i-have-a-lot-of-changes-to-my-users-and-i-am-not-sure-what-the-cause-of-it-is"></a>Ho una grande quantità di modifiche agli utenti e non so quale sia la sua origine.

### <a name="symptoms"></a>Sintomi

Si controllano i log di controllo Azure AD e si verificano più aggiornamenti utente nel tenant di Azure AD. Questi eventi **utente di aggiornamento** non visualizzano le informazioni sugli **attori** , causando incertezze su cosa/chi ha attivato le modifiche di massa agli utenti. 

### <a name="cause"></a>Causa

 Un motivo comune alla base delle modifiche apportate agli oggetti di massa è un'operazione back-end non sincrona denominata **ProxyCalc**.  **ProxyCalc** è la logica che determina gli indirizzi **userPrincipalName** e **Proxy**appropriati, aggiornati in Azure ad utenti, gruppi o contatti. La progettazione alla base di **ProxyCalc** è garantire che tutti **gli indirizzi** **userPrincipalName** e proxy siano coerenti in Azure ad in qualsiasi momento. **ProxyCalc** deve essere attivato da una modifica esplicita come una modifica del dominio verificato e non viene eseguita continuamente in background come attività. 

  

#### <a name="what-does-userprincipalname-consistency-mean"></a>Cosa significa la coerenza UserPrincipalName? 

Per gli utenti solo cloud, la coerenza indica che **userPrincipalName** è impostato su un suffisso di dominio verificato. Quando viene elaborato un **userPrincipalName** incoerente, **ProxyCalc** lo converte nel suffisso onmicrosoft.com predefinito, ad esempio: username@Contoso.onmicrosoft.com 

Per gli utenti sincronizzati, la coerenza indica che **userPrincipalName** è impostato su un suffisso di dominio verificato e che corrisponde al valore **userPrincipalName** locale (ShadowUserPrincipalName). Quando viene elaborato un **userPrincipalName** incoerente, **ProxyCalc** ripristina lo stesso valore di **ShadowUserPrincipalName** o, nel caso in cui il suffisso di dominio sia stato rimosso dal tenant, lo convertirà nel suffisso di dominio *. onmicrosoft.com predefinito. 

  

#### <a name="what-does-proxy-address-consistency-mean"></a>Cosa significa la coerenza degli indirizzi proxy? 

Per gli utenti solo cloud, la coerenza indica che gli indirizzi proxy corrispondono a un suffisso di dominio verificato. Quando viene elaborato un indirizzo proxy incoerente, **ProxyCalc** lo converte nel suffisso di dominio *. onmicrosoft.com predefinito, ad esempio: SMTP:username@Contoso.onmicrosoft.com 

Per gli utenti sincronizzati, la coerenza indica che gli indirizzi proxy corrispondono ai valori degli indirizzi proxy locali (es), ovvero ShadowProxyAddresses). Si prevede che **proxyAddresses** sia sincronizzato con **ShadowProxyAddresses**. Se per l'utente sincronizzato è stata assegnata una licenza di Exchange, è necessario che gli indirizzi proxy corrispondano ai valori degli indirizzi proxy locali e che corrispondano anche a un suffisso di dominio verificato. In questo scenario, **ProxyCalc** eliminerà l'indirizzo proxy incoerente con un suffisso di dominio non verificato e verrà rimosso dall'oggetto in Azure ad. Se il dominio non verificato viene verificato in un secondo momento, **ProxyCalc** ricalcolo e aggiunge l'indirizzo proxy da **ShadowProxyAddresses** all'oggetto in Azure ad.  

> [!NOTE]
> Per gli oggetti sincronizzati, per evitare che la logica **ProxyCalc** calcoli risultati imprevisti, è preferibile impostare gli indirizzi proxy su un Azure ad dominio verificato nell'oggetto locale.  

  
Una delle attività amministrative che possono attivare **ProxyCalc** è ogni volta che viene verificata una modifica del dominio. Questa attività viene eseguita ogni volta che un dominio verificato viene aggiunto o rimosso da un tenant di Azure AD, che attiva internamente **ProxyCalc**.  

Ad esempio, se si aggiunge un dominio verificato Fabrikam.com al tenant Contoso.onmicrosoft.com, questa azione attiverà un'operazione ProxyCalc su tutti gli oggetti nel tenant. Questo evento verrà acquisito nei registri di controllo Azure AD come eventi di **aggiornamento utente** preceduti da un evento del **dominio di aggiunta verificata** . D'altra parte, se Fabrikam.com è stato rimosso dal tenant di Contoso.onmicrosoft.com, tutti gli eventi **dell'utente di aggiornamento** saranno preceduti da un evento **Rimuovi dominio verificato** .   

#### <a name="additional-notes"></a>Note aggiuntive:

ProxyCalc non provoca modifiche a determinati oggetti che: 

- non dispone di una licenza di Exchange attiva 
- **MSExchRemoteRecipientType** è impostato su null 
- non sono considerate risorse condivise. La risorsa condivisa è quando **CloudMSExchRecipientDisplayType** contiene uno dei valori seguenti: **MailboxUser (Shared)**, **PublicFolder**, **ConferenceRoomMailbox**, **EquipmentMailbox**, **ArbitrationMailbox**, **room** **, TeamMailboxUser,** **gruppo Mailbox**, **scheduling Mailbox**, **ACLableMailboxUser**, **ACLableTeamMailboxUser** 
  
 Per creare una maggiore correlazione tra questi due eventi diversi, Microsoft sta lavorando per aggiornare le informazioni sull' **attore** nei log di controllo per identificare queste modifiche come attivato da una modifica del dominio verificato. Questa azione consente di controllare quando l'evento di modifica del dominio verificato ha avuto luogo e ha iniziato a eseguire l'aggiornamento di massa degli oggetti nel tenant. 

Inoltre, nella maggior parte dei casi, non sono presenti modifiche agli utenti poiché i loro **userPrincipalName** e **gli indirizzi proxy** sono coerenti, quindi stiamo lavorando per visualizzare nei log di controllo solo gli aggiornamenti che hanno causato una modifica effettiva all'oggetto. Questa azione impedisce il rumore nei log di controllo e gli amministratori della Guida correlano le modifiche degli utenti rimanenti all'evento di modifica del dominio verificato, come illustrato in precedenza. 

## <a name="next-steps"></a>Passaggi successivi

[Attributi shadow del servizio di sincronizzazione Azure AD Connect](../hybrid/how-to-connect-syncservice-shadow-attributes.md)
