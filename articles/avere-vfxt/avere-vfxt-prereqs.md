---
title: Prerequisiti per Avere vFXT - Azure
description: Prerequisiti per Avere vFXT per Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: rohogue
ms.openlocfilehash: a183989cc666f00da4be077c719c40d2524fd6e0
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78372130"
---
# <a name="prepare-to-create-the-avere-vfxt"></a>Preparare la creazione di Avere vFXT

Questo articolo illustra le attività preliminari per la creazione di un cluster vFXT.

## <a name="create-a-new-subscription"></a>Creare una nuova sottoscrizione

Iniziare creando una nuova sottoscrizione di Azure. Usare una sottoscrizione separata per ogni progetto vFXT per semplificare il rilevamento e la pulizia delle spese e per assicurarsi che altri progetti non siano interessati dalle quote o dalla limitazione delle risorse durante il provisioning del flusso di lavoro del cluster.

Per creare una nuova sottoscrizione di Azure nel portale di Azure:

1. Passare al [pannello Sottoscrizioni](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)
1. Fare clic sul pulsante **+ Aggiungi** in alto
1. Accedere se richiesto
1. Selezionare un'offerta e seguire la procedura per creare una nuova sottoscrizione

## <a name="configure-subscription-owner-permissions"></a>Configurare le autorizzazioni di proprietario della sottoscrizione

Il cluster vFXT deve essere creato da un utente con autorizzazioni di proprietario per la sottoscrizione. Per la creazione di cluster è necessario che un proprietario accetti le condizioni del servizio software e autorizzare le modifiche alle risorse di rete e di archiviazione.

Esistono alcune soluzioni alternative per consentire a un utente non proprietario di creare un vFXT per il cluster di Azure. Questi scenari coinvolgono la limitazione delle risorse e l'assegnazione di ruoli di controllo degli accessi in base al ruolo (RBAC) aggiuntivi al creatore. In tutti questi casi, è necessario che un proprietario della sottoscrizione accetti in anticipo [i termini del software vFXT](#accept-software-terms) .

