---
title: Domande frequenti sul provisioning cloud di Azure AD Connect
description: Questo documento include le domande frequenti per il provisioning cloud.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/25/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ef482804c80602771963633bcc46feaf014c363
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85373829"
---
# <a name="azure-active-directory-connect-faq"></a>Domande frequenti su Azure Active Directory Connect

Leggere le domande frequenti per il provisioning cloud di Azure Active Directory (Azure AD) Connect.

## <a name="general-installation"></a>Installazione generale

**D: Con quale frequenza viene eseguito il provisioning cloud?**

Il provisioning cloud è pianificato per l'esecuzione ogni 2 minuti. Ogni 2 minuti, verrà effettuato il provisioning di qualsiasi modifica apportata all'hash delle password, a gruppi e a utenti.

**D: Alla prima esecuzione vengono visualizzati errori di sincronizzazione dell'hash della password. Perché?**

Si tratta di un comportamento previsto. Gli errori sono dovuti all'oggetto utente non presente in Azure AD. Una volta effettuato il provisioning dell'utente in Azure AD, il provisioning degli hash delle password verrà effettuato nell'esecuzione successiva. Attendere che vengano completate un paio di esecuzioni e verificare che la sincronizzazione degli hash delle password non generi più errori.

**D: cosa accade se l'istanza di Active Directory dispone di attributi non supportati dal provisioning cloud (ad esempio, le estensioni di directory)?**

Il provisioning cloud viene eseguito ed effettua il provisioning degli attributi supportati. Il provisioning degli attributi non supportati non verrà effettuato in Azure AD. Esaminare le estensioni della directory in Active Directory e assicurarsi che questi attributi non siano necessari per la propagazione Azure AD. Se uno o più attributi sono necessari, è consigliabile usare il servizio di sincronizzazione Azure AD Connect o spostare le informazioni richieste in uno degli attributi supportati, ad esempio gli attributi di estensioni 1-15.

**D: Qual è la differenza tra il servizio di sincronizzazione di Azure AD Connect e il provisioning cloud?**

Con il servizio di sincronizzazione di Azure AD Connect, il provisioning viene effettuato nel server di sincronizzazione locale. La configurazione viene archiviata nel server di sincronizzazione locale. Con il provisioning cloud di Azure AD Connect, la configurazione del provisioning viene archiviata ed eseguita nel cloud come parte del servizio di provisioning di Azure AD. 

**D: È possibile usare il provisioning cloud per la sincronizzazione da più foreste Active Directory?**

Sì. Il provisioning cloud può essere usato per la sincronizzazione da più foreste di Active Directory. Nell'ambiente a più foreste, tutti i riferimenti (ad esempio, Manager) devono trovarsi all'interno del dominio.  

**D: Come viene aggiornato l'agente?**

Gli agenti vengono aggiornati automaticamente da Microsoft. Per i team IT, in questo modo si riduce il carico di test e convalida delle nuove versioni degli agenti. 

**D: È possibile disabilitare l'aggiornamento automatico?**

La disabilitazione dell'aggiornamento automatico non è supportata.

**D: È possibile cambiare l'ancoraggio di origine per il provisioning cloud?**

Per impostazione predefinita, il provisioning cloud usa ms-ds-consistency-GUID con un fallback a ObjectGUID come ancoraggio di origine. La modifica dell'ancoraggio di origine non è supportata.

**D: Quando si usa il provisioning cloud, vengono visualizzate nuove entità servizio con i nomi di dominio di AD. È un comportamento previsto?**

Sì, il provisioning cloud crea un'entità servizio per la configurazione del provisioning, con lo stesso nome del dominio. Non apportare modifiche alla configurazione dell'entità servizio.

**D: Cosa succede quando un utente sincronizzato deve cambiare la password all'accesso successivo?**

Se la sincronizzazione degli hash delle password è abilitata nel provisioning cloud e l'utente sincronizzato è necessario per modificare la password all'accesso successivo in AD locale, il provisioning nel cloud non esegue il provisioning dell'hash della password "da modificare" a Azure AD. Dopo che l'utente ha cambiato la password, viene effettuato il provisioning dell'hash della password da AD ad Azure AD.

**D: Il provisioning cloud supporta il writeback di ms-ds-consistencyGUID per qualsiasi oggetto?**

No, il provisioning cloud non supporta il writeback di ms-ds-consistencyGUID per qualsiasi oggetto (inclusi gli oggetti utente). 

**D: Dopo aver effettuato il provisioning degli utenti con il provisioning cloud, si elimina la configurazione. Perché è ancora possibile vedere gli oggetti sincronizzati precedenti in Azure AD?** 

Quando si elimina la configurazione, il provisioning del cloud non rimuove automaticamente gli oggetti sincronizzati in Azure AD. Per assicurarsi che non siano presenti oggetti obsoleti, cambiare l'ambito della configurazione impostandolo su un gruppo o su unità organizzative vuote. Dopo l'effettuazione del provisioning e la pulizia degli oggetti, disabilitare ed eliminare la configurazione. 

**D:  Cosa significa che la distribuzione ibrida di Exchange non è supportata?**

La funzionalità Distribuzione ibrida di Exchange consente la coesistenza di cassette postali di Exchange in locale e in Office 365. Azure AD Connect sincronizza un set specifico di attributi da Azure AD alla directory locale.  L'agente di provisioning cloud non sincronizza attualmente questi attributi nella directory locale e quindi non può essere usato in sostituzione di Azure AD Connect.

**D:  È possibile installare l'agente di provisioning cloud in Windows Server Core?**

No, l'installazione dell'agente in Server Core non è supportata.

**D: è possibile utilizzare un server di staging con l'agente di provisioning cloud?**

No, i server di gestione temporanea non sono supportati.

## <a name="next-steps"></a>Passaggi successivi 

- [Cos'è il provisioning?](what-is-provisioning.md)
- [Che cos'è il provisioning cloud di Azure AD Connect?](what-is-cloud-provisioning.md)
