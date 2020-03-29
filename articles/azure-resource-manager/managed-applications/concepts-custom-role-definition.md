---
title: Panoramica delle definizioni di ruolo personalizzate
description: Viene descritto il concetto di creazione di definizioni di ruolo personalizzate per le applicazioni gestite.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 09/16/2019
ms.openlocfilehash: 88e42fd9626276f6c77b46b33c138407f91d06ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650760"
---
# <a name="custom-role-definition-artifact-in-azure-managed-applications"></a>Elemento di definizione dei ruoli personalizzato nelle applicazioni gestite di AzureCustom role definition artifact in Azure Managed Applications

La definizione di ruolo personalizzata è un elemento facoltativo nelle applicazioni gestite. Viene utilizzato per determinare le autorizzazioni necessarie all'applicazione gestita per eseguire le funzioni.

In questo articolo viene fornita una panoramica dell'elemento di definizione dei ruoli personalizzato e delle relative funzionalità.

## <a name="custom-role-definition-artifact"></a>Elemento di definizione dei ruoli personalizzati

È necessario denominare l'elemento di definizione del ruolo personalizzato customRoleDefinition.json.You need to name the custom role definition artifact customRoleDefinition.json. Posizionarlo allo stesso livello di createUiDefinition.json e mainTemplate.json nel pacchetto .zip che crea una definizione di applicazione gestita. Per informazioni su come creare il pacchetto .zip e pubblicare una definizione di applicazione gestita, vedere [Pubblicare una definizione di applicazione gestita.](publish-managed-app-definition-quickstart.md)

## <a name="custom-role-definition-schema"></a>Schema di definizione del ruolo personalizzato

Il file customRoleDefinition.json ha `roles` una proprietà di primo livello che è una matrice di ruoli. Questi ruoli sono le autorizzazioni necessarie per il funzionamento dell'applicazione gestita. Attualmente sono consentiti solo i ruoli predefiniti, ma è possibile specificare più ruoli. A un ruolo può essere fatto riferimento dall'ID della definizione del ruolo o dal nome del ruolo.

JSON di esempio per la definizione di ruolo personalizzato:Sample JSON for custom role definition:

```json
{
    "contentVersion": "0.0.0.1",
    "roles": [
        {
            "properties": {
                "roleName": "Contributor"
            }
        },
        {
            "id": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
        },
        {
            "id": "/providers/Microsoft.Authorization/roledefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
        }
    ]
}
```

## <a name="roles"></a>Ruoli

Un ruolo è composto `$.properties.roleName` da `id`un:

```json
{
    "id": null,
    "properties": {
        "roleName": "Contributor"
    }
}
```

> [!NOTE]
> È possibile utilizzare `id` `roleName` il campo o . Ne è richiesto solo uno. Questi campi vengono utilizzati per cercare la definizione del ruolo da applicare. Se vengono forniti entrambi, verrà utilizzato il `id` campo.

|Proprietà|Obbligatorio?|Descrizione|
|---------|---------|---------|
|id|Sì|ID del ruolo predefinito. È possibile utilizzare l'ID completo o solo il GUID.|
|Rolename|Sì|Nome del ruolo predefinito.|