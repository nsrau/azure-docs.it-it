---
title: Prerequisiti per Avere vFXT - Azure
description: Prerequisiti per Avere vFXT per Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: d32c664049b7e7c1231e78c552e7c61d016fbe84
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2018
ms.locfileid: "51286759"
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
* Consentire al nodo del controller del cluster di gestire i gruppi di risorse e le reti virtuali 
* Consentire al controller del cluster di creare e modificare i nodi del cluster 

Sono disponibili due soluzioni alternative se non si vuole concedere l'accesso come proprietario agli utenti che creano il cluster vFXT:

* Il proprietario di un gruppo di risorse può creare un cluster se vengono soddisfatte queste condizioni:

  * Il proprietario di una sottoscrizione deve [accettare le condizioni software per Avere vFXT](#accept-software-terms-in-advance) e [creare il ruolo di accesso dei nodi del cluster](avere-vfxt-deploy.md#create-the-cluster-node-access-role).
  * Tutte le risorse di Avere vFXT devono essere distribuite all'interno del gruppo di risorse, tra cui:
    * Controller del cluster
    * Nodi del cluster
    * Archiviazione BLOB
    * Elementi di rete
 
* Un utente senza privilegi di proprietario può creare un cluster vFXT se viene creato preventivamente un ruolo di accesso aggiuntivo da assegnare all'utente. Tuttavia, questo ruolo concede autorizzazioni significative agli utenti. [Questo articolo](avere-vfxt-non-owner.md) spiega come autorizzare i non proprietari a creare cluster.

## <a name="quota-for-the-vfxt-cluster"></a>Quota per il cluster vFXT

È necessario avere una quota sufficiente per i componenti di Azure seguenti. Se necessario, [richiedere un aumento della quota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

> [!NOTE]
> Le macchine virtuali e i componenti SSD elencati di seguito sono per il cluster vFXT stesso. È necessaria una quota aggiuntiva per le macchine virtuali e le unità SSD che si intende usare per la farm di calcolo.  Assicurarsi che la quota sia abilitata per l'area in cui si prevede di eseguire il flusso di lavoro.

|Componente di Azure|Quota|
|----------|-----------|
|Macchine virtuali|3 o più D16s_v3 o E32s_v3|
|Archiviazione SSD Premium|200 GB di spazio del sistema operativo più 1-4 TB di spazio di memorizzazione nella cache per nodo |
|Account di archiviazione (facoltativo) |v2|
|Archiviazione back-end dei dati (facoltativa) |Un nuovo contenitore BLOB di archiviazione con ridondanza locale |

## <a name="accept-software-terms-in-advance"></a>Accettare preventivamente le condizioni software

> [!NOTE] 
> Questo passaggio non è richiesto se il proprietario di una sottoscrizione crea il cluster Avere vFXT.

Prima di creare un cluster, è necessario accettare le condizioni d'uso per il software Avere vFXT. Se non si è il proprietario di una sottoscrizione, fare in modo che le condizioni vengano preventivamente accettate dal proprietario di una sottoscrizione. Questo passaggio deve essere eseguito una volta sola per ogni sottoscrizione.

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

## <a name="next-step-create-the-vfxt-cluster"></a>Passaggio successivo: creare il cluster vFXT

Dopo aver completato questi prerequisiti, è possibile passare direttamente alla creazione del cluster stesso. Leggere [Distribuire il cluster vFXT](avere-vfxt-deploy.md) per istruzioni.