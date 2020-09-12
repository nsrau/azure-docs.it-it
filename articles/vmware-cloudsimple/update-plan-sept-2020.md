---
title: Aggiornamento della soluzione VMware di Azure di CloudSimple settembre 2020
description: In questo articolo vengono fornite informazioni su cosa aspettarsi durante questa operazione di manutenzione e le modifiche apportate al cloud privato.
author: dikamath
ms.author: dikamath
ms.date: 09/3/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
manager: dikamath
ms.openlocfilehash: ae9c1ba5259e95ed030d7099e5dafe2d4f7935b4
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2020
ms.locfileid: "89447979"
---
# <a name="azure-vmware-solution-by-cloudsimple-september-2020-update"></a>Aggiornamento della soluzione VMware di Azure di CloudSimple settembre 2020

Un aggiornamento importante del servizio della soluzione VMware di Azure verrà eseguito a settembre. Una notifica di posta elettronica, inviata come parte della manutenzione, includerà la sequenza temporale della manutenzione. In questo articolo vengono fornite informazioni su cosa aspettarsi durante questa operazione di manutenzione e le modifiche apportate al cloud privato.

> [!NOTE]
> Si tratta di un aggiornamento senza problemi. Durante l'aggiornamento, è possibile che venga visualizzato uno dei componenti ridondanti.

## <a name="vmware-infrastructure-upgrade"></a>Aggiornamento dell'infrastruttura VMware

L'infrastruttura VMware del cloud privato verrà aggiornata a una versione più recente. Sono inclusi gli aggiornamenti ai componenti vCenter, ESXi, NSX-T e Hybrid Cloud Extension (HCX) (se distribuiti) del cloud privato.

Durante l'aggiornamento, un nuovo nodo verrà aggiunto al cloud privato prima che un nodo venga inserito in modalità di manutenzione per l'operazione di aggiornamento. In questo modo si garantisce che la capacità del cloud privato e la disponibilità del cloud privato vengano mantenute durante il processo di aggiornamento. Durante l'aggiornamento dei componenti VMware, è possibile che vengano visualizzati avvisi sull'interfaccia utente Web di vCenter. Gli allarmi fanno parte delle operazioni di manutenzione eseguite dal team operativo del servizio.

**Versione componente**

- ESXi 6.7 U3
- vCenter 6.7 U3
- Rete VSAN 6,7
- Data Center di NSX 2.5.1
- 3.5.2 HCX

## <a name="datacenter-updates"></a>Aggiornamenti del Data Center

Questo aggiornamento include gli aggiornamenti dell'infrastruttura del Data Center. Durante il periodo di manutenzione verranno eseguiti aggiornamenti non problematici. Durante il processo di aggiornamento si noterà una riduzione della ridondanza. È possibile che vengano generati avvisi nell'infrastruttura VMware del cloud privato, i circuiti ExpressRoute, le connessioni GlobalReach e tutti i dispositivi VPN da sito a sito correlati a una delle disponibilità dei collegamenti. Si tratta di una situazione normale durante l'aggiornamento perché i componenti verranno riavviati come parte dell'aggiornamento.

-   Se una VPN da sito a sito viene distribuita come una singola istanza (non a disponibilità elevata), potrebbe essere necessario ristabilire la connessione VPN.

-   Se si usa una connessione VPN da punto a sito, sarà necessario ristabilire la connessione VPN.

## <a name="post-update"></a>Post-aggiornamento

Una volta completati gli aggiornamenti, verranno visualizzate le versioni più recenti dei componenti VMware. Se si riscontrano problemi o si verificano domande, contattare il team di supporto aprendo un [ticket di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
