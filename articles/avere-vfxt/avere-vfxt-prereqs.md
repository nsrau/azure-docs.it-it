---
title: Prerequisiti per Avere vFXT - Azure
description: Prerequisiti per Avere vFXT per Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: rohogue
ms.openlocfilehash: a183989cc666f00da4be077c719c40d2524fd6e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252545"
---
# <a name="prepare-to-create-the-avere-vfxt"></a>Preparare la creazione di Avere vFXT

In questo articolo vengono illustrate le attività preliminari per la creazione di un cluster Avere vFXT.

## <a name="create-a-new-subscription"></a>Creare una nuova sottoscrizione

Iniziare creando una nuova sottoscrizione di Azure. Usare una sottoscrizione separata per ogni progetto Avere vFXT per semplificare il rilevamento e la pulizia delle spese e per assicurarsi che altri progetti non siano interessati dalle quote o dalla limitazione delle risorse durante il provisioning del flusso di lavoro del cluster.

Per creare una nuova sottoscrizione di Azure nel portale di Azure:

1. Passare al [pannello Sottoscrizioni](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)
1. Fare clic sul pulsante **+ Aggiungi** in alto
1. Accedere se richiesto
1. Selezionare un'offerta e seguire la procedura per creare una nuova sottoscrizione

## <a name="configure-subscription-owner-permissions"></a>Configurare le autorizzazioni di proprietario della sottoscrizione

Il cluster vFXT deve essere creato da un utente con autorizzazioni di proprietario per la sottoscrizione. La creazione del cluster richiede al proprietario di accettare i termini di servizio del software e di autorizzare le modifiche alle risorse di rete e di archiviazione.

Esistono alcune soluzioni alternative per consentire a un non proprietario di creare un cluster Avere vFXT per Azure.There are some workarounds to allow a non-owner to create an Avere vFXT for Azure cluster. Questi scenari implicano la limitazione delle risorse e l'assegnazione di ruoli di controllo degli accessi in base al ruolo aggiuntivi al creatore. In tutti questi casi, il proprietario di un abbonamento deve anche accettare in anticipo le condizioni del [software Avere vFXT.](#accept-software-terms)

| Scenario | Restrizioni | Ruoli di accesso necessari per creare il cluster Hai vFXTAccess roles required to create the Avere vFXT cluster |
|----------|--------|-------|
| L'amministratore del gruppo di risorse crea il vFXT | I nodi della rete virtuale, del controller del cluster e del cluster devono essere creati all'interno del gruppo di risorse. | [Ruoli Di amministratore dell'accesso utente](../role-based-access-control/built-in-roles.md#user-access-administrator) e [Collaboratore,](../role-based-access-control/built-in-roles.md#contributor) entrambi con ambito al gruppo di risorse di destinazione. |
| Usare una rete virtuale esterna esistenteUse an existing, external virtual network | I nodi del controller del cluster e del cluster vengono creati all'interno del gruppo di risorse di vFXT, ma usano una rete virtuale esistente in un gruppo di risorse diverso. | (1) [Ruoli di amministratore](../role-based-access-control/built-in-roles.md#user-access-administrator) di accesso utenti e [collaboratori](../role-based-access-control/built-in-roles.md#contributor) nell'ambito del gruppo di risorse vFXT; e (2) [Collaboratore macchina virtuale](../role-based-access-control/built-in-roles.md#virtual-machine-contributor), [Amministratore accesso utenti](../role-based-access-control/built-in-roles.md#user-access-administrator)e [Collaboratore Avere](../role-based-access-control/built-in-roles.md#avere-contributor) nell'ambito del gruppo di risorse della rete virtuale. |
| Ruolo personalizzato per i creatori di clusterCustom role for cluster creators | Nessuna restrizione di posizionamento delle risorse. Questo metodo concede ai non proprietari privilegi significativi. | Il proprietario della sottoscrizione crea un ruolo RBAC personalizzato, come illustrato in [questo articolo.](avere-vfxt-non-owner.md) |

## <a name="quota-for-the-vfxt-cluster"></a>Quota per il cluster vFXT

Verificare di disporre di una quota sufficiente per i componenti di Azure seguenti. Se necessario, [richiedere un aumento della quota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

> [!NOTE]
> Le macchine virtuali e i componenti SSD elencati di seguito sono per il cluster vFXT stesso. Tenere presente che è necessaria anche la quota per le macchine virtuali e gli SSD che verranno utilizzati per la farm di calcolo.
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
> Ignorare questo passaggio se il proprietario di una sottoscrizione creerà il cluster Avere vFXT.Skip this step if a subscription owner will create the Avere vFXT cluster.

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

## <a name="create-a-storage-service-endpoint-in-your-virtual-network-if-needed"></a>Creare un endpoint del servizio di archiviazione nella rete virtuale (se necessario)Create a storage service endpoint in your virtual network (if needed)

Un [endpoint del servizio](../virtual-network/virtual-network-service-endpoints-overview.md) mantiene il traffico BLOB di Azure locale anziché instradarlo all'esterno della rete virtuale. È consigliabile per qualsiasi cluster Avere vFXT per Azure che usa blob di Azure per l'archiviazione dei dati back-end.

Se si crea una nuova rete virtuale durante la creazione del cluster, viene creato automaticamente un endpoint. Se si fornisce una rete virtuale esistente, deve disporre di un endpoint del servizio di archiviazione Microsoft se si vuole creare un nuovo contenitore di archiviazione BLOB durante la creazione del cluster.<!-- if there is no endpoint in that situation, the cluster creation will fail -->

> [!TIP]
>
>* Ignorare questo passaggio se si sta creando una nuova rete virtuale come parte della creazione del cluster.
>* Un endpoint è facoltativo se non si crea l'archiviazione BLOB durante la creazione del cluster. In that case, you can create the service endpoint later if you decide to use Azure Blob.

Creare l'endpoint del servizio di archiviazione dal portale di Azure.Create the storage service endpoint from the Azure portal.

1. Nel portale aprire l'elenco delle reti virtuali.
1. Selezionare la rete virtuale per il cluster.
1. Fare clic su **Endpoint del servizio** nel menu a sinistra.
1. Fai clic su **Aggiungi** in alto.
1. Scegliere il ``Microsoft.Storage``servizio .
1. Selezionare la subnet del cluster.
1. Nella parte inferiore fare clic su **Aggiungi**.

   ![Screenshot del portale di Azure con annotazioni per la procedura di creazione dell'endpoint di servizio](media/avere-vfxt-service-endpoint.png)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver completato questi prerequisiti, è possibile creare il cluster. Leggere [Distribuire il cluster vFXT](avere-vfxt-deploy.md) per istruzioni.
