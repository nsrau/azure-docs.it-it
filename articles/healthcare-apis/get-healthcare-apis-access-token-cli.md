---
title: Ottenere il token di accesso usando l'interfaccia della riga di comando di Azure per FHIR
description: Questo articolo illustra come ottenere un token di accesso per l'API di Azure per FHIR usando l'interfaccia della riga di comando di Azure.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: matjazl
ms.openlocfilehash: 4d1c4cfcb15d97a2c54a04344f0bd098f65c1392
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660373"
---
# <a name="get-access-token-for-azure-api-for-fhir-using-azure-cli"></a>Ottenere il token di accesso per l'API Azure per FHIR usando l'interfaccia della riga di comando

In questo articolo si apprenderà come ottenere un token di accesso per l'API di Azure per FHIR usando l'interfaccia della riga di comando di Azure. Quando si esegue [il provisioning dell'API di Azure per FHIR](fhir-paas-portal-quickstart.md), si configura un set di utenti o entità servizio che possono accedere al servizio. Se l'ID oggetto utente è nell'elenco degli ID oggetto consentiti, è possibile accedere al servizio usando un token ottenuto usando l'interfaccia della riga di comando di Azure.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="obtain-a-token"></a>Ottenere un token

L'API di Azure per FHIR usa un oggetto `resource`  o `Audience` con URI uguale all'URI del server FHIR `https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com` . È possibile ottenere un token e archiviarlo in una variabile (denominata `$token` ) con il comando seguente:

```azurecli-interactive
token=$(az account get-access-token --resource=https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com --query accessToken --output tsv)
```

## <a name="use-with-azure-api-for-fhir"></a>Usare con l'API di Azure per FHIR

```azurecli-interactive
curl -X GET --header "Authorization: Bearer $token" https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com/Patient
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come ottenere un token di accesso per l'API di Azure per FHIR usando l'interfaccia della riga di comando di Azure. Per informazioni su come accedere all'API FHIR tramite Postman, passare all'esercitazione su Postman.

>[!div class="nextstepaction"]
>[Accedere all'API FHIR con Postman](access-fhir-postman-tutorial.md)
