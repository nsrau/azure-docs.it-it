---
title: "Avvio rapido: Distribuire l'API di Azure per FHIR con PowerShell"
description: Questa guida di avvio rapido illustra come distribuire l'API di Azure per FHIR con PowerShell.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 10/15/2019
ms.author: mihansen
ms.openlocfilehash: d7156543a66cdf50d7cfddec27e685429324f9e6
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "84819998"
---
# <a name="quickstart-deploy-azure-api-for-fhir-using-powershell"></a>Avvio rapido: Distribuire l'API di Azure per FHIR con PowerShell

Questa guida di avvio rapido illustra come distribuire l'API di Azure per FHIR con PowerShell.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="register-the-azure-api-for-fhir-resource-provider"></a>Registrare il provider di risorse dell'API di Azure per FHIR

Se il provider di risorse `Microsoft.HealthcareApis` non è ancora stato registrato per la sottoscrizione, è possibile registrarlo con le operazioni seguenti:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.HealthcareApis
```

## <a name="create-azure-resource-group"></a>Creare un gruppo di risorse di Azure

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroupName" -Location westus2
```

## <a name="deploy-azure-api-for-fhir"></a>Distribuire l'API di Azure per FHIR

```azurepowershell-interactive
New-AzHealthcareApisService -Name nameoffhirservice -ResourceGroupName myResourceGroupName -Location westus2 -Kind fhir-R4
```

> [!NOTE]
> A seconda della versione del modulo `Az` PowerShell installato, è possibile configurare il server FHIR di cui è stato effettuato il provisioning per l'uso del [controllo degli accessi in base al ruolo locale](configure-local-rbac.md) e impostare l'utente di PowerShell attualmente connesso nell'elenco degli ID oggetto identità consentiti per il servizio FHIR distribuito. Per il futuro, è consigliabile usare il [controllo degli accessi in base al ruolo di Azure](configure-azure-rbac.md) per l'assegnazione di ruoli del piano dati e potrebbe essere necessario eliminare questo ID oggetto dell'utente dopo la distribuzione per abilitare la modalità di controllo degli accessi in base al ruolo di Azure.


## <a name="fetch-capability-statement"></a>Recuperare la dichiarazione di funzionalità

Sarà possibile verificare che l'account API di Azure per FHIR sia in esecuzione recuperando una dichiarazione di funzionalità di FHIR:

```azurepowershell-interactive
$metadata = Invoke-WebRequest -Uri "https://nameoffhirservice.azurehealthcareapis.com/metadata"
$metadata.RawContent
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare questa applicazione, eliminare il gruppo di risorse seguendo questa procedura:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupName
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stata distribuita l'API di Azure per FHIR nella sottoscrizione. Per configurare le impostazioni aggiuntive nell'API di Azure per FHIR, passare alla guida pratica sulle impostazioni aggiuntive.

>[!div class="nextstepaction"]
>[Impostazioni aggiuntive nell'API di Azure per FHIR](azure-api-for-fhir-additional-settings.md)
