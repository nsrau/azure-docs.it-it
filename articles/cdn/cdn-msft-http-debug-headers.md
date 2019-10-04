---
title: Eseguire il debug di intestazioni HTTP per la rete CDN di Azure da Microsoft | Microsoft Docs
description: Le intestazioni della richiesta di debug della cache forniscono informazioni aggiuntive sui criteri della cache applicati all'asset richiesto. Queste intestazioni sono specifiche per la rete CDN di Azure di Microsoft.
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
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68814065"
---
# <a name="debug-http-header-for-azure-cdn-from-microsoft"></a>Eseguire il debug dell'intestazione HTTP per la rete CDN di Azure da Microsoft
L'intestazione della risposta di `X-Cache`debug,, fornisce informazioni dettagliate sul livello dello stack della rete CDN dal quale è stato servito il contenuto. Questa intestazione è specifica della rete CDN di Azure di Microsoft.

### <a name="response-header-format"></a>Formato dell'intestazione della risposta

Intestazione | Descrizione
-------|------------
X-cache: TCP_HIT | Questa intestazione viene restituita quando il contenuto viene servito dalla cache perimetrale della rete CDN. 
X-cache: TCP_REMOTE_HIT | Questa intestazione viene restituita quando il contenuto viene servito dalla cache regionale della rete CDN (livello di protezione dell'origine)
X-cache: TCP_MISS | Questa intestazione viene restituita quando si verifica un mancato riscontro nella cache e il contenuto viene servito dall'origine. 


