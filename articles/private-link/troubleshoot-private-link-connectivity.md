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
ms.openlocfilehash: 1e5253d617c87d5869cebc817da6d265ebfdfa7e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77539468"
---
# <a name="troubleshoot-azure-private-link-connectivity-problems"></a>Risolvere i problemi di connettività del collegamento privato di Azure

Questo articolo fornisce istruzioni dettagliate per convalidare e diagnosticare la connettività per la configurazione del collegamento privato di Azure.

Con il collegamento privato di Azure è possibile accedere ai servizi della piattaforma distribuita come servizio (PaaS) di Azure, ad esempio archiviazione di Azure, Azure Cosmos DB e il database SQL di Azure e ai servizi di clienti o partner ospitati in Azure tramite un endpoint privato nella rete virtuale. Il traffico tra la rete virtuale e il servizio attraversa la rete backbone Microsoft, che elimina l'esposizione dalla rete Internet pubblica. È anche possibile creare un servizio di collegamento privato nella rete virtuale e distribuirlo privatamente ai clienti.

È possibile abilitare il servizio in esecuzione dietro il livello standard di Azure Load Balancer per l'accesso ai collegamenti privati. I consumer del servizio possono creare un endpoint privato all'interno della rete virtuale e associarlo a questo servizio per accedervi privatamente.

Ecco gli scenari di connettività disponibili con collegamento privato:

- Rete virtuale dalla stessa area
- Reti virtuali con peering a livello di area
- Reti virtuali con peering globale
- Clienti locali tramite VPN o circuiti ExpressRoute di Azure

## <a name="deployment-troubleshooting"></a>Risoluzione dei problemi di distribuzione

Esaminare le informazioni sulla [disabilitazione dei criteri di rete nel servizio di collegamento privato](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy) per i casi in cui non si è in grado di selezionare l'indirizzo IP di origine dalla subnet scelta per il servizio di collegamento privato.

Assicurarsi che l'impostazione **privateLinkServiceNetworkPolicies** sia disabilitata per la subnet da cui si sta selezionando l'indirizzo IP di origine.

## <a name="diagnose-connectivity-problems"></a>Diagnosticare i problemi di connettività

Se si verificano problemi di connettività con la configurazione del collegamento privato, rivedere questi passaggi per assicurarsi che tutte le configurazioni usuali siano quelle previste.

1. Esaminare la configurazione del collegamento privato esplorando la risorsa.

    a. Passare a **private Link Center**.

      ![Centro collegamenti privati](./media/private-link-tsg/private-link-center.png)

    b. Nel riquadro sinistro selezionare Servizi di **collegamento privato**.

      ![Servizi di collegamento privato](./media/private-link-tsg/private-link-service.png)

    c. Filtrare e selezionare il servizio di collegamento privato che si desidera diagnosticare.

    d. Esaminare le connessioni all'endpoint privato.
     - Assicurarsi che l'endpoint privato da cui si sta cercando la connettività sia elencato con uno stato di connessione **approvato** .
     - Se lo stato è **in sospeso**, selezionarlo e approvarlo.

       ![Connessioni a endpoint privati](./media/private-link-tsg/pls-private-endpoint-connections.png)

     - Passare all'endpoint privato dal quale si esegue la connessione selezionando il nome. Verificare che lo stato della connessione sia visualizzato come **approvato**.

       ![Panoramica della connessione all'endpoint privato](./media/private-link-tsg/pls-private-endpoint-overview.png)

     - Dopo l'approvazione di entrambi i lati, riprovare la connettività.

    e. Esaminare l' **alias** nella scheda **Panoramica** e nell' **ID risorsa** della scheda **Proprietà** .
     - Verificare che le informazioni relative all' **alias** e all' **ID risorsa** corrispondano all' **alias** e all' **ID risorsa** utilizzati per creare un endpoint privato per questo servizio.

       ![Verificare le informazioni sull'alias](./media/private-link-tsg/pls-overview-pane-alias.png)

       ![Verificare le informazioni sull'ID risorsa](./media/private-link-tsg/pls-properties-pane-resourceid.png)

    f. Esaminare le informazioni sulla **visibilità** nella scheda **Panoramica** .
     - Assicurarsi che la sottoscrizione rientri nell'ambito di **visibilità** .

       ![Verificare le informazioni sulla visibilità](./media/private-link-tsg/pls-overview-pane-visibility.png)

    g. Esaminare le informazioni relative al servizio di **bilanciamento del carico** nella scheda **Panoramica** .
     - È possibile passare al servizio di bilanciamento del carico selezionando il collegamento del servizio di bilanciamento del carico.

       ![Verificare le informazioni del servizio di bilanciamento del carico](./media/private-link-tsg/pls-overview-pane-ilb.png)

     - Assicurarsi che le impostazioni del servizio di bilanciamento del carico siano configurate in base alle aspettative.
       - Esaminare la **configurazione IP**front-end.
       - Esaminare i **pool back-end**.
       - Esaminare **le regole di bilanciamento del carico**.

       ![Verificare le proprietà del servizio di bilanciamento del carico](./media/private-link-tsg/pls-ilb-properties.png)

     - Verificare che il servizio di bilanciamento del carico funzioni in base alle impostazioni precedenti.
       - Selezionare una macchina virtuale in una subnet diversa dalla subnet in cui è disponibile il pool back-end del servizio di bilanciamento del carico.
       - Provare ad accedere al front-end del servizio di bilanciamento del carico dalla VM precedente.
       - Se la connessione lo rende il pool back-end in base alle regole di bilanciamento del carico, il servizio di bilanciamento del carico è operativo.
       - È anche possibile esaminare la metrica di bilanciamento del carico tramite monitoraggio di Azure per verificare se i dati passano attraverso il servizio di bilanciamento del carico.

1. Usare [monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/overview) per verificare se i dati vengono propagati.

    a. Nella risorsa servizio di collegamento privato selezionare **metriche**.
     - Selezionare **byte in** uscita o in **byte**.
     - Verificare se i dati vengono propagati quando si tenta di connettersi al servizio di collegamento privato. Si prevede un ritardo di circa 10 minuti.

       ![Verificare le metriche del servizio di collegamento privato](./media/private-link-tsg/pls-metrics.png)

1. Se il problema non è ancora risolto, contattare il team di [supporto di Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) ed esiste ancora un problema di connettività.

## <a name="next-steps"></a>Passaggi successivi

 * [Creare un servizio di collegamento privato (CLI)](https://docs.microsoft.com/azure/private-link/create-private-link-service-cli)
 * [Guida alla risoluzione dei problemi dell'endpoint privato di Azure](troubleshoot-private-endpoint-connectivity.md)
