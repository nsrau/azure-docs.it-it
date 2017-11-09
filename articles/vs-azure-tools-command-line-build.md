---
title: Compilazione da riga di comando per Azure | Documentazione Microsoft
description: Compilazione da riga di comando per Azure
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 94b35d0d-0d35-48b6-b48b-3641377867fd
ms.service: multiple
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2017
ms.author: kraigb
ms.openlocfilehash: 5fe910e2757dd5ec783538e23e7f52e2f5725b39
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="building-azure-projects-from-the-command-line"></a>Compilazione di progetti Azure dalla riga di comando
Tramite Microsoft Build Engine (MSBuild) è possibile compilare prodotti in ambienti lab di compilazione in cui Visual Studio non è installato. MSBuild usa per i file di progetto il formato XML, che è estendibile e completamente supportato da Microsoft. Usando il formato file MSBuild è possibile indicare quali elementi devono essere compilati per una o più piattaforme o configurazioni.

È anche possibile eseguire MSBuild alla riga dei comandi, come illustrato in questo argomento. Impostando le proprietà nella riga dei comandi, è possibile compilare configurazioni specifiche di un progetto. Analogamente, è possibile definire le destinazioni compilate dal comando di MSBuild. Per altre informazioni sui parametri della riga di comando e su MSBuild, vedere [Riferimenti alla riga di comando di MSBuild](https://msdn.microsoft.com/library/ms164311.aspx).

## <a name="msbuild-parameters"></a>Parametri MSBuild
Il modo più semplice per creare un pacchetto consiste nell'eseguire MSBuild con l'opzione `/t:Publish` . Per impostazione predefinita, questo comando crea una directory in relazione alla cartella radice del progetto, ad esempio `<ProjectDirectory>\bin\Configuration\app.publish\`. Quando si compila un progetto Azure, vengono generati due file, il file del pacchetto e il file di configurazione di accompagnamento:

* File del pacchetto (`project.cspkg`)
* File di configurazione(`ServiceConfiguration.TargetProfile.cscfg`)

Per impostazione predefinita, ogni progetto Azure include un file di configurazione del servizio per le compilazioni locali (debug) e un altro per le compilazioni cloud (gestione temporanea o produzione), ma è possibile aggiungere o rimuovere i file di configurazione del servizio in base alle proprie esigenze. Quando si compila un pacchetto all'interno di Visual Studio, viene chiesto quale file di configurazione del servizio includere insieme al pacchetto. Quando si compila un pacchetto usando MSBuild, il file di configurazione del servizio locale viene incluso per impostazione predefinita. Per includere un file di configurazione del servizio diverso, impostare la proprietà `TargetProfile` del comando MSBuild (`MSBuild /t:Publish /p:TargetProfile=ProfileName`).

Se si vuole usare una directory alternativa per i file di configurazione e del pacchetto archiviati, impostare il percorso usando l'opzione `/p:PublishDir=Directory\`, inclusa la barra rovesciata finale.

## <a name="next-steps"></a>Passaggi successivi
Dopo la compilazione, sarà possibile distribuire il pacchetto in Azure. Per un'esercitazione su come automatizzare questo processo, vedere [Recapito continuo per Servizi cloud in Azure](./cloud-services/cloud-services-dotnet-continuous-delivery.md).

