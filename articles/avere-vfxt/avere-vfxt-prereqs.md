---
title: Prerequisiti per Avere vFXT - Azure
description: Prerequisiti per Avere vFXT per Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: rohogue
ms.openlocfilehash: dce359d1567ee763cd988e778b1e0e44475388cc
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255359"
---
# <a name="prepare-to-create-the-avere-vfxt"></a>Preparare la creazione di Avere vFXT

Questo articolo illustra le attività preliminari per la creazione di un cluster Avere vFXT.

## <a name="create-a-new-subscription"></a>Creare una nuova sottoscrizione

Iniziare creando una nuova sottoscrizione di Azure. Usare una sottoscrizione distinta per ogni progetto di Avere vFXT per tenere traccia con facilità di tutte le spese e le risorse del progetto, proteggere altri progetti dalla possibile limitazione delle risorse durante il provisioning e semplificare la pulizia.  

Per creare una nuova sottoscrizione di Azure nel portale di Azure:

* Passare al [pannello Sottoscrizioni](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)
* Fare clic sul pulsante **+ Aggiungi** in alto
* Accedere se richiesto
* Selezionare un'offerta e seguire la procedura per creare una nuova sottoscrizione

## <a name="configure-subscription-owner-permissions"></a>Configurare le autorizzazioni di proprietario della sottoscrizione

Il cluster vFXT deve essere creato da un utente con autorizzazioni di proprietario per la sottoscrizione. Le autorizzazioni del proprietario della sottoscrizione sono necessarie per accettare le condizioni del servizio software ed eseguire altre azioni. 

Esistono alcuni scenari di soluzione alternativa che consentono a un utente non proprietario di creare un vFTX per il cluster di Azure. Questi scenari coinvolgono la limitazione delle risorse e l'assegnazione di ruoli aggiuntivi al creatore. In entrambi i casi, anche il proprietario di una sottoscrizione deve accettare in anticipo [le condizioni del software vFXT](#accept-software-terms) . 

| Scenario | Restrizioni | Accedere ai ruoli necessari per creare il cluster vFXT. | 
|----------|--------|-------|
| Amministratore del gruppo di risorse | La rete virtuale, il controller cluster e i nodi del cluster devono essere creati all'interno del gruppo di risorse | Ruoli [collaboratore](../role-based-access-control/built-in-roles.md#contributor) e [amministratore accesso utenti](../role-based-access-control/built-in-roles.md#user-access-administrator) , entrambi con ambito per il gruppo di risorse di destinazione | 
| VNET esterno | Il controller del cluster e i nodi del cluster vengono creati all'interno del gruppo di risorse, ma viene usata una rete virtuale esistente in un gruppo di risorse diverso | (1) i ruoli [amministratore accesso utente](../role-based-access-control/built-in-roles.md#user-access-administrator) e [collaboratore](../role-based-access-control/built-in-roles.md#contributor) hanno come ambito il gruppo di risorse vFXT. e (2) [collaboratore macchina virtuale](../role-based-access-control/built-in-roles.md#virtual-machine-contributor), [amministratore accesso utenti](../role-based-access-control/built-in-roles.md#user-access-administrator)e ruoli di [collaboratore](../role-based-access-control/built-in-roles.md#avere-contributor) di avere nell'ambito del gruppo di risorse vnet. |
 
In alternativa, è possibile creare un ruolo personalizzato di controllo degli accessi in base al ruolo (RBAC) in anticipo e assegnare privilegi all'utente, come illustrato in [questo articolo](avere-vfxt-non-owner.md). Questo metodo consente di concedere autorizzazioni significative a questi utenti. 

## <a name="quota-for-the-vfxt-cluster"></a>Quota per il cluster vFXT

