---
title: Risolvere i problemi di connettività all'endpoint privato di Azure
description: Istruzioni dettagliate per la diagnosi della connettività degli endpoint privati
services: private-endpoint
documentationcenter: na
author: rdhillon
manager: narayan
editor: ''
ms.service: private-link
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: rdhillon
ms.openlocfilehash: f861f9efa6ecc1886647ed6c460b6718ff97e8a1
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95522329"
---
# <a name="troubleshoot-azure-private-endpoint-connectivity-problems"></a>Risolvere i problemi di connettività all'endpoint privato di Azure

Questo articolo fornisce istruzioni dettagliate per convalidare e diagnosticare la configurazione della connettività degli endpoint privati di Azure.

Endpoint privato di Azure è un'interfaccia di rete che si connette privatamente e in modo sicuro a un servizio di collegamento privato. Questa soluzione consente di proteggere i carichi di lavoro in Azure fornendo connettività privata alle risorse dei servizi di Azure dalla rete virtuale. Questa soluzione consente di portare i servizi alla rete virtuale.

Ecco gli scenari di connettività disponibili con l'endpoint privato:

- Rete virtuale dalla stessa area
- Reti virtuali con peering a livello di area
- Reti virtuali con peering globale
- Clienti locali tramite VPN o circuiti ExpressRoute di Azure

## <a name="diagnose-connectivity-problems"></a>Diagnosticare i problemi di connettività 

Esaminare questi passaggi per assicurarsi che tutte le configurazioni usuali siano quelle previste per risolvere i problemi di connettività con la configurazione dell'endpoint privato.

1. Esaminare la configurazione dell'endpoint privato esplorando la risorsa.

    a. Passare a **private Link Center**.

      ![Centro collegamenti privati](./media/private-endpoint-tsg/private-link-center.png)

    b. Nel riquadro sinistro selezionare **endpoint privati**.
    
      ![Endpoint privati](./media/private-endpoint-tsg/private-endpoints.png)

    c. Filtrare e selezionare l'endpoint privato che si desidera diagnosticare.

    d. Esaminare la rete virtuale e le informazioni DNS.
     - Verificare che lo stato della connessione sia **approvato**.
     - Assicurarsi che la macchina virtuale disponga della connettività alla rete virtuale che ospita gli endpoint privati.
     - Verificare che siano assegnate le informazioni di FQDN (copia) e l'indirizzo IP privato.
    
       ![Rete virtuale e configurazione DNS](./media/private-endpoint-tsg/vnet-dns-configuration.png)
    
1. Usare [monitoraggio di Azure](../azure-monitor/overview.md) per verificare se i dati vengono propagati.

    a. Nella risorsa endpoint privato selezionare **monitoraggio**.
     - Consente **di selezionare i dati in** uscita o in **uscita**. 
     - Verificare se i dati vengono propagati quando si tenta di connettersi all'endpoint privato. Si prevede un ritardo di circa 10 minuti.
    
       ![Verificare la telemetria dell'endpoint privato](./media/private-endpoint-tsg/private-endpoint-monitor.png)

1.  Usare la **risoluzione dei problemi di connessione della macchina virtuale** da Azure Network Watcher.

    a. Selezionare la macchina virtuale client.

    b. Selezionare **risoluzione dei problemi di connessione**, quindi selezionare la scheda **connessioni in uscita** .
    
      ![Network Watcher test delle connessioni in uscita](./media/private-endpoint-tsg/network-watcher-outbound-connection.png)
    
    c. Selezionare **usa Network Watcher per traccia dettagliata della connessione**.
    
      ![Risoluzione dei problemi di connessione Network Watcher](./media/private-endpoint-tsg/network-watcher-connection-troubleshoot.png)

    d. Selezionare **test per nome di dominio completo**.
     - Incollare il nome di dominio completo dalla risorsa endpoint privato.
     - Specificare una porta. In genere, usare 443 per archiviazione di Azure o Azure Cosmos DB e 1336 per SQL.

    e. Selezionare **test** e convalidare i risultati del test.
    
      ![Risultati del test Network Watcher](./media/private-endpoint-tsg/network-watcher-test-results.png)
    
        
1. La risoluzione DNS dai risultati del test deve avere lo stesso indirizzo IP privato assegnato all'endpoint privato.

    a. Se le impostazioni DNS non sono corrette, attenersi alla procedura seguente:
     - Se si usa una zona privata: 
       - Assicurarsi che la rete virtuale della VM client sia associata alla zona privata.
       - Verificare che sia presente il record della zona DNS privata. Se non esiste, crearlo.
     - Se si usa il DNS personalizzato:
       - Verificare le impostazioni DNS personalizzate e verificare che la configurazione DNS sia corretta.
       Per istruzioni, vedere [Cenni preliminari sugli endpoint privati: configurazione DNS](./private-endpoint-overview.md#dns-configuration).

    b. Se la connettività ha esito negativo a causa di gruppi di sicurezza di rete (gruppi) o route definite dall'utente:
     - Esaminare le regole in uscita NSG e creare le regole in uscita appropriate per consentire il traffico.
    
       ![Regole in uscita NSG](./media/private-endpoint-tsg/nsg-outbound-rules.png)

1. Se la connessione ha convalidato i risultati, il problema di connettività potrebbe essere correlato ad altri aspetti come segreti, token e password a livello di applicazione.
   - In questo caso, esaminare la configurazione della risorsa di collegamento privato associata all'endpoint privato. Per ulteriori informazioni, vedere la [Guida alla risoluzione dei problemi del collegamento privato di Azure](troubleshoot-private-link-connectivity.md).

1. Se il problema non è ancora risolto, contattare il team di [supporto di Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) ed esiste ancora un problema di connettività.

## <a name="next-steps"></a>Passaggi successivi

 * [Creare un endpoint privato nella subnet aggiornata (portale di Azure)](./create-private-endpoint-portal.md)
 * [Guida alla risoluzione dei problemi del collegamento privato di Azure](troubleshoot-private-link-connectivity.md)