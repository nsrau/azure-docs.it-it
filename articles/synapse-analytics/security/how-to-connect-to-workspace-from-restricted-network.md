---
title: Connettersi alla risorsa dell'area di lavoro di sinapsi studio da una rete con restrizioni
description: Questo articolo illustra come connettersi alle risorse dell'area di lavoro di Azure sinapsi studio da una rete con restrizioni
author: xujxu
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 10/25/2020
ms.author: xujiang1
ms.reviewer: jrasnick
ms.openlocfilehash: 5d28b8f2ff3045c9fdf5e8a866419a22bfbc6504
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321788"
---
# <a name="connect-to-synapse-studio-workspace-resources-from-a-restricted-network"></a>Connettersi alle risorse dell'area di lavoro di sinapsi studio da una rete con restrizioni

Il lettore di destinazione di questo articolo è l'amministratore IT aziendale che gestisce la rete con restrizioni della società. L'amministratore IT sta per abilitare la connessione di rete tra Azure sinapsi studio e la workstation all'interno di questa rete con restrizioni.

Questo articolo illustra come connettersi all'area di lavoro di Azure sinapsi da un ambiente di rete con restrizioni. 

## <a name="prerequisites"></a>Prerequisiti

* **Sottoscrizione di Azure** : se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* **Area di lavoro di Azure sinapsi** : se non si ha una sinapsi studio, creare un'area di lavoro sinapsi da Azure sinapsi Analytics. Il nome dell'area di lavoro sarà necessario nel passaggio 4 seguente.
* **Rete con restrizioni** : la rete con restrizioni viene gestita dall'amministratore IT della società. l'amministratore IT dispone dell'autorizzazione per la configurazione dei criteri di rete. Il nome della rete virtuale e la relativa subnet saranno necessari nel passaggio 3.



## <a name="step-1-add-network-outbound-security-rules-to-the-restricted-network"></a>Passaggio 1: aggiungere regole di sicurezza in uscita della rete alla rete con restrizioni

È necessario aggiungere quattro regole di sicurezza in uscita di rete con quattro tag di servizio. Scopri di più sulla [Panoramica dei tag di servizio](/azure/virtual-network/service-tags-overview.md) 
* AzureResourceManager
* AzureFrontDoor.Frontend
* AzureActiveDirectory
* AzureMonitor (facoltativo. Aggiungere questo tipo di regola solo quando si desidera condividere i dati a Microsoft.

**Azure Resource Manager** dettagli della regola in uscita come indicato di seguito. Quando si creano le altre tre regole, sostituire il valore di " **destinazione tag servizio** " scegliendo il nome del tag di servizio " **AzureFrontDoor. frontend** ", " **AzureActiveDirectory** ", " **AzureMonitor** " nell'elenco a discesa.

![AzureResourceManager](./media/how-to-connect-to-workspace-from-restricted-network/arm-servicetag.png)


## <a name="step-2-create-azure-synapse-analytics-private-link-hubs"></a>Passaggio 2: creare l'analisi delle sinapsi di Azure (hub di collegamento privato)

È necessario creare un Azure sinapsi Analytics (hub di collegamento privato) da portale di Azure. Cercare " **Azure sinapsi Analytics (hub collegamenti privati)** " tramite il portale di Azure, quindi compilare il campo necessario e crearlo. 

> [!Note]
> L'area deve essere uguale a quella in cui si trova l'area di lavoro sinapsi.

![Creazione di hub di collegamento privato di sinapsi Analytics](./media/how-to-connect-to-workspace-from-restricted-network/private-links.png)

## <a name="step-3-create-private-link-endpoint-for-synapse-studio-gateway"></a>Passaggio 3: creare un endpoint di collegamento privato per il gateway di sinapsi Studio

Per accedere a sinapsi Studio gateway, è necessario creare un endpoint di collegamento privato da portale di Azure. Cercare " **collegamento privato** " tramite il portale di Azure. Selezionare " **Crea endpoint privato** " nel " **centro collegamenti privati** ", quindi compilare il campo necessario e crearlo. 

> [!Note]
> L'area deve essere uguale a quella in cui si trova l'area di lavoro sinapsi.

![Creazione di un endpoint privato per sinapsi Studio 1](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-1.png)

Nella scheda successiva di " **Resource** " scegliere l'hub di collegamento privato, creato nel passaggio 2 precedente.

![Creazione di un endpoint privato per sinapsi Studio 2](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-2.png)

Nella scheda successiva di "Configuration" ( **configurazione** ), 
* Scegliere il nome di rete virtuale con restrizioni per " **rete virtuale** ".
* Scegliere la subnet della rete virtuale con restrizioni per " **subnet** ". 
* Selezionare " **Sì** " per " **integrare con la zona DNS privata** ".

![Creazione di un endpoint privato per sinapsi Studio 3](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-3.png)

Dopo aver creato l'endpoint del collegamento privato, è possibile accedere alla pagina di accesso dello strumento Web di sinapsi Studio. Tuttavia, non è ancora possibile accedere alle risorse all'interno dell'area di lavoro sinapsi finché non sarà necessario completare il passaggio successivo.

## <a name="step-4-create-private-link-endpoints-for-synapse-studio-workspace-resource"></a>Passaggio 4: creare endpoint di collegamento privato per la risorsa dell'area di lavoro di sinapsi Studio

Per accedere alle risorse all'interno della risorsa dell'area di lavoro di sinapsi studio, è necessario creare almeno un endpoint di collegamento privato con tipo " **dev** " di " **risorsa secondaria di destinazione** " e altri due endpoint di collegamento privato facoltativi con i tipi " **SQL** " o " **sqlondemand** " dipendono dalle risorse nell'area di lavoro di sinapsi studio a cui si vuole accedere. La creazione di un endpoint di collegamento privato per l'area di lavoro di sinapsi studio è simile alla creazione dell'endpoint precedente.  

Prestare attenzione alle aree seguenti nella scheda " **Resource** ":
* Selezionare " **Microsoft. sinapsi/Workspaces** " su " **tipo di risorsa** ".
* Selezionare " **NomeAreadilavoro** " nella sezione " **Resource** " creata in precedenza.
* Selezionare il tipo di endpoint in " **destinazione risorse secondarie** ":
  * **SQL** : è per l'esecuzione di query SQL nel pool SQL.
  * **Sqlondemand** : per l'esecuzione di query incorporate di SQL.
  * **Dev** : per accedere a tutti gli altri elementi all'interno di aree di lavoro di sinapsi Studio. È necessario creare almeno l'endpoint di collegamento privato di con questo tipo.

![Creazione dell'endpoint privato per l'area di lavoro di sinapsi Studio](./media/how-to-connect-to-workspace-from-restricted-network/plinks-endpoint-ws-1.png)

A questo punto, tutti impostati. È possibile accedere alla risorsa dell'area di lavoro di sinapsi Studio.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla [rete virtuale dell'area di lavoro gestita](./synapse-workspace-managed-vnet.md)

Altre informazioni sugli [endpoint privati gestiti](./synapse-workspace-managed-private-endpoints.md)
