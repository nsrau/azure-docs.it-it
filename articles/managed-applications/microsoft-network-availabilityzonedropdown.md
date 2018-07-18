---
title: Elemento AvailabilityZoneDropdown dell'interfaccia utente di Azure | Microsoft Docs
description: Descrive l'elemento Microsoft.Network.AvailabilityZoneDropdown dell'interfaccia utente per il portale di Azure.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 1698bae19ad8ec5c3076db1489ac513e304a4e13
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2018
ms.locfileid: "37108740"
---
# <a name="microsoftnetworkavailabilityzonedropdown-ui-element"></a>Elemento Microsoft.Network.AvailabilityZoneDropdown dell'interfaccia utente
Un controllo per la selezione della zona di disponibilità.

## <a name="ui-sample"></a>Esempio di interfaccia utente
![Microsoft.Network.AvailabilityZoneDropDown](./media/managed-application-elements/microsoft.network.availabilityzonedropdown.png)

## <a name="schema"></a>SCHEMA
```json
{
  "name": "element1",
  "type": "Microsoft.Network.AvailabilityZoneDropdown",
  "label": "Availability zone dropdown label",
  "toolTip": "This is the tooltip for availability zones",
  "defaultValue": "None",
  "constraints": {
    "required": "true"
  },
  "options": {
  },
  "visible": true
}
```

## <a name="next-steps"></a>Passaggi successivi
* Per un'introduzione alla creazione delle definizioni dell'interfaccia utente, vedere [Introduzione a CreateUiDefinition](create-uidefinition-overview.md).
* Per una descrizione delle proprietà comuni negli elementi dell'interfaccia utente, vedere [Elementi di CreateUiDefinition](create-uidefinition-elements.md).
