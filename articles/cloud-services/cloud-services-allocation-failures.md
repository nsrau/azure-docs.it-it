---
title: Risoluzione dei problemi relativi a errori di allocazione di servizi cloud | Documentazione Microsoft
description: Risoluzione dei problemi relativi ad errori di allocazione quando si distribuiscono servizi Cloud in Azure
services: azure-service-management, cloud-services
documentationcenter: 
author: simonxjx
manager: felixwu
editor: 
tags: top-support-issue
ms.assetid: 529157eb-e4a1-4388-aa2b-09e8b923af74
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 7/26/2017
ms.author: v-six
ms.translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: c91a34eb34a73abe5c5ac2bb6aeb08c818a97856
ms.contentlocale: it-it
ms.lasthandoff: 11/17/2016

---
# <a name="troubleshooting-allocation-failure-when-you-deploy-cloud-services-in-azure"></a>Risoluzione dei problemi relativi ad errori di allocazione quando si distribuiscono servizi Cloud in Azure
## <a name="summary"></a>Riepilogo
Quando si distribuiscono istanze a un servizio Cloud o si aggiungono nuove istanze del ruolo di lavoro o web, Microsoft Azure alloca le risorse di calcolo. In alcuni casi possono verificarsi errori quando si eseguono queste operazioni anche prima di raggiungere i limiti della sottoscrizione di Azure. Questo articolo illustra le cause di alcuni dei più comuni errori di allocazione e suggerisce una possibile correzione. Queste informazioni potrebbero risultare utili anche quando si pianifica la distribuzione dei servizi.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

### <a name="background--how-allocation-works"></a>Informazioni preliminari: come funziona l'allocazione
I server nei data center di Azure sono partizionati in cluster. Una nuova richiesta di allocazione del servizio cloud viene eseguita in più cluster. Quando la prima istanza è distribuita in un servizio cloud (in gestione temporanea o produzione), il servizio cloud viene bloccato su un cluster. Tutte le altre distribuzioni per il servizio cloud verranno eseguite nello stesso cluster. In questo articolo si farà riferimento a questa operazione con il termine "bloccata su un cluster". Il diagramma 1 seguente illustra un'allocazione normale eseguita in più cluster. Il diagramma 2 illustra un'allocazione bloccata sul Cluster 2, perché è quello che ospita il servizio cloud CS_1.

![Diagramma di allocazione](./media/cloud-services-allocation-failure/Allocation1.png)

### <a name="why-allocation-failure-happens"></a>Perché si verifica un errore di allocazione
Quando una richiesta di allocazione è bloccata su un cluster, la probabilità di non riuscire a trovare risorse disponibili è più alta, perché il pool di risorse disponibili è limitato ad un cluster. Inoltre, se la richiesta di allocazione è bloccata su un cluster, ma il tipo di risorsa richiesto non è supportato da quel cluster, la richiesta non riuscirà anche se nel cluster ci sono risorse disponibili. Il diagramma 3 seguente illustra un'allocazione bloccata non riuscita perché nel solo cluster candidato non ci sono risorse disponibili. Il diagramma 4 illustra un'allocazione bloccata non riuscita perché il solo cluster candidato non supporta le dimensioni della VM richieste, anche se nel cluster ci sono risorse disponibili.

![Errore di allocazione bloccata](./media/cloud-services-allocation-failure/Allocation2.png)

## <a name="troubleshooting-allocation-failure-for-cloud-services"></a>Risoluzione di problemi di allocazione non riuscita per i servizi cloud
### <a name="error-message"></a>Messaggio di errore
Se viene visualizzato il messaggio di errore seguente:

    "Azure operation '{operation id}' failed with code Compute.ConstrainedAllocationFailed. Details: Allocation failed; unable to satisfy constraints in request. The requested new service deployment is bound to an Affinity Group, or it targets a Virtual Network, or there is an existing deployment under this hosted service. Any of these conditions constrains the new deployment to specific Azure resources. Please retry later or try reducing the VM size or number of role instances. Alternatively, if possible, remove the aforementioned constraints or try deploying to a different region."

