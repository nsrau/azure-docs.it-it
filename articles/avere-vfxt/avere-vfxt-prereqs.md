---
title: Prerequisiti per Avere vFXT - Azure
description: Prerequisiti per Avere vFXT per Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: v-erkell
ms.openlocfilehash: 352833b12c00abbefcf7016d27dfb580ee25e450
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60409269"
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

Il cluster vFXT deve essere creato da un utente con autorizzazioni di proprietario per la sottoscrizione. Per accettare le condizioni d'uso del software ed eseguire altre azioni sono necessarie autorizzazioni di proprietario di sottoscrizione. 

Esistono alcuni scenari di soluzioni alternative che consentono un non-owner creare un vFTX Avere per cluster di Azure. Questi scenari comportano imponendo restrizioni sulle risorse e l'assegnazione di ruoli aggiuntivi al creatore. In entrambi i casi, un proprietario della sottoscrizione deve inoltre [accettare le condizioni di software vFXT Avere](#accept-software-terms) anticipatamente. 

| Scenario | Restrizioni | Ruoli di accesso necessari per creare il cluster vFXT Avere | 
|----------|--------|-------|
| Amministratore di gruppo di risorse | La rete virtuale, controller del cluster e nodi del cluster devono essere creati all'interno del gruppo di risorse | [Amministratore accesso utenti](../role-based-access-control/built-in-roles.md#user-access-administrator) e [collaboratore](../role-based-access-control/built-in-roles.md#contributor) ruoli, sia come ambito il gruppo di risorse di destinazione | 
| Rete virtuale esterna | Il controller del cluster e nodi del cluster vengono creati all'interno del gruppo di risorse, ma viene usata una rete virtuale esistente in un gruppo di risorse diverso | (1) [amministratore accesso utenti](../role-based-access-control/built-in-roles.md#user-access-administrator) e [collaboratore](../role-based-access-control/built-in-roles.md#contributor) avranno come ambiti il gruppo di risorse vFXT; e (2) ruoli [collaboratore macchina virtuale](../role-based-access-control/built-in-roles.md#virtual-machine-contributor), [accesso utente Amministratore](../role-based-access-control/built-in-roles.md#user-access-administrator), e [collaboratore Avere](../role-based-access-control/built-in-roles.md#avere-contributor) ruoli nell'ambito del gruppo di risorse di rete virtuale. |
 
Un'alternativa consiste nel creare un ruolo controllo degli accessi basata sui ruoli di accesso personalizzato anticipatamente e assegnare i privilegi di utente, come illustrato in [questo articolo](avere-vfxt-non-owner.md). Questo metodo consente di concedere autorizzazioni significative a questi utenti. 

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

## <a name="create-a-storage-service-endpoint-in-your-virtual-network-if-needed"></a>Creare un endpoint di servizio di archiviazione in una rete virtuale (se necessario)

Oggetto [endpoint del servizio](../virtual-network/virtual-network-service-endpoints-overview.md) mantiene il traffico di Blob di Azure in locale anziché il routing all'esterno della rete virtuale. È consigliabile per qualsiasi vFXT Avere per cluster di Azure che usa i Blob di Azure per archiviare i dati di back-end. 

Se si specifica una rete virtuale esistente e creare un nuovo contenitore Blob di Azure per l'archiviazione back-end come parte della creazione del cluster, è necessario un endpoint del servizio nella rete virtuale per archiviazione di Microsoft. Questo endpoint deve esistere prima della creazione del cluster o la creazione avrà esito negativo. 

Un endpoint di servizio di archiviazione è consigliato per qualsiasi vFXT Avere per cluster di Azure che usa l'archiviazione Blob di Azure, anche se si aggiungerla lo spazio di archiviazione in un secondo momento. 

> [!TIP] 
> * Ignorare questo passaggio se si sta creando una nuova rete virtuale come parte della creazione del cluster. 
> * Questo passaggio è facoltativo se non si intende creare nell'archivio Blob durante la creazione del cluster. In tal caso, è possibile creare l'endpoint del servizio in un secondo momento se si decide di usare Blob di Azure.

Creare l'endpoint di servizio di archiviazione dal portale di Azure. 

1. Nel portale fare clic su **Reti virtuali** a sinistra.
1. Selezionare la rete virtuale per il cluster. 
1. Fare clic su **Endpoint servizio** a sinistra.
1. Fare clic su **Aggiungi** nella parte superiore.
1. Lasciare il servizio come ``Microsoft.Storage`` e scegli subnet del cluster.
1. Nella parte inferiore fare clic su **Aggiungi**.

   ![Screenshot del portale di Azure con annotazioni per la procedura di creazione dell'endpoint di servizio](media/avere-vfxt-service-endpoint.png)


## <a name="next-step-create-the-vfxt-cluster"></a>Passaggio successivo: Creare il cluster vFXT

Dopo aver completato questi prerequisiti, è possibile passare direttamente alla creazione del cluster stesso. Leggere [Distribuire il cluster vFXT](avere-vfxt-deploy.md) per istruzioni.