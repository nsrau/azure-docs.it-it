---
title: 'Esempio JSON di Criteri di Azure: controllare lo stato Transparent Data Encryption per Database SQL | Microsoft Docs'
description: Questo criterio di esempio JSON controlla se Transparent Data Encryption del database SQL non è abilitato.
services: azure-policy
documentationcenter: ''
author: DCtheGeek
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-policy
ms.devlang: ''
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: ''
ms.date: 04/27/2018
ms.author: dacoulte
ms.custom: mvc
ms.openlocfilehash: 91759155a828c9da4de7f2190b1d27fd1d312bd6
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34601667"
---
# <a name="audit-sql-database-encryption"></a>Controllare la crittografia di Database SQL

Questo criterio predefinito controlla se Transparent Data Encryption del database SQL non è abilitato.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Modello di esempio

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Sql/servers/databases"
      },
      {
        "field": "name",
        "notEquals": "master"
      }
    ]
  },
  "then": {
    "effect": "AuditIfNotExists",
    "details": {
      "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
      "name": "current",
      "existenceCondition": {
        "allOf": [
          {
            "field": "Microsoft.Sql/transparentDataEncryption.status",
            "equals": "enabled"
          }
        ]
      }
    }
  }
}
```

È possibile distribuire questo modello usando il [portale di Azure](#deploy-with-the-portal), con [PowerShell](#deploy-with-powershell) o con l'[interfaccia della riga di comando di Azure](#deploy-with-azure-cli). Per ottenere i criteri predefiniti, usare l'ID `17k78e20-9358-41c9-923c-fb736d382a12`.

## <a name="deploy-with-the-portal"></a>Eseguire la distribuzione con il portale

Quando si assegna un criterio, selezionare **Controlla stato di Transparent Data Encryption** dalle definizioni predefinite disponibili.

## <a name="deploy-with-powershell"></a>Distribuire con PowerShell

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

```powershell
$definition = Get-AzureRmPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/17k78e20-9358-41c9-923c-fb736d382a12

New-AzureRmPolicyAssignment -name "SQL TDE Audit" -PolicyDefinition $definition -Scope <scope>
```

### <a name="clean-up-powershell-deployment"></a>Eliminare la distribuzione di PowerShell

Eseguire il comando seguente per rimuovere l'assegnazione del criterio.

```powershell
Remove-AzureRmPolicyAssignment -Name "SQL TDE Audit" -Scope <scope>
```

## <a name="deploy-with-azure-cli"></a>Distribuire con l'interfaccia della riga di comando di Azure

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy assignment create --scope <scope> --name "SQL TDE Audit" --policy 17k78e20-9358-41c9-923c-fb736d382a12
```

### <a name="clean-up-azure-cli-deployment"></a>Eliminare la distribuzione dell'interfaccia della riga di comando di Azure

Eseguire il comando seguente per rimuovere l'assegnazione del criterio.

```azurecli-interactive
az policy assignment delete --name "SQL TDE Audit" --resource-group myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

- Vedere altri esempi in [Esempi di Criteri di Azure](../json-samples.md).