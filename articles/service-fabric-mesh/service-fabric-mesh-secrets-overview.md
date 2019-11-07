---
title: Archiviazione e utilizzo dei segreti delle applicazioni Azure Service Fabric Mesh | Microsoft Docs
description: Service Fabric Mesh supporta i segreti come risorse di Azure. Ecco come archiviare e gestire i segreti con le applicazioni mesh Service Fabric.
services: service-fabric-mesh
keywords: chiavi private
author: v-steg
ms.author: jeconnoc
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: jeconnoc
ms.openlocfilehash: 72188517c237b170b709c48f16d3c131985f95d1
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686231"
---
# <a name="service-fabric-mesh-application-secrets"></a>Segreti delle applicazioni Azure Service Fabric Mesh
Service Fabric Mesh supporta i segreti come risorse di Azure. Un segreto di Service Fabric Mesh può essere costituito da informazioni di testo riservate, come stringhe di connessione per l'archiviazione, password o altri valori che devono essere archiviati e trasmessi in modo sicuro.

![Panoramica dei segreti di Mesh][sf-mesh-secrets-overview]

## <a name="mesh-secrets-resources"></a>Risorse Segreti di Mesh
Un segreto dell'applicazione Mesh è costituto da:
* Una risorsa **Segreti**, ossia un contenitore in cui sono archiviati i segreti di testo. I segreti contenuti nella risorsa **Segreti** vengono archiviati e trasmessi in modo sicuro.
* Una o più risorse **Segreti/Valori** archiviate nel contenitore di risorse **Segreti**. Ogni risorsa **Segreti/Valori** è contraddistinta da un numero di versione.

## <a name="next-steps"></a>Passaggi successivi 
Per altre informazioni sui segreti di Service Fabric Mesh, vedere:
- [Gestire i segreti delle applicazioni Azure Service Fabric Mesh](service-fabric-mesh-howto-manage-secrets.md)
- [Introduzione al modello di risorsa di Service Fabric](service-fabric-mesh-service-fabric-resources.md)

<!-- pics -->
[sf-mesh-secrets-overview]: ./media/service-fabric-mesh-secrets-overview/MeshAppSecretsOverview.png
