---
title: 'Debug interattivo: codice VS & istanze di calcolo ML'
titleSuffix: Azure Machine Learning
description: Configurare VS Code Remote per eseguire il debug interattivo del codice con Azure Machine Learning.Set up VS Code Remote to interactively debug your code with Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jmartens
author: j-martens
ms.date: 12/09/2019
ms.openlocfilehash: 1999d29db21f820fbcdbca08f2258b657673be3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77169748"
---
# <a name="debug-interactively-on-an-azure-machine-learning-compute-instance-with-vs-code-remote"></a>Eseguire il debug in modo interattivo in un'istanza di calcolo di Azure Machine Learning con code remoto VSDebug ly on an Azure Machine Learning Compute Instance with VS Code Remote

In questo articolo imparerai a configurare Visual Studio Code Remote in un'istanza di calcolo di Azure Machine Learning in modo da poter **eseguire il debug interattivo del codice** dal codice VS. 

+ Un'istanza di [Calcolo di Azure Machine Learning](concept-compute-instance.md) è una workstation basata su cloud completamente gestita per i data scientist e fornisce funzionalità di gestione e preparazione aziendale per gli amministratori IT. 


+ [Remoto codice di Visual StudioVisual Studio Code Remote](https://code.visualstudio.com/docs/remote/remote-overview) Lo sviluppo consente di utilizzare un contenitore, un computer remoto o il sottosistema Windows per Linux (WSL) come ambiente di sviluppo completo. 

## <a name="prerequisite"></a>Prerequisito  

Nelle piattaforme Windows, è necessario [installare un client SSH compatibile con OpenSSH,](https://code.visualstudio.com/docs/remote/troubleshooting#_installing-a-supported-ssh-client) se non ne è già presente uno. 

> [!Note]
> PuTTY non è supportato in Windows poiché il comando ssh deve trovarsi nel percorso. 

## <a name="get-ip-and-ssh-port"></a>Ottenere la porta IP e SSHGet IP and SSH port 

1. Passare allo studio di https://ml.azure.com/Azure Machine Learning all'indirizzo .

2. Selezionare [l'area di lavoro](concept-workspace.md).
1. Fare clic sulla scheda **Istanze di calcolo.**
1. Nella colonna **URI applicazione** fare clic sul collegamento **SSH** dell'istanza di calcolo che si desidera utilizzare come calcolo remoto. 
1. Nella finestra di dialogo, prendere nota dell'indirizzo IP e della porta SSH. 
1. Salvare la chiave privata nella directory .ssh/ del computer locale; ad esempio, aprire un editor per un nuovo file e incollare la chiave in: 

   **Linux**: 
   ```sh
   vi ~/.ssh/id_azmlcitest_rsa  
   ```

   **Finestre di windows**: 
   ```
   notepad C:\Users\<username>\.ssh\id_azmlcitest_rsa 
   ```

   La chiave privata sarà simile alla seguente:
   ```
   -----BEGIN RSA PRIVATE KEY----- 

   MIIEpAIBAAKCAQEAr99EPm0P4CaTPT2KtBt+kpN3rmsNNE5dS0vmGWxIXq4vAWXD 
   ..... 
   ewMtLnDgXWYJo0IyQ91ynOdxbFoVOuuGNdDoBykUZPQfeHDONy2Raw== 

   -----END RSA PRIVATE KEY----- 
   ```

1. Modificare le autorizzazioni sul file per assicurarsi che solo tu possa leggere il file.  
   ```sh
   chmod 600 ~/.ssh/id_azmlcitest_rsa   
   ```

## <a name="add-instance-as-a-host"></a>Aggiungere un'istanza come hostAdd instance as a host 

Aprire il `~/.ssh/config` file (Linux) o `C:\Users<username>.ssh\config` (Windows) in un editor e aggiungere una nuova voce simile alla seguente:

```
Host azmlci1 

    HostName 13.69.56.51 

    Port 50000 

    User azureuser 

    IdentityFile ~/.ssh/id_azmlcitest_rsa   
```

Ecco alcuni dettagli sui campi: 

|Campo|Descrizione|
|----|---------|
|Host|Usare qualsiasi abbreviazione desiderata per l'istanza di calcoloUse whatever shorthand you like for the compute instance |
|HostName|Questo è l'indirizzo IP dell'istanza di calcolo |
|Porta|Questa è la porta visualizzata nella finestra di dialogo SSH sopra |
|Utente|Questo deve essere `azureuser` |
|FileIdentityFile|Deve puntare al file in cui è stata salvata la chiave privata |

A questo punto, si dovrebbe essere in grado di eseguire `ssh azmlci1`lo ssh per l'istanza di calcolo utilizzando l'abbreviazione utilizzata in precedenza, . 

## <a name="connect-vs-code-to-the-instance"></a>Connettere il codice VS all'istanza 

1. [Installare Visual Studio Code](https://code.visualstudio.com/).

1. [Installare l'estensione SSH remota](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh). 

1. Fare clic sull'icona Remote-SSH a sinistra per visualizzare le configurazioni SSH.

1. Fare clic con il pulsante destro del mouse sulla configurazione host SSH appena creata.

1. Selezionare **Connetti all'host nella finestra corrente**. 

Da qui in poi, si sta lavorando interamente sull'istanza di calcolo ed è ora possibile modificare, eseguire il debug, utilizzare git, utilizzare estensioni e così via - proprio come è possibile con il codice locale di Visual Studio. 

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Visual Studio Code Remote, è possibile usare un'istanza di calcolo come calcolo remoto dal codice di Visual Studio per eseguire il debug interattivo del codice. 

[Esercitazione: Eseguire il training del primo modello](tutorial-1st-experiment-sdk-train.md) di ML viene illustrato come usare un'istanza di calcolo con un blocco appunti integrato.