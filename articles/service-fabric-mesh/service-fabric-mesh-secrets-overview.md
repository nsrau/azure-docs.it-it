---
title: Archiviazione e utilizzo dei segreti delle applicazioni Azure Service Fabric Mesh | Microsoft Docs
description: Archiviazione e utilizzo dei segreti di Service Fabric Mesh.
services: service-fabric-mesh
keywords: chiavi private
author: v-steg
ms.author: v-steg
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: jeanpaul.connock
ms.openlocfilehash: 4268356db5f46e92862e19d6391cfe5a94511270
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60810640"
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
