---
title: Chiamate PUT per operazioni di creazione o aggiornamento
description: Inserire le chiamate per le operazioni di creazione o aggiornamento sulle risorse di calcolo
author: mimckitt
ms.author: mimckitt
ms.reviewer: cynthn
ms.topic: conceptual
ms.service: virtual-machines
ms.date: 08/4/2020
ms.custom: avverma
ms.openlocfilehash: c57025346a9f623e3fe5536b36820ea62f355cc0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91344639"
---
# <a name="put-calls-for-creation-or-updates-on-compute-resources"></a>Inserire le chiamate per la creazione o gli aggiornamenti nelle risorse di calcolo

`Microsoft.Compute` le risorse non supportano la definizione convenzionale della semantica *PUT http* . Al contrario, queste risorse usano la semantica delle PATCH per i verbi PUT e PATCH.

Quando necessario, le operazioni **create** applicano i valori predefiniti. Tuttavia, **gli aggiornamenti** delle risorse eseguiti tramite put o patch non applicano alcuna proprietà predefinita. Le operazioni di **aggiornamento** applicano la semantica delle patch restrittiva.

Ad esempio, la `caching` Proprietà disco di una macchina virtuale viene impostata automaticamente su `ReadWrite` se la risorsa è un disco del sistema operativo.

```json
    "storageProfile": {
      "osDisk": {
        "name": "myVMosdisk",
        "image": {
          "uri": "http://{existing-storage-account-name}.blob.core.windows.net/{existing-container-name}/{existing-generalized-os-image-blob-name}.vhd"
        },
        "osType": "Windows",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "vhd": {
          "uri": "http://{existing-storage-account-name}.blob.core.windows.net/{existing-container-name}/myDisk.vhd"
        }
      }
    },
```

Tuttavia, per le operazioni di **aggiornamento** quando una proprietà viene esclusa o viene passato un valore *null* , rimarrà invariato e non saranno presenti valori predefiniti.

Questo è importante quando si inviano le operazioni di aggiornamento a una risorsa con l'intenzione di rimuovere un'associazione. Se tale risorsa è una `Microsoft.Compute` risorsa, la proprietà corrispondente da rimuovere deve essere esplicitamente denominata e un valore assegnato. A tale scopo, gli utenti possono passare una stringa vuota, ad esempio **""**. In questo modo si indicherà alla piattaforma di rimuovere l'associazione.

> [!IMPORTANT]
> Non è disponibile alcun supporto per l'applicazione di patch a un elemento di matrice. Al contrario, il client deve eseguire una richiesta PUT o PATCH con l'intero contenuto della matrice aggiornata. Ad esempio, per scollegare un disco dati da una macchina virtuale, eseguire una richiesta GET per ottenere il modello di macchina virtuale corrente, rimuovere il disco da scollegare `properties.storageProfile.dataDisks` ed eseguire una richiesta PUT con l'entità VM aggiornata.

## <a name="examples"></a>Esempi

### <a name="correct-payload-to-remove-a-proximity-placement-groups-association"></a>Correggere il payload per rimuovere un'associazione gruppi di posizionamento prossimità

`
{ "location": "westus", "properties": { "platformFaultDomainCount": 2, "platformUpdateDomainCount": 20, "proximityPlacementGroup": "" } }
`

### <a name="incorrect-payloads-to-remove-a-proximity-placement-groups-association"></a>Payload non corretti per rimuovere un'associazione gruppi di posizionamento prossimità

`
{ "location": "westus", "properties": { "platformFaultDomainCount": 2, "platformUpdateDomainCount": 20, "proximityPlacementGroup": null } }
`

`
{ "location": "westus", "properties": { "platformFaultDomainCount": 2, "platformUpdateDomainCount": 20 } }
`

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su come creare o aggiornare le chiamate per le [macchine virtuali](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate) e i [set di scalabilità](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate) di macchine virtuali

