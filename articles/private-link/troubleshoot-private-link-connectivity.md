---
title: Risolvere i problemi di connettività del collegamento privato di Azure
description: Istruzioni dettagliate per la diagnosi della connettività dei collegamenti privati
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77539468"
---
# <a name="troubleshoot-azure-private-link-connectivity-problems"></a>Risolvere i problemi di connettività del collegamento privato di Azure

Questo articolo fornisce istruzioni dettagliate per convalidare e diagnosticare la connettività per l'installazione di Azure Private Link.This article provides step-by-step guidance to validate and diagnose connectivity for your Azure Private Link setup.

Con Azure Private Link, è possibile accedere ai servizi della piattaforma Azure come servizio (PaaS), ad esempio Archiviazione di Azure, Database cosmo di Azure e Database SQL di Azure e Servizi cliente o partner ospitati da Azure su un endpoint privato nella rete virtuale. Il traffico tra la rete virtuale e il servizio attraversa la rete backbone Microsoft, eliminando l'esposizione dalla rete Internet pubblica. È inoltre possibile creare il proprio servizio di collegamento privato nella rete virtuale e distribuirlo privatamente ai clienti.

È possibile abilitare il servizio che viene eseguito dietro il livello Standard di Azure Load Balancer per l'accesso a Private Link.You can enable your service that runs behind the Standard tier of Azure Load Balancer for Private Link access. I consumer del servizio possono creare un endpoint privato all'interno della rete virtuale ed eseguire il mapping a questo servizio per accedervi privatamente.

Di seguito sono riportati gli scenari di connettività disponibili con Private Link:

- Rete virtuale dalla stessa areaVirtual network from the same region
- Reti virtuali con peered a livello regionale
- Reti virtuali con peered globale
- Cliente locale su circuiti VPN o Azure ExpressRoute

## <a name="deployment-troubleshooting"></a>Risoluzione dei problemi di distribuzione

Esaminare le informazioni sulla [disabilitazione dei criteri](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy) di rete nel servizio di collegamento privato per i casi in cui non è possibile selezionare l'indirizzo IP di origine dalla subnet desiderata per il servizio di collegamento privato.

Assicurarsi che l'impostazione **privateLinkServiceNetworkPolicies** sia disabilitata per la subnet da cui si seleziona l'indirizzo IP di origine.

## <a name="diagnose-connectivity-problems"></a>Diagnosticare i problemi di connettività

Se si verificano problemi di connettività con la configurazione del collegamento privato, esaminare questi passaggi per assicurarsi che tutte le configurazioni usuali siano come previsto.

1. Esaminare la configurazione di Private Link esplorando la risorsa.

    a. Passare a **Centro collegamenti privati**.

      ![Centro collegamenti privati](./media/private-link-tsg/private-link-center.png)

    b. Nel riquadro sinistro selezionare **Servizi di collegamento privati**.

      ![Servizi di collegamento privato](./media/private-link-tsg/private-link-service.png)

    c. Filtrare e selezionare il servizio di collegamento privato che si desidera diagnosticare.

    d. Esaminare le connessioni all'endpoint privato.
     - Assicurarsi che l'endpoint privato da cui si sta cercando la connettività sia elencato con uno stato di connessione **Approvato.**
     - Se lo stato è **In sospeso,** selezionarlo e approvarlo.

       ![Connessioni endpoint privati](./media/private-link-tsg/pls-private-endpoint-connections.png)

     - Passare all'endpoint privato da cui ci si connette selezionando il nome. Assicurarsi che lo stato della connessione sia **Approvato**.

       ![Panoramica della connessione all'endpoint privatoPrivate endpoint connection overview](./media/private-link-tsg/pls-private-endpoint-overview.png)

     - Dopo aver approvato entrambi i lati, provare di nuovo la connettività.

    e. Alias **Alias** di revisione nella scheda **Panoramica** e **ID risorsa** nella scheda **Proprietà.**
     - Assicurarsi che le informazioni **sull'alias** e **sull'ID risorsa** corrispondano **all'alias** e all'ID **risorsa** in uso per creare un endpoint privato per questo servizio.

       ![Verificare le informazioni sull'alias](./media/private-link-tsg/pls-overview-pane-alias.png)

       ![Verificare le informazioni sull'ID risorsaVerify Resource ID information](./media/private-link-tsg/pls-properties-pane-resourceid.png)

    f. Esaminare le informazioni **sulla visibilità** nella scheda **Panoramica.Review** Visibility information on the Overview tab.
     - Assicurarsi che la sottoscrizione rientri nell'ambito **Visibilità.**

       ![Verificare le informazioni sulla visibilità](./media/private-link-tsg/pls-overview-pane-visibility.png)

    g. Esaminare le informazioni di **bilanciamento del carico** nella scheda **Panoramica.Review** Load balancer information on the Overview tab.
     - È possibile passare al servizio di bilanciamento del carico selezionando il collegamento del servizio di bilanciamento del carico.

       ![Verificare le informazioni sul bilanciamento del caricoVerify Load balancer information](./media/private-link-tsg/pls-overview-pane-ilb.png)

     - Assicurarsi che le impostazioni del servizio di bilanciamento del carico siano configurate in base alle aspettative.
       - Esaminare la **configurazione IP front-end**.
       - Esaminare **i pool back-end**.
       - Esaminare **Regole di bilanciamento del carico**.

       ![Verificare le proprietà del servizio di bilanciamento del caricoVerify load balancer](./media/private-link-tsg/pls-ilb-properties.png)

     - Assicurarsi che il servizio di bilanciamento del carico funzioni in base alle impostazioni precedenti.
       - Selezionare una macchina virtuale in qualsiasi subnet diversa da quella in cui è disponibile il pool back-end del servizio di bilanciamento del carico.
       - Provare ad accedere al front-end del servizio di bilanciamento del carico dalla macchina virtuale precedente.
       - Se la connessione viene emessa al pool back-end in base alle regole di bilanciamento del carico, il servizio di bilanciamento del carico è operativo.
       - È anche possibile esaminare la metrica del servizio di bilanciamento del carico tramite Monitoraggio di Azure per verificare se i dati scorrono attraverso il servizio di bilanciamento del carico.

1. Usare [Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/overview) per verificare se i dati scorrono.

    a. Nella risorsa del servizio di collegamento privato selezionare **Metriche**.
     - Selezionare **Byte in** iro o **Byte in uscita**.
     - Verificare se i dati scorrono quando si tenta di connettersi al servizio di collegamento privato. Aspettatevi un ritardo di circa 10 minuti.

       ![Verificare le metriche del servizio di collegamento privatoVerify private link service metrics](./media/private-link-tsg/pls-metrics.png)

1. Contattare il team di supporto di [Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) se il problema persiste e si verifica ancora un problema di connettività.

## <a name="next-steps"></a>Passaggi successivi

 * [Creare un servizio di collegamento privato (CLI)Create a private link service (CLI)](https://docs.microsoft.com/azure/private-link/create-private-link-service-cli)
 * [Guida alla risoluzione dei problemi relativi agli endpoint privati di AzureAzure Private Endpoint troubleshooting guide](troubleshoot-private-endpoint-connectivity.md)
