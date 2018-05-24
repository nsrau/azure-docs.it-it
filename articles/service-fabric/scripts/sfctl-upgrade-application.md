---
title: Esempio di script dell'interfaccia della riga di comando di Service Fabric - Aggiornare un'applicazione in un cluster
description: Esempio di script dell'interfaccia della riga di comando di Service Fabric - Aggiornare un'applicazione con una nuova versione. In questo esempio viene inoltre illustrato come aggiornare un'applicazione distribuita con le nuove funzionalità.
services: service-fabric
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''
tags: ''
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 12/06/2017
ms.author: adegeo
ms.custom: ''
ms.openlocfilehash: e14e65e365389b33891794a3f12b86b3a4705533
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2018
ms.locfileid: "34204380"
---
# <a name="add-an-application-certificate-to-a-service-fabric-cluster"></a>Aggiungere la certificazione dell'applicazione a un cluster di Service Fabric.

Questo script di esempio carica una nuova versione di un'applicazione esistente e quindi aggiorna un'applicazione distribuita con le nuove funzionalità.

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>Script di esempio

[!code-sh[main](../../../cli_scripts/service-fabric/upgrade-application/upgrade-application.sh "Upload and update an application on a Service Fabric cluster")]

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere la [documentazione sull'interfaccia della riga di comando di Service Fabric](../service-fabric-cli.md).

Altri esempi dell'interfaccia della riga di comando di Service Fabric per Azure Service Fabric sono disponibili negli [esempi dell'interfaccia della riga di comando di Service Fabric](../samples-cli.md).
