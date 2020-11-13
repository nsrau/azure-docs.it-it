---
title: Usare endpoint privati con account Azure Batch
description: Informazioni su come connettersi privatamente a un account Azure Batch usando endpoint privati.
ms.topic: how-to
ms.date: 09/28/2020
ms.custom: references_regions
ms.openlocfilehash: 38d92d787a8d01dd3f87e1cdcacd336982c8c910
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94579556"
---
# <a name="use-private-endpoints-with-azure-batch-accounts"></a>Usare endpoint privati con account Azure Batch

Per impostazione predefinita, gli [account Azure batch](accounts.md) hanno un endpoint pubblico e sono accessibili pubblicamente. Il servizio batch offre la possibilità di creare account batch privati, disabilitando l'accesso alla rete pubblica.

Con il [collegamento privato di Azure](../private-link/private-link-overview.md)è possibile connettersi a un account Azure batch tramite un [endpoint privato](../private-link/private-endpoint-overview.md). L'endpoint privato è un set di indirizzi IP privati in una subnet all'interno della rete virtuale. È quindi possibile limitare l'accesso a un account Azure Batch su indirizzi IP privati.

Il collegamento privato consente agli utenti di accedere a un account Azure Batch dall'interno della rete virtuale o da qualsiasi rete virtuale con peering. Le risorse mappate al collegamento privato sono accessibili anche in locale tramite peering privato tramite VPN o [Azure ExpressRoute](../expressroute/expressroute-introduction.md). È possibile connettersi a un account Azure Batch configurato con un collegamento privato utilizzando il [metodo di approvazione automatico o manuale](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow).

> [!IMPORTANT]
> Il supporto per la connettività privata in Azure Batch è attualmente disponibile per tutte le aree pubbliche eccetto Germania centrale e Germania nord-orientale.

Questo articolo descrive i passaggi per creare un account batch privato e accedervi usando un endpoint privato.

## <a name="azure-portal"></a>Portale di Azure

Usare la procedura seguente per creare un account batch privato usando il portale di Azure:

1. Dal riquadro **Crea una risorsa** scegliere **servizio batch** , quindi selezionare **Crea**.
2. Immettere la sottoscrizione, il gruppo di risorse, l'area e il nome dell'account batch nella scheda **nozioni di base** , quindi selezionare **Avanti: avanzate**.
3. Nella scheda **Avanzate** impostare accesso alla **rete pubblica** su **disabilitato**.
4. In **Impostazioni** selezionare **connessioni endpoint privato** e quindi selezionare **+ endpoint privato**.
   :::image type="content" source="media/private-connectivity/private-endpoint-connections.png" alt-text="Connessioni a endpoint privati":::
5. Nel riquadro informazioni di **base** immettere o selezionare la sottoscrizione, il gruppo di risorse, il nome della risorsa dell'endpoint privato e i dettagli dell'area, quindi selezionare **Avanti: risorsa**.
6. Nel riquadro **delle risorse** impostare il **tipo di risorsa** su **Microsoft.Batch/batchAccounts**. Selezionare l'account batch privato a cui si vuole accedere, quindi selezionare **Avanti: configurazione**.
   :::image type="content" source="media/private-connectivity/create-private-endpoint.png" alt-text="Creare un endpoint privato-riquadro delle risorse":::
7. Nel riquadro **configurazione** immettere o selezionare queste informazioni:
   - **Rete virtuale** : selezionare la rete virtuale.
   - **Subnet** : selezionare la subnet.
   - Eseguire l' **integrazione con la zona DNS privata** : selezionare **Sì**. Per connettersi in privato con l'endpoint privato, è necessario un record DNS. È consigliabile integrare l'endpoint privato con una zona DNS privata. È anche possibile usare i propri server DNS o creare record DNS usando i file host delle macchine virtuali.
   - **Zona DNS privato** : selezionare \<region\> privatelink. batch.azure.com. La zona DNS privata viene determinata automaticamente. Non è possibile modificarla usando il portale di Azure.
