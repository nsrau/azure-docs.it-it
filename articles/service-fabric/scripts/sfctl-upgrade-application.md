---
title: Esempio di script dell'interfaccia della riga di comando di Service Fabric - Aggiornare un'applicazione in un cluster
description: Esempio di script dell'interfaccia della riga di comando di Service Fabric - Aggiornare un'applicazione con una nuova versione. In questo esempio viene inoltre illustrato come aggiornare un'applicazione distribuita con le nuove funzionalità.
services: service-fabric
documentationcenter: ''
author: aljo-microsoft
manager: timlt
editor: ''
tags: ''
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 12/06/2017
ms.author: aljo
ms.custom: ''
ms.openlocfilehash: 7c6b4b6214f5fc2788827ae34aad4019dad1d8ae
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56806964"
---
# <a name="add-an-application-certificate-to-a-service-fabric-cluster"></a>Aggiungere la certificazione dell'applicazione a un cluster di Service Fabric.

Questo script di esempio carica una nuova versione di un'applicazione esistente e quindi aggiorna un'applicazione distribuita con le nuove funzionalità.

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>Script di esempio

[!code-sh[main](../../../cli_scripts/service-fabric/upgrade-application/upgrade-application.sh "Upload and update an application on a Service Fabric cluster")]

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere la [documentazione sull'interfaccia della riga di comando di Service Fabric](../service-fabric-cli.md).

Altri esempi dell'interfaccia della riga di comando di Service Fabric per Azure Service Fabric sono disponibili negli [esempi dell'interfaccia della riga di comando di Service Fabric](../samples-cli.md).
