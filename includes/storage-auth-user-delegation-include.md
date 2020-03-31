---
title: File di inclusione
description: File di inclusione
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/15/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: a0a4a10797109cb3363027e2445259d06d9aa071
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "76118088"
---
## <a name="about-the-user-delegation-sas"></a>Informazioni sulla delega della server di accesso sAS degli utenti

Un token di firma di accesso condiviso per l'accesso a un contenitore o a un BLOB può essere protetto usando le credenziali di Azure AD o una chiave dell'account. Una firma di accesso condiviso con credenziali di Azure AD viene definita firma di accesso condiviso di delega utente, perché il token OAuth 2.0 usato per firmare la firma di accesso condiviso viene richiesto per conto dell'utente.

Microsoft consiglia di usare le credenziali di Azure AD quando possibile come procedura consigliata per la sicurezza, anziché usare la chiave dell'account, che può essere compromessa più facilmente. Quando la progettazione dell'applicazione richiede firme di accesso condiviso, usare le credenziali di Azure AD per creare una firma di accesso condiviso di delega utente per una sicurezza di livello superiore. Per ulteriori informazioni sulla delega utente SAS, vedere [Creare una sAS](/rest/api/storageservices/create-user-delegation-sas)di delega utente .

> [!CAUTION]
> Qualsiasi client che possiede una firma di accesso sas valida può accedere ai dati nell'account di archiviazione come consentito dalla firma di accesso locale. È importante proteggere una sAS da utilizzi dannosi o non intenzionali. Usare la discrezionalità nella distribuzione di una sAS e disporre di un piano per la revoca di una classe Di sAS compromessa.

Per altre informazioni sulle firme di accesso condiviso, vedere [Concedere l'accesso limitato alle risorse di Archiviazione di Azure usando le firme](../articles/storage/common/storage-sas-overview.md)di accesso condiviso.
