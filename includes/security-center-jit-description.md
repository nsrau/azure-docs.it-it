---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 02/24/2020
ms.topic: include
ms.openlocfilehash: c77849b2285283a34e6adf84dc3845a4076407af
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77597942"
---
## <a name="attack-scenario"></a>Scenario di attacco

Gli attacchi di forza bruta generalmente prendono di mira le porte di gestione per tentare di ottenere l'accesso a una macchina virtuale. Se l'attacco ha esito positivo, un utente malintenzionato può assumere il controllo della macchina virtuale e penetrare nell'ambiente.

Un modo per ridurre l'esposizione agli attacchi di forza bruta consiste nel limitare la quantità di tempo per cui la porta è aperta. Non è necessario aprire le porte di gestione in qualsiasi momento. Devono essere aperti solo quando si è connessi alla macchina virtuale, ad esempio per eseguire attività di gestione o manutenzione. Quando è abilitata l'opzione JIT, il Centro sicurezza usa il [gruppo di sicurezza di rete](../articles/virtual-network/security-overview.md#security-rules) (NSG) e le regole del firewall di Azure, che limitano l'accesso alle porte di gestione in modo che non possano essere usate dagli utenti malintenzionati.

![Scenario JIT](../articles/security-center/media/security-center-just-in-time/just-in-time-scenario.png)

## <a name="how-does-jit-access-work"></a>Come funziona l'accesso JIT?

Quando è abilitata la funzionalità JIT, Centro sicurezza protegge il traffico in ingresso alle macchine virtuali di Azure creando una regola NSG. Selezionare le porte nella macchina virtuale per cui proteggere il traffico in ingresso. Queste porte sono controllate dalla soluzione JIT.

Quando un utente richiede l'accesso a una macchina virtuale, il Centro sicurezza verifica che l'utente disponga delle autorizzazioni di [controllo degli accessi in base al ruolo (RBAC)](../articles/role-based-access-control/role-assignments-portal.md) per tale macchina virtuale. Se la richiesta viene approvata, il Centro sicurezza configura automaticamente i gruppi di sicurezza di rete (gruppi) e il firewall di Azure per consentire il traffico in ingresso alle porte selezionate e gli intervalli o gli indirizzi IP di origine richiesti per il periodo di tempo specificato. Al termine di questo periodo, Centro sicurezza ripristina gli stati precedenti dei gruppi di sicurezza di rete. Le connessioni ancora attive non vengono tuttavia interrotte.

 > [!NOTE]
 > Se una richiesta di accesso JIT viene approvata per una macchina virtuale dietro un firewall di Azure, il Centro sicurezza modifica automaticamente le regole dei criteri NSG e firewall. Per la quantità di tempo specificata, le regole consentono il traffico in ingresso verso le porte selezionate e gli indirizzi IP o gli intervalli di origine richiesti. Al termine del periodo di tempo, il Centro sicurezza Ripristina le regole firewall e NSG agli stati precedenti.


## <a name="permissions-needed-to-configure-and-use-jit"></a>Autorizzazioni necessarie per configurare e usare JIT

| Per consentire a un utente di: | Autorizzazioni da impostare|
| --- | --- |
| Configurare o modificare un criterio JIT per una macchina virtuale | *Assegnare queste azioni al ruolo:*  <ul><li>Nell'ambito di una sottoscrizione o di un gruppo di risorse associato alla macchina virtuale:<br/> `Microsoft.Security/locations/jitNetworkAccessPolicies/write` </li><li> Nell'ambito di una sottoscrizione o di un gruppo di risorse della macchina virtuale: <br/>`Microsoft.Compute/virtualMachines/write`</li></ul> | 
|Richiedere l'accesso JIT a una macchina virtuale | *Assegnare queste azioni all'utente:*  <ul><li>Nell'ambito di una sottoscrizione o di un gruppo di risorse associato alla macchina virtuale:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action` </li><li>Nell'ambito di una sottoscrizione o di un gruppo di risorse associato alla macchina virtuale:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/*/read` </li><li>  Nell'ambito di una sottoscrizione o di un gruppo di risorse o di una macchina virtuale:<br/> `Microsoft.Compute/virtualMachines/read` </li><li>  Nell'ambito di una sottoscrizione o di un gruppo di risorse o di una macchina virtuale:<br/> `Microsoft.Network/networkInterfaces/*/read` </li></ul>|