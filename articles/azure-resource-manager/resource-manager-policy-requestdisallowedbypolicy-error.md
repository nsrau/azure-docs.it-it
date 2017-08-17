---
title: Errore RequestDisallowedByPolicy con i criteri delle risorse di Azure | Microsoft Docs
description: Descrive la causa dell'errore RequestDisallowedByPolicy.
services: azure-resource-manager,azure-portal
documentationcenter: 
author: genlin
manager: cshepard
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 182a27e444c2f5db66d518a1a0c608d3e319d553
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>Errore RequestDisallowedByPolicy con i criteri delle risorse di Azure

Questo articolo descrive la causa dell'errore RequestDisallowedByPolicy e indica la relativa soluzione.

## <a name="symptom"></a>Sintomo

Quando si tenta di eseguire un'azione durante la distribuzione, è possibile che venga visualizzato un errore **RequestDisallowedByPolicy** che impedisce l'esecuzione dell'azione. Di seguito è riportato un esempio dell'errore:

```
{
  "statusCode": "Forbidden",
  "serviceRequestId": null,
  "statusMessage": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}",
  "responseBody": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}"
}
```

## <a name="troubleshooting"></a>Risoluzione dei problemi

Per recuperare i dettagli sui criteri bloccati nella distribuzione, usare uno dei metodi seguenti:

### <a name="method-1"></a>Metodo 1

In PowerShell specificare l'identificatore di criteri come parametro **Id** per recuperare i dettagli relativi al criterio che ha bloccato la distribuzione.

```PowerShell
(Get-AzureRmPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="method-2"></a>Metodo 2 

Nell'interfaccia della riga di comando di Azure 2.0 specificare il nome della definizione del criterio: 

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>Soluzione

Per assicurare la sicurezza o la conformità, il reparto IT può applicare un criterio di risorsa che impedisce la creazione di indirizzi IP pubblici, gruppi di sicurezza di rete, route definite dall'utente o tabelle di routing. Nell'esempio di messaggio di errore descritto nella sezione "Sintomi", il criterio è denominato **regionPolicyDefinition**, ma potrebbe essere diverso.
Per risolvere questo problema, collaborare con il reparto IT per controllare i criteri delle risorse e determinare come eseguire l'azione richiesta in conformità con questi criteri.


Per altre informazioni, vedere gli articoli seguenti:

- [Cenni preliminari sui criteri delle risorse](resource-manager-policy.md)
- [Errori di distribuzione comuni -RequestDisallowedByPolicy](resource-manager-common-deployment-errors.md#requestdisallowedbypolicy)

 



