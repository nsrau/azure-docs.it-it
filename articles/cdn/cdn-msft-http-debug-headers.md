---
title: Eseguire il debug delle intestazioni HTTP per la rete CDN di Azure da Microsoft . Documenti Microsoft
description: Le intestazioni di richiesta della cache di debug forniscono informazioni aggiuntive sui criteri di cache applicati all'asset richiesto. Queste intestazioni sono specifiche della rete CDN di Azure di Microsoft.These headers are specific to Azure CDN from Microsoft.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2019
ms.author: magattus
ms.openlocfilehash: 297c65c1cd89163b8663819f844dc6c2a83fd1bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68814065"
---
# <a name="debug-http-header-for-azure-cdn-from-microsoft"></a>Debug HTTP header for Azure CDN from Microsoft
L'intestazione `X-Cache`della risposta di debug, , fornisce dettagli sul livello dello stack CDN da cui è stato servito il contenuto. Questa intestazione è specifica della rete CDN di Azure di Microsoft.This header is specific to Azure CDN from Microsoft.

### <a name="response-header-format"></a>Formato dell'intestazione della risposta

Intestazione | Descrizione
-------|------------
X-Cache: TCP_HIT | Questa intestazione viene restituita quando il contenuto viene servito dalla cache perimetrale della rete CDN. 
X-Cache: TCP_REMOTE_HIT | Questa intestazione viene restituita quando il contenuto viene servito dalla cache regionale della rete CDN (livello di protezione dell'origine)This header is returned when the content is served from the CDN regional cache (Origin shield layer)
X-Cache: TCP_MISS | Questa intestazione viene restituita quando si verifica un mancato riscontro nella cache e il contenuto viene servito dall'origine. 