È necessario avere una quota sufficiente per i componenti di Azure seguenti. Se necessario, [richiedere un aumento della quota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

> [!NOTE]
> Le macchine virtuali e i componenti SSD elencati di seguito sono per il cluster vFXT stesso. È necessaria una quota aggiuntiva per le macchine virtuali e le unità SSD che si intende usare per la farm di calcolo.  Assicurarsi che la quota sia abilitata per l'area in cui si prevede di eseguire il flusso di lavoro.

|Componente di Azure|Quota|
|----------|-----------|
|Macchine virtuali|3 o più E32_v3|
|Archiviazione SSD Premium|200 GB di spazio del sistema operativo più 1-4 TB di spazio di memorizzazione nella cache per nodo |
|Account di archiviazione (facoltativo) |v2|
|Archiviazione back-end dei dati (facoltativa) |Un nuovo contenitore BLOB di archiviazione con ridondanza locale |

## <a name="accept-software-terms"></a>Accettare le condizioni d'uso del software

> [!NOTE] 
> Questo passaggio non è richiesto se il proprietario di una sottoscrizione crea il cluster Avere vFXT.

Durante la creazione di un cluster, è necessario accettare le condizioni d'uso per il software Avere vFXT. Se non si è il proprietario di una sottoscrizione, fare in modo che le condizioni vengano preventivamente accettate dal proprietario di una sottoscrizione. Questo passaggio deve essere eseguito una volta sola per ogni sottoscrizione.

Per accettare preventivamente le condizioni software: 

1. Aprire una cloud shell nel portale di Azure o passando a <https://shell.azure.com>. Accedere con il proprio ID sottoscrizione.

   ```azurecli
    az login
    az account set --subscription abc123de-f456-abc7-89de-f01234567890
   ```

1. Eseguire questo comando per accettare le condizioni d'uso e abilitare l'accesso a livello di codice per l'immagine software di Avere vFXT per Azure: 

   ```azurecli
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-controller:latest
   ```

## <a name="create-a-storage-service-endpoint-in-your-virtual-network-if-needed"></a>Creare un endpoint del servizio di archiviazione nella rete virtuale (se necessario)

Un [endpoint di servizio](../virtual-network/virtual-network-service-endpoints-overview.md) mantiene il traffico BLOB di Azure locale anziché instradarlo all'esterno della rete virtuale. È consigliabile per tutti i cluster vFXT per Azure che usano il BLOB di Azure per l'archiviazione dei dati back-end. 

Se si fornisce un VNET esistente e si crea un nuovo contenitore BLOB di Azure per l'archiviazione back-end nell'ambito della creazione del cluster, è necessario disporre di un endpoint di servizio in VNET per l'archiviazione Microsoft. Questo endpoint deve esistere prima di creare il cluster, altrimenti la creazione avrà esito negativo. 

Un endpoint del servizio di archiviazione è consigliato per tutti i vFXT per cluster di Azure che usano l'archiviazione BLOB di Azure, anche se si aggiunge l'archiviazione in un secondo momento. 

> [!TIP] 
> * Ignorare questo passaggio se si sta creando una nuova rete virtuale come parte della creazione del cluster. 
> * Questo passaggio è facoltativo se non si crea l'archiviazione BLOB durante la creazione del cluster. In tal caso, è possibile creare l'endpoint del servizio in un secondo momento se si decide di usare il BLOB di Azure.

Creare l'endpoint del servizio di archiviazione dal portale di Azure. 

1. Nel portale fare clic su **Reti virtuali** a sinistra.
1. Selezionare il VNET per il cluster. 
1. Fare clic su **Endpoint servizio** a sinistra.
1. Fare clic su **Aggiungi** nella parte superiore.
1. Lasciare il servizio come ``Microsoft.Storage`` e scegliere la subnet del cluster.
1. Nella parte inferiore fare clic su **Aggiungi**.

   ![Screenshot del portale di Azure con annotazioni per la procedura di creazione dell'endpoint di servizio](media/avere-vfxt-service-endpoint.png)


## <a name="next-step-create-the-vfxt-cluster"></a>Passaggio successivo: Creare il cluster vFXT

Dopo aver completato questi prerequisiti, è possibile passare direttamente alla creazione del cluster stesso. Leggere [Distribuire il cluster vFXT](avere-vfxt-deploy.md) per istruzioni.