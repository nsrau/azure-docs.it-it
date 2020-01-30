---
title: 'Debug interattivo: istanze di calcolo di VS Code & ML'
titleSuffix: Azure Machine Learning
description: Configurare VS Code remoto per eseguire il debug interattivo del codice con Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jmartens
author: j-martens
ms.date: 12/09/2019
ms.openlocfilehash: 27243f47df7da22ab4adda088bdf631c1030dd6c
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845189"
---
# <a name="debug-interactively-on-an-azure-machine-learning-compute-instance-with-vs-code-remote"></a>Eseguire il debug interattivo su un'istanza di calcolo Azure Machine Learning con VS Code remoto

In questo articolo si apprenderà come configurare Visual Studio Code remoto in un'istanza di calcolo Azure Machine Learning per poter **eseguire il debug interattivo del codice** da vs code. 

> [!NOTE]
> Le istanze di calcolo sono disponibili solo per le aree di lavoro con gli **Stati Uniti centro-settentrionali**, * * Stati Uniti orientali 2 * *, **europa settentrionale** o **Regno Unito meridionale**.

+ Un' [istanza di calcolo Azure Machine Learning](concept-compute-instance.md) è una workstation basata su cloud completamente gestita per gli esperti di dati e offre funzionalità di gestione e disponibilità aziendale per gli amministratori IT. 


+ [Visual Studio Code remoto](https://code.visualstudio.com/docs/remote/remote-overview) Lo sviluppo consente di usare un contenitore, un computer remoto o il sottosistema Windows per Linux (WSL) come un ambiente di sviluppo completo. 

## <a name="prerequisite"></a>Prerequisito  

Nelle piattaforme Windows è necessario [installare un client SSH compatibile con OpenSSH](https://code.visualstudio.com/docs/remote/troubleshooting#_installing-a-supported-ssh-client) , se non ne è già presente uno. 

> [!Note]
> PuTTy non è supportato in Windows perché il comando ssh deve trovarsi nel percorso. 

## <a name="get-ip-and-ssh-port"></a>Ottenere la porta IP e SSH 

1. Passare a Azure Machine Learning Studio in https://ml.azure.com/.

2. Selezionare l' [area di lavoro](concept-workspace.md).
1. Fare clic sulla scheda **istanze di calcolo** .
1. Nella colonna **URI dell'applicazione** fare clic sul collegamento **SSH** dell'istanza di calcolo che si vuole usare come calcolo remoto. 
1. Nella finestra di dialogo prendere nota dell'indirizzo IP e della porta SSH. 
1. Salvare la chiave privata nella directory ~/.ssh/nel computer locale. ad esempio, aprire un editor per un nuovo file e incollare la chiave in: 

   **Linux**: 
   ```sh
   vi ~/.ssh/id_azmlcitest_rsa  
   ```

   **Windows**: 
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

1. Modificare le autorizzazioni per il file per assicurarsi solo che sia possibile leggere il file.  
   ```sh
   chmod 600 ~/.ssh/id_azmlcitest_rsa   
   ```

## <a name="add-instance-as-a-host"></a>Aggiungere un'istanza come host 

Aprire il file `~/.ssh/config` (Linux) o `C:\Users<username>.ssh\config` (Windows) in un editor e aggiungere una nuova voce simile alla seguente:

```
Host azmlci1 

    HostName 13.69.56.51 

    Port 50000 

    User azureuser 

    IdentityFile ~/.ssh/id_azmlcitest_rsa   
```

Di seguito sono riportati alcuni dettagli sui campi: 

|Campo|Description|
|----|---------|
|Host|Usa qualsiasi abbreviazione che preferisci per l'istanza di calcolo |
|HostName|Si tratta dell'indirizzo IP dell'istanza di calcolo |
|Porta|Si tratta della porta visualizzata nella finestra di dialogo SSH sopra |
|Utente|Questa operazione deve essere `azureuser` |
|IdentityFile|Deve puntare al file in cui è stata salvata la chiave privata |

A questo punto, si dovrebbe essere in grado di eseguire ssh nell'istanza di calcolo usando la sintassi abbreviata usata in precedenza, `ssh azmlci1`. 

## <a name="connect-vs-code-to-the-instance"></a>Connetti VS Code all'istanza 

1. [Installare Visual Studio Code](https://code.visualstudio.com/).

1. [Installare l'estensione SSH remota](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh). 

1. Fare clic sull'icona SSH remoto a sinistra per visualizzare le configurazioni SSH.

1. Fare clic con il pulsante destro del mouse sulla configurazione host SSH appena creata.

1. Selezionare **Connetti a host nella finestra corrente**. 

Da questo punto in poi si sta lavorando completamente sull'istanza di calcolo ed è ora possibile modificare, eseguire il debug, usare git, usare le estensioni e così via, esattamente come è possibile con la Visual Studio Code locale. 

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato configurato Visual Studio Code remoto, è possibile usare un'istanza di calcolo come calcolo remoto da Visual Studio Code per eseguire il debug interattivo del codice. 

[Esercitazione: eseguire il training del primo modello ml](tutorial-1st-experiment-sdk-train.md) Mostra come usare un'istanza di calcolo con un notebook integrato.