---
title: Errori relativi al nome della risorsa riservata di Azure | Microsoft Docs
description: Descrive come risolvere gli errori quando si fornisce un nome di risorsa che include una parola riservata.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/08/2017
ms.author: tomfitz
ms.openlocfilehash: 922389b7c6c1bb7ad1d9b8f6ec35ccc1c5656723
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60390852"
---
# <a name="resolve-reserved-resource-name-errors"></a>Risolvere gli errori relativi ai nomi riservati delle risorse

Questo articolo descrive l'errore generato quando si distribuisce una risorsa, il cui nome include una parola riservata.

## <a name="symptom"></a>Sintomo

Quando si distribuisce una risorsa disponibile tramite un endpoint pubblico, potrebbe essere visualizzato l'errore seguente:

```
Code=ReservedResourceName;
Message=The resource name <resource-name> or a part of the name is a trademarked or reserved word.
```

## <a name="cause"></a>Causa

Il nome delle risorse con un endpoint pubblico non possono includere parole riservate o marchi registrati.

Le parole seguenti sono riservate:

* ACCESS
* AZURE
* BING
* BIZSPARK
* BIZTALK
* CORTANA
* DIRECTX
* DOTNET
* DYNAMICS
* EXCEL
* EXCHANGE
* FOREFRONT
* GROOVE
* HOLOLENS
* HYPERV
* KINECT
* LYNC
* MSDN
* O365
* OFFICE
* OFFICE365
* ONEDRIVE
* ONENOTE
* OUTLOOK
* POWERPOINT
* SHAREPOINT
* SKYPE
* VISIO
* VISUALSTUDIO

Non è possibile usare le parole seguenti come parola intera o sottostringa nel nome:

* LOGIN
* MICROSOFT
* WINDOWS
* XBOX

## <a name="solution"></a>Soluzione

Specificare un nome che non includa una delle parole riservate.