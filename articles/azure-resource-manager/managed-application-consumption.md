---
title: Utilizzare un'applicazione gestita di Azure | Microsoft Docs
description: Descrive come un cliente crea un'applicazione gestita di Azure dai file pubblicati.
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 08/23/2017
ms.author: gauravbh; tomfitz
ms.openlocfilehash: ed8fbaf2a4546c8e31eeced11cd0b5627fd62c0c
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2017
---
# <a name="consume-an-internal-managed-application"></a>Usare un'applicazione gestita interna

È possibile usare [applicazioni gestite](managed-application-overview.md) di Azure studiate per i membri della propria organizzazione. È, ad esempio, possibile selezionare applicazioni gestite disponibili dal reparto IT che garantiscano la conformità agli standard aziendali. Queste applicazioni gestite sono disponibili nel catalogo dei servizi, non in Azure Marketplace.

Prima di procedere con questo articolo, è necessario disporre di un'applicazione gestita nel catalogo dei servizi per la sottoscrizione. Se qualcuno nell'organizzazione ha già creato un'applicazione gestita, vedere [Creare e pubblicare un'applicazione gestita di Azure](managed-application-publishing.md).

Attualmente, per usare un'applicazione gestita è possibile usare l'interfaccia della riga di comando di Azure o il portale di Azure.

## <a name="create-the-managed-application-by-using-the-portal"></a>Creare l'applicazione gestita con il portale

Per distribuire un'applicazione gestita tramite il portale, seguire questa procedura:

1. Accedere al portale di Azure. Cercare **Service Catalog Managed Application** (Applicazione gestita del catalogo di servizi).

   ![Applicazione gestita del catalogo di servizi](./media/managed-application-consumption/create-service-catalog-managed-application.png)

1. Selezionare l'applicazione gestita che si vuole creare dall'elenco di soluzioni disponibili. Selezionare **Crea**.

   ![Selezione di applicazioni gestite](./media/managed-application-consumption/select-offer.png)

1. Immettere i parametri necessari per il provisioning delle risorse. Selezionare **West Central US** (Stati Uniti centro-occidentali) per il percorso. Selezionare **OK**.

   ![Parametri delle applicazioni gestite](./media/managed-application-consumption/input-parameters.png)

