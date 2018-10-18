---
author: jluk
ms.service: cloud-shell
ms.topic: persist-storage
ms.date: 9/7/2018
ms.author: juluk
ms.openlocfilehash: 6055b70c7df2704a334b7f14c9365863ddafbd5a
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/07/2018
ms.locfileid: "44164547"
---
# <a name="persist-files-in-azure-cloud-shell"></a>Rendere persistenti i file in Azure Cloud Shell
Cloud Shell utilizza l'archiviazione dei file di Azure per mantenere i file in più sessioni. Al primo avvio Cloud Shell richiede di associare una condivisione file nuova o esistente per mantenere i file in più sessioni.

> [!NOTE]
> La condivisione Bash e PowerShell sono la stessa condivisione file. Solo una condivisione file può essere associata con il montaggio automatico in Cloud Shell.

## <a name="create-new-storage"></a>Creare una nuova risorsa di archiviazione

Quando si usano le impostazioni di base e si seleziona una sola sottoscrizione, Cloud Shell crea tre risorse per conto dell'utente nell'area di Azure supportata più vicina all'utente:
* Gruppo di risorse: `cloud-shell-storage-<region>`
* Account di archiviazione: `cs<uniqueGuid>`
* Condivisione file: `cs-<user>-<domain>-com-<uniqueGuid>`

![Impostazione della sottoscrizione](../articles/cloud-shell/media/persisting-shell-storage/basic-storage.png)

La condivisione viene montata come `clouddrive` nella directory `$Home`. Questa azione viene eseguita una sola volta e la condivisione file viene ripetuta automaticamente nelle sessioni successive. 

> [!NOTE]
> Per la sicurezza, ogni utente deve effettuare il provisioning del proprio account di archiviazione.  Per il controllo degli accessi in base al ruolo, gli utenti devono disporre dell'accesso come collaboratore o superiore a livello dell'account di archiviazione.

La condivisione file contiene anche un'immagine da 5 GB creata per l'utente che rende automaticamente persistenti i dati nella directory `$Home`. Questo vale sia per Bash che per PowerShell.

## <a name="use-existing-resources"></a>Usare le risorse esistenti

Con l'opzione Avanzate è possibile associare risorse esistenti. Al prompt di impostazione dell'archiviazione, selezionare **Mostra impostazioni avanzate** per visualizzare le opzioni aggiuntive. Il filtro di opzioni di archiviazione popolato per gli account di archiviazione con ridondanza locale, archiviazione con ridondanza della zona e archiviazione con ridondanza geografica. Andare [qui per altre informazioni](https://docs.microsoft.com/azure/storage/common/storage-redundancy#choosing-a-replication-option) sulle opzioni di replica per gli account di Archiviazione di Azure.

![Impostazione del gruppo di risorse](../articles/cloud-shell/media/persisting-shell-storage/advanced-storage.png)

Quando si seleziona un'area Cloud Shell è necessario selezionare per montare un account di archiviazione di backup in tale area.

### <a name="supported-storage-regions"></a>Aree di archiviazione supportate
Gli account di archiviazione di Azure associati devono risiedere nella stessa area del computer Cloud Shell in cui vengono montati. Per trovare l'area corrente, è possibile eseguire `env` in Bash e individuare la variabile `ACC_LOCATION`. Le condivisioni file ricevono un'immagine da 5 GB creata automaticamente per mantenere persistente la directory `$Home`.

I computer Cloud Shell esistono nelle aree seguenti:
|Area|Region|
|---|---|
|Americhe|Stati Uniti orientali, Stati Uniti centro-meridionali, Stati Uniti occidentali|
|Europa|Europa settentrionale, Europa occidentale|
|Asia/Pacifico|India centrale, Asia sud-orientale|

## <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Limitare la creazione di risorse con i criteri delle risorse di Azure
Gli account di archiviazione che si creano in Cloud Shell sono contrassegnati con `ms-resource-usage:azure-cloud-shell`. Se si desidera impedire agli utenti di creare account di archiviazione con Cloud Shell, creare [criteri di risorse di Azure per tag](../articles/azure-policy/json-samples.md) che vengono attivati dal tag specificato.
