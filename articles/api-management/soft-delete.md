---
title: Eliminazione temporanea di gestione API di Azure (anteprima) | Microsoft Docs
description: L'eliminazione temporanea consente di ripristinare le istanze di gestione API eliminate.
ms.service: api-management
ms.topic: conceptual
author: vladvino
ms.author: apimpm
ms.date: 11/27/2020
ms.openlocfilehash: fca98414a87f3b8a4f3c0969a28ee95c7ed47dc3
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96501574"
---
# <a name="api-management-soft-delete-preview"></a>Eliminazione temporanea di gestione API (anteprima)

Con l'eliminazione temporanea (anteprima) di gestione API è possibile recuperare e ripristinare le istanze di gestione API (gestione API) eliminate recentemente.

> [!IMPORTANT]
> Solo le istanze di gestione API eliminate con `2020-01-01-preview` e versioni successive dell'API verranno eliminate temporaneamente e recuperabili usando la procedura descritta in questo articolo. Le istanze di gestione API eliminate con le versioni precedenti dell'API continueranno a essere eliminate definitivamente. Azure PowerShell e l'interfaccia della riga di comando di Azure attualmente non usano la versione e determineranno `2020-06-01-preview` anche un comportamento di eliminazione.

## <a name="supporting-interfaces"></a>Supporto di interfacce

La funzionalità di eliminazione temporanea è disponibile tramite l' [API REST](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/restore).