8. Selezionare **Verifica + crea** , quindi attendere che Azure convalidi la configurazione.
9. Quando viene visualizzato il messaggio **Convalida superata** , selezionare **Crea**.

Dopo il provisioning dell'endpoint privato, è possibile accedere all'account batch dalle macchine virtuali nella stessa rete virtuale usando l'endpoint privato.

> [!IMPORTANT]
> L'esecuzione di operazioni al di fuori della rete virtuale in cui viene effettuato il provisioning dell'endpoint privato comporterà un messaggio "autorizzazione" nel portale di Azure.

Per visualizzare l'indirizzo IP dal portale di Azure:

1. Selezionare **Tutte le risorse**.
2. Cercare l'endpoint privato creato in precedenza.
3. Selezionare la scheda **Panoramica** per visualizzare le impostazioni DNS e gli indirizzi IP.

:::image type="content" source="media/private-connectivity/access-private.png" alt-text="Impostazioni DNS endpoint privato e indirizzi IP":::

## <a name="azure-resource-manager-template"></a>Modello di Azure Resource Manager

Quando si [Crea un account batch usando Azure Resource Manager modello](quick-create-template.md), modificare il modello per impostare **publicNetworkAccess** su **disabled** , come illustrato di seguito.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "batchAccountName": {
      "type": "string",
      },
    "location": {
      "type": "string",
    }
  },
  "resources": [
    {
      "name": "[parameters('batchAccountName')]",
      "type": "Microsoft.Batch/batchAccounts",
      "apiVersion": "2020-03-01-preview",
      "location": "[parameters('location')]",
      "dependsOn": []
      "properties": {
        "poolAllocationMode": "BatchService"
        "publicNetworkAccess": "Disabled"
      }
    }
  ]
}
```

## <a name="configure-dns-zones"></a>Configurare le zone DNS

Usare una [zona DNS privata](../dns/private-dns-privatednszone.md) all'interno della subnet in cui è stato creato l'endpoint privato. Configurare gli endpoint in modo che ogni indirizzo IP privato venga mappato a una voce DNS.

Quando si crea l'endpoint privato, è possibile integrarlo con una [zona DNS privata](../dns/private-dns-privatednszone.md) in Azure. Se si sceglie di usare invece un [dominio personalizzato](../dns/dns-custom-domain.md), è necessario configurarlo per aggiungere i record DNS per tutti gli indirizzi IP privati riservati per l'endpoint privato.

## <a name="pricing"></a>Prezzi

Per informazioni dettagliate sui costi correlati agli endpoint privati, vedere [prezzi di collegamento privato di Azure](https://azure.microsoft.com/pricing/details/private-link/).

## <a name="current-limitations-and-best-practices"></a>Limitazioni correnti e procedure consigliate

Quando si crea l'account batch privato, tenere presente quanto segue:

- È necessario creare le risorse degli endpoint privati nella stessa sottoscrizione dell'account batch.
- Per eliminare la connessione privata, è necessario eliminare la risorsa dell'endpoint privato.
- Una volta creato un account batch con accesso alla rete pubblico, non è possibile modificarlo solo con accesso privato.
- I record DNS nella zona DNS privata non vengono rimossi automaticamente quando si elimina un endpoint privato o quando si rimuove un'area dall'account batch. È necessario rimuovere manualmente i record DNS prima di aggiungere un nuovo endpoint privato collegato a questa zona DNS privata. Se non si eliminano i record DNS, potrebbero verificarsi problemi del piano dati imprevisti, ad esempio le interruzioni dei dati alle aree aggiunte dopo la rimozione dell'endpoint privato o la rimozione dell'area.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare pool di batch in reti virtuali](batch-virtual-network.md).
- Informazioni su come [creare pool di batch senza indirizzi IP pubblici](batch-pool-no-public-ip-address.md)
- Informazioni su come [creare pool di batch con indirizzi IP pubblici specificati](create-pool-public-ip.md).
- Informazioni sul [collegamento privato di Azure](../private-link/private-link-overview.md).
