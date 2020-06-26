---
title: 'PowerShell: Distribuire il server FHIR per Azure - API di Azure per FHIR'
description: Questa guida di avvio rapido illustra come distribuire il server FHIR open source Microsoft con PowerShell.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: f959b884f67f354599b99bb6dd24918b28d13382
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "84819888"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-powershell"></a>Avvio rapido: Distribuire il server FHIR open source con PowerShell

Questa guida di avvio rapido illustra come distribuire il server Microsoft FHIR open source per Azure con PowerShell.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Scegliere un nome per il gruppo di risorse che conterrà le risorse di cui è stato effettuato il provisioning e crearlo:

```azurepowershell-interactive
$fhirServiceName = "MyFhirService"
$rg = New-AzResourceGroup -Name $fhirServiceName -Location westus2
```

## <a name="deploy-the-fhir-server-template"></a>Distribuire il modello del server FHIR

Il [repository di GitHub](https://github.com/Microsoft/fhir-server) per il server Microsoft FHIR per Azure contiene un modello che distribuirà tutte le risorse necessarie. Distribuirlo con:

```azurepowershell-interactive
New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Microsoft/fhir-server/master/samples/templates/default-azuredeploy.json -ResourceGroupName $rg.ResourceGroupName -serviceName $fhirServiceName
```

## <a name="verify-fhir-server-is-running"></a>Verificare che il server FHIR sia in esecuzione

```azurepowershell-interactive
$metadataUrl = "https://" + $fhirServiceName + ".azurewebsites.net/metadata" 
$metadata = Invoke-WebRequest -Uri $metadataUrl
$metadata.RawContent
```

La prima volta, il server impiegherà circa un minuto per rispondere.

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare questa applicazione, eliminare il gruppo di risorse seguendo questa procedura:

```azurepowershell-interactive
Remove-AzResourceGroup -Name $rg.ResourceGroupName
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato distribuito il server FHIR open source Microsoft per Azure nella sottoscrizione. Per informazioni su come accedere all'API FHIR tramite Postman, passare all'esercitazione su Postman.
 
>[!div class="nextstepaction"]
>[Accedere all'API FHIR con Postman](access-fhir-postman-tutorial.md)
