---
title: Archiviazione e utilizzo dei segreti delle applicazioni Azure Service Fabric Mesh | Microsoft Docs
description: Archiviazione e utilizzo dei segreti di Service Fabric Mesh.
services: service-fabric-mesh
keywords: segreti
author: v-steg
ms.author: v-steg
ms.date: 10/25/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: jeanpaul.connock
ms.openlocfilehash: 63ac34e184d537eba2b915d4d108c7c1d8368aba
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2018
ms.locfileid: "52891931"
---
# <a name="service-fabric-mesh-application-secrets"></a>Segreti delle applicazioni Azure Service Fabric Mesh
Service Fabric Mesh supporta i segreti come risorse di Azure. Un segreto di Service Fabric Mesh può essere costituito da informazioni di testo riservate, come stringhe di connessione per l'archiviazione, password o altri valori che devono essere archiviati e trasmessi in modo sicuro.

![Panoramica dei segreti di Mesh][sf-mesh-secrets-overview]

## <a name="mesh-secrets-resources"></a>Risorse Segreti di Mesh
Un segreto dell'applicazione Mesh è costituto da:
* Una risorsa **Segreti**, ossia un contenitore in cui sono archiviati i segreti di testo. I segreti contenuti nella risorsa **Segreti** vengono archiviati e trasmessi in modo sicuro.
* Una o più risorse **Segreti/Valori** archiviate nel contenitore di risorse **Segreti**. Ogni risorsa **Segreti/Valori** è contraddistinta da un numero di versione.

## <a name="inline-or-stored-in-azure-key-vault"></a>Inline o archiviati in Azure Key Vault
- Le applicazioni e le risorse di servizio Mesh includono Identità del servizio gestita con Azure Active Directory (AAD) per poter accedere ai segreti in Azure Key Vault.
- I segreti e i certificati possono essere automaticamente sottoposti a rollover con i criteri.

## <a name="next-steps"></a>Passaggi successivi 
Per altre informazioni sui segreti di Service Fabric Mesh, vedere:
- [Gestire i segreti delle applicazioni Azure Service Fabric Mesh](service-fabric-mesh-howto-manage-secrets.md)
- [Introduzione al modello di risorsa di Service Fabric](service-fabric-mesh-service-fabric-resources.md)

<!-- pics -->
[sf-mesh-secrets-overview]: ./media/service-fabric-mesh-secrets-overview/MeshAppSecretsOverview.png
