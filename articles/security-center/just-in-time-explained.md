---
title: Informazioni sull'accesso just-in-time alle macchine virtuali nel centro sicurezza di Azure
description: Questo documento illustra il modo in cui l'accesso just-in-time alle macchine virtuali nel centro sicurezza di Azure consente di controllare l'accesso alle macchine virtuali di Azure
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: be2aa75fb7c532d48188493b2ed09adc8b141b6a
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92340020"
---
# <a name="understanding-just-in-time-jit-vm-access"></a>Informazioni sull'accesso JIT alle macchine virtuali

Questa pagina illustra i principi alla base della funzionalità di accesso alla macchina virtuale JIT (just-in-Time) del Centro sicurezza di Azure e la logica alla base di questa raccomandazione.

Per informazioni su come applicare JIT alle macchine virtuali usando il portale di Azure (Centro sicurezza o macchine virtuali di Azure) o a livello, vedere [How to secure the Management ports with JIT](security-center-just-in-time.md).


## <a name="the-risk-of-open-management-ports-on-a-virtual-machine"></a>Il rischio di aprire porte di gestione in una macchina virtuale

Gli attori minacciano attivamente la ricerca di computer accessibili con porte di gestione aperte, ad esempio RDP o SSH. Tutte le macchine virtuali sono obiettivi potenziali per un attacco. Quando una VM viene compromessa, viene usata come punto di ingresso per attaccare altre risorse nell'ambiente.



## <a name="why-jit-vm-access-is-the-solution"></a>Perché l'accesso alla macchina virtuale JIT è la soluzione 

Come per tutte le tecniche di prevenzione Cybersecurity, l'obiettivo consiste nel ridurre la superficie di attacco. In questo caso, significa avere meno porte aperte, in particolare le porte di gestione.

Anche gli utenti legittimi usano queste porte, quindi non è pratico tenerle chiuse.

Per risolvere questo problema, il Centro sicurezza di Azure offre JIT. Con JIT, è possibile bloccare il traffico in ingresso verso le macchine virtuali, riducendo l'esposizione agli attacchi offrendo un accesso facile per connettersi alle VM quando necessario.



## <a name="how-jit-operates-with-network-security-groups-and-azure-firewall"></a>Funzionamento di JIT con i gruppi di sicurezza di rete e il firewall di Azure

Quando si Abilita l'accesso just-in-time alle macchine virtuali, è possibile selezionare le porte nella macchina virtuale in cui verrà bloccato il traffico in ingresso. Il Centro sicurezza garantisce che esistano le regole "nega tutto il traffico in ingresso" per le porte selezionate nel [gruppo di sicurezza di rete](../virtual-network/network-security-groups-overview.md#security-rules) (NSG) e [le regole del firewall di Azure](../firewall/rule-processing.md). Queste regole limitano l'accesso alle porte di gestione delle macchine virtuali di Azure e le difendono dagli attacchi. 

Se sono già presenti altre regole per le porte selezionate, le regole esistenti hanno la priorità sulla nuova regola "nega tutto il traffico in ingresso". Se non sono presenti regole esistenti sulle porte selezionate, le nuove regole hanno la priorità più alta in NSG e nel firewall di Azure.

Quando un utente richiede l'accesso a una macchina virtuale, il Centro sicurezza verifica che l'utente disponga delle autorizzazioni di [controllo degli accessi in base al ruolo di Azure](../role-based-access-control/role-assignments-portal.md) per tale macchina virtuale. Se la richiesta viene approvata, il Centro sicurezza Configura gruppi e il firewall di Azure per consentire il traffico in ingresso alle porte selezionate dall'indirizzo IP (o intervallo) pertinente per il periodo di tempo specificato. Al termine di questo periodo, Centro sicurezza ripristina gli stati precedenti dei gruppi di sicurezza di rete. Le connessioni già stabilite non vengono interrotte.

