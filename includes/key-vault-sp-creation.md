---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: c6a9f17d46ef8feb571c0ecc7a0a93a169f74725
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87285552"
---
Il modo più semplice per autenticare un'applicazione Python basata sul cloud consiste nell'usare un'identità gestita. Per informazioni dettagliate, vedere [Usare un'identità gestita del servizio app per accedere ad Azure Key Vault](/azure/key-vault/general/managed-identity). 

Per semplicità, tuttavia, in questo avvio rapido viene creata un'applicazione desktop, per la quale sono richiesti un'entità servizio e un criterio di controllo di accesso. L'entità servizio richiede un nome univoco nel formato "http://&lt;nome-univoco-entità-servizio&gt;".

Creare un'entità servizio usando il comando [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) dell'interfaccia della riga di comando di Azure:

```azurecli
az ad sp create-for-rbac -n "http://<my-unique-service-principal-name>" --sdk-auth
```

Questa operazione restituisce una serie di coppie chiave-valore.

```console
{
  "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
  "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
  "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
  "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```