1. Il modello convalida i valori specificati. Se la convalida ha esito positivo, selezionare **OK** per iniziare la distribuzione.

   ![Convalida dell'applicazione gestita](./media/managed-application-consumption/validation.png)

Al termine della distribuzione, nel gruppo di risorse gestite specificato viene eseguito il provisioning delle risorse appropriate definite nel modello.

## <a name="create-the-managed-application-by-using-azure-cli"></a>Creare l'applicazione gestita con l'interfaccia della riga di comando di Azure

A tale scopo, è possibile procedere in due modi usando l'interfaccia della riga di comando di Azure per creare un'applicazione gestita:

* Usare il comando per la creazione di applicazioni gestite.
* Usare il normale comando di distribuzione modelli.

### <a name="use-the-template-deployment-command"></a>Usare il comando di distribuzione modelli.

Distribuire il file applianceMainTemplate.json creato dal fornitore.

Creare quindi due gruppi di risorse. Il primo gruppo di risorse deve essere creato nel punto in cui viene creata la risorsa applicazione gestita: Microsoft.Solutions/appliances. Il secondo gruppo contiene tutte le risorse definite nel file mainTemplate.json. Questo gruppo di risorse è gestito dal fornitore di software indipendente.

```azurecli
az group create --name mainResourceGroup --location westcentralus
az group create --name managedResourceGroup --location westcentralus
```

> [!NOTE]
> Usare `westcentralus` come località del gruppo di risorse.
>

Per distribuire il file applianceMainTemplate.json in mainResourceGroup, usare il comando seguente:

```azurecli
az group deployment create --name managedAppDeployment --resourceGroup mainResourceGroup --templateUri
```

Dopo l'esecuzione del modello precedente, vengono richiesti i valori dei parametri definiti nel modello. Oltre ai parametri richiesti per il provisioning delle risorse in un modello, sono necessari due valori di parametri chiave:

- **managedResourceGroupId**: ID del gruppo di risorse contenente le risorse definite nel file applianceMainTemplate.json. L'ID è nel formato `/subscriptions/{subscriptionId}/resourceGroups/{resoureGroupName}`. Nell'esempio precedente si tratta dell'ID di `managedResourceGroup`.
- **applianceDefinitionId**: ID della risorsa di definizione di applicazione gestita. Questo valore viene fornito dal fornitore di software indipendente.

> [!NOTE]
> Il server di pubblicazione deve concedere l'accesso al gruppo di risorse che contiene la definizione di applicazione gestita. La risorsa di definizione viene creata nella sottoscrizione dell'editore. È quindi necessario che utenti, gruppi di utenti o applicazioni nel tenant del cliente abbiano accesso in lettura a questa risorsa.

Al termine della distribuzione, l'applicazione gestita viene creata in mainResourceGroup. La risorsa storageAccount viene creata in managedResourceGroup.

### <a name="use-the-create-command"></a>Usare il comando di creazione

È possibile usare il comando `az managedapp create` per creare un'applicazione gestita dalla definizione di applicazione gestita.

```azurecli
az managedapp create --name ravtestappliance401 --location "westcentralus"
    --kind "Servicecatalog" --resource-group "ravApplianceCustRG401"
    --managedapp-definition-id "/subscriptions/{guid}/resourceGroups/ravApplianceDefRG401/providers/Microsoft.Solutions/applianceDefinitions/ravtestAppDef401"
    --managed-rg-id "/subscriptions/{guid}/resourceGroups/ravApplianceCustManagedRG401"
    --parameters "{\"storageAccountName\": {\"value\": \"ravappliancedemostore1\"}}"
    --debug
```

* **appliance-definition-Id**: ID risorsa della definizione di applicazione gestita creata nel passaggio precedente. Per ottenere questo ID, eseguire questo comando:

  ```azurecli
  az appliance definition show -n ravtestAppDef1 -g ravApplianceRG2
  ```

  Questo comando restituisce la definizione di applicazione gestita. È necessario il valore della proprietà ID.

* **managed-rg-id**: nome del gruppo di risorse contenente le risorse definite nel file applianceMainTemplate.json. Questo gruppo di risorse è il gruppo di risorse gestite e viene gestito dall'editore. Se non esiste, viene creato automaticamente.
* **resource-group**: gruppo di risorse in cui viene creata la risorsa applicazione gestita. La risorsa Microsoft.Solutions/appliance si trova in questo gruppo di risorse.
* **parameters**: parametri necessari per le risorse definite nel file applianceMainTemplate.json.

## <a name="known-issues"></a>Problemi noti

Questa versione di anteprima contiene i problemi seguenti:

* Un messaggio 500 - Errore interno del server viene visualizzato durante la creazione dell'applicazione gestita. In questo caso, è probabile che si tratti di un problema intermittente. Ripetere l'operazione.
* Per il gruppo di risorse gestite è necessario un nuovo gruppo di risorse. Se si usa un gruppo di risorse esistente, la distribuzione ha esito negativo.
* Il gruppo di risorse contenente la risorsa Microsoft.Solutions/appliances deve essere creato nella località **westcentralus**.

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione alle applicazioni gestite, vedere [Panoramica delle applicazioni gestite di Azure](managed-application-overview.md).
* Per informazioni sulla pubblicazione di un'applicazione gestita del catalogo di servizi, vedere [Creare e pubblicare un'applicazione gestita del catalogo di servizi](managed-application-publishing.md).
* Per informazioni sulla pubblicazione di applicazioni gestite in Azure Marketplace, vedere [Applicazioni gestite di Azure nel Marketplace](managed-application-author-marketplace.md).
* Per informazioni sull'uso di un'applicazione gestita dal Marketplace, vedere [Consume Azure managed applications in the Marketplace](managed-application-consume-marketplace.md) (Uso delle applicazioni gestite di Azure nel Marketplace).
