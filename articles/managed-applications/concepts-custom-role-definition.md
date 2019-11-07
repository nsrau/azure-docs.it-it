---
title: Panoramica delle definizioni di ruolo personalizzate nelle applicazioni gestite di Azure | Microsoft Docs
description: Viene descritto il concetto di creazione di definizioni di ruolo personalizzate per le applicazioni gestite.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 09/16/2019
ms.openlocfilehash: d645eebefde473e404f7760d2bc8a67c7e3e9087
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73609034"
---
# <a name="custom-role-definition-artifact-in-azure-managed-applications"></a>Elemento di definizione del ruolo personalizzato nelle applicazioni gestite di Azure

La definizione di ruolo personalizzata è un elemento facoltativo nelle applicazioni gestite. Viene utilizzato per determinare le autorizzazioni necessarie all'applicazione gestita per eseguire le relative funzioni.

Questo articolo fornisce una panoramica dell'artefatto di definizione dei ruoli personalizzati e delle relative funzionalità.

## <a name="custom-role-definition-artifact"></a>Artefatto di definizione del ruolo personalizzato

L'artefatto di definizione del ruolo personalizzato deve essere denominato **customRoleDefinition. JSON** e inserito allo stesso livello di **createUiDefinition. JSON** e **mainTemplate. JSON** nel pacchetto zip che consente di creare una definizione di applicazione gestita. Per informazioni su come creare il pacchetto zip e pubblicare una definizione di applicazione gestita, vedere [pubblicare una definizione di applicazione gestita.](publish-managed-app-definition-quickstart.md)

## <a name="custom-role-definition-schema"></a>Schema di definizione del ruolo personalizzato

Il file **customRoleDefinition. JSON** ha una proprietà `roles` di primo livello, che è una matrice di ruoli. Ognuno di questi ruoli è costituito dalle autorizzazioni necessarie per il funzionamento dell'applicazione gestita. Attualmente sono consentiti solo i ruoli predefiniti, ma è possibile specificare più ruoli. È possibile fare riferimento al ruolo mediante l'ID della definizione di ruolo o in base al nome del ruolo.

Esempio di JSON per la definizione di ruolo personalizzata:

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

## <a name="role"></a>Ruolo

Un ruolo è costituito da un `$.properties.roleName` o `id`.

```json
{
    "id": null,
    "properties": {
        "roleName": "Contributor"
    }
}
```

> [!Note]
> È necessario solo uno dei campi `id` o `roleName`. Questi campi vengono usati per cercare la definizione di ruolo da applicare. Se vengono specificati entrambi, verrà usato il campo `id`.

|Proprietà|Obbligatorio|Descrizione|
|---------|---------|---------|
|ID|*sì*|ID del ruolo predefinito. Questa proprietà può essere l'ID completo o solo il GUID.|
|RoleName|*sì*|Nome del ruolo predefinito.|