> [!NOTE]
> JIT non supporta le macchine virtuali protette dai firewall di Azure controllati da [gestione firewall di Azure](../firewall-manager/overview.md).




## <a name="how-security-center-identifies-which-vms-should-have-jit-applied"></a>Come il Centro sicurezza identifica le macchine virtuali con l'applicazione JIT

Il diagramma seguente mostra la logica applicata dal centro sicurezza quando si decide come suddividere in categorie le macchine virtuali supportate: 

[![Flusso logico della macchina virtuale (VM) just-in-time (JIT)](media/just-in-time-explained/jit-logic-flow.png)](media/just-in-time-explained/jit-logic-flow.png#lightbox)

Quando il Centro sicurezza trova un computer che può trarre vantaggio da JIT, aggiunge tale macchina alla scheda **risorse non integre** della raccomandazione. 

![Indicazione di accesso alle macchine virtuali JIT (just-in-Time)](./media/just-in-time-explained/unhealthy-resources.png)


## <a name="faq---questions-about-just-in-time-virtual-machine-access"></a>Domande frequenti sull'accesso just-in-time alle macchine virtuali

### <a name="what-permissions-are-needed-to-configure-and-use-jit"></a>Quali autorizzazioni sono necessarie per configurare e usare JIT?

Se si desidera creare ruoli personalizzati che possono essere utilizzati con JIT, saranno necessari i dettagli della tabella riportata di seguito.

> [!TIP]
> Per creare un ruolo con privilegi minimi per gli utenti che devono richiedere l'accesso JIT a una macchina virtuale e non eseguire altre operazioni JIT, usare lo [script set-JitLeastPrivilegedRole](https://github.com/Azure/Azure-Security-Center/tree/master/Powershell%20scripts/JIT%20Custom%20Role) delle pagine della community di GitHub del Centro sicurezza.

| Per consentire a un utente di: | Autorizzazioni da impostare|
| --- | --- |
| Configurare o modificare un criterio JIT per una macchina virtuale | *Assegnare queste azioni al ruolo:*  <ul><li>Nell'ambito di una sottoscrizione o di un gruppo di risorse associato alla macchina virtuale:<br/> `Microsoft.Security/locations/jitNetworkAccessPolicies/write` </li><li> Nell'ambito di una sottoscrizione o di un gruppo di risorse della macchina virtuale: <br/>`Microsoft.Compute/virtualMachines/write`</li></ul> | 
|Richiedere l'accesso JIT a una macchina virtuale | *Assegnare queste azioni all'utente:*  <ul><li>Nell'ambito di una sottoscrizione o di un gruppo di risorse associato alla macchina virtuale:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action` </li><li>Nell'ambito di una sottoscrizione o di un gruppo di risorse associato alla macchina virtuale:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/*/read` </li><li>  Nell'ambito di una sottoscrizione o di un gruppo di risorse o di una macchina virtuale:<br/> `Microsoft.Compute/virtualMachines/read` </li><li>  Nell'ambito di una sottoscrizione o di un gruppo di risorse o di una macchina virtuale:<br/> `Microsoft.Network/networkInterfaces/*/read` </li></ul>|
|Lettura di criteri JIT| *Assegnare queste azioni all'utente:*  <ul><li>`Microsoft.Security/locations/jitNetworkAccessPolicies/read`</li><li>`Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action`</li><li>`Microsoft.Security/policies/read`</li><li>`Microsoft.Compute/virtualMachines/read`</li><li>`Microsoft.Network/*/read`</li>|
|||





## <a name="next-steps"></a>Passaggi successivi

In questa pagina è stato illustrato il _motivo per cui_ usare l'accesso alle macchine virtuali JIT (just-in-Time). 

Passare all'articolo sulle procedure per informazioni sull'abilitazione di JIT e sulla richiesta di accesso alle macchine virtuali abilitate per JIT:

> [!div class="nextstepaction"]
> [Come proteggere le porte di gestione con JIT](security-center-just-in-time.md)