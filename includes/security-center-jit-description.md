---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 02/24/2020
ms.topic: include
ms.openlocfilehash: c77849b2285283a34e6adf84dc3845a4076407af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597942"
---
## <a name="attack-scenario"></a>Scenario di attacco

Gli attacchi di forza bruta generalmente prendono di mira le porte di gestione per tentare di ottenere l'accesso a una macchina virtuale. Se l'attacco ha esito positivo, un utente malintenzionato può assumere il controllo della macchina virtuale e penetrare nell'ambiente.

Un modo per ridurre l'esposizione agli attacchi di forza bruta consiste nel limitare la quantità di tempo per cui la porta è aperta. Le porte di gestione non devono essere sempre aperte. Devono essere aperti solo quando si è connessi alla macchina virtuale, ad esempio per eseguire attività di gestione o manutenzione. Quando il gruppo di sicurezza usa il gruppo di sicurezza di [rete](../articles/virtual-network/security-overview.md#security-rules) (NSG) e le regole del firewall di Azure, che limitano l'accesso alle porte di gestione in modo che non possano essere prese di mira da utenti malintenzionati.

![Scenario JIT](../articles/security-center/media/security-center-just-in-time/just-in-time-scenario.png)

## <a name="how-does-jit-access-work"></a>Come funziona l'accesso JIT?

Quando è abilitata la funzionalità JIT, Centro sicurezza protegge il traffico in ingresso alle macchine virtuali di Azure creando una regola NSG. Selezionare le porte nella macchina virtuale per cui proteggere il traffico in ingresso. Queste porte sono controllate dalla soluzione JIT.

Quando un utente richiede l'accesso a una macchina virtuale, il Centro sicurezza verifica che l'utente disponga delle autorizzazioni di controllo degli accessi in [base](../articles/role-based-access-control/role-assignments-portal.md) al ruolo per tale macchina virtuale. Se la richiesta viene approvata, il Centro sicurezza configura automaticamente i gruppi di sicurezza di rete e Firewall di Azure per consentire il traffico in ingresso verso le porte selezionate e gli indirizzi IP di origine o gli intervalli richiesti per il periodo di tempo specificato. Al termine di questo periodo, Centro sicurezza ripristina gli stati precedenti dei gruppi di sicurezza di rete. Le connessioni ancora attive non vengono tuttavia interrotte.

 > [!NOTE]
 > Se una richiesta di accesso JIT viene approvata per una macchina virtuale dietro un firewall di Azure, il Centro sicurezza modifica automaticamente le regole del gruppo di sicurezza di rete e dei criteri del firewall. Per il periodo di tempo specificato, le regole consentono il traffico in ingresso verso le porte selezionate e gli indirizzi o gli intervalli IP di origine richiesti. Al termine del tempo, il Centro sicurezza ripristina le regole del firewall e del gruppo di sicurezza di rete ai relativi stati precedenti.


## <a name="permissions-needed-to-configure-and-use-jit"></a>Autorizzazioni necessarie per configurare e usare JIT

| Per consentire a un utente di: | Autorizzazioni da impostare|
| --- | --- |
| Configurare o modificare un criterio JIT per una macchina virtualeConfigure or edit a JIT policy for a VM | *Assegnare queste azioni al ruolo:*  <ul><li>Nell'ambito di una sottoscrizione o di un gruppo di risorse associato alla macchina virtuale:On the scope of a subscription or resource group that is associated with the VM:<br/> `Microsoft.Security/locations/jitNetworkAccessPolicies/write` </li><li> Nell'ambito di una sottoscrizione o di un gruppo di risorse di VM:On the scope of a subscription or resource group of VM: <br/>`Microsoft.Compute/virtualMachines/write`</li></ul> | 
|Richiedere l'accesso JIT a una macchina virtuale | *Assegnare queste azioni all'utente:*  <ul><li>Nell'ambito di una sottoscrizione o di un gruppo di risorse associato alla macchina virtuale:On the scope of a subscription or resource group that is associated with the VM:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action` </li><li>Nell'ambito di una sottoscrizione o di un gruppo di risorse associato alla macchina virtuale:On the scope of a subscription or resource group that is associated with the VM:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/*/read` </li><li>  Nell'ambito di una sottoscrizione, di un gruppo di risorse o di una macchina virtuale:On the scope of a subscription or resource group or VM:<br/> `Microsoft.Compute/virtualMachines/read` </li><li>  Nell'ambito di una sottoscrizione, di un gruppo di risorse o di una macchina virtuale:On the scope of a subscription or resource group or VM:<br/> `Microsoft.Network/networkInterfaces/*/read` </li></ul>|