> [!TIP]
> Vedere le informazioni di [riferimento sull'API REST di Azure](/rest/api/azure/) per suggerimenti e strumenti per chiamare le API REST di Azure.

| Operazione | Descrizione | Spazio dei nomi gestione API | Versione minima API |
|--|--|--|--|
| [Crea o aggiorna](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/createorupdate) | Crea o aggiorna un servizio gestione API.  | Servizio gestione API | Qualsiasi |
| [Crea o aggiorna](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/createorupdate) con la `restore` proprietà impostata su **true** | Annulla l'eliminazione del servizio gestione API se è stato precedentemente eliminato temporaneamente. Se `restore` viene specificato e impostato su `true` tutte le altre proprietà verranno ignorate.  | Servizio gestione API |  2020-06-01-anteprima |
| [Elimina](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/delete) | Elimina un servizio gestione API esistente. | Servizio gestione API | 2020-01-01-anteprima|
| [Ottieni per nome](/rest/api/apimanagement/2020-06-01-preview/deletedservices/getbyname) | Ottenere il servizio gestione API eliminato temporaneamente in base al nome. | Servizi eliminati | 2020-06-01-anteprima |
| [Elenca per sottoscrizione](/rest/api/apimanagement/2020-06-01-preview/deletedservices/listbysubscription) | Elenca tutti i servizi eliminati temporaneamente disponibili per l'annullamento dell'eliminazione per la sottoscrizione specificata. | Servizi eliminati | 2020-06-01-anteprima
| [Ripulisci](/rest/api/apimanagement/2020-06-01-preview/deletedservices/purge) | Elimina il servizio gestione API (lo elimina senza alcuna opzione per annullare l'eliminazione). | Servizi eliminati | 2020-06-01-anteprima

## <a name="soft-delete-behavior"></a>Comportamento della funzione di eliminazione temporanea

È possibile usare qualsiasi versione dell'API per creare l'istanza di gestione API. Tuttavia, è necessario usare `2020-01-01-preview` o versioni successive per eliminare temporaneamente l'istanza di gestione API (ed è possibile recuperarla).

Quando si elimina un'istanza di gestione API, il servizio esiste in uno stato eliminato, rendendolo inaccessibile per qualsiasi operazione gestione API. In questo stato, l'istanza di gestione API può essere elencata, ripristinata o eliminata definitivamente (eliminata definitivamente).

Allo stesso tempo, Azure pianifica l'eliminazione dei dati sottostanti corrispondenti all'istanza di gestione API per l'esecuzione dopo l'intervallo di conservazione predeterminato (48 ore). Il record DNS corrispondente all'istanza viene mantenuto anche per la durata dell'intervallo di conservazione. Non è possibile riutilizzare il nome di un'istanza di gestione API che è stata eliminata temporaneamente finché non viene superato il periodo di conservazione.

Se l'istanza di gestione API non viene recuperata entro 48 ore, verrà eliminata definitivamente (irreversibile). È anche possibile scegliere di [ripulire](#purge-a-soft-deleted-apim-instance) (eliminare definitivamente) l'istanza di gestione API, rinunciando al periodo di conservazione dell'eliminazione temporanea.

## <a name="list-deleted-apim-instances"></a>Elencare le istanze di gestione API eliminate

È possibile verificare che sia disponibile un'istanza di gestione API eliminata temporaneamente per il ripristino (annullamento dell'eliminazione) utilizzando le operazioni di sottoscrizione [recuperate dai servizi eliminati in base al nome](/rest/api/apimanagement/2020-06-01-preview/deletedservices/getbyname) o all' [elenco](/deletedservices/listbysubscription) .

### <a name="get-a-soft-deleted-instance-by-name"></a>Ottenere un'istanza eliminata temporaneamente per nome

Usare l'operazione di gestione API [Get by Name](/rest/api/apimanagement/2020-06-01-preview/deletedservices/getbyname) , sostituendo `{subscriptionId}` , `{location}` e `{serviceName}` con la sottoscrizione di Azure, il percorso della risorsa e il nome dell'istanza di gestione API:

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ApiManagement/locations/{location}/deletedservices/{serviceName}?api-version=2020-06-01-preview
```

Se disponibile per l'annullamento dell'eliminazione, Azure restituirà un record dell'istanza di gestione API che mostra i relativi `deletionDate` e `scheduledPurgeDate` , ad esempio:

```json
{
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ApiManagement/locations/southcentralus/deletedservices/apimtest",
    "name": "apimtest",
    "type": "Microsoft.ApiManagement/deletedservices",
    "location": "South Central US",
    "properties": {
        "serviceId": "/subscriptions/########-####-####-####-############/resourceGroups/apimtestgroup/providers/Microsoft.ApiManagement/service/apimtest",
        "scheduledPurgeDate": "2020-11-26T19:40:26.3596893Z",
        "deletionDate": "2020-11-24T19:40:50.1013572Z"
    }
}
```

### <a name="list-all-soft-deleted-instances-for-a-given-subscription"></a>Elencare tutte le istanze eliminate temporaneamente per una determinata sottoscrizione

Usare l'operazione [di elenco Gestione API per sottoscrizione](/deletedservices/listbysubscription) , sostituendo `{subscriptionId}` con l'ID sottoscrizione:

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ApiManagement/deletedservices?api-version=2020-06-01-preview
```

Verrà restituito un elenco di tutti i servizi eliminati temporaneamente disponibili per l'annullamento dell'eliminazione nella sottoscrizione specificata, mostrando `deletionDate` e `scheduledPurgeDate` per ciascuno di essi.

## <a name="recover-a-deleted-apim-instance"></a>Ripristinare un'istanza di gestione API eliminata

Usare l'operazione di [creazione o aggiornamento](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/createorupdate) di gestione API, sostituendo `{subscriptionId}` , `{resourceGroup}` e `{apimServiceName}` con la sottoscrizione di Azure, il nome del gruppo di risorse e il nome di gestione API:

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.ApiManagement/service/{apimServiceName}?api-version=2020-06-01-preview
```

. . . e impostare la `restore` proprietà su `true` nel corpo della richiesta. Quando questo flag viene specificato e impostato su *true*, tutte le altre proprietà verranno ignorate. Per esempio:

```json
{
  "properties": {
    "publisherEmail": "help@contoso.com",
    "publisherName": "Contoso",
    "restore": true
  },
  "sku": {
    "name": "Developer",
    "capacity": 1
  },
  "location": "South Central US"
}
```

## <a name="purge-a-soft-deleted-apim-instance"></a>Elimina un'istanza di gestione API eliminata temporaneamente

Usare l'operazione di [ripulitura](/rest/api/apimanagement/2020-06-01-preview/deletedservices/purge) di gestione API, sostituendo `{subscriptionId}` , `{location}` e `{serviceName}` con la sottoscrizione di Azure, il percorso della risorsa e il nome gestione API:

```rest
DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ApiManagement/locations/{location}/deletedservices/{serviceName}?api-version=2020-06-01-preview
```

Questa operazione eliminerà definitivamente l'istanza di gestione API da Azure.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle opzioni di backup e ripristino di gestione API a lungo termine:

- [Come implementare il ripristino di emergenza usando il backup e il ripristino dei servizi in Gestione API di Azure](api-management-howto-disaster-recovery-backup-restore.md)