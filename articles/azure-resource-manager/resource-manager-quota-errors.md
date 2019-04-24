---
title: Errori di quota di Azure | Microsoft Docs
description: Viene descritto come risolvere gli errori di quota delle risorse.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 03/09/2018
ms.author: tomfitz
ms.openlocfilehash: 7938f2c47e4af8d8804191fbb9e55b379f9554ef
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60390191"
---
# <a name="resolve-errors-for-resource-quotas"></a>Risolvere gli errori di quota delle risorse

Questo articolo descrive gli errori di quota che possono verificarsi durante la distribuzione delle risorse.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="symptom"></a>Sintomo

Se si distribuisce un modello che crea risorse che superano la quota di Azure, verrà visualizzato un errore di distribuzione come il seguente:

```
Code=OperationNotAllowed
Message=Operation results in exceeding quota limits of Core.
Maximum allowed: 4, Current in use: 4, Additional requested: 2.
```

In alternativa, è possibile visualizzare:

```
Code=ResourceQuotaExceeded
Message=Creating the resource of type <resource-type> would exceed the quota of <number>
resources of type <resource-type> per resource group. The current resource count is <number>,
please delete some resources of this type before creating a new one.
```

## <a name="cause"></a>Causa

Le quote vengono applicate per ogni gruppo di risorse, sottoscrizioni, account e altri ambiti. Ad esempio, la sottoscrizione potrebbe essere configurata in modo da limitare il numero di core per un'area. Se si prova a distribuire una macchina virtuale con un numero di core superiore alla quantità consentita, verrà visualizzato un messaggio di errore che informa che la quota è stata superata.
Per informazioni complete sulle quote, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md).

## <a name="troubleshooting"></a>risoluzione dei problemi

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per l'interfaccia della riga di comando di Azure, usare il comando `az vm list-usage` per individuare le quote delle macchine virtuali.

```azurecli
az vm list-usage --location "South Central US"
```

Che restituisce:

```azurecli
[
  {
    "currentValue": 0,
    "limit": 2000,
    "name": {
      "localizedValue": "Availability Sets",
      "value": "availabilitySets"
    }
  },
  ...
]
```

### <a name="powershell"></a>PowerShell

Per PowerShell, usare il comando **Get-AzVMUsage** per individuare le quote delle macchine virtuali.

```powershell
Get-AzVMUsage -Location "South Central US"
```

Che restituisce:

```powershell
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional Cores                         0   100 Count
Virtual Machines                             0 10000 Count
```

## <a name="solution"></a>Soluzione

Per ottenere un aumento della quota, accedere al portale e inviare una richiesta di supporto. Nella richiesta specificare un aumento della quota per l'area di destinazione della distribuzione.

> [!NOTE]
> Tenere presente che per i gruppi di risorse, la quota è riferita alle singole aree e non all'intera sottoscrizione. Se è necessario distribuire 30 core nell'area Stati Uniti occidentali, è necessario richiedere 30 core di gestione delle risorse per Stati Uniti occidentali. Se è necessario distribuire 30 core in qualsiasi area a cui si ha accesso, è consigliabile richiedere 30 core di Resource Manager in tutte le aree.
>
>

1. Selezionare **Sottoscrizioni**.

   ![Sottoscrizioni](./media/resource-manager-quota-errors/subscriptions.png)

2. Selezionare la sottoscrizione per cui è necessario ottenere un aumento della quota.

   ![Selezionare la sottoscrizione](./media/resource-manager-quota-errors/select-subscription.png)

3. Selezionare **Utilizzo e quote**.

   ![Selezionare Utilizzo e quote](./media/resource-manager-quota-errors/select-usage-quotas.png)

4. Nell'angolo superiore destro selezionare **Richiedi aumento**.

   ![Richiedi aumento](./media/resource-manager-quota-errors/request-increase.png)

5. Compilare i moduli per il tipo di quota che è necessario aumentare.

   ![Compilare i moduli](./media/resource-manager-quota-errors/forms.png)