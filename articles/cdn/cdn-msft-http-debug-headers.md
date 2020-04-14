---
title: Eseguire il debug delle intestazioni HTTP per la rete CDN di Azure da Microsoft . Documenti Microsoft
description: Le intestazioni di richiesta della cache di debug forniscono informazioni aggiuntive sui criteri di cache applicati all'asset richiesto. Queste intestazioni sono specifiche della rete CDN di Azure di Microsoft.These headers are specific to Azure CDN from Microsoft.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2019
ms.author: allensu
ms.openlocfilehash: 2475bdce3ab8f153cc837601964bf4a2e90a470c
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260412"
---
# <a name="debug-http-header-for-azure-cdn-from-microsoft"></a>Debug HTTP header for Azure CDN from Microsoft
L'intestazione `X-Cache`della risposta di debug, , fornisce dettagli sul livello dello stack CDN da cui è stato servito il contenuto. Questa intestazione è specifica della rete CDN di Azure di Microsoft.This header is specific to Azure CDN from Microsoft.

### <a name="response-header-format"></a>Formato dell'intestazione della risposta

Intestazione | Descrizione
-------|------------
X-Cache: TCP_HIT | Questa intestazione viene restituita quando il contenuto viene servito dalla cache perimetrale della rete CDN. 
X-Cache: TCP_REMOTE_HIT | Questa intestazione viene restituita quando il contenuto viene servito dalla cache regionale della rete CDN (livello di protezione dell'origine)This header is returned when the content is served from the CDN regional cache (Origin shield layer)
X-Cache: TCP_MISS | Questa intestazione viene restituita quando si verifica un mancato riscontro nella cache e il contenuto viene servito dall'origine. 


