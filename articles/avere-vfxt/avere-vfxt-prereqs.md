---
title: Prerequisiti per Avere vFXT - Azure
description: Prerequisiti per Avere vFXT per Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: v-erkell
ms.openlocfilehash: 04af92f21cecaa832e857a7017b67f815f6ab685
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58417973"
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

Il cluster vFXT deve essere creato da un utente con autorizzazioni di proprietario per la sottoscrizione. Le autorizzazioni di proprietario della sottoscrizione sono necessarie, a titolo esemplificativo, per eseguire queste operazioni:

* Accettare le condizioni per il software Avere vFXT
* Creare il ruolo di accesso dei nodi del cluster 

Sono disponibili due soluzioni alternative se non si vuole concedere l'accesso come proprietario agli utenti che creano il cluster vFXT:

* Il proprietario di un gruppo di risorse può creare un cluster se vengono soddisfatte queste condizioni:

  * Il proprietario di una sottoscrizione deve [accettare le condizioni software per Avere vFXT](#accept-software-terms) e [creare il ruolo di accesso dei nodi del cluster](#create-the-cluster-node-access-role). 
  * Tutte le risorse di Avere vFXT devono essere distribuite all'interno del gruppo di risorse, tra cui:
    * Controller del cluster
    * Nodi del cluster
    * Archiviazione BLOB
    * Elementi di rete
 
* Un utente senza privilegi di proprietario può creare cluster vFXT usando il controllo degli accessi in base al ruolo anticipatamente per assegnare privilegi all'utente. Questo metodo consente di concedere autorizzazioni significative a questi utenti. [Questo articolo](avere-vfxt-non-owner.md) spiega come creare un ruolo di accesso per autorizzare utenti non proprietari per la creazione di cluster.

## <a name="quota-for-the-vfxt-cluster"></a>Quota per il cluster vFXT

È necessario avere una quota sufficiente per i componenti di Azure seguenti. Se necessario, [richiedere un aumento della quota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

> [!NOTE]
> Le macchine virtuali e i componenti SSD elencati di seguito sono per il cluster vFXT stesso. È necessaria una quota aggiuntiva per le macchine virtuali e le unità SSD che si intende usare per la farm di calcolo.  Assicurarsi che la quota sia abilitata per l'area in cui si prevede di eseguire il flusso di lavoro.

|Componente di Azure|Quota|
|----------|-----------|
|Macchine virtuali|3 o più E32s_v3|
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

## <a name="create-access-roles"></a>Creare ruoli di accesso 

Il [controllo degli accessi in base al ruolo](../role-based-access-control/index.yml) fornisce al controller del cluster vFXT e ai nodi del cluster l'autorizzazione per eseguire le attività necessarie.

* Il controller del cluster necessita dell'autorizzazione di creazione e modifica delle macchine virtuali per creare il cluster. 

* I singoli nodi vFXT devono eseguire operazioni come la lettura delle proprietà delle risorse di Azure, la gestione dell'archiviazione e il controllo delle impostazioni dell'interfaccia di rete degli altri nodi come parte del normale funzionamento del cluster.

Prima di creare un cluster Avere vFXT, è necessario definire un ruolo personalizzato da usare con i nodi del cluster. 

Per il controller del cluster, è possibile accettare il ruolo predefinito del modello. L'impostazione predefinita offre al controller del cluster privilegi di proprietario del gruppo di risorse. Se si preferisce creare un ruolo personalizzato per il controller, vedere [Ruolo di controllo degli accessi personalizzato](avere-vfxt-controller-role.md).

> [!NOTE] 
> Solo il proprietario di una sottoscrizione o un utente con il ruolo Proprietario o Amministratore Accesso utenti può creare ruoli. I ruoli possono essere creati in anticipo.  

### <a name="create-the-cluster-node-access-role"></a>Creare il ruolo di accesso dei nodi del cluster

<!-- caution - this header is linked to in the template so don't change it unless you can change that -->

Per poter creare il cluster Avere vFXT per Azure, è necessario prima creare il ruolo dei nodi del cluster.

> [!TIP] 
> Gli utenti Microsoft interni devono usare il ruolo esistente denominato "Avere Cluster Runtime Operator" invece di tentare di crearne uno. 

1. Copiare il file. Aggiungere l'ID sottoscrizione nella riga AssignableScopes.

   La versione corrente di questo file viene archiviata nel repository github.com/Azure/Avere come [AvereOperator.txt](https://github.com/Azure/Avere/blob/master/src/vfxt/src/roles/AvereOperator.txt).  

   ```json
   {
      "AssignableScopes": [
          "/subscriptions/PUT_YOUR_SUBSCRIPTION_ID_HERE"
      ],
      "Name": "Avere Operator",
      "IsCustom": "true",
      "Description": "Used by the Avere vFXT cluster to manage the cluster",
      "NotActions": [],
      "Actions": [
          "Microsoft.Compute/virtualMachines/read",
          "Microsoft.Network/networkInterfaces/read",
          "Microsoft.Network/networkInterfaces/write",
          "Microsoft.Network/virtualNetworks/read",
          "Microsoft.Network/virtualNetworks/subnets/read",
          "Microsoft.Network/virtualNetworks/subnets/join/action",
          "Microsoft.Network/networkSecurityGroups/join/action",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
          "Microsoft.Storage/storageAccounts/blobServices/containers/read",
          "Microsoft.Storage/storageAccounts/blobServices/containers/write"
      ],
      "DataActions": [
          "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
          "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
          "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ]
   }
   ```

1. Salvare il file come ``avere-operator.json`` o un nome di file simile facile da ricordare. 


1. Aprire Azure Cloud Shell e accedere con l'ID sottoscrizione (descritto [in precedenza in questo documento](#accept-software-terms)). Usare questo comando per creare il ruolo:

   ```bash
   az role definition create --role-definition /avere-operator.json
   ```

Il nome del ruolo viene usato durante la creazione del cluster. In questo esempio il nome è ``avere-operator``.

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