### <a name="common-issues"></a>Problemi comuni
Ecco gli scenari di allocazione comuni che causano una richiesta di allocazione bloccata a un solo cluster.

* La distribuzione in uno slot di gestione temporanea: se un servizio cloud dispone di una distribuzione in uno slot, l’intero servizio cloud è bloccato a un cluster specifico.  Ciò significa che se una distribuzione già esiste nello slot di produzione, una nuova distribuzione di gestione temporanea può essere allocata solo nello stesso cluster come slot di produzione. Se la capacità del cluster è quasi stata raggiunta, la richiesta potrebbe avere esito negativo.
* Scalabilità: l’aggiunta di nuove istanze a un servizio cloud esistente deve allocare nello stesso cluster.  Le piccole richieste di ridimensionamento in genere possono essere allocate, ma non è sempre possibile. Se la capacità del cluster è quasi stata raggiunta, la richiesta potrebbe avere esito negativo.
* Gruppo di affinità: una nuova distribuzione in un servizio cloud vuoto può essere allocata dall'infrastruttura in qualsiasi cluster in tale area, a meno che il servizio cloud sia bloccato a un gruppo di affinità. Le distribuzioni allo stesso gruppo di affinità verranno eseguite nello stesso cluster. Se la capacità del cluster è quasi stata raggiunta, la richiesta potrebbe avere esito negativo.
* Gruppo di affinità tra reti: le reti virtuali meno recenti vengono legate a gruppi di affinità anziché ad aree e i servizi cloud in tali reti virtuali verrebbero bloccati al cluster del gruppo di affinità. Le distribuzioni a questo tipo di rete virtuale verranno eseguite nel cluster bloccato. Se la capacità del cluster è quasi stata raggiunta, la richiesta potrebbe avere esito negativo.

## <a name="solutions"></a>Soluzioni
1. Ridistribuzione in un nuovo servizio cloud. Questa soluzione potrebbe essere la più efficace in quanto consente alla piattaforma di scegliere tra tutti i cluster in tale area.

   * Distribuire il carico di lavoro in un nuovo servizio cloud  
   * Aggiornare il CNAME o un record per puntare il traffico al nuovo servizio cloud
   * Quando nel sito precedente non c’è nessun traffico, è possibile eliminare il precedente servizio cloud. Questa soluzione non deve causare tempi di inattività.
2. Eliminazione di slot di produzione e di gestione temporanea: questa soluzione conserverà il nome DNS esistente, ma determinerà tempi di inattività dell'applicazione.

   * Eliminare slot di produzione e di gestione temporanea di un servizio cloud esistente in modo che il servizio cloud sia vuoto, quindi
   * creare una nuova distribuzione nel servizio cloud esistente. Questo tenterà nuovamente di eseguire l'allocazione in tutti i cluster nell'area. Assicurarsi che il servizio cloud non sia associato a un gruppo di affinità.
3. IP riservato: questa soluzione consente di mantenere l'indirizzo IP esistente, ma determinerà tempi di inattività dell'applicazione.  

   * Creare un ReservedIP per la distribuzione esistente utilizzando Powershell

     ```
     New-AzureReservedIP -ReservedIPName {new reserved IP name} -Location {location} -ServiceName {existing service name}
     ```
   * Seguire #2 dall'alto, assicurandosi di specificare il nuovo ReservedIP nel CSCFG del servizio.
4. Rimuovere il gruppo di affinità per le nuove distribuzioni: i gruppi di affinità non sono più consigliati. Attenersi alla procedura #1 indicata in precedenza per distribuire un nuovo servizio cloud. Assicurarsi che il servizio cloud non sia in un gruppo di affinità.
5. Eseguire la conversione a una rete virtuale regionale: vedere [Come eseguire la migrazione da gruppi di affinità a una rete virtuale (VNet) regionale](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).

