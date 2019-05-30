---
title: Soluzioni di VMware da CloudSimple - servizio
description: Descrive la panoramica del servizio CloudSimple e concetti.
author: sharaths-cs
ms.author: b-shsury
ms.date: 4/24/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f7b4be0ff3997e27dd5b5321dd44b5006ae52102
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66358109"
---
# <a name="cloudsimple-service-overview"></a>Panoramica di CloudSimple Service

Il servizio CloudSimple consente all'utente di Azure VMware Solution by CloudSimple.  Creazione del servizio consente di acquistare i nodi, di riservarlo nodi e creare cloud privati.  Si crea il servizio CloudSimple in ogni area di Azure in cui il servizio CloudSimple è disponibile.  Il servizio definisce la rete perimetrale di Azure VMware Solution by CloudSimple.  La rete perimetrale supporta i servizi che includono VPN, ExpressRoute e la connettività internet per i cloud privati.

## <a name="gateway-subnet"></a>Subnet gateway

Una subnet del gateway è necessaria per ogni servizio CloudSimple ed è univoca nell'area in cui viene creato. La subnet del gateway viene usata quando si crea la rete perimetrale e richiede / 28 blocco CIDR.  Lo spazio degli indirizzi di subnet del gateway deve essere univoco. Non deve sovrapporsi con reti che comunica con l'ambiente CloudSimple. Le reti che comunicano con CloudSimple includono reti locali e rete virtuale di Azure.  Una subnet del gateway non può essere eliminata dopo averla creata.  La subnet del gateway viene rimossa quando il servizio viene eliminato.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [creare un servizio CloudSimple in Azure](quickstart-create-cloudsimple-service.md)