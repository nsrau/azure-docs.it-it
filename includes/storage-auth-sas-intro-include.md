---
title: File di inclusione
description: File di inclusione
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/20/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: aec1faa4de1149f08fb6fbc1cc5bf3aa2ab6becd
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75371778"
---
Una firma di accesso condiviso consente di concedere l'accesso limitato a contenitori e BLOB nell'account di archiviazione. Quando si crea una firma di accesso condiviso, si specificano i relativi vincoli, incluse le risorse di archiviazione di Azure a cui un client può accedere, le autorizzazioni per tali risorse e il tempo di validità della firma di accesso condiviso.

Ogni firma di accesso condiviso è firmata con una chiave. È possibile firmare una firma di accesso condiviso in uno dei due modi seguenti:

- Con una chiave creata con le credenziali Azure Active Directory (Azure AD). Una firma di accesso condiviso con Azure AD credenziali è una firma di accesso condiviso *dell'utente* .
- Con la chiave dell'account di archiviazione. Una firma di accesso condiviso del *servizio* e una firma di accesso condiviso dell' *account* sono firmate con la chiave account di archiviazione

Una firma di accesso condiviso di delega utente offre una protezione superiore a una firma di accesso condiviso con la chiave dell'account di archiviazione. Quando possibile, Microsoft consiglia di usare una firma di accesso condiviso di delega utente. Per altre informazioni, vedere [concedere l'accesso limitato ai dati con le firme di accesso condiviso (SAS)](../articles/storage/common/storage-sas-overview.md).
