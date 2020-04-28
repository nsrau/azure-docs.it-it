---
title: File di inclusione
description: File di inclusione
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 01/14/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 6b5aa4f409b8c2f5a9125ab01e8587f4ac9c4ee5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75945183"
---
## <a name="create-a-project-zip-file"></a>Creare un file ZIP del progetto

>[!NOTE]
> Se i file sono stati scaricati in un file ZIP, occorre innanzitutto estrarli. Ad esempio, se è stato scaricato un file ZIP da GitHub, non è possibile distribuire il file così com'è. GitHub aggiunge altre directory annidate che non funzionano con il servizio app. 
>

In una finestra terminale locale, passare alla directory radice del progetto dell'app. 

Questa directory deve contenere il file di ingresso dell'app Web, ad esempio _index.html_, _index.php_ e _app.js_. Può inoltre contenere file di gestione del pacchetto come _project.json_, _composer.json_, _package.json_, _bower.json_ e _requirements.txt_.

Se non si vuole che il servizio app esegua automaticamente l'automazione della distribuzione, eseguire tutte le attività di compilazione `npm`, `bower`ad `gulp`esempio `composer`,, `pip`, e, e assicurarsi di avere tutti i file necessari per eseguire l'app. Questo passaggio è obbligatorio se si desidera [eseguire direttamente il pacchetto](../articles/app-service/deploy-run-package.md).

Creare un archivio ZIP per tutti gli elementi del progetto. Il comando seguente usa lo strumento predefinito nel terminale:

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

