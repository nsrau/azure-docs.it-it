---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 0019e50615f3e66778709ad8cb28f92967c66e2e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86050294"
---
## <a name="deployment-customization"></a>Personalizzazione della distribuzione

Il processo di distribuzione presuppone che il file ZIP di cui si esegue il push contenga un'app pronta da eseguire. Per impostazione predefinita, non vengono eseguite personalizzazioni. Per abilitare gli stessi processi di compilazione ottenuti con l'integrazione continua, aggiungere quanto segue alle impostazione applicazione:

`SCM_DO_BUILD_DURING_DEPLOYMENT=true`

Quando si usa la distribuzione push tramite file ZIP, questa impostazione è **false** per impostazione predefinita. L'impostazione predefinita è **true** per le distribuzioni tramite integrazione continua. Se impostata su **true**, durante la distribuzione vengono usate le impostazioni relative alla distribuzione. È possibile configurare queste impostazioni come impostazioni app o in un file di configurazione con estensione deployment disponibile nella radice del file ZIP. Per altre informazioni, vedere [Repository and deployment-related settings](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings) (Repository e impostazioni relative alla distribuzione) nelle informazioni di riferimento sulla distribuzione.