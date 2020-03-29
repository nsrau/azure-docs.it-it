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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "75371778"
---
Una firma di accesso condiviso consente di concedere un accesso limitato ai contenitori e ai BLOB nell'account di archiviazione. Quando si crea una firma di accesso locale, si specificano i vincoli, incluse le risorse di Archiviazione di Azure a cui un client può accedere, le autorizzazioni di cui dispongono per tali risorse e la durata della firma di accesso locale.

Ogni firma di accesso base è firmata con una chiave. È possibile firmare una firma di accesso base in uno dei due modi seguenti:You can sign a SAS in one of two ways:

- Con una chiave creata con le credenziali di Azure Active Directory (Azure AD). Una firma di accesso condiviso firmata con le credenziali di Azure AD è una firma di accesso condiviso *di delega utente.*
- Con la chiave dell'account di archiviazione. Sia una *firma di* accesso livello accesso livello del servizio che una firma di accesso livello *dell'account* sono firmate con la chiave dell'account di archiviazione.

Una firma di accesso sas di delega degli utenti offre una sicurezza superiore a una firma di accesso livello firmata con la chiave dell'account di archiviazione. Microsoft consiglia di usare una sAS di delega utente quando possibile. Per ulteriori informazioni, vedere [Concedere un accesso limitato ai dati con firme](../articles/storage/common/storage-sas-overview.md)di accesso condiviso.
