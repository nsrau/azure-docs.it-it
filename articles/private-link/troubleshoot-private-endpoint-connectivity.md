---
title: Risolvere i problemi di connettività degli endpoint privati di Azure
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
ms.openlocfilehash: 1c3fc4ebaffe2347a9c46b626b94042d1c9cfe11
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031855"
---
# <a name="troubleshoot-private-endpoint-connectivity-problems"></a>Risolvere i problemi di connettività degli endpoint privati

Questa guida fornisce istruzioni dettagliate per convalidare e diagnosticare la configurazione della connettività degli endpoint privati. 

Endpoint privato di Azure è un'interfaccia di rete che si connette privatamente e in modo sicuro a un servizio di collegamento privato. Questa soluzione consente di proteggere i carichi di lavoro in Azure fornendo connettività privata alle risorse dei servizi di Azure dalla rete virtuale. Questa operazione consente di portare i servizi alla rete virtuale. 

Ecco gli scenari di connettività disponibili con gli endpoint privati 
- rete virtuale dalla stessa area 
- reti virtuali con peering a livello di area
- reti virtuali con peering globale
- clienti locali tramite VPN o circuiti Express Route

## <a name="diagnosing-connectivity-problems"></a>Diagnosi dei problemi di connettività 
Eseguire i passaggi elencati di seguito per assicurarsi che tutte le configurazioni usuali siano quelle previste per risolvere i problemi di connettività con la configurazione dell'endpoint privato.

1. Esaminare la configurazione dell'endpoint privato esplorando la risorsa 

    a) passare a **collegamento privato**

      ![Centro collegamenti privati](./media/private-endpoint-tsg/private-link-center.png)

    b) selezionare gli endpoint privati dal riquadro di spostamento a sinistra
    
      ![Endpoint privati](./media/private-endpoint-tsg/private-endpoints.png)

    c) filtrare e selezionare l'endpoint privato che si desidera diagnosticare

    d) esaminare la rete virtuale e le informazioni DNS
    
     - Convalida dello stato della connessione **approvata**
     - Assicurarsi che la macchina virtuale disponga della connettività al VNet che ospita gli endpoint privati
     - Informazioni di FQDN (copia) e indirizzo IP privato assegnati
    
       ![Configurazione di VNet e DNS](./media/private-endpoint-tsg/vnet-dns-configuration.png)    
    
2. Usare [**monitoraggio di Azure**](https://docs.microsoft.com/azure/azure-monitor/overview) per esaminare il flusso dei dati

    a) nella risorsa endpoint privato selezionare **monitoraggio**
     - Selezionare i dati o i dati e verificare se i dati vengono propagati durante il tentativo di connessione all'endpoint privato. Si prevede un ritardo di circa 10 minuti.
    
       ![Verificare la telemetria dell'endpoint privato](./media/private-endpoint-tsg/private-endpoint-monitor.png)

3. Usare la risoluzione dei problemi di connessione della macchina virtuale da **Network Watcher**

    a) selezionare la macchina virtuale client

    b) selezionare la sezione **risoluzione dei problemi di connessione** , scheda **connessione in uscita**
    
      ![Network Watcher test delle connessioni in uscita](./media/private-endpoint-tsg/network-watcher-outbound-connection.png)
    
    c) selezionare **usa Network Watcher per la traccia della connessione dettagliata**
    
      ![Risoluzione dei problemi di connessione Network Watcher](./media/private-endpoint-tsg/network-watcher-connection-troubleshoot.png)

    d) selezionare **test per nome di dominio completo**
     - Incollare il nome di dominio completo dalla risorsa endpoint privato
     - Fornire una porta (*in genere 443 per archiviazione di Azure o Cosmos, 1336 per SQL...* )

    e) fare clic su **test** e convalidare i risultati del test
    
      ![Risultati del test Network Watcher](./media/private-endpoint-tsg/network-watcher-test-results.png)
    
        
4. La risoluzione DNS dai risultati del test deve avere lo stesso indirizzo IP privato assegnato all'endpoint privato

    a) se le impostazioni DNS non sono corrette, eseguire le operazioni seguenti
     - Uso della zona privata: 
       - Assicurarsi che la macchina virtuale client VNet sia associata alla zona privata
       - Esaminare il record della zona DNS privato esistente, creare se non esiste
    
     - Uso di DNS personalizzato:
       - Verificare le impostazioni DNS del cliente e verificare che la configurazione DNS sia corretta.
       Per informazioni aggiuntive [, vedere Panoramica degli endpoint privati-configurazione DNS](https://docs.microsoft.com/azure/private-link/private-endpoint-overview#dns-configuration) .

    b) se la connettività ha esito negativo a causa di NSG/UdR
     - Esaminare le regole in uscita NSG e creare regole in uscita appropriate per consentire il traffico
    
       ![Regole in uscita NSG](./media/private-endpoint-tsg/nsg-outbound-rules.png)

5. Se la connessione ha convalidato i risultati, il problema di connettività potrebbe essere correlato ad altri aspetti come segreti, token, password a livello di applicazione.
   - In questo caso, rivedere la configurazione della risorsa di collegamento privato associata all'endpoint privato. Vedere la [Guida alla risoluzione dei problemi del collegamento privato](https://docs.microsoft.com/azure/private-link/private-link-connectivity-troubleshooting). 

6. Se il problema non è ancora risolto, contattare il team di [supporto di Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) e il problema di connettività esiste ancora. 

## <a name="next-steps"></a>Passaggi successivi

 * [Creare un endpoint privato nella subnet aggiornata (portale di Azure)](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal)

 * [Guida alla risoluzione dei problemi di collegamento privato](https://docs.microsoft.com/azure/private-link/private-link-connectivity-troubleshooting)
