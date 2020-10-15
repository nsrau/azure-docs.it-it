---
title: Connettersi a un'istanza di calcolo in Visual Studio Code (anteprima)
titleSuffix: Azure Machine Learning
description: Informazioni su come connettersi a un'istanza di calcolo Azure Machine Learning in Visual Studio Code
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: jmartens
author: j-martens
ms.date: 09/03/2020
ms.openlocfilehash: 6ac116b315d4a11b51b37c5b51edf35aa0676713
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91708374"
---
# <a name="connect-to-an-azure-machine-learning-compute-instance-in-visual-studio-code-preview"></a>Connettersi a un'istanza di calcolo Azure Machine Learning in Visual Studio Code (anteprima)

In questo articolo si apprenderà come connettersi a un'istanza di calcolo Azure Machine Learning usando Visual Studio Code.

Un' [istanza di calcolo Azure Machine Learning](concept-compute-instance.md) è una workstation basata su cloud completamente gestita per gli esperti di dati e offre funzionalità di gestione e disponibilità aziendale per gli amministratori IT.

Esistono due modi per connettersi a un'istanza di calcolo da Visual Studio Code:

* Server Jupyter Notebook remoto. Questa opzione consente di impostare un'istanza di calcolo come server di Jupyter Notebook remoto.
* [Visual Studio Code lo sviluppo remoto](https://code.visualstudio.com/docs/remote/remote-overview). Visual Studio Code sviluppo remoto consente di usare un contenitore, un computer remoto o il sottosistema Windows per Linux (WSL) come ambiente di sviluppo completo.

## <a name="configure-compute-instance-as-remote-notebook-server"></a>Configurare un'istanza di calcolo come server notebook remoto

Per configurare un'istanza di calcolo come server di Jupyter Notebook remoto sono necessari alcuni prerequisiti:

* Azure Machine Learning Visual Studio Code estensione. Per ulteriori informazioni, vedere la [Guida all'installazione di Azure Machine Learning Visual Studio Code Extension](tutorial-setup-vscode-extension.md).
* Area di lavoro Azure Machine Learning. [Usare l'estensione Azure Machine Learning Visual Studio Code per creare una nuova area di lavoro](how-to-manage-resources-vscode.md#create-a-workspace) , se non è già presente.

Per connettersi a un'istanza di calcolo:

1. Aprire un Jupyter Notebook in Visual Studio Code.
1. Quando viene caricata l'esperienza del notebook integrato, selezionare **Jupyter server**.

    > [!div class="mx-imgBorder"]
    > ![Avviare Azure Machine Learning elenco a discesa Server Jupyter notebook remoto](media/how-to-set-up-vs-code-remote/launch-server-selection-dropdown.png)

    In alternativa, è possibile usare anche il riquadro comandi:

    1. Per aprire il riquadro comandi selezionare **Visualizza > Riquadro comandi** nella barra dei menu.
    1. Immettere nella casella di testo `Azure ML: Connect to Compute instance Jupyter server` .

1. Scegliere `Azure ML Compute Instances` dall'elenco di opzioni del server Jupyter.
1. Selezionare la sottoscrizione dall'elenco di sottoscrizioni. Se è già stata configurata l'area di lavoro predefinita Azure Machine Learning, questo passaggio viene ignorato.
1. Selezionare l'area di lavoro.
1. Selezionare l'istanza di calcolo dall'elenco. Se non è presente, selezionare **Crea nuova istanza di Azure ambiente di calcolo di ml** e seguire le istruzioni per crearne una.
1. Per rendere effettive le modifiche, è necessario ricaricare Visual Studio Code.
1. Aprire una Jupyter Notebook ed eseguire una cella.

> [!IMPORTANT]
> Per stabilire la connessione, è **necessario** eseguire una cella.

A questo punto, è possibile continuare a eseguire le celle nel notebook di Jupyter.

> [!TIP]
> È anche possibile usare i file di script Python (con estensione py) contenenti celle di codice simili a Jupyter. Per ulteriori informazioni, vedere il [Visual Studio Code documentazione interattiva di Python](https://code.visualstudio.com/docs/python/jupyter-support-py).

## <a name="configure-compute-instance-remote-development"></a>Configurare lo sviluppo remoto dell'istanza di calcolo

Per un'esperienza di sviluppo remota completa, sono necessari alcuni prerequisiti:

* [Visual Studio Code estensione SSH remota](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh).
* Istanza di calcolo abilitata per SSH. Per ulteriori informazioni, [vedere la Guida creare un'istanza di calcolo](how-to-create-manage-compute-instance.md).

> [!NOTE]
> Nelle piattaforme Windows è necessario [installare un client SSH compatibile con OpenSSH](https://code.visualstudio.com/docs/remote/troubleshooting#_installing-a-supported-ssh-client) , se non ne è già presente uno. PuTTy non è supportato in Windows perché il comando ssh deve trovarsi nel percorso.

### <a name="get-the-ip-and-ssh-port-for-your-compute-instance"></a>Ottenere l'indirizzo IP e la porta SSH per l'istanza di calcolo

1. Passare a Azure Machine Learning Studio all'indirizzo https://ml.azure.com/ .
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

   ```cmd
   notepad C:\Users\<username>\.ssh\id_azmlcitest_rsa
   ```

   La chiave privata sarà simile alla seguente:

   ```text
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

### <a name="add-instance-as-a-host"></a>Aggiungere un'istanza come host

Aprire il file `~/.ssh/config` (Linux) o `C:\Users<username>.ssh\config` (Windows) in un editor e aggiungere una nuova voce simile al contenuto seguente:

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

A questo punto, si dovrebbe essere in grado di eseguire ssh nell'istanza di calcolo usando la sintassi abbreviata usata in precedenza, `ssh azmlci1` .

### <a name="connect-vs-code-to-the-instance"></a>Connetti VS Code all'istanza

1. Fare clic sull'icona SSH remoto dalla barra attività Visual Studio Code per visualizzare le configurazioni SSH.

1. Fare clic con il pulsante destro del mouse sulla configurazione host SSH appena creata.

1. Selezionare **Connetti a host nella finestra corrente**. 

Da questo punto in poi si sta lavorando completamente sull'istanza di calcolo ed è ora possibile modificare, eseguire il debug, usare git, usare le estensioni e così via, esattamente come è possibile con la Visual Studio Code locale.

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato configurato Visual Studio Code remoto, è possibile usare un'istanza di calcolo come calcolo remoto da Visual Studio Code per [eseguire il debug interattivo del codice](how-to-debug-visual-studio-code.md).

In [Esercitazione: Eseguire il training del primo modello di Machine Learning](tutorial-1st-experiment-sdk-train.md) viene illustrato come usare un'istanza di calcolo con un notebook integrato.