---
title: Risolvere i problemi di connettività all'endpoint privato di Azure
description: Istruzioni dettagliate per diagnosticare la connettività degli endpoint privati
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
ms.openlocfilehash: fcc482e6231bbd925fd500a37989052765dede58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77538535"
---
# <a name="troubleshoot-azure-private-endpoint-connectivity-problems"></a>Risolvere i problemi di connettività all'endpoint privato di Azure

Questo articolo fornisce istruzioni dettagliate per convalidare e diagnosticare l'impostazione della connettività dell'endpoint privato di Azure.This article provides step-by-step guidance to validate and diagnose your Azure Private Endpoint connectivity setup.

L'endpoint privato di Azure è un'interfaccia di rete che si connette privatamente e in modo sicuro a un servizio di collegamento privato. Questa soluzione consente di proteggere i carichi di lavoro in Azure fornendo connettività privata alle risorse del servizio di Azure dalla rete virtuale. Questa soluzione porta effettivamente tali servizi alla rete virtuale.

Di seguito sono riportati gli scenari di connettività disponibili con Private Endpoint:Here are the connectivity scenarios that are available with Private Endpoint:

- Rete virtuale dalla stessa areaVirtual network from the same region
- Reti virtuali con peered a livello regionale
- Reti virtuali con peered globale
- Cliente locale su circuiti VPN o Azure ExpressRoute

## <a name="diagnose-connectivity-problems"></a>Diagnosticare i problemi di connettività 

Esaminare questi passaggi per assicurarsi che tutte le configurazioni usuali siano come previsto per risolvere i problemi di connettività con la configurazione dell'endpoint privato.

1. Esaminare la configurazione dell'endpoint privato esplorando la risorsa.

    a. Passare a **Centro collegamenti privati**.

      ![Centro collegamenti privati](./media/private-endpoint-tsg/private-link-center.png)

    b. Nel riquadro sinistro selezionare **Endpoint privati**.
    
      ![Endpoint privati](./media/private-endpoint-tsg/private-endpoints.png)

    c. Filtrare e selezionare l'endpoint privato che si desidera diagnosticare.

    d. Esaminare la rete virtuale e le informazioni DNS.
     - Verificare che lo stato della connessione sia **Approvato**.
     - Assicurarsi che la macchina virtuale disponga della connettività alla rete virtuale che ospita gli endpoint privati.
     - Verificare che siano assegnate le informazioni sul nome di dominio completo (copia) e l'indirizzo IP privato.
    
       ![Configurazione della rete virtuale e del DNS](./media/private-endpoint-tsg/vnet-dns-configuration.png)
    
1. Usare [Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/overview) per verificare se i dati scorrono.

    a. Nella risorsa endpoint privato selezionare **Monitora**.
     - Selezionare **Dati in** o **Uscita dati**. 
     - Verificare se i dati scorrono quando si tenta di connettersi all'endpoint privato. Aspettatevi un ritardo di circa 10 minuti.
    
       ![Verificare la telemetria dell'endpoint privatoVerify private endpoint telemetry](./media/private-endpoint-tsg/private-endpoint-monitor.png)

1.  Usare **la risoluzione dei problemi di connessione VM** da Azure Network Watcher.Use VM Connection troubleshoot from Azure Network Watcher.

    a. Selezionare la macchina virtuale client.

    b. Selezionare **Risoluzione dei problemi di connessione**, quindi selezionare la scheda Connessioni in **uscita** .
    
      ![Network Watcher - Testare le connessioni in uscita](./media/private-endpoint-tsg/network-watcher-outbound-connection.png)
    
    c. Selezionare **Usa Network Watcher per la traccia dettagliata della connessione**.
    
      ![Network Watcher - Risoluzione dei problemi di connessione](./media/private-endpoint-tsg/network-watcher-connection-troubleshoot.png)

    d. Selezionare **Test in base al nome di dominio completo**.
     - Incollare il nome di dominio completo dalla risorsa endpoint privato.
     - Fornire una porta. In genere, usare 443 per Archiviazione di Azure o database Cosmos di Azure e 1336 per SQL.

    e. Selezionare **Test**e convalidare i risultati del test.
    
      ![Network Watcher - Risultati del test](./media/private-endpoint-tsg/network-watcher-test-results.png)
    
        
1. La risoluzione DNS dai risultati del test deve avere lo stesso indirizzo IP privato assegnato all'endpoint privato.

    a. Se le impostazioni DNS non sono corrette, attenersi alla seguente procedura:
     - Se si utilizza una zona privata: 
       - Assicurarsi che la rete virtuale della macchina virtuale client sia associata all'area privata.
       - Verificare che il record della zona DNS privata esista. Se non esiste, crearlo.
     - Se si utilizza DNS personalizzato:
       - Esaminare le impostazioni DNS personalizzate e verificare che la configurazione DNS sia corretta.
       Per istruzioni, vedere [Panoramica degli endpoint privati: configurazione DNS](https://docs.microsoft.com/azure/private-link/private-endpoint-overview#dns-configuration).

    b. Se la connettività non riesce a causa di gruppi di sicurezza di rete (NSG) o route definite dall'utente:If connectivity is failing because of network security groups (NSGs) or user-defined routes:
     - Esaminare le regole in uscita del gruppo di sicurezza di gruppo di sicurezza di gruppo e creare le regole in uscita appropriate per consentire il traffico.
    
       ![Regole in uscita del gruppo di sicurezza di gruppo](./media/private-endpoint-tsg/nsg-outbound-rules.png)

1. Se la connessione ha convalidato i risultati, il problema di connettività potrebbe essere correlato ad altri aspetti come segreti, token e password a livello di applicazione.
   - In questo caso, esaminare la configurazione della risorsa collegamento privato associata all'endpoint privato. Per altre informazioni, vedere la guida alla [risoluzione dei problemi](troubleshoot-private-link-connectivity.md)di Azure Private Link .

1. Contattare il team di supporto di [Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) se il problema persiste e si verifica ancora un problema di connettività.

## <a name="next-steps"></a>Passaggi successivi

 * [Creare un endpoint privato nella subnet aggiornata (portale di Azure)Create a private endpoint on the updated subnet (Azure portal)](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal)
 * [Guida alla risoluzione dei problemi di Azure Private LinkAzure Private Link troubleshooting guide](troubleshoot-private-link-connectivity.md)
