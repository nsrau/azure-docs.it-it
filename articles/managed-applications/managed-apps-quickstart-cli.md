---
title: Creare un'applicazione gestita di Azure con l'interfaccia della riga di comando di Azure | Microsoft Docs
description: Questo articolo descrive come creare un'applicazione gestita di Azure studiata per i membri della propria organizzazione.
services: azure-resource-manager
author: tfitzmac
manager: timlt
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.date: 12/15/2017
ms.author: tomfitz
ms.openlocfilehash: 35059603096279f7d58da1c1b40dd2ab3f1b5c38
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2017
---
# <a name="create-and-deploy-an-azure-managed-application-with-azure-cli"></a>Creare e distribuire un'applicazione gestita di Azure con l'interfaccia della riga di comando di Azure

Questo articolo fornisce un'introduzione all'uso delle applicazioni gestite. È possibile aggiungere una definizione di applicazione gestita a un catalogo interno per gli utenti dell'organizzazione e distribuire quindi l'applicazione gestita nella sottoscrizione. Per semplificare l'introduzione, i file per l'applicazione gestita sono già stati compilati. Un file definisce l'infrastruttura per la soluzione. Un secondo file definisce l'interfaccia utente per la distribuzione dell'applicazione gestita tramite il portale. Questi file sono disponibili tramite GitHub. Nell'esercitazione [Create service catalog application](publish-service-catalog-app.md) (Creareun'applicazione del catalogo di servizi) viene illustrato come compilare questi file.

Al termine, sono disponibili tre gruppi di risorse che includono diverse parti dell'applicazione gestita.

| Gruppo di risorse | Contiene | DESCRIZIONE |
| -------------- | -------- | ----------- |
| appDefinitionGroup | Definizione dell'applicazione gestita. | L'autore crea questo gruppo di risorse e la definizione dell'applicazione gestita. Tutti gli utenti autorizzati ad accedere alla definizione dell'applicazione gestita possono distribuirla. |
| applicationGroup | Istanza dell'applicazione gestita. | Il consumer crea questo gruppo di risorse e l'istanza dell'applicazione gestita. Il consumer può aggiornare l'applicazione gestita tramite questa istanza. |
| infrastructureGroup | Risorse per l'applicazione gestita. | Questo gruppo di risorse viene creato automaticamente quando viene creata l'applicazione gestita. L'autore può accedere a questo gruppo di risorse per gestire l'applicazione. Il consumer ha accesso limitato al gruppo di risorse. |

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group-for-definition"></a>Creare un gruppo di risorse per la definizione

La definizione dell'applicazione gestita esiste in un gruppo di risorse. Un gruppo di risorse è una raccolta logica in cui le risorse di Azure vengono distribuite e gestite.

Per creare un gruppo di risorse, usare il comando seguente:

```azurecli-interactive
az group create --name appDefinitionGroup --location westcentralus
```

## <a name="create-the-managed-application-definition"></a>Creare la definizione di applicazione gestita

Quando si definisce l'applicazione gestita, si seleziona un utente, un gruppo o un'applicazione che gestisce le risorse per conto del consumer. Questa identità ha autorizzazioni per il gruppo di risorse gestito in base al ruolo assegnato. In genere si crea un gruppo di Azure Active Directory per gestire le risorse. Per questo articolo, tuttavia, usare la propria identità.

Per ottenere l'ID oggetto dell'identità, specificare il nome dell'entità utente nel comando seguente:

```azurecli-interactive
userid=$(az ad user show --upn-or-object-id example@contoso.org --query objectId --output tsv)
```

Ora è necessario l'ID di definizione del ruolo Controllo degli accessi in base al ruolo predefinito a cui si vuole concedere l'accesso all'utente. Il comando seguente illustra come ottenere l'ID di definizione per il ruolo Proprietario:

```azurecli-interactive
roleid=$(az role definition list --name Owner --query [].name --output tsv)
```

Creare a questo punto la definizione di applicazione gestita. L'applicazione gestita contiene solo un account di archiviazione.

```azurecli-interactive
az managedapp definition create \
  --name "ManagedStorage" \
  --location "westcentralus" \
  --resource-group appDefinitionGroup \
  --lock-level ReadOnly \
  --display-name "Managed Storage Account" \
  --description "Managed Azure Storage Account" \
  --authorizations "$userid:$roleid" \
  --package-file-uri "https://raw.githubusercontent.com/Azure/azure-managedapp-samples/master/samples/201-managed-storage-account/managedstorage.zip"
```

Al completamento del comando, è disponibile una definizione dell'applicazione gestita nel gruppo di risorse. 

Ecco alcuni parametri usati nell'esempio precedente:

* **resource-group**: nome del gruppo di risorse in cui viene creata la definizione di applicazione gestita.
* **lock-level**: tipo di blocco inserito nel gruppo di risorse gestito. Impedisce al cliente di eseguire operazioni indesiderate su questo gruppo di risorse. ReadOnly è attualmente il solo livello di blocco supportato. Quando ReadOnly è specificato, il cliente può solo leggere le risorse presenti nel gruppo di risorse gestito.
* **authorizations**: indica l'ID dell'entità di sicurezza e l'ID di definizione del ruolo usati per concedere l'autorizzazione al gruppo di risorse gestito. Viene specificato nel formato `<principalId>:<roleDefinitionId>`. Per questa proprietà si possono specificare anche altri valori. Se sono necessari più valori, deve essere specificata nel formato `<principalId1>:<roleDefinitionId1> <principalId2>:<roleDefinitionId2>`. I valori multipli sono separati da uno spazio.
* **package-file-uri**: percorso di un pacchetto con estensione zip che include i file necessari. Il pacchetto contiene almeno i file **mainTemplate.json** e **createUiDefinition.json**. **mainTemplate.json** definisce le risorse di Azure di cui viene effettuato il provisioning nell'ambito dell'applicazione gestita. Il modello non è diverso da un modello standard di Resource Manager. **createUiDefinition.json** genera l'interfaccia utente per gli utenti che creano l'applicazione gestita tramite il portale.

## <a name="create-resource-group-for-managed-application"></a>Creare un gruppo di risorse per l'applicazione gestita

È ora possibile distribuire l'applicazione gestita. 

Per contenere l'applicazione gestita distribuita è necessario un gruppo di risorse. Usare **westcentralus** come posizione.

```azurecli-interactive
az group create --name applicationGroup --location westcentralus
```

## <a name="deploy-the-managed-application"></a>Distribuire l'applicazione gestita

Per distribuire l'applicazione, usare i comandi seguenti:

```azurecli-interactive
appid=$(az managedapp definition show --name ManagedStorage --resource-group appDefinitionGroup --query id --output tsv)
subid=$(az account show --query id --output tsv)
managedGroupId=/subscriptions/$subid/resourceGroups/infrastructureGroup

az managedapp create \
  --name storageApp \
  --location "westcentralus" \
  --kind "Servicecatalog" \
  --resource-group applicationGroup \
  --managedapp-definition-id $appid \
  --managed-rg-id $managedGroupId \
  --parameters "{\"storageAccountNamePrefix\": {\"value\": \"storage\"}, \"storageAccountType\": {\"value\": \"Standard_LRS\"}}"
```

Ecco alcuni parametri usati nell'esempio precedente:

* **managedapp-definition-id**: ID della definizione creata in precedenza in questo articolo.
* **managed-rg-id**: ID del gruppo di risorse per le risorse associate all'applicazione gestita. Il comando crea questo gruppo di risorse. **Non deve esistere prima dell'esecuzione del comando**. Questo gruppo di risorse è gestito dall'autore. 
* **resource-group**: gruppo di risorse in cui viene creata la risorsa applicazione gestita.
* **parameters**: parametri necessari per le risorse associate all'applicazione gestita.

Al termine della distribuzione, l'applicazione gestita viene creata in applicationGroup. La risorsa storageAccount viene creata in infrastructureGroup.

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione alle applicazioni gestite, vedere [Panoramica delle applicazioni gestite di Azure](overview.md).
* Per gli esempi dei file, vedere [Managed Application samples](https://github.com/Azure/azure-managedapp-samples/tree/master/samples) (Esempi di applicazione gestita).
* Per informazioni sulla creazione di un file di definizione dell'interfaccia utente per un'applicazione gestita, vedere [Introduzione a CreateUiDefinition](create-uidefinition-overview.md).
