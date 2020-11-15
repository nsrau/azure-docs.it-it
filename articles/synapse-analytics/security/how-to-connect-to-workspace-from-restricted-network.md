---
title: Connettersi alle risorse dell'area di lavoro in Azure sinapsi Analytics studio da una rete con restrizioni
description: In questo articolo viene illustrato come connettersi alle risorse dell'area di lavoro da una rete con restrizioni
author: xujxu
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 10/25/2020
ms.author: xujiang1
ms.reviewer: jrasnick
ms.openlocfilehash: 7cff2d8245095489fbba3b7af24b416885995e4d
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2020
ms.locfileid: "94637133"
---
# <a name="connect-to-workspace-resources-from-a-restricted-network"></a>Connettersi alle risorse dell'area di lavoro da una rete con restrizioni

Si supponga di essere un amministratore IT che gestisce la rete con restrizioni dell'organizzazione. Si vuole abilitare la connessione di rete tra Azure sinapsi Analytics studio e una workstation all'interno di questa rete con restrizioni. Questo articolo illustra i passaggi da eseguire.

## <a name="prerequisites"></a>Prerequisiti

* **Sottoscrizione di Azure** : se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* **Area di lavoro di Azure sinapsi Analytics** : è possibile crearne uno da Azure sinapsi Analytics. Il nome dell'area di lavoro è necessario nel passaggio 4.
* **Una rete con restrizioni** : l'amministratore IT gestisce la rete con restrizioni per l'organizzazione e dispone dell'autorizzazione per la configurazione dei criteri di rete. Il nome della rete virtuale e la relativa subnet sono necessari nel passaggio 3.


## <a name="step-1-add-network-outbound-security-rules-to-the-restricted-network"></a>Passaggio 1: aggiungere regole di sicurezza in uscita della rete alla rete con restrizioni

