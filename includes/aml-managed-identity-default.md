---
title: includere file
description: includere file
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/24/2020
ms.openlocfilehash: aa8aea66c5481454f0d7d4d118934f5fbf34a911
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89147472"
---
L' **identità gestita predefinita** è l'identità gestita assegnata dal sistema o la prima identità gestita assegnata dall'utente.

Durante un'esecuzione sono presenti due applicazioni di un'identità:

1. Il sistema usa un'identità per configurare i montaggi di archiviazione, il registro contenitori e gli archivi dati dell'utente.

    * In questo caso, il sistema utilizzerà l'identità gestita automaticamente.

1. L'utente applica un'identità per accedere alle risorse dall'interno del codice per un'esecuzione inviata

    * In questo caso, specificare il *client_id* corrispondente all'identità gestita che si vuole usare per recuperare le credenziali.
    * In alternativa, ottenere l'ID client dell'identità assegnata dall'utente tramite la variabile di ambiente *DEFAULT_IDENTITY_CLIENT_ID* .

    Ad esempio, per recuperare un token per un archivio dati con l'identità gestita predefinita:

    ```python
    client_id = os.environ.get('DEFAULT_IDENTITY_CLIENT_ID')
    credential = ManagedIdentityCredential(client_id=client_id)
    token = credential.get_token('https://storage.azure.com/')
    ```