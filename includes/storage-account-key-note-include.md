---
title: File di inclusione
description: File di inclusione
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 3cfdca99c91dc54a711801d92aa0da91fb9703e4
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66115497"
---
> [!IMPORTANT]
> La chiave dell’account di archiviazione è simile alla password radice per l'account di archiviazione. Proteggere sempre con cura la chiave dell'account. Evitare di distribuirla ad altri utenti, impostarla come hardcoded o salvarla in un file di testo normale accessibile ad altri. Rigenerare la chiave dell'account tramite il portale di Azure se si ritiene che possa essere stata compromessa.
> 
> È fondamentale proteggere i token di firma di accesso condiviso, proprio come le chiavi di accesso degli account. Oltre a offrire granularità, la firma di accesso condiviso concede ai client l'accesso alle risorse nel proprio account di archiviazione e non deve essere condivisa pubblicamente. Quando la condivisione è necessaria per risolvere eventuali problemi, è consigliabile usare una versione dei file di log con alcune modifiche o eliminare i token di firma di accesso condiviso (se presenti) dai file di log e assicurarsi che nemmeno gli screenshot contengano informazioni relative alla firma di accesso condiviso.
> 
> Microsoft consiglia di usare il più possibile l'autenticazione di Azure Active Directory (Azure AD) per le applicazioni di archiviazione BLOB e code (anteprima) per ottimizzare la sicurezza. Per altre informazioni, vedere [Autenticare l'accesso ai BLOB e alle code di Azure usando Azure Active Directory (anteprima)](https://docs.microsoft.com/azure/storage/common/storage-auth-aad).
