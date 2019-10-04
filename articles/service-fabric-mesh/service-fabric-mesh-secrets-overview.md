---
title: Archiviazione e utilizzo dei segreti delle applicazioni Azure Service Fabric Mesh | Microsoft Docs
description: Archiviazione e utilizzo dei segreti di Service Fabric Mesh.
services: service-fabric-mesh
keywords: chiavi private
author: v-steg
ms.author: jeconnoc
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: jeconnoc
ms.openlocfilehash: b5cfa93298222d914069b6ab63deb8ba8a9b59c3
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875338"
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
