---
title: Distribuire Gestione traffico per il bilanciamento dei carichi di lavoro di soluzione VMware (AVS) di Azure
description: Informazioni su come integrare Gestione traffico con la soluzione VMware di Azure (AVS) per bilanciare i carichi di lavoro dell'applicazione tra più endpoint in aree diverse.
ms.topic: how-to
ms.date: 08/14/2020
ms.openlocfilehash: d461cc444c60e1907a34a08c68139446301c133c
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91580150"
---
# <a name="deploy-traffic-manager-to-balance-azure-vmware-solution-avs-workloads"></a>Distribuire Gestione traffico per il bilanciamento dei carichi di lavoro di soluzione VMware (AVS) di Azure

Questo articolo illustra l'integrazione di gestione traffico con la soluzione VMware di Azure (AVS) per bilanciare i carichi di lavoro delle applicazioni tra più endpoint. Verrà esaminato uno scenario in cui Traffic Manager indirizza il traffico tra tre gateway applicazione che si estendono su diverse aree AVS: Stati Uniti occidentali, Europa occidentale e locale nell'area Stati Uniti orientali. 

Gestione traffico di Azure è un servizio di bilanciamento del carico del traffico basato su DNS che consente di distribuire il traffico in modo ottimale ai servizi nelle aree di Azure globali. Il bilanciamento del carico del traffico delle applicazioni tra i carichi di lavoro in esecuzione di Azure e gli endpoint pubblici esterni verrà bilanciato. Per altre informazioni su Gestione traffico, vedere [che cos'è gestione traffico?](../traffic-manager/traffic-manager-overview.md)

Esaminare prima i [prerequisiti](#prerequisites) ; verranno quindi illustrate le procedure per:

> [!div class="checklist"]
> * Verificare la configurazione dei gateway applicazione
> * Verificare la configurazione del segmento NSX-T
> * Creare il profilo di gestione traffico
> * Aggiungere endpoint esterni al profilo di gestione traffico

## <a name="topology"></a>Topologia

Come illustrato nella figura seguente, gestione traffico di Azure fornisce il bilanciamento del carico per le applicazioni a livello di DNS tra gli endpoint internazionali. I gateway applicazione hanno membri del pool back-end configurati come server IIS e a cui viene fatto riferimento come endpoint esterni AVS.

Connessione attraverso la rete virtuale tra le due aree del cloud privato AVS, Stati Uniti occidentali ed Europa occidentale e un server locale nell'area Stati Uniti orientali, usa un gateway ExpressRoute.   

![Integrazione di gestione traffico con AVS](media/traffic-manager/traffic-manager-topology.png)
 
## <a name="prerequisites"></a>Prerequisiti

- Tre macchine virtuali configurate come server Microsoft IIS in esecuzione in aree AVS diverse: Stati Uniti occidentali, Europa occidentale e in locale. 

- Un gateway applicazione con endpoint esterni negli Stati Uniti occidentali, in Europa occidentale e in locale.

- Host con connettività Internet per la verifica. 

## <a name="verify-configuration-of-your-application-gateways"></a>Verificare la configurazione dei gateway applicazione

[Applicazione Azure gateway](https://azure.microsoft.com/services/application-gateway/) è un servizio di bilanciamento del carico del traffico Web di livello 7 che consente di gestire il traffico per le applicazioni Web. Per altre informazioni sul gateway applicazione, vedere [che cos'è applicazione Azure gateway?](../application-gateway/overview.md) 

In questo scenario, tre istanze del gateway applicazione sono configurate come endpoint AVS esterni. I gateway applicazione hanno macchine virtuali AVS configurate come membri del pool back-end per il bilanciamento del carico delle richieste di livello 7 in ingresso. Per informazioni su come configurare il gateway applicazione con le macchine virtuali AVS come pool back-end, vedere [usare applicazione Azure gateway per proteggere le app Web in una soluzione VMware di Azure](protect-azure-vmware-solution-with-application-gateway.md).  

Nei passaggi seguenti viene verificata la corretta configurazione dei gateway applicazione.

1. Aprire il portale di Azure e selezionare **gateway applicazione** per visualizzare un elenco dei gateway applicazione correnti. 

    Per questo scenario sono stati configurati tre gateway applicazione:
    - AVS-GW-WUS
    - AVS-GW-EUS (locale)
    - AVS-GW-UEO

    :::image type="content" source="media/traffic-manager/app-gateways-list-1.png" alt-text="Elenco di gateway applicazione." lightbox="media/traffic-manager/app-gateways-list-1.png":::

2. Selezionare uno dei gateway applicazione distribuiti in precedenza. Viene visualizzata una finestra che mostra varie informazioni sul gateway applicazione. Selezionare **pool back-end** per verificare la configurazione di uno dei pool back-end.

   :::image type="content" source="media/traffic-manager/backend-pool-config.png" alt-text="Elenco di gateway applicazione." lightbox="media/traffic-manager/backend-pool-config.png":::
 
3. In questo caso, viene visualizzato un membro del pool back-end della macchina virtuale configurato come server Web con un indirizzo IP di 172.29.1.10.
 
    :::image type="content" source="media/traffic-manager/backend-pool-ip-address.png" alt-text="Elenco di gateway applicazione.":::

    È possibile verificare in modo analogo la configurazione degli altri gateway applicazione e dei membri del pool back-end. 

## <a name="verify-configuration-of-the-nsx-t-segment"></a>Verificare la configurazione del segmento NSX-T

I segmenti di rete creati in NSX-T Manager vengono usati come reti per le macchine virtuali in vCenter. Per altre informazioni, vedere l'esercitazione [creare un segmento di rete NSX-T in Azure VMware Solution (AVS)](tutorial-nsx-t-network-segment.md).

In questo scenario, un segmento NSX-T viene configurato nell'ambiente AVS in cui è collegata la macchina virtuale membro del pool back-end.

1. Selezionare **segmenti** per visualizzare i segmenti configurati. In questo caso, si noterà che contoso-segment1 è connesso al gateway contoso-T01, un router flessibile di livello 1.

    :::image type="content" source="media/traffic-manager/nsx-t-segment-avs.png" alt-text="Elenco di gateway applicazione.":::    

2. Selezionare **gateway di primo livello** per visualizzare un elenco dei gateway di livello 1 con il numero di segmenti collegati. Selezionare il segmento collegato a Contoso-T01. Viene visualizzata una finestra che mostra l'interfaccia logica configurata nel router Tier-01. Funge da gateway per la macchina virtuale membro del pool back-end connessa al segmento.

   :::image type="content" source="media/traffic-manager/nsx-t-segment-linked-2.png" alt-text="Elenco di gateway applicazione.":::    

3. Nel client VM vSphere selezionare la macchina virtuale per visualizzarne i dettagli. Si noti che l'indirizzo IP corrisponde a quanto visto nel passaggio 3 della sezione precedente: 172.29.1.10.

    :::image type="content" source="media/traffic-manager/nsx-t-vm-details.png" alt-text="Elenco di gateway applicazione.":::    

4. Selezionare la macchina virtuale, quindi fare clic su **azioni > modifica impostazioni** per verificare la connessione al segmento NSX-T.

## <a name="create-your-traffic-manager-profile"></a>Creare il profilo di gestione traffico

1. Accedere al [Portale di Azure](https://rc.portal.azure.com/#home). In **servizi di Azure > rete**selezionare **profili di gestione traffico**.

2. Selezionare **+ Aggiungi** per creare un nuovo profilo di gestione traffico.
 
3. Fornire il nome del profilo, il metodo di routing (verrà usato ponderato in questo scenario, vedere [metodi di routing di gestione traffico](../traffic-manager/traffic-manager-routing-methods.md)), sottoscrizione e gruppo di risorse e selezionare **Crea**.

## <a name="add-external-endpoints-into-the-traffic-manager-profile"></a>Aggiungere endpoint esterni al profilo di gestione traffico

1. Selezionare il profilo di gestione traffico nel riquadro dei risultati della ricerca, selezionare **endpoint** e quindi **+ Aggiungi**.

2. Immettere i dettagli richiesti: tipo, nome, nome di dominio completo (FQDN) o IP e peso (in questo scenario viene assegnato un peso di 1 a ogni endpoint). Selezionare **Aggiungi**.

   :::image type="content" source="media/traffic-manager/traffic-manager-profile.png" alt-text="Elenco di gateway applicazione.":::  
 
   Verrà creato l'endpoint esterno. Lo stato del monitoraggio deve essere **online**. 

   Ripetere gli stessi passaggi per creare altri due endpoint esterni, uno in un'area diversa e l'altro in locale. Una volta creati, tutti e tre verranno visualizzati nel profilo di gestione traffico e lo stato di tutti e tre dovrebbe essere **online**.

3. Selezionare **Panoramica**. Copiare l'URL in **nome DNS**.

   :::image type="content" source="media/traffic-manager/traffic-manager-endpoints.png" alt-text="Elenco di gateway applicazione."::: 

4. Incollare l'URL del nome DNS in un browser. La schermata seguente mostra il traffico indirizzato all'area Europa occidentale.

   :::image type="content" source="media/traffic-manager/traffic-to-west-europe.png" alt-text="Elenco di gateway applicazione."::: 

5. Aggiornare il browser. La schermata seguente mostra il traffico che ora indirizza a un altro set di membri del pool back-end nell'area Stati Uniti occidentali.

   :::image type="content" source="media/traffic-manager/traffic-to-west-us.png" alt-text="Elenco di gateway applicazione."::: 

6. Aggiornare di nuovo il browser. La schermata seguente mostra il traffico che ora indirizza al set finale di membri del pool back-end in locale.

   :::image type="content" source="media/traffic-manager/traffic-to-on-premises.png" alt-text="Elenco di gateway applicazione.":::

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su:

- [Uso di applicazione Azure gateway in una soluzione VMware di Azure (AVS)](protect-azure-vmware-solution-with-application-gateway.md)
- [Metodi di routing di Gestione traffico](../traffic-manager/traffic-manager-routing-methods.md)
- [Combinazione dei servizi di bilanciamento del carico in Azure](../traffic-manager/traffic-manager-load-balancing-azure.md)
- [Misurazione delle prestazioni di gestione traffico](../traffic-manager/traffic-manager-performance-considerations.md)
