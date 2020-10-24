---
title: Come usare le autorizzazioni nel cloud Spring di Azure
description: Questo articolo illustra come creare ruoli personalizzati per le autorizzazioni nel cloud Spring di Azure.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/04/2020
ms.custom: devx-track-java
ms.openlocfilehash: edd9898580c60199b761b24b172a366069f09cb2
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92516189"
---
# <a name="how-to-use-permissions-in-azure-spring-cloud"></a>Come usare le autorizzazioni nel cloud Spring di Azure
Questo articolo illustra come creare ruoli personalizzati che delegano le autorizzazioni per le risorse cloud di Azure Spring. I ruoli personalizzati estendono i [ruoli predefiniti di Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) con varie autorizzazioni per le scorte.

Si implementeranno i seguenti ruoli personalizzati:

* **Ruolo sviluppatore**: 
    * Distribuire
    * Test
    * Riavviare le app
    * Può applicare e apportare modifiche alle configurazioni delle app nel repository git
    * È possibile ottenere il flusso di log
* **Operazioni di progettazione dell'affidabilità del sito**: 
    * Riavviare le app
    * Ottenere i flussi di log
    * Non è possibile apportare modifiche alle app o alle configurazioni
* **Ruolo azioni Azure Pipelines/Jenkins/GitHub**:
    * Consente di eseguire operazioni di creazione, lettura, aggiornamento ed eliminazione
    * Può creare e configurare tutto il cloud e le app di Azure Spring all'interno dell'istanza del servizio: Azure Pipelines, Jenkins o GitHub, usando i modelli di gestione risorse di Azure

## <a name="define-developer-role"></a>Definire il ruolo Sviluppatore

Il ruolo sviluppatore include le autorizzazioni per riavviare le app e visualizzare i relativi flussi di log, ma non può apportare modifiche alle app e alla configurazione.

### <a name="navigate-subscription-and-resource-group-access-control-iam"></a>Esplorare la sottoscrizione e il controllo di accesso del gruppo di risorse (IAM)

Per iniziare a definire un ruolo, seguire questa procedura.

1. Nella portale di Azure aprire la sottoscrizione e il gruppo di risorse in cui si vuole assegnare il ruolo personalizzato.
2. Aprire il **controllo di accesso (IAM)**.
3. Fare clic su **+ Aggiungi**.
4. Fare clic su **Aggiungi ruolo personalizzato**.
5. Fare clic su **Avanti**.

   ![Creare un ruolo personalizzato](media/spring-cloud-permissions/create-custom-role.png)

6. Fare clic su **Aggiungi autorizzazioni**.

   ![Inizio aggiunta autorizzazioni](media/spring-cloud-permissions/add-permissions.png)

### <a name="search-for-azure-spring-cloud-permissions"></a>Cercare le autorizzazioni cloud Spring per Azure:
7. Nella casella di ricerca cercare *Microsoft. app*.
Selezionare *Microsoft Azure Spring cloud*.

   ![Selezionare Azure Spring cloud](media/spring-cloud-permissions/spring-cloud-permissions.png)

8. Selezionare le autorizzazioni per il ruolo Sviluppatore:

Da **Microsoft. AppPlatform/Spring**selezionare:
* Scrittura: creare o aggiornare l'istanza del servizio cloud di Azure Spring
* Leggere: ottenere l'istanza del servizio cloud Spring di Azure
* Altro: elencare le chiavi di test dell'istanza del servizio cloud di Azure Spring

Da **Microsoft. AppPlatform/Spring/Apps**selezionare:
* Leggere: leggere Microsoft Azure applicazione Spring cloud
* Altro: ottenere Microsoft Azure URL di caricamento delle risorse dell'applicazione Spring cloud

Da **Microsoft. AppPlatform/Spring/Apps/bindings**selezionare:
* Leggere: leggere Microsoft Azure associazione dell'applicazione Spring cloud

