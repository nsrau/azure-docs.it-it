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
ms.openlocfilehash: f3b9f6c27fb8d423350eac5d286c9859ad6fbd37
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70104265"
---
## <a name="about-the-user-delegation-sas-preview"></a>Informazioni sulla firma di accesso condiviso della delega utente (anteprima)

Un token di firma di accesso condiviso per l'accesso a un contenitore o a un BLOB può essere protetto tramite Azure AD credenziali o una chiave dell'account. Una firma di accesso condiviso protetta con credenziali Azure AD viene chiamata firma di accesso condiviso dell'utente, perché il token OAuth 2,0 usato per firmare la firma di accesso condiviso viene richiesto per conto dell'utente.

Microsoft consiglia di utilizzare le credenziali di Azure AD quando possibile come procedura di sicurezza consigliata, anziché utilizzare la chiave dell'account, che può essere compromessa in modo più semplice. Quando la progettazione dell'applicazione richiede firme di accesso condiviso, usare le credenziali Azure AD per creare una firma di accesso condiviso di delega utente per la protezione superiore. Per altre informazioni sulla firma di accesso condiviso della delega utente, vedere [creare una firma di accesso condiviso dell'utente](/rest/api/storageservices/create-user-delegation-sas).

> [!NOTE]
> L'anteprima della firma di accesso condiviso della delega utente è destinata solo all'uso non in produzione.

> [!CAUTION]
> Qualsiasi client che dispone di una firma di accesso condiviso valida può accedere ai dati nell'account di archiviazione come consentito dalla firma di accesso condiviso. È importante proteggere una firma di accesso condiviso da un utilizzo dannoso o imprevisto. Usare la discrezione per la distribuzione di una firma di accesso condiviso e un piano per la revoca di una firma di accesso condiviso compromessa.

Per altre informazioni sulle firme di accesso condiviso, vedere [concedere l'accesso limitato alle risorse di archiviazione di Azure usando le firme di accesso condiviso (SAS)](../articles/storage/common/storage-sas-overview.md).