È necessario aggiungere quattro regole di sicurezza in uscita di rete con quattro tag di servizio. 
* AzureResourceManager
* AzureFrontDoor.Frontend
* AzureActiveDirectory
* AzureMonitor (questo tipo di regola è facoltativo. Aggiungerlo solo quando si desidera condividere i dati con Microsoft.

La schermata seguente mostra i dettagli per la regola in uscita Azure Resource Manager.

![Screenshot dei dettagli del tag del servizio Azure Resource Manager.](./media/how-to-connect-to-workspace-from-restricted-network/arm-servicetag.png)

Quando si creano le altre tre regole, sostituire il valore del **tag del servizio di destinazione** con **AzureFrontDoor. frontend** , **AzureActiveDirectory** o **AzureMonitor** nell'elenco.

Per altre informazioni, vedere [Cenni preliminari sui tag di servizio](/azure/virtual-network/service-tags-overview.md).

## <a name="step-2-create-private-link-hubs"></a>Passaggio 2: creare hub di collegamento privato

Successivamente, creare hub di collegamento privati dal portale di Azure. Per trovarlo nel portale, cercare *Azure sinapsi Analytics (hub collegamento privato)* , quindi immettere le informazioni necessarie per crearlo. 

> [!Note]
> Verificare che il valore **Region** sia uguale a quello in cui si trova l'area di lavoro di Azure sinapsi Analytics.

![Screenshot della creazione di un hub di collegamento privato sinapsi.](./media/how-to-connect-to-workspace-from-restricted-network/private-links.png)

## <a name="step-3-create-a-private-endpoint-for-your-gateway"></a>Passaggio 3: creare un endpoint privato per il gateway

Per accedere al gateway di Azure sinapsi Analytics studio, è necessario creare un endpoint privato dal portale di Azure. Per trovarlo nel portale, cercare il *collegamento privato*. Nel **centro collegamenti privati** selezionare **Crea endpoint privato** e quindi immettere le informazioni necessarie per crearlo. 

> [!Note]
> Verificare che il valore **Region** sia uguale a quello in cui si trova l'area di lavoro di Azure sinapsi Analytics.

![Screenshot di creare un endpoint privato, scheda nozioni di base.](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-1.png)

Nella scheda **risorsa** scegliere l'hub collegamento privato, creato nel passaggio 2.

![Screenshot della scheda creare un endpoint privato e una risorsa.](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-2.png)

Nella scheda **configurazione** : 
* In **rete virtuale** selezionare il nome della rete virtuale con restrizioni.
* Per **subnet** selezionare la subnet della rete virtuale con restrizioni. 
* Per l' **integrazione con la zona DNS privata** , selezionare **Sì**.

![Screenshot della creazione di un endpoint privato, scheda configurazione.](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-3.png)

Dopo aver creato l'endpoint del collegamento privato, è possibile accedere alla pagina di accesso dello strumento Web per Azure sinapsi Analytics Studio. Tuttavia, non è ancora possibile accedere alle risorse all'interno dell'area di lavoro. A tale proposito, è necessario completare il passaggio successivo.

## <a name="step-4-create-private-endpoints-for-your-workspace-resource"></a>Passaggio 4: creare endpoint privati per la risorsa dell'area di lavoro

Per accedere alle risorse all'interno della risorsa dell'area di lavoro di Azure sinapsi Analytics studio, è necessario creare quanto segue:

- Almeno un endpoint di collegamento privato con un tipo di **sviluppatore** di una **risorsa secondaria di destinazione**.
- Altri due endpoint di collegamento privato facoltativi con tipi di **SQL** o **sqlondemand** , a seconda delle risorse nell'area di lavoro a cui si vuole accedere.

La creazione di questi parametri è simile alla creazione dell'endpoint nel passaggio precedente.  

Nella scheda **risorsa** :

* Per **tipo di risorsa** selezionare **Microsoft. sinapsi/Workspaces**.
* Per **risorsa** selezionare il nome dell'area di lavoro creato in precedenza.
* Per **risorsa secondaria di destinazione** selezionare il tipo di endpoint:
  * **SQL** è per l'esecuzione di query SQL nel pool SQL.
  * **Sqlondemand** è per l'esecuzione di query incorporate di SQL.
  * Lo **sviluppo** è per accedere a tutti gli altri elementi all'interno delle aree di lavoro di Azure sinapsi Analytics Studio. È necessario creare almeno un endpoint di collegamento privato di questo tipo.

![Screenshot della pagina creare un endpoint privato, una scheda risorse, un'area di lavoro.](./media/how-to-connect-to-workspace-from-restricted-network/plinks-endpoint-ws-1.png)


## <a name="step-5-create-private-endpoints-for-workspace-linked-storage"></a>Passaggio 5: creare endpoint privati per l'archiviazione collegata all'area di lavoro

Per accedere alla risorsa di archiviazione collegata con Storage Explorer nell'area di lavoro di Azure sinapsi Analytics studio, è necessario creare un endpoint privato. I passaggi per questa operazione sono simili a quelli del passaggio 3. 

Nella scheda **risorsa** :
* Per **tipo di risorsa** selezionare **Microsoft. sinapsi/storageAccounts**.
* Per **Resource** selezionare il nome dell'account di archiviazione creato in precedenza.
* Per **risorsa secondaria di destinazione** selezionare il tipo di endpoint:
  * il **BLOB** è per archiviazione BLOB di Azure.
  * il **DFS** è per Azure Data Lake storage Gen2.

![Screenshot della creazione di un endpoint privato, scheda risorse, archiviazione.](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-storage.png)

A questo punto, è possibile accedere alla risorsa di archiviazione collegata. All'interno della rete virtuale, nell'area di lavoro di Azure sinapsi Analytics studio è possibile usare Storage Explorer per accedere alla risorsa di archiviazione collegata.

È possibile abilitare una rete virtuale gestita per l'area di lavoro, come illustrato in questo screenshot:

![Screenshot dell'area di lavoro crea sinapsi con l'opzione Abilita rete virtuale gestita evidenziata.](./media/how-to-connect-to-workspace-from-restricted-network/ws-network-config.png)

Se si vuole che il notebook acceda alle risorse di archiviazione collegate in un determinato account di archiviazione, aggiungere endpoint privati gestiti in Azure sinapsi Analytics Studio. Il nome dell'account di archiviazione deve essere quello a cui deve accedere il notebook. Per altre informazioni, vedere [creare un endpoint privato gestito per l'origine dati](./how-to-create-managed-private-endpoints.md).

Dopo aver creato l'endpoint, lo stato di approvazione Mostra lo stato **in sospeso**. Richiedere l'approvazione da parte del proprietario di questo account di archiviazione, nella scheda **connessioni endpoint privato** di questo account di archiviazione nell'portale di Azure. Una volta approvata, il notebook può accedere alle risorse di archiviazione collegate in questo account di archiviazione.

A questo punto, tutti impostati. È possibile accedere alla risorsa dell'area di lavoro di Azure sinapsi Analytics Studio.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla [rete virtuale dell'area di lavoro gestita](./synapse-workspace-managed-vnet.md).

Altre informazioni sugli [endpoint privati gestiti](./synapse-workspace-managed-private-endpoints.md).
