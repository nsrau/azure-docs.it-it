---
title: Rete virtuale gestita & endpoint privati gestiti
description: Informazioni sulla rete virtuale gestita e sugli endpoint privati gestiti in Azure Data Factory.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/15/2020
ms.openlocfilehash: c0f23c864430b6cb2f49f924d5aaa8bde296037c
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2020
ms.locfileid: "88135964"
---
# <a name="azure-data-factory-managed-virtual-network-preview"></a>Rete virtuale gestita Azure Data Factory (anteprima)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In questo articolo vengono illustrate le reti virtuali gestite e gli endpoint privati gestiti in Azure Data Factory.


## <a name="managed-virtual-network"></a>Rete virtuale gestita

Quando si crea un Azure Integration Runtime (IR) in Azure Data Factory rete virtuale gestita (VNET), il runtime di integrazione verrà sottoposto a provisioning con la rete virtuale gestita e utilizzerà gli endpoint privati per connettersi in modo sicuro agli archivi dati supportati. 

La creazione di un Azure IR all'interno di una rete virtuale gestita garantisce che il processo di integrazione dei dati sia isolato e sicuro. 

Vantaggi dell'uso della rete virtuale gestita:

- Con una rete virtuale gestita, è possibile eseguire l'offload del carico di gestione della rete virtuale per Azure Data Factory. Non è necessario creare una subnet per Azure Integration Runtime che potrebbe usare molti indirizzi IP privati dalla rete virtuale e richiederebbe prima la pianificazione dell'infrastruttura di rete. 
- Non richiede una conoscenza approfondita delle funzionalità di rete di Azure per l'integrazione dei dati in modo sicuro. L'introduzione a ETL sicuro è invece molto semplificata per gli ingegneri di dati. 
- La rete virtuale gestita insieme a endpoint privati gestiti protegge da dati exfiltration. 

> [!IMPORTANT]
>Attualmente, il VNet gestito è supportato solo nella stessa area geografica Azure Data Factory area.
 

![Architettura della rete virtuale gestita da ADF](./media/managed-vnet/managed-vnet-architecture-diagram.png)

## <a name="managed-private-endpoints"></a>Endpoint privati gestiti

Gli endpoint privati gestiti sono endpoint privati creati nella Azure Data Factory rete virtuale gestita che stabilisce un collegamento privato alle risorse di Azure. Azure Data Factory gestisce questi endpoint privati per conto dell'utente. 

![Nuovo endpoint privato gestito](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png)

Azure Data Factory supporta i collegamenti privati. Il collegamento privato consente di accedere ai servizi di Azure (PaaS), ad esempio archiviazione di Azure, Azure Cosmos DB, Azure sinapsi Analytics (in precedenza Azure SQL Data Warehouse)).

Quando si usa un collegamento privato, il traffico tra gli archivi dati e la rete virtuale gestita attraversa interamente la rete dorsale Microsoft. Il collegamento privato protegge da rischi di esfiltrazione dei dati. Per stabilire un collegamento privato a una risorsa, è necessario creare un endpoint privato.

L'endpoint privato usa un indirizzo IP privato nella rete virtuale gestita per portare il servizio al suo interno. Gli endpoint privati sono associati a una risorsa specifica in Azure e non all'intero servizio. I clienti possono limitare la connettività a una risorsa specifica approvata dall'organizzazione. Altre informazioni su [collegamenti ed endpoint privati](https://docs.microsoft.com/azure/private-link/).

> [!NOTE]
> È consigliabile creare endpoint privati gestiti per connettersi a tutte le origini dati di Azure. 
 
> [!WARNING]
> Se un archivio dati PaaS (BLOB, ADLS Gen2, SQL DW) dispone di un endpoint privato già creato su di esso e anche se consente l'accesso da tutte le reti, ADF può accedervi solo tramite endpoint privato gestito. Assicurarsi di creare un endpoint privato in questi scenari. 

Quando si crea un endpoint privato gestito in Azure Data Factory, viene creata una connessione all'endpoint privato in stato "in sospeso". Viene avviato un flusso di lavoro di approvazione. Il proprietario della risorsa del collegamento privato ha la responsabilità di approvare la connessione.

![Endpoint privato gestito](./media/tutorial-copy-data-portal-private/manage-private-endpoint.png)

Se il proprietario approva la connessione, il collegamento privato viene stabilito. In caso contrario, il collegamento privato non verrà stabilito. In entrambi i casi, l'endpoint privato gestito verrà aggiornato con lo stato della connessione.

![Approva endpoint privato gestito](./media/tutorial-copy-data-portal-private/approve-private-endpoint.png)

Solo un endpoint privato gestito in uno stato approvato può inviare il traffico a una risorsa del collegamento privato specificata.

## <a name="limitations-and-known-issues"></a>Limitazioni e problemi noti
### <a name="supported-data-sources"></a>Origini dati supportate
Le origini dati seguenti sono supportate per la connessione tramite un collegamento privato dalla rete virtuale gestita di ADF.
- Archiviazione BLOB di Azure
- Archiviazione tabelle di Azure
- File di Azure
- Azure Data Lake Gen2
- Database SQL di Azure (senza includere Istanza gestita SQL di Azure)
- Azure Synapse Analytics (in precedenza Azure SQL Data Warehouse)
- SQL di Azure CosmosDB
- Insieme di credenziali chiave di Azure
- Collegamento privato di Azure

### <a name="outbound-communications-through-public-endpoint-from-adf-managed-virtual-network"></a>Comunicazioni in uscita tramite endpoint pubblico da rete virtuale gestita da ADF
- Solo la porta 443 è aperta per le comunicazioni in uscita.
- Archiviazione di Azure e Azure Data Lake Gen2 non sono supportati per la connessione tramite un endpoint pubblico dalla rete virtuale gestita di ADF.

### <a name="other-known-issues"></a>Altri problemi noti
L'esecuzione del debug per la connettività CosmosDB non funziona, inclusi il debug del flusso di lavoro e il debug della pipeline.


## <a name="next-steps"></a>Passaggi successivi

- Esercitazione: [creare una pipeline di copia usando reti virtuali gestite e endpoint privati](tutorial-copy-data-portal-private.md) 
- Esercitazione: [creare una pipeline del flusso di flussi di mapping tramite la rete virtuale gestita e gli endpoint privati](tutorial-data-flow-private.md)