Da **Microsoft. AppPlatform/Spring/Apps/Deployments**selezionare:
* Scrittura: scrivere Microsoft Azure distribuzione di applicazioni cloud Spring
* Leggere: leggere Microsoft Azure distribuzione di applicazioni cloud Spring
* Altro: avviare Microsoft Azure distribuzione di applicazioni cloud Spring
* Altro: arrestare Microsoft Azure distribuzione di applicazioni cloud Spring
* Altro: riavviare Microsoft Azure distribuzione di applicazioni cloud Spring
* Altro: ottenere Microsoft Azure URL del file di log della distribuzione dell'applicazione Spring cloud

Da **Microsoft. AppPlatform/Spring/Apps/Domains**selezionare:
* Leggere: leggere Microsoft Azure dominio personalizzato dell'applicazione Spring cloud

Da **Microsoft. AppPlatform/Spring/Certificates**selezionare:
* Leggere: leggere Microsoft Azure il certificato di Spring cloud

Da **Microsoft. AppPlatform/locations/operationresults/Spring**selezionare:
* Read: risultato dell'operazione di lettura

Da **Microsoft. AppPlatform/locations/OperationStatus/OperationId**selezionare:
* Lettura: stato dell'operazione di lettura

    [![Creare autorizzazioni ](media/spring-cloud-permissions/developer-permissions-box.png) Developler](media/spring-cloud-permissions/developer-permissions-box.png#lightbox)

9. Fare clic su **Aggiungi**.

10. Esaminare le autorizzazioni.

11. Fare clic su **Rivedi e crea**.

## <a name="define-devops-engineer-role"></a>Definire il ruolo di ingegnere DevOps
Questa procedura definisce un ruolo con le autorizzazioni per la distribuzione, il test e il riavvio delle app Cloud Spring di Azure.

1. Ripetere la procedura per esplorare la sottoscrizione, il gruppo di risorse e il controllo di accesso (IAM).
2. Selezionare le autorizzazioni per il ruolo di ingegnere DevOps:

Da **Microsoft. AppPlatform/Spring**selezionare:
* Scrittura: creare o aggiornare l'istanza del servizio cloud di Azure Spring
* Elimina: Elimina l'istanza del servizio cloud di Azure Spring
* Leggere: ottenere l'istanza del servizio cloud Spring di Azure
* Altro: abilitare l'endpoint di test dell'istanza del servizio cloud di Azure Spring
* Altro: disabilitare l'endpoint di test dell'istanza del servizio cloud di Azure Spring
* Altro: elencare le chiavi di test dell'istanza del servizio cloud di Azure Spring
* Altro: rigenera la chiave di test dell'istanza del servizio cloud Spring di Azure

Da **Microsoft. AppPlatform/Spring/Apps**selezionare:
* Scrittura: scrivere Microsoft Azure applicazione Spring cloud
* Elimina: Elimina Microsoft Azure applicazione Spring cloud
* Leggere: leggere Microsoft Azure applicazione Spring cloud
* Altro: ottenere Microsoft Azure URL di caricamento delle risorse dell'applicazione Spring cloud
* Altro: convalida Microsoft Azure dominio personalizzato dell'applicazione Spring cloud

Da **Microsoft. AppPlatform/Spring/Apps/bindings**selezionare:
* Scrittura: scrivere Microsoft Azure associazione di applicazioni cloud Spring
* Elimina: Elimina Microsoft Azure associazione di applicazioni cloud Spring
* Leggere: leggere Microsoft Azure associazione dell'applicazione Spring cloud

Da **Microsoft. AppPlatform/Spring/Apps/Deployments**selezionare:
* Scrittura: scrivere Microsoft Azure distribuzione di applicazioni cloud Spring
* Elimina: Elimina la distribuzione di applicazioni cloud Spring Azure
* Leggere: leggere Microsoft Azure distribuzione di applicazioni cloud Spring
* Altro: avviare Microsoft Azure distribuzione di applicazioni cloud Spring
* Altro: arrestare Microsoft Azure distribuzione di applicazioni cloud Spring
* Altro: riavviare Microsoft Azure distribuzione di applicazioni cloud Spring
* Altro: ottenere Microsoft Azure URL del file di log della distribuzione dell'applicazione Spring cloud

Da **Microsoft. AppPlatform/Spring/Apps/Deployments/SKUS**selezionare:
* Leggere: elencare gli SKU disponibili per la distribuzione di applicazioni

Da **Microsoft. AppPlatform/locations**selezionare:
* Altro: controllare la disponibilità del nome

Da Microsoft. AppPlatform/locations/operationResults/Spring Select: Read: Read Operation Result

Da **Microsoft. AppPlatform/locations/OperationStatus/OperationId**selezionare:
* Lettura: stato dell'operazione di lettura

Da **Microsoft. AppPlatform/SKU**selezionare:
* Lettura: elenca gli SKU disponibili

   [![Autorizzazioni ](media/spring-cloud-permissions/dev-ops-permissions.png) di sviluppo/op](media/spring-cloud-permissions/dev-ops-permissions.png#lightbox)

3. Fare clic su **Aggiungi**.

4. Esaminare le autorizzazioni.

5. Fare clic su **Rivedi e crea**.

## <a name="define-ops---site-reliability-engineering-role"></a>Definire il ruolo operazioni di progettazione dell'affidabilità del sito
Questa procedura definisce un ruolo con le autorizzazioni per la distribuzione, il test e il riavvio delle app Cloud Spring di Azure.

1. Ripetere la procedura per esplorare la sottoscrizione, il gruppo di risorse e il controllo di accesso (IAM).

2. Selezionare le autorizzazioni per il ruolo di progettazione dell'affidabilità del sito Ops:

Da **Microsoft. AppPlatform/Spring**selezionare:
* Leggere: ottenere l'istanza del servizio cloud Spring di Azure
* Altro: elencare le chiavi di test dell'istanza del servizio cloud di Azure Spring

Da **Microsoft. AppPlatform/Spring/Apps**selezionare:
* Leggere: leggere Microsoft Azure applicazione Spring cloud

Da **Microsoft. AppPlatform/Apps/Deployments**selezionare:
* Leggere: leggere Microsoft Azure distribuzione di applicazioni cloud Spring
* Altro: avviare Microsoft Azure distribuzione di applicazioni cloud Spring
* Altro: arrestare Microsoft Azure distribuzione di applicazioni cloud Spring
* Altro: riavviare Microsoft Azure distribuzione di applicazioni cloud Spring

Da **Microsoft. AppPlatform/locations/operationresults/Spring**selezionare:
* Read: risultato dell'operazione di lettura

Da **Microsoft. AppPlatform/locations/OperationStatus/OperationId**selezionare:
* Lettura: stato dell'operazione di lettura

   [![Autorizzazioni ](media/spring-cloud-permissions/ops-sre-permissions.png) Ops/SRE](media/spring-cloud-permissions/ops-sre-permissions.png#lightbox)

3. Fare clic su **Aggiungi**.

4. Esaminare le autorizzazioni.

5. Fare clic su **Rivedi e crea**.

## <a name="define-azure-pipelinesprovisioning-role"></a>Definire il ruolo di Azure Pipelines/provisioning
Questo ruolo Jenkins/GitHub actions può creare e configurare tutti gli elementi in Azure Spring cloud e le app con un'istanza del servizio. Questo ruolo è per il rilascio o la distribuzione del codice.

1. Ripetere la procedura per esplorare la sottoscrizione, il gruppo di risorse e il controllo di accesso (IAM).

2. Aprire le opzioni **autorizzazioni** .

3. Selezionare le autorizzazioni per il ruolo di Azure Pipelines/provisioning:
  
Da **Microsoft. AppPlatform/Spring**selezionare:
* Scrittura: creare o aggiornare l'istanza del servizio cloud di Azure Spring
* Elimina: Elimina l'istanza del servizio cloud di Azure Spring
* Leggere: ottenere l'istanza del servizio cloud Spring di Azure
* Altro: abilitare l'endpoint di test dell'istanza del servizio cloud di Azure Spring
* Altro: disabilitare l'endpoint di test dell'istanza del servizio cloud di Azure Spring
* Altro: elencare le chiavi di test dell'istanza del servizio cloud di Azure Spring
* Altro: rigenera la chiave di test dell'istanza del servizio cloud Spring di Azure

Da **Microsoft. AppPlatform/Spring/Apps**selezionare:
* Scrittura: scrivere Microsoft Azure applicazione Spring cloud
* Elimina: Elimina Microsoft Azure applicazione Spring cloud
* Leggere: leggere Microsoft Azure applicazione Spring cloud
* Altro: ottenere Microsoft Azure URL di caricamento delle risorse dell'applicazione Spring cloud
* Altro: convalida Microsoft Azure dominio personalizzato dell'applicazione Spring cloud

Da **Microsoft. AppPlatform/Spring/Apps/bindings**selezionare:
* Scrittura: scrivere Microsoft Azure associazione di applicazioni cloud Spring
* Elimina: Elimina Microsoft Azure associazione di applicazioni cloud Spring
* Leggere: leggere Microsoft Azure associazione dell'applicazione Spring cloud

Da **Microsoft. AppPlatform/Spring/Apps/Deployments**selezionare:
* Scrittura: scrivere Microsoft Azure distribuzione di applicazioni cloud Spring
* Elimina: Elimina la distribuzione di applicazioni cloud Spring Azure
* Leggere: leggere Microsoft Azure distribuzione di applicazioni cloud Spring
* Altro: avviare Microsoft Azure distribuzione di applicazioni cloud Spring
* Altro: arrestare Microsoft Azure distribuzione di applicazioni cloud Spring
* Altro: riavviare Microsoft Azure distribuzione di applicazioni cloud Spring
* Altro: ottenere Microsoft Azure URL del file di log della distribuzione dell'applicazione Spring cloud

Da **Microsoft. AppPlatform/SKU**selezionare:
* Lettura: elenca gli SKU disponibili

Da **Microsoft. AppPlatform/locations**selezionare:
* Altro: controllare la disponibilità del nome

Da **Microsoft. AppPlatform/locations/operationresults/Spring**selezionare:
* Read: risultato dell'operazione di lettura

Da **Microsoft. AppPlatform/locations/OperationStatus/OperationId**selezionare:
* Lettura: stato dell'operazione di lettura

Da **Microsoft. AppPlatform/SKU**selezionare:
* Lettura: elenca gli SKU disponibili

   [![Autorizzazioni ](media/spring-cloud-permissions/pipelines-permissions-box.png) per pipeline](media/spring-cloud-permissions/pipelines-permissions-box.png#lightbox)  

4. Fare clic su **Aggiungi**.

5. Esaminare le autorizzazioni.

6. Fare clic su **Rivedi e crea**.


## <a name="see-also"></a>Vedi anche
* [Creare o aggiornare ruoli personalizzati di Azure usando il portale di Azure](https://docs.microsoft.com/azure/role-based-access-control/custom-roles-portal)

Per ulteriori informazioni su tre metodi che definiscono le autorizzazioni personalizzate, vedere:
* [Clonare un ruolo](https://docs.microsoft.com/azure/role-based-access-control/custom-roles-portal#clone-a-role)
* [Iniziare da zero](https://docs.microsoft.com/azure/role-based-access-control/custom-roles-portal#start-from-scratch)
* [Inizia da JSON](https://docs.microsoft.com/azure/role-based-access-control/custom-roles-portal#start-from-json)