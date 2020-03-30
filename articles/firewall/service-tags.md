---
title: Panoramica dei tag del servizio Firewall di Azure
description: Un tag di servizio rappresenta un gruppo di prefissi di indirizzo IP che consente di ridurre al minimo la complessità nella creazione di regole di sicurezza.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: ec9fb3c9b27d21ac65888379f59d51ecc8042eb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74168681"
---
# <a name="azure-firewall-service-tags"></a>Tag del servizio Firewall di Azure

Un tag di servizio rappresenta un gruppo di prefissi di indirizzo IP che consente di ridurre al minimo la complessità nella creazione di regole di sicurezza. Non è possibile creare tag di servizio personalizzati, né specificare gli indirizzi IP inclusi in un tag. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che aggiorna automaticamente il tag in caso di modifica degli indirizzi.

I tag del servizio Firewall di Azure possono essere usati nel campo di destinazione delle regole di rete. È possibile usarli al posto di indirizzi IP specifici.

## <a name="supported-service-tags"></a>Tag di servizio supportati

Vedere Gruppi di sicurezza per un elenco di tag di servizio disponibili per l'uso nelle regole di rete firewall di Azure.See [Security groups](../virtual-network/security-overview.md#service-tags) for a list of service tags that are available for use in Azure firewall network rules.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle regole di Firewall di Azure, vedere [Logica di elaborazione delle regole del Firewall di Azure](rule-processing.md).