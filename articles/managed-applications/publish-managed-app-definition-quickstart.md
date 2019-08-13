---
title: Creare una definizione di applicazione gestita di Azure | Microsoft Docs
description: Questo articolo descrive come creare un'applicazione gestita di Azure studiata per i membri della propria organizzazione.
services: managed-applications
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.date: 08/06/2019
ms.author: tomfitz
ms.openlocfilehash: 7682670131b0ef50a1480285bc379b634169e49e
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840613"
---
# <a name="publish-an-azure-managed-application-definition"></a>Pubblicare una definizione di applicazione gestita di Azure

Questa guida introduttiva fornisce un'introduzione all'uso delle applicazioni gestite. È possibile aggiungere una definizione di applicazione gestita a un catalogo interno per gli utenti dell'organizzazione Per semplificare l'introduzione, i file per l'applicazione gestita sono già stati compilati. Questi file sono disponibili tramite GitHub. Nell'esercitazione [Create service catalog application](publish-service-catalog-app.md) (Creareun'applicazione del catalogo di servizi) viene illustrato come compilare questi file.

Al termine si avrà un gruppo di risorse denominato **appDefinitionGroup** con la definizione di applicazione gestita.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group-for-definition"></a>Creare un gruppo di risorse per la definizione

La definizione dell'applicazione gestita esiste in un gruppo di risorse. Un gruppo di risorse è una raccolta logica in cui le risorse di Azure vengono distribuite e gestite.

Per creare un gruppo di risorse, usare il comando seguente:

```azurecli-interactive
az group create --name appDefinitionGroup --location westcentralus
```

## <a name="create-the-managed-application-definition"></a>Creare la definizione di applicazione gestita

Quando si definisce l'applicazione gestita, si seleziona un utente, un gruppo o un'applicazione che gestisce le risorse per il consumer. Questa identità ha autorizzazioni per il gruppo di risorse gestito in base al ruolo assegnato. In genere si crea un gruppo di Azure Active Directory per gestire le risorse. Per questo articolo, tuttavia, usare la propria identità.

Per ottenere l'ID oggetto dell'identità, specificare il nome dell'entità utente nel comando seguente:

```azurecli-interactive
userid=$(az ad user show --id example@contoso.org --query objectId --output tsv)
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
* **lock-level**: tipo di blocco inserito nel gruppo di risorse gestito. Impedisce al cliente di eseguire operazioni indesiderate su questo gruppo di risorse. ReadOnly è attualmente il solo livello di blocco supportato. Quando ReadOnly è specificato, il cliente può solo leggere le risorse presenti nel gruppo di risorse gestito. Le identità degli autori a cui è concesso l'accesso al gruppo di risorse gestito sono esenti dal blocco.
* **authorizations**: indica l'ID dell'entità di sicurezza e l'ID di definizione del ruolo usati per concedere l'autorizzazione al gruppo di risorse gestito. Viene specificato nel formato `<principalId>:<roleDefinitionId>`. Se sono necessari più valori, specificarli nel formato `<principalId1>:<roleDefinitionId1> <principalId2>:<roleDefinitionId2>`. I valori sono separati da uno spazio.
* **package-file-uri**: percorso di un pacchetto con estensione zip che include i file necessari. Il pacchetto deve contenere i file **mainTemplate.json** e **createUiDefinition.json**. **mainTemplate.json** definisce le risorse di Azure create nell'ambito dell'applicazione gestita. Il modello non è diverso da un modello standard di Resource Manager. **createUiDefinition.json** genera l'interfaccia utente per gli utenti che creano l'applicazione gestita tramite il portale.

## <a name="next-steps"></a>Passaggi successivi

La definizione di applicazione gestita è stata pubblicata. Ora si vedrà come distribuire un'istanza di tale definizione.

> [!div class="nextstepaction"]
> [Guida introduttiva: Distribuire un'app del catalogo di servizi](deploy-service-catalog-quickstart.md)