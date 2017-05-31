---
title: Elemento VirtualNetworkCombo dell&quot;interfaccia utente di Applicazione gestita di Azure | Microsoft Docs
description: Illustra l&quot;elemento Microsoft.Network.VirtualNetworkCombo dell&quot;interfaccia utente per le applicazioni gestite di Azure
services: azure-resource-manager
documentationcenter: na
author: tabrezm
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/12/2017
ms.author: tabrezm;tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: 8bb255b76ac5c3de570fa569a1cfb3ee953f9687
ms.contentlocale: it-it
ms.lasthandoff: 05/12/2017


---
# <a name="microsoftnetworkvirtualnetworkcombo-ui-element"></a>Elemento Microsoft.Network.VirtualNetworkCombo dell'interfaccia utente
Gruppo di controlli per la selezione di una rete virtuale nuova o esistente. Usare questo elemento quando si [crea un'applicazione Azure gestita](managed-application-publishing.md).

## <a name="ui-sample"></a>Esempio di interfaccia utente
![Microsoft.Network.VirtualNetworkCombo](./media/managed-application-elements/microsoft.network.virtualnetworkcombo.png)

- Nel wireframe in alto l'utente ha selezionato una nuova rete virtuale, quindi può personalizzare il nome e il prefisso dell'indirizzo di ogni subnet. In questo caso la configurazione delle subnet è facoltativa.
- Nel wireframe in basso l'utente ha selezionato una rete virtuale esistente, quindi deve eseguire il mapping di ogni subnet necessaria per il modello di distribuzione a una subnet esistente. In questo caso la configurazione delle subnet è obbligatoria.

## <a name="schema"></a>Schema
```json
{
  "name": "element1",
  "type": "Microsoft.Network.VirtualNetworkCombo",
  "label": {
    "virtualNetwork": "Virtual network",
    "subnets": "Subnets"
  },
  "toolTip": {
    "virtualNetwork": "",
    "subnets": ""
  },
  "defaultValue": {
    "name": "vnet01",
    "addressPrefixSize": "/16"
  },
  "constraints": {
    "minAddressPrefixSize": "/16"
  },
  "options": {
    "hideExisting": false
  },
  "subnets": {
    "subnet1": {
      "label": "First subnet",
      "defaultValue": {
        "name": "subnet-1",
        "addressPrefixSize": "/24"
      },
      "constraints": {
        "minAddressPrefixSize": "/24",
        "minAddressCount": 12,
        "requireContiguousAddresses": true
      }
    },
    "subnet2": {
      "label": "Second subnet",
      "defaultValue": {
        "name": "subnet-2",
        "addressPrefixSize": "/26"
      },
      "constraints": {
        "minAddressPrefixSize": "/26",
        "minAddressCount": 8,
        "requireContiguousAddresses": true
      }
    }
  },
  "visible": true
}
```

## <a name="remarks"></a>Osservazioni
- Se specificato, il primo prefisso di indirizzo non sovrapposto di dimensioni pari a `defaultValue.addressPrefixSize` viene determinato automaticamente in base alle reti virtuali esistenti nella sottoscrizione dell'utente.
- Il valore predefinito per `defaultValue.name` e `defaultValue.addressPrefixSize` è **null**.
- Specificare `constraints.minAddressPrefixSize`. Le reti virtuali esistenti con uno spazio indirizzi inferiore al valore specificato non sono disponibili per la selezione.
- Specificare `subnets` e specificare `constraints.minAddressPrefixSize` per ogni subnet.
- Quando si crea una nuova rete virtuale, il prefisso dell'indirizzo di ogni subnet viene calcolato automaticamente in base al prefisso dell'indirizzo della rete virtuale e al rispettivo `addressPrefixSize`.
- Quando si usa una rete virtuale esistente, le subnet di dimensioni inferiori al rispettivo `constraints.minAddressPrefixSize` non sono disponibili per la selezione. Inoltre, se specificato, le subnet che non contengono almeno `minAddressCount` indirizzi disponibili non sono disponibili per la selezione.
Il valore predefinito è **0**. Per assicurarsi che gli indirizzi disponibili siano contigui, specificare **true** per `requireContiguousAddresses`. Il valore predefinito è **true**.
- La creazione di subnet in una rete virtuale esistente non è supportata.
- Se `options.hideExisting` è **true**, l'utente non può scegliere una rete virtuale esistente. Il valore predefinito è **false**.

## <a name="sample-output"></a>Output di esempio
```json
{
  "name": "vnet01",
  "resourceGroup": "rg01",
  "addressPrefixes": ["10.0.0.0/16"],
  "newOrExisting": "new",
  "subnets": {
    "subnet1": {
      "name": "subnet-1",
      "addressPrefix": "10.0.0.0/24",
      "startAddress": "10.0.0.1"
    },
    "subnet2": {
      "name": "subnet-2",
      "addressPrefix": "10.0.1.0/26",
      "startAddress": "10.0.1.1"
    }
  }
}
```

## <a name="next-steps"></a>Passaggi successivi
* Per un'introduzione alle applicazioni gestite, vedere [Panoramica di Applicazione gestita di Azure](managed-application-overview.md).
* Per un'introduzione alla creazione delle definizioni dell'interfaccia utente, vedere [Introduzione a CreateUiDefinition](managed-application-createuidefinition-overview.md).
* Per una descrizione delle proprietà comuni negli elementi dell'interfaccia utente, vedere [Elementi di CreateUiDefinition](managed-application-createuidefinition-elements.md).

