---
title: Risolvere i problemi di connettività del collegamento privato di Azure
description: Istruzioni dettagliate per la diagnosi della connettività di collegamento privato
services: private-link
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
ms.openlocfilehash: 0d26ad6802e551523875dcad13066fdbdbf39ada
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77191050"
---
# <a name="troubleshoot-private-link-service-connectivity-problems"></a>Risoluzione dei problemi di connettività al servizio Collegamento privato

Questa guida fornisce istruzioni dettagliate per convalidare e diagnosticare la connettività per la configurazione del servizio di collegamento privato. 

Il collegamento privato di Azure consente di accedere ai servizi PaaS di Azure, ad esempio archiviazione di Azure, Azure Cosmos DB e database SQL, e ai servizi cliente/partner ospitati in Azure tramite un endpoint privato nella rete virtuale. Il traffico tra la rete virtuale e il servizio attraversa la rete backbone Microsoft, impedendone l'esposizione alla rete Internet pubblica. È anche possibile creare un servizio di collegamento privato nella rete virtuale (VNet) e distribuirlo privatamente ai clienti. 

È possibile abilitare il servizio in esecuzione dietro Azure Load Balancer Standard per l'accesso ai collegamenti privati. I consumer del servizio possono creare un endpoint privato all'interno della rete virtuale e associarlo a questo servizio per accedervi privatamente.

Ecco gli scenari di connettività disponibili con il servizio di collegamento privato
- rete virtuale dalla stessa area 
- reti virtuali con peering a livello di area
- reti virtuali con peering globale
- clienti locali tramite VPN o circuiti Express Route

## <a name="deployment-troubleshooting"></a>Risoluzione dei problemi di distribuzione

Esaminare le informazioni sulla [disabilitazione dei criteri di rete nel servizio di collegamento privato](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy) per i casi in cui non è possibile selezionare l'indirizzo IP di origine dalla subnet scelta per il servizio di collegamento privato.

Assicurarsi che l'impostazione **privateLinkServiceNetworkPolicies** sia disabilitata per la subnet da cui si seleziona l'indirizzo IP di origine.

## <a name="diagnosing-connectivity-problems"></a>Diagnosi dei problemi di connettività

Se si verificano problemi di connettività con la configurazione del collegamento privato, procedere con i passaggi elencati di seguito per assicurarsi che tutte le configurazioni comuni siano quelle previste.

1. Controllare la configurazione del servizio di collegamento privato esplorando la risorsa 

    a) passare a **collegamento privato**

      ![Centro collegamenti privati](./media/private-link-tsg/private-link-center.png)

    b) selezionare **servizi di collegamento privato** dal riquadro di spostamento a sinistra

      ![Servizi di collegamento privato](./media/private-link-tsg/private-link-service.png)

    c) filtrare e selezionare il servizio di collegamento privato che si desidera diagnosticare

    d) esaminare le connessioni all'endpoint privato
     - Verificare che l'endpoint privato da cui si sta cercando la connettività sia elencato con lo stato di connessione **approvato** . 
     - Se è **in sospeso**, selezionarlo e approvare. 

       ![Connessioni a endpoint privati](./media/private-link-tsg/pls-private-endpoint-connections.png)

     - Passare all'endpoint privato da cui ci si connette, facendo clic sul nome. Verificare che lo stato della connessione indichi **approvato**.

       ![Panoramica della connessione all'endpoint privato](./media/private-link-tsg/pls-private-endpoint-overview.png)

     - Una volta approvati entrambi i lati, riprovare la connettività.

    e) esaminare l' **alias** dalla scheda Panoramica e dall' **ID risorsa** dalla scheda Proprietà 
     - Assicurarsi che l' **ID alias/risorsa** corrisponda all' **alias/ID risorsa** usato per creare un endpoint privato per questo servizio. 

       ![Verifica alias](./media/private-link-tsg/pls-overview-pane-alias.png)

       ![Verificare l'ID risorsa](./media/private-link-tsg/pls-properties-pane-resourceid.png)

    f) informazioni sulla visibilità della verifica (sezione Panoramica)
     - Assicurarsi che la sottoscrizione rientri nell'ambito di **visibilità**

       ![Verificare la visibilità](./media/private-link-tsg/pls-overview-pane-visibility.png)

    g) esaminare le informazioni Load Balancer (panoramica)
     - È possibile passare al servizio di bilanciamento del carico facendo clic sul collegamento del servizio di bilanciamento del carico

       ![Verifica Load Balancer](./media/private-link-tsg/pls-overview-pane-ilb.png)

     - Assicurarsi che le impostazioni di Load Balancer siano configurate in base alle aspettative
       - Esaminare la configurazione IP front-end
       - Esaminare i pool back-end
       - Esaminare le regole di bilanciamento del carico

       ![Verificare le proprietà di Load Balancer](./media/private-link-tsg/pls-ilb-properties.png)

     - Assicurarsi che Load Balancer funzioni come per le impostazioni precedenti
       - Selezionare una macchina virtuale in una subnet diversa dalla subnet in cui è disponibile Load Balancer pool back-end
       - Provare ad accedere al front-end del servizio di bilanciamento del carico dalla VM precedente
       - Se la connessione effettua il pool back-end in base alle regole di bilanciamento del carico, il servizio di bilanciamento del carico è operativo
       - È anche possibile esaminare la metrica Load Balancer tramite monitoraggio di Azure per verificare se i dati passano attraverso il servizio di bilanciamento del carico

2. Usare [**monitoraggio di Azure**](https://docs.microsoft.com/azure/azure-monitor/overview) per esaminare il flusso dei dati

    a) nella risorsa del servizio di collegamento privato selezionare **metriche**
     - Seleziona **byte-in** o **byte-out**
     - Quando si tenta di connettersi al servizio di collegamento privato, i dati della verifica vengono propagati. Si prevede un ritardo di circa 10 minuti.

       ![Verificare le metriche del servizio di collegamento privato](./media/private-link-tsg/pls-metrics.png)

3. Se il problema non è ancora risolto, contattare il team di [supporto di Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) e il problema di connettività esiste ancora. 

## <a name="next-steps"></a>Passaggi successivi

 * [Creare un servizio di collegamento privato (CLI)](https://docs.microsoft.com/azure/private-link/create-private-link-service-cli)

 * [Guida alla risoluzione dei problemi di endpoint privato](troubleshoot-private-endpoint-connectivity.md)
