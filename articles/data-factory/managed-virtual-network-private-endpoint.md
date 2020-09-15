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
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 07/15/2020
ms.openlocfilehash: 5a40faa1feac20ae096dfe39a5b1d109d4a11d3d
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90563999"
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
> Se un archivio dati PaaS (BLOB, ADLS Gen2, Azure sinapsi Analytics) ha un endpoint privato già creato su di esso e anche se consente l'accesso da tutte le reti, ADF può accedervi solo tramite endpoint privato gestito. Assicurarsi di creare un endpoint privato in questi scenari. 

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
- Servizio di collegamento privato di Azure
- Ricerca di Azure
- Database di Azure per MySQL
- Database di Azure per PostgreSQL
- Database di Azure per MariaDB

### <a name="azure-data-factory-managed-virtual-network-is-available-in-the-following-azure-regions"></a>Azure Data Factory rete virtuale gestita è disponibile nelle aree di Azure seguenti:
- Stati Uniti orientali
- Stati Uniti orientali 2
- Stati Uniti centro-occidentali
- Stati Uniti occidentali 2
- Stati Uniti centro-meridionali
- Stati Uniti centrali
- Europa settentrionale
- Europa occidentale
- Regno Unito meridionale
- Asia sud-orientale
- Australia orientale

### <a name="outbound-communications-through-public-endpoint-from-adf-managed-virtual-network"></a>Comunicazioni in uscita tramite endpoint pubblico da rete virtuale gestita da ADF
- Solo la porta 443 è aperta per le comunicazioni in uscita.
- Archiviazione di Azure e Azure Data Lake Gen2 non sono supportati per la connessione tramite un endpoint pubblico dalla rete virtuale gestita di ADF.

### <a name="linked-service-creation-of-azure-key-vault"></a>Creazione del servizio collegato di Azure Key Vault 
- Quando si crea un servizio collegato per Azure Key Vault, non esiste alcun riferimento Azure Integration Runtime. Non è quindi possibile creare un endpoint privato durante la creazione del servizio collegato di Azure Key Vault. Tuttavia, quando si crea un servizio collegato per gli archivi dati che fa riferimento Azure Key Vault servizio collegato e questo servizio collegato fa riferimento Azure Integration Runtime con la rete virtuale gestita abilitata, è possibile creare un endpoint privato per il servizio collegato Azure Key Vault durante la creazione. 
- L'operazione di **test della connessione** per il servizio collegato di Azure Key Vault convalida solo il formato dell'URL, ma non esegue alcuna operazione di rete.

## <a name="next-steps"></a>Passaggi successivi

- Esercitazione: [creare una pipeline di copia usando reti virtuali gestite e endpoint privati](tutorial-copy-data-portal-private.md) 
- Esercitazione: [creare una pipeline del flusso di flussi di mapping tramite la rete virtuale gestita e gli endpoint privati](tutorial-data-flow-private.md)
