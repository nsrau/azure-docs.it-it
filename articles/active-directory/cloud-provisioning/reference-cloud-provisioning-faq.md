---
title: Domande frequenti sul provisioning cloud di Azure AD Connect
description: Questo documento include le domande frequenti per il provisioning cloud.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 93c88b167884c455ffb995f35356b121bce8a207
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794991"
---
# <a name="azure-active-directory-connect-faq"></a>Domande frequenti su Azure Active Directory Connect

Leggere le domande frequenti per il provisioning cloud di Azure Active Directory (Azure AD) Connect.

## <a name="general-installation"></a>Installazione generale

**D: Con quale frequenza viene eseguito il provisioning cloud?**

Il provisioning cloud è pianificato per l'esecuzione ogni 2 minuti. Ogni 2 minuti, verrà effettuato il provisioning di qualsiasi modifica apportata all'hash delle password, a gruppi e a utenti.

**D: Alla prima esecuzione vengono visualizzati errori di sincronizzazione dell'hash della password. Perché?**

Si tratta di un comportamento previsto. Gli errori sono dovuti all'oggetto utente non presente in Azure AD. Una volta effettuato il provisioning dell'utente in Azure AD, il provisioning degli hash delle password verrà effettuato nell'esecuzione successiva. Attendere che vengano completate un paio di esecuzioni e verificare che la sincronizzazione degli hash delle password non generi più errori.

**D: Qual è la differenza tra il servizio di sincronizzazione di Azure AD Connect e il provisioning cloud?**

Con il servizio di sincronizzazione di Azure AD Connect, il provisioning viene effettuato nel server di sincronizzazione locale. La configurazione viene archiviata nel server di sincronizzazione locale. Con il provisioning cloud di Azure AD Connect, la configurazione del provisioning viene archiviata ed eseguita nel cloud come parte del servizio di provisioning di Azure AD. 

**D: È possibile usare il provisioning cloud per la sincronizzazione da più foreste Active Directory?**

Sì. Il provisioning cloud può essere usato per la sincronizzazione da più foreste di Active Directory. Nell'ambiente a più foreste, tutti i riferimenti (ad esempio, Manager) devono trovarsi all'interno del dominio.  

**D: Come viene aggiornato l'agente?**

Gli agenti vengono aggiornati automaticamente da Microsoft. In questo modo si riduce il carico di test e convalida delle nuove versioni degli agenti per l'IT. 

**D: È possibile disabilitare l'aggiornamento automatico?**

La disabilitazione dell'aggiornamento automatico non è supportata.

**D: È possibile cambiare l'ancoraggio di origine per il provisioning cloud?**

Per impostazione predefinita, il provisioning cloud usa ms-ds-consistency-GUID con un fallback a ObjectGUID come ancoraggio di origine. La modifica dell'ancoraggio di origine non è supportata.

**D: Quando si usa il provisioning cloud, vengono visualizzate nuove entità servizio con i nomi di dominio di AD. È un comportamento previsto?**

Sì, il provisioning cloud crea un'entità servizio per la configurazione del provisioning, con lo stesso nome del dominio. Non apportare modifiche alla configurazione dell'entità servizio.

**D: Cosa succede quando un utente sincronizzato deve cambiare la password all'accesso successivo?**

Se la sincronizzazione degli hash delle password è abilitata nel provisioning cloud e l'utente sincronizzato deve cambiare la password all'accesso successivo in AD locale, il provisioning cloud non viene effettuato sull'hash della password da cambiare in Azure AD. Dopo che l'utente ha cambiato la password, viene effettuato il provisioning dell'hash della password da AD ad Azure AD.

**D: Il provisioning cloud supporta il writeback di ms-ds-consistencyGUID per qualsiasi oggetto?**

No, il provisioning cloud non supporta il writeback di ms-ds-consistencyGUID per qualsiasi oggetto (inclusi gli oggetti utente). 

**D: Dopo aver effettuato il provisioning degli utenti con il provisioning cloud, si elimina la configurazione. Perché è ancora possibile vedere gli oggetti sincronizzati precedenti in Azure AD?** 

Quando si elimina la configurazione, il provisioning cloud non pulisce gli oggetti sincronizzati in Azure AD. Per assicurarsi che non siano presenti oggetti obsoleti, cambiare l'ambito della configurazione impostandolo su un gruppo o su unità organizzative vuote. Dopo l'effettuazione del provisioning e la pulizia degli oggetti, disabilitare ed eliminare la configurazione. 

## <a name="next-steps"></a>Passaggi successivi 

- [Cos'è il provisioning?](what-is-provisioning.md)
- [Che cos'è il provisioning cloud di Azure AD Connect?](what-is-cloud-provisioning.md)
