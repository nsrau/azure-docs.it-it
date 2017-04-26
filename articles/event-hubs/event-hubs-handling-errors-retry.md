---
title: Procedure consigliate per la gestione degli errori in Hub eventi di Azure | Microsoft Docs
description: Gestione degli errori e ripetizione in Hub di eventi di Azure
services: event-hubs
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/09/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 200bc0828574bca72739dea5badb22da129cd896
ms.lasthandoff: 04/12/2017

---

# <a name="-event-hubs-best-practices-for-handling-errors-and-retry"></a>🔧 Procedure consigliate per la gestione degli errori e la ripetizione in Hub eventi

> [!NOTE]
> 
> Questo argomento non è stato ancora scritto 
>
> Sono graditi suggerimenti per l'approfondimento di questi contenuti. È possibile tenere traccia dello stato e fornire suggerimenti su questo [problema in GitHub](https://github.com/Azure/azure-event-hubs/issues/305).
> 
> Altre informazioni su come è possibile fornire contributi in [GitHub](https://github.com/Microsoft/azure-docs/blob/master/contributor-guide/contributor-guide-index.md).

Questo articolo illustrerà quanto segue:

- Quali sono i diversi tipi di errori presenti?
- Cosa accade quando si verifica un errore?
- Con quali tipi di errori si deve riprovare? Con quali non si deve riprovare?
- Criteri di ripetizione personalizzati

