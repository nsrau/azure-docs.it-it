---
title: "Avvio rapido: Nuova assegnazione di criteri con l'API REST"
description: In questo argomento di avvio rapido viene usata un'API REST per creare un'assegnazione di Criteri di Azure per identificare le risorse non conformi.
ms.date: 08/10/2020
ms.topic: quickstart
ms.openlocfilehash: 04880ef013060bc5ff12618af6a9156295a26a88
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2020
ms.locfileid: "88137088"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-with-rest-api"></a>Avvio rapido: Creare un criterio per identificare le risorse non conformi con un'API REST

Il primo passaggio per comprendere la conformità in Azure consiste nell'identificare lo stato delle risorse.
Questa guida introduttiva illustra il processo di creazione di un'assegnazione di criteri per identificare le macchine virtuali che non usano dischi gestiti.

Alla fine di questo processo, verranno identificate le macchine virtuali che non usano dischi gestiti e che sono quindi _non conformi_ all'assegnazione di criteri.

L'API REST viene usata per creare e gestire le risorse di Azure. Questa guida usa un'API REST per creare un'assegnazione di criteri per identificare le risorse non conformi nell'ambiente Azure.

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

- Se non è già stato fatto, installare [ARMClient](https://github.com/projectkudu/ARMClient). È uno strumento che invia richieste HTTP alle API REST basate su Azure Resource Manager. In alternativa è possibile usare la funzionalità "Prova" nella documentazione di REST o strumenti come [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) di PowerShell o [Postman](https://www.postman.com).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-policy-assignment"></a>Creare un'assegnazione di criteri

In questo avvio rapido i crea un'assegnazione di criteri e si assegna la definizione **Controlla macchine virtuali che non usano dischi gestiti** (`06a78e20-9358-41c9-923c-fb736d382a4d`). Questa definizione di criteri identifica le risorse che non rispettano le condizioni in essa impostate.

Eseguire questo comando per creare un'assegnazione di criteri:

   - URI DELL'API REST

     ```http
     PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/audit-vm-manageddisks?api-version=2019-09-01
     ```

   - Request Body

     ```json
     {
       "properties": {
         "displayName": "Audit VMs without managed disks Assignment",
         "description": "Shows all virtual machines not using managed disks",
         "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d",
       }
     }
     ```

L'endpoint e il corpo della richiesta precedenti usano le informazioni seguenti:

URI DELL'API REST:
- **Scope**: ambito che determina le risorse o il raggruppamento di risorse a cui viene applicata l'assegnazione di criteri. Può spaziare da un gruppo di gestione a una singola risorsa. Assicurarsi di sostituire `{scope}` con uno dei criteri seguenti:
  - Gruppo di gestione: `/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - Sottoscrizione: `/subscriptions/{subscriptionId}`
  - Gruppo di risorse: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - Risorsa: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]{resourceType}/{resourceName}`
- **Name**: nome effettivo dell'assegnazione. Per questo esempio è stato usato il nome _audit-vm-manageddisks_.

Corpo della richiesta:
- **DisplayName**: nome visualizzato per l'assegnazione di criteri. In questo caso viene usato _controllare le macchine virtuali senza assegnazione di dischi gestiti_.
- **Description**: spiegazione più dettagliata delle operazioni eseguite dal criterio o dei motivi per cui è assegnato a questo ambito.
- **policyDefinitionId**: ID della definizione di criteri in base alla quale si crea l'assegnazione. In questo caso si tratta dell'ID della definizione di criteri _Controllare le macchine virtuali che non usano i dischi gestiti_.

## <a name="identify-non-compliant-resources"></a>Identificare risorse non conformi

Per visualizzare le risorse non conformi in base a questa nuova assegnazione, eseguire quindi questo comando per ottenere gli ID risorsa delle risorse non conformi restituite in un file JSON:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$filter=IsCompliant eq false and PolicyAssignmentId eq 'audit-vm-manageddisks'&$apply=groupby((ResourceId))"
```

I risultati saranno simili all'esempio seguente:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 3,
    "value": [{
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachineId>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine2Id>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine3Id>"
        }

    ]
}
```

I risultati sono paragonabili alle informazioni in genere riportate sotto **Non-compliant resources** (Risorse non conformi) nella visualizzazione nel portale di Azure.

## <a name="clean-up-resources"></a>Pulire le risorse

Eseguire il comando seguente per rimuovere l'assegnazione creata:

```http
DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/audit-vm-manageddisks?api-version=2019-09-01
```

Sostituire `{scope}` con l'ambito usato durante la creazione iniziale dell'assegnazione di criteri.

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva è stata assegnata una definizione dei criteri per identificare le risorse non conformi nell'ambiente Azure.

Per altre informazioni sull'assegnazione di criteri per convalidare la conformità delle nuove risorse, continuare con l'esercitazione seguente:

> [!div class="nextstepaction"]
> [Creazione e gestione dei criteri](./tutorials/create-and-manage.md)
