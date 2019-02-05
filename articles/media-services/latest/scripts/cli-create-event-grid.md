---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Creare una sottoscrizione di Griglia di eventi di Azure | Microsoft Docs
description: Lo script dell'interfaccia della riga di comando di Azure in questo argomento mostra come creare una sottoscrizione di Griglia di eventi di Azure a livello di account per modifiche dello stato dei processi.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/25/2019
ms.author: juliako
ms.openlocfilehash: 2fcacd559db0ab143ffa7ed49bbd546785765154
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2019
ms.locfileid: "55098906"
---
# <a name="cli-example-create-an-azure-event-grid-subscription"></a>Esempio di interfaccia della riga di comando: Creare una sottoscrizione di Griglia di eventi di Azure 

Lo script dell'interfaccia della riga di comando di Azure in questo articolo mostra come creare una sottoscrizione di Griglia di eventi di Azure a livello di account per modifiche dello stato dei processi.

## <a name="prerequisites"></a>Prerequisiti 

[Creare un account di Servizi multimediali di Azure](../create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions.md](../../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/create-event-grid/Create-EventGrid.sh "Create an EventGrid subscription")]

## <a name="next-steps"></a>Passaggi successivi

Per altri esempi, vedere [Azure CLI samples](../cli-samples.md) (Esempi dell'interfaccia della riga di comando di Azure).
