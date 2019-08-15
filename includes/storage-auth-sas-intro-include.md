---
title: File di inclusione
description: File di inclusione
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/25/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 3af8077627d56ce44c5e2959e2c79b967b09d9e5
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "69011949"
---
Una firma di accesso condiviso consente di concedere l'accesso limitato a contenitori e BLOB nell'account di archiviazione. Quando si crea una firma di accesso condiviso, si specificano i relativi vincoli, incluse le risorse di archiviazione di Azure a cui un client può accedere, le autorizzazioni per tali risorse e il tempo di validità della firma di accesso condiviso.

Ogni firma di accesso condiviso è firmata con una chiave. È possibile firmare una firma di accesso condiviso in uno dei due modi seguenti:

- Con una chiave creata con le credenziali Azure Active Directory (Azure AD). Una firma di accesso condiviso con Azure AD credenziali è una firma di accesso condiviso *dell'utente* .
- Con la chiave dell'account di archiviazione. Una firma di accesso condiviso del *servizio* e una firma di accesso condiviso dell' *account* sono firmate con la chiave account di archiviazione