| Scenario | Restrizioni | Accedere ai ruoli necessari per creare il cluster vFXT. |
|----------|--------|-------|
| L'amministratore del gruppo di risorse crea il vFXT | La rete virtuale, il controller cluster e i nodi del cluster devono essere creati all'interno del gruppo di risorse. | Ruoli [collaboratore](../role-based-access-control/built-in-roles.md#contributor) e [amministratore accesso utenti](../role-based-access-control/built-in-roles.md#user-access-administrator) , entrambi limitati al gruppo di risorse di destinazione. |
| Usa una rete virtuale esterna esistente | Il controller del cluster e i nodi del cluster vengono creati all'interno del gruppo di risorse di vFXT, ma usano una rete virtuale esistente in un gruppo di risorse diverso. | (1) i ruoli [amministratore accesso utente](../role-based-access-control/built-in-roles.md#user-access-administrator) e [collaboratore](../role-based-access-control/built-in-roles.md#contributor) hanno come ambito il gruppo di risorse vFXT. e (2) [collaboratore macchina virtuale](../role-based-access-control/built-in-roles.md#virtual-machine-contributor), [amministratore accesso utenti](../role-based-access-control/built-in-roles.md#user-access-administrator)e ruoli di [collaboratore](../role-based-access-control/built-in-roles.md#avere-contributor) di avere nell'ambito del gruppo di risorse della rete virtuale. |
| Ruolo personalizzato per gli autori del cluster | Nessuna restrizione sulla posizione delle risorse. Questo metodo fornisce privilegi significativi non proprietari. | Il proprietario della sottoscrizione crea un ruolo personalizzato RBAC come descritto in [questo articolo](avere-vfxt-non-owner.md). |

## <a name="quota-for-the-vfxt-cluster"></a>Quota per il cluster vFXT

Verificare di disporre di una quota sufficiente per i componenti di Azure seguenti. Se necessario, [richiedere un aumento della quota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

> [!NOTE]
> Le macchine virtuali e i componenti SSD elencati di seguito sono per il cluster vFXT stesso. Tenere presente che è necessaria anche la quota per le macchine virtuali e le unità SSD che si utilizzeranno per la farm di calcolo.
>
> Assicurarsi che la quota sia abilitata per l'area in cui si prevede di eseguire il flusso di lavoro.

|Componente di Azure|Quota|
|----------|-----------|
|Macchine virtuali|3 o più E32s_v3 (uno per nodo del cluster) |
|Archiviazione SSD Premium|200 GB di spazio del sistema operativo più 1-4 TB di spazio di memorizzazione nella cache per nodo |
|Account di archiviazione (facoltativo) |v2|
|Archiviazione back-end dei dati (facoltativa) |Un nuovo contenitore BLOB di archiviazione con ridondanza locale |
<!-- this table also appears in the overview - update it there if updating here -->

## <a name="accept-software-terms"></a>Accettare le condizioni d'uso del software

> [!TIP]
> Ignorare questo passaggio se il proprietario della sottoscrizione creerà il cluster vFXT.

Durante la creazione di un cluster, è necessario accettare le condizioni d'uso per il software Avere vFXT. Se non si è il proprietario di una sottoscrizione, fare in modo che le condizioni vengano preventivamente accettate dal proprietario di una sottoscrizione.

Questo passaggio deve essere eseguito una volta sola per ogni sottoscrizione.

Per accettare preventivamente le condizioni software:

1. Aprire una cloud shell nel portale di Azure o passando a <https://shell.azure.com>. Accedere con il proprio ID sottoscrizione.

   ```azurecli
    az login
    az account set --subscription <subscription ID>
   ```

1. Eseguire questo comando per accettare le condizioni d'uso e abilitare l'accesso a livello di codice per l'immagine software di Avere vFXT per Azure:

   ```azurecli
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-controller:latest
   ```

## <a name="create-a-storage-service-endpoint-in-your-virtual-network-if-needed"></a>Creare un endpoint del servizio di archiviazione nella rete virtuale (se necessario)

Un [endpoint di servizio](../virtual-network/virtual-network-service-endpoints-overview.md) mantiene il traffico BLOB di Azure locale anziché instradarlo all'esterno della rete virtuale. È consigliabile per tutti i cluster vFXT per Azure che usano il BLOB di Azure per l'archiviazione dei dati back-end.

Se durante la creazione del cluster si crea una nuova rete virtuale, viene creato automaticamente un endpoint. Se si specifica una rete virtuale esistente, è necessario che disponga di un endpoint del servizio di archiviazione Microsoft se si desidera creare un nuovo contenitore di archiviazione BLOB durante la creazione del cluster.<!-- if there is no endpoint in that situation, the cluster creation will fail -->

> [!TIP]
>
>* Ignorare questo passaggio se si sta creando una nuova rete virtuale come parte della creazione del cluster.
>* Un endpoint è facoltativo se non si crea l'archiviazione BLOB durante la creazione del cluster. In tal caso, è possibile creare l'endpoint del servizio in un secondo momento se si decide di usare il BLOB di Azure.

Creare l'endpoint del servizio di archiviazione dal portale di Azure.

1. Nel portale aprire l'elenco di reti virtuali.
1. Selezionare la rete virtuale per il cluster.
1. Fare clic su **endpoint servizio** nel menu a sinistra.
1. Fare clic su **Aggiungi** nella parte superiore.
1. Scegliere il ``Microsoft.Storage``del servizio.
1. Selezionare la subnet del cluster.
1. Nella parte inferiore fare clic su **Aggiungi**.

   ![Screenshot del portale di Azure con annotazioni per la procedura di creazione dell'endpoint di servizio](media/avere-vfxt-service-endpoint.png)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver completato questi prerequisiti, è possibile creare il cluster. Leggere [Distribuire il cluster vFXT](avere-vfxt-deploy.md) per istruzioni.
