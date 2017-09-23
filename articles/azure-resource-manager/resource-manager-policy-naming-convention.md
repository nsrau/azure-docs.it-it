---
title: Criteri delle risorse di Azure per le convenzioni di denominazione| Microsoft Docs
description: Descrive i criteri di Azure Resource Manager per le convenzioni di denominazione delle risorse.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: 51b3519bbba8cb4c768bfdd7dadf92fced434f22
ms.contentlocale: it-it
ms.lasthandoff: 06/30/2017

---
# <a name="apply-resource-policies-for-names-and-text"></a>Applicare criteri delle risorse per nomi e testo
Questo argomento indica diversi [criteri delle risorse](resource-manager-policy.md) che è possibile applicare per stabilire le convenzioni di denominazione e testo. Questi criteri assicurano la coerenza per i nomi delle risorse e i valori dei tag. 

## <a name="set-naming-convention-with-wildcard"></a>Impostare la convenzione di denominazione con caratteri jolly
L'esempio seguente illustra l'uso dei caratteri jolly supportato dalla condizione **like**. La condizione dichiara che la richiesta deve essere negata se il nome non corrisponde al modello indicato (namePrefix\*nameSuffix):

```json
{
  "if": {
    "not": {
      "field": "name",
      "like": "namePrefix*nameSuffix"
    }
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="set-naming-convention-with-pattern"></a>Impostare la convenzione di denominazione con modelli

Per specificare che i nomi di risorsa corrispondono a un modello, usare la condizione match. Nell'esempio seguente i nomi devono iniziare con `contoso` e contenere altre sei lettere:

```json
{
  "if": {
    "not": {
      "field": "name",
      "match": "contoso??????"
    }
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="set-date-pattern-for-tag-value"></a>Impostare il modello di data per il valore di tag

Per imporre un modello di data di due cifre, trattino, tre lettere, trattino e quattro cifre, usare:

```json
{
  "if": {
    "field": "tags.date",
    "match": "##-???-####"
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="next-steps"></a>Passaggi successivi
* Dopo aver definito una regola di criterio, come mostrato negli esempi precedenti, è necessario creare la definizione di criterio e assegnarla a un ambito. L'ambito può essere una sottoscrizione, un gruppo di risorse o una risorsa. Per assegnare i criteri tramite il portale, vedere [Use Azure portal to assign and manage resource policies](resource-manager-policy-portal.md) (Usare il portale di Azure per assegnare e gestire i criteri delle risorse). Per assegnare i criteri tramite l'API REST, PowerShell o l'interfaccia della riga di comando di Azure, vedere [Assegnare e gestire i criteri tramite script](resource-manager-policy-create-assign.md). 
* Per indicazioni su come le aziende possono usare Resource Manager per gestire efficacemente le sottoscrizioni, vedere [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Scaffolding aziendale Azure - Governance prescrittiva per le sottoscrizioni).


