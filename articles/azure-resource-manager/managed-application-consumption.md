---
title: Utilizzare un&quot;applicazione gestita di Azure | Microsoft Docs
description: "Descrive come un cliente può creare un&quot;applicazione gestita di Azure dai file pubblicati."
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/11/2017
ms.author: gauravbh; tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 2763da60fe25f2ca55603ecfcbbcefe3e368c25d
ms.contentlocale: it-it
ms.lasthandoff: 05/11/2017


---
# <a name="consume-an-azure-managed-application"></a>Utilizzare un'applicazione gestita di Azure

Come illustrato nell'articolo [Panoramica di Applicazione gestita di Azure](managed-application-overview.md), gli scenari nell'esperienza end-to-end sono due. Nel primo l'entità di pubblicazione o il fornitore di software indipendente vuole creare un'applicazione gestita che verrà usata dai clienti. Il secondo è relativo al cliente finale o all'utente dell'applicazione gestita. Questo articolo riguarda il secondo scenario e illustra l'utilizzo di un'applicazione gestita fornita da un fornitore di software indipendente da parte di un cliente finale.

Attualmente, per utilizzare un'applicazione gestita è possibile usare l'interfaccia della riga di comando di Azure o il portale di Azure. 

## <a name="create-the-managed-application-using-cli"></a>Creare l'applicazione gestita con l'interfaccia della riga di comando 

È necessario ottenere l'ID di definizione per l'appliance da utilizzare.

A tale scopo, è possibile procedere in due modi usando l'interfaccia della riga di comando di Azure per creare un'applicazione gestita. Il primo consiste nell'usare il normale comando di distribuzione modelli, mentre il secondo prevede l'uso di un nuovo comando specificato solo a questo scopo.

### <a name="create-using-template-deployment-command"></a>Creare l'applicazione gestita con il comando di distribuzione modelli

Distribuire il file applianceMainTemplate.json creato dal fornitore.

Creare due gruppi di risorse. Il primo gruppo deve essere creato nel punto in cui viene creata la risorsa appliance, Microsoft.Solutions/appliances. Il secondo contiene tutte le risorse definite nel file mainTemplate.json. Questo gruppo di risorse è gestito dal fornitore di software indipendente.

```azurecli
az group create --name mainResourceGroup --location westcentralus    
az group create --name managedResourceGroup --location westcentralus
```

> [!NOTE]
> Usare `westcentralus` come località del gruppo di risorse.
>


Successivamente, usare il comando seguente per distribuire il file applianceMainTemplate.json in mainResourceGroup:

```azurecli
az group deployment --name managedAppDeployment --resourceGroup mainResourceGroup 
      --templateUri  
```

Quando viene eseguito il modello precedente, vengono richiesti i valori dei parametri definiti nel modello. Oltre ai parametri richiesti per il provisioning delle risorse in un modello, sono necessari due valori di parametri chiave:

- managedResourceGroupId: ID del gruppo di risorse in cui vengono create le risorse definite nel file applianceMainTemplate.json. L'ID è nel formato `/subscriptions/{subscriptionId}/resourceGroups/{resoureGroupName}`. Nell'esempio precedente si tratta dell'ID di `managedResourceGroup`.
- applianceDefinitionId: ID della risorsa di definizione dell'applicazione gestita. Questo valore viene fornito dal fornitore di software indipendente. 

> [!NOTE] 
> Il fornitore di software indipendente deve concedere l'accesso al gruppo di risorse in cui viene creata la risorsa di definizione dell'appliance. La risorsa di definizione dell'appliance viene creata nella sottoscrizione del fornitore di software indipendente. È quindi necessario che utenti, gruppi di utenti o applicazioni nel tenant del cliente abbiano accesso in lettura a questa risorsa. 

Al termine della distribuzione, la risorsa appliance viene creata in **mainResourceGroup**. La risorsa storageAccount viene creata in **managedResourceGroup**.

### <a name="create-the-managed-application-using-create-command"></a>Creare l'applicazione gestita con il comando di creazione

È possibile usare il comando `az managedapp create` per creare un'applicazione gestita dalla definizione di applicazione gestita. 

```azurecli
az managedapp create --name ravtestappliance401 --location "westcentralus" 
    --kind "Servicecatalog" --resource-group "ravApplianceCustRG401" 
       --managedapp-definition-id "/subscriptions/{guid}/resourceGroups/ravApplianceDefRG401/providers/Microsoft.Solutions/applianceDefinitions/ravtestAppDef401" 
       --managed-rg-id "/subscriptions/{guid}/resourceGroups/ravApplianceCustManagedRG401" 
       --parameters "{\"storageAccountName\": {\"value\": \"ravappliancedemostore1\"}}" 
       --debug
```

**appliance-definition-Id**: ID della risorsa di definizione dell'appliance creata nel passaggio precedente. Per ottenere questo ID, eseguire questo comando:

```azurecli
az appliance definition show -n ravtestAppDef1 -g ravApplianceRG2
```

Questo comando restituisce la definizione dell'appliance. È necessario il valore della proprietà **Id**.

**managed-rg-id**: nome del gruppo di risorse in cui vengono create tutte le risorse definite nel file applianceMainTemplate.json. Si tratta del gruppo di risorse gestite dal server di pubblicazione. Se non esiste, verrà creato.

**resource-group**: gruppo di risorse in cui viene creata la risorsa appliance. La risorsa Microsoft.Solutions/appliance si trova in questo gruppo di risorse. 

**parameters**: parametri necessari per le risorse definite nel file applianceMainTemplate.json.

## <a name="create-the-managed-application-using-portal"></a>Creare l'applicazione gestita con il portale

Il supporto per l'utilizzo di applicazioni gestite pubblicate dai fornitori di software indipendenti è disponibile anche nel portale. È possibile seguire questa procedura:

Selezionare l'applicazione gestita dal pannello Crea del portale di Azure.

![](./media/managed-application-consumption/create-blade.png)

Verrà visualizzato l'elenco di offerte dei vari fornitori di software indipendenti o partner. Scegliere quale creare e fare clic su "Crea".

![](./media/managed-application-consumption/select-offer.png)

Dopo aver fatto clic su Crea, specificare i parametri richiesti per il provisioning delle risorse nel pannello visualizzato. 

![](./media/managed-application-consumption/input-parameters.png)

Al termine, fare clic su OK. Il modello viene convalidato in base agli input specificati. Se la convalida ha esito positivo, viene avviata la distribuzione del modello. Al termine della distribuzione, nel gruppo di risorse gestite specificato viene eseguito il provisioning delle risorse appropriate definite nel modello.

## <a name="known-issues"></a>Problemi noti

Questa versione di anteprima contiene i problemi seguenti:

* Se viene visualizzato il messaggio 500 - Errore interno del server durante la creazione dell'appliance, è probabile che si tratti di un problema intermittente. Se si verifica questo problema, ripetere l'operazione.
* Per il gruppo di risorse gestite è necessario un nuovo gruppo di risorse. Se si cerca di usare un gruppo di risorse esistente, la distribuzione ha esito negativo.
* Il gruppo di risorse contenente la risorsa Microsoft.Solutions/appliances deve essere creato nella località **westcentralus**.

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione alle applicazioni gestite, vedere [Panoramica di Applicazione gestita di Azure](managed-application-overview.md).
* Per informazioni sull'esperienza del fornitore, vedere [Creare e pubblicare un'applicazione gestita di Azure](managed-application-publishing.md).
