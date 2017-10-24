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
ms.topic: support-article
ms.date: 07/12/2017
ms.author: genli
ms.openlocfilehash: 3b11dc6afac716ef391976093839547e8fd37a91
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>Errore RequestDisallowedByPolicy con i criteri delle risorse di Azure

Questo articolo descrive la causa dell'errore RequestDisallowedByPolicy e indica la relativa soluzione.

## <a name="symptom"></a>Sintomo

Quando si tenta di eseguire un'azione durante la distribuzione, è possibile che venga visualizzato un errore **RequestDisallowedByPolicy** che impedisce il completamento dell'azione. Nell'esempio seguente viene descritto l'errore:

```json
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

In PowerShell specificare questo identificatore di criterio come parametro `Id` per recuperare i dettagli relativi ai criteri che hanno bloccato la distribuzione.

```PowerShell
(Get-AzureRmPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="method-2"></a>Metodo 2 

Nell'interfaccia della riga di comando di Azure 2.0 specificare il nome della definizione del criterio: 

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>Soluzione

Per assicurare la sicurezza o la conformità, il reparto IT può applicare un criterio di risorsa che impedisce la creazione di indirizzi IP pubblici, gruppi di sicurezza di rete, route definite dall'utente o tabelle di routing. Il messaggio di errore nella sezione **Sintomo** illustra un criterio denominato **regionPolicyDefinition**. Il nome del criterio potrebbe essere diverso.
Per risolvere questo problema, collaborare con il reparto IT per controllare i criteri delle risorse e determinare come eseguire l'azione richiesta in conformità con questi criteri.

Per altre informazioni, vedere gli articoli seguenti:

- [Cenni preliminari sui criteri delle risorse](resource-manager-policy.md)
- [Visualizzare le assegnazioni di criteri tramite il portale](resource-manager-policy-portal.md#view-policy-assignments)