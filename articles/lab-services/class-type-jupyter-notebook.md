---
title: Configurare un Lab per insegnare data science con i notebook Python e Jupyter | Microsoft Docs
description: Informazioni su come configurare un Lab per insegnare data science uso di Python e Jupyter notebook.
author: emaher
ms.topic: article
ms.date: 09/29/2020
ms.author: enewman
ms.openlocfilehash: 4bbf4c9d4bc83b48b8ecc62946fa9bffa8af50bc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91533521"
---
# <a name="set-up-a-lab-to-teach-data-science-with-python-and-jupyter-notebooks"></a>Configurare un Lab per insegnare data science con i notebook Python e Jupyter
Questo articolo illustra come configurare una macchina virtuale (VM) modello in Lab Services con gli strumenti necessari per insegnare agli studenti come usare i [notebook di Jupyter](http://jupyter-notebook.readthedocs.io/)e come gli studenti possono connettersi ai propri notebook nelle macchine virtuali (VM).

Jupyter Notebooks è un progetto open source che consente di combinare facilmente testo RTF e codice sorgente Python eseguibile in un'unica area di disegno denominata notebook. L'esecuzione di un notebook comporta un record lineare di input e output. Questi output possono includere testo, tabelle di informazioni, grafici a dispersione e altro ancora.

## <a name="set-up-the-lab"></a>Configurare il Lab

### <a name="lab-configuration"></a>Configurazione del lab
Per configurare questo Lab, è necessario accedere a una sottoscrizione di Azure e a un account Lab. Discutere con l'amministratore dell'organizzazione per verificare se è possibile ottenere l'accesso a una sottoscrizione di Azure esistente. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

Quando si dispone di una sottoscrizione di Azure, creare un nuovo account Lab in Azure Lab Services seguendo le istruzioni riportate nell'esercitazione: [configurare un account Lab](tutorial-setup-lab-account.md). È anche possibile usare un account lab esistente.

### <a name="lab-account-settings"></a>Impostazioni dell'account Lab
Abilitare le impostazioni descritte nella tabella seguente per l'account Lab. Per altre informazioni sull'abilitazione delle immagini del Marketplace, vedere [specificare le immagini del Marketplace disponibili per gli autori del Lab](specify-marketplace-images.md).

| Impostazione dell'account lab | Istruzioni |
| ------------------- | ------------ |
| Immagine del Marketplace | All'interno dell'account Lab, abilitare una delle immagini di Azure Marketplace in base alle esigenze del sistema operativo: <br/><ul><li>Data Science Virtual Machine: Windows Server 2019</li><li>Data Science Virtual Machine-Ubuntu 18,04</li></ul> |

> [!NOTE]
> Questo articolo usa le immagini di macchine virtuali per l'analisi scientifica dei dati disponibili in Azure Marketplace perché sono preconfigurate con Jupyter Notebook. Queste immagini, tuttavia, includono anche molti altri strumenti di sviluppo e modellazione per data science. Se non si vuole usare questi strumenti aggiuntivi e si vuole un'installazione leggera con solo notebook Jupyter, creare un'immagine di macchina virtuale personalizzata. Per un esempio, [installazione di JupyperHub in Azure](http://tljh.jupyter.org/en/latest/install/azure.html). Una volta creata l'immagine personalizzata, è possibile caricarla in una raccolta di immagini condivise per usare l'immagine all'interno Azure Lab Services. Altre informazioni sull' [uso della raccolta immagini condivise in Azure Lab Services](how-to-attach-detach-shared-image-gallery.md). 

### <a name="lab-settings"></a>Impostazioni del lab
Configurare le impostazioni relative alle **dimensioni della macchina** virtuale e alle **Immagini della macchina virtuale** come illustrato nella tabella seguente durante la configurazione di un Lab della classe. Per istruzioni sulla creazione di un Lab per le aule, vedere [configurare un Lab](tutorial-setup-classroom-lab.md)per la classe.

| Impostazioni del lab | Valore/istruzioni |
| ------------ | ------------------ | 
| Dimensioni della macchina virtuale | <p>Le dimensioni selezionate variano a seconda del carico di lavoro che si vuole eseguire:</p><ul><li>Piccolo o medio-valido per una configurazione di base per l'accesso ai notebook di Jupyter</li><li>Piccola GPU (calcolo): ideale per applicazioni a elevato utilizzo di calcolo e di rete, come l'intelligenza artificiale e l'apprendimento avanzato</li></ul> | 
| Immagine di macchina virtuale | <p>Scegliere una delle immagini seguenti in base alle esigenze del sistema operativo:</p><ul><li>[Data Science Virtual Machine: Windows Server 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019)</li><li>[Data Science Virtual Machine-Ubuntu 18,04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview)</li></ul> |


### <a name="template-virtual-machine"></a>Macchina virtuale modello
Dopo aver creato un Lab, verrà creata una macchina virtuale modello basata sulle dimensioni della macchina virtuale e sull'immagine scelta. Configurare la macchina virtuale del modello con tutti gli elementi che si desidera fornire agli studenti per questa classe. Per altre informazioni, vedere [How to manage the template Virtual Machine](how-to-create-manage-template.md). 

Per impostazione predefinita, per questo tipo di classe le immagini Data Science VM sono dotate di molti data science Framework e strumenti necessari. Ad esempio, le immagini includono:

- [Notebook di Jupyter](http://jupyter-notebook.readthedocs.io/): un'applicazione Web che consente agli esperti di dati di eseguire dati non elaborati, eseguire calcoli e visualizzare tutti i risultati nello stesso ambiente. Verrà eseguito localmente nella macchina virtuale del modello.  
- [Visual Studio Code](https://code.visualstudio.com/): un Integrated Development Environment (IDE) che offre un'esperienza interattiva avanzata per la scrittura e il test di un notebook. Per altre informazioni, vedere [uso di notebook di Jupyter in Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support).

### <a name="provide-notebooks-for-the-class"></a>Fornire notebook per la classe
L'attività successiva consiste nel fornire agli studenti i notebook che si vuole usare. Per fornire Notebook personalizzati, è possibile salvare i notebook localmente nella macchina virtuale del modello. 

Per usare notebook di esempio di Azure Machine Learning, vedere [come configurare un ambiente con notebook di Jupyter](../machine-learning/how-to-configure-environment.md#jupyter). 

### <a name="optional-enable-graphical-desktop-for-linux"></a>Facoltativo: Abilita desktop grafico per Linux 
Il provisioning dell'immagine **Data Science Virtual Machine-Ubuntu** è già stato eseguito con il server X2GO ed è pronto ad accettare le connessioni client. Non sono necessari altri passaggi quando si configura la macchina virtuale del modello. 

### <a name="publish-the-template-machine"></a>Pubblicare il computer modello
Quando si pubblica il modello, ogni studente registrato nel Lab otterrà una copia della macchina virtuale modello con tutti gli strumenti e i notebook locali impostati.

## <a name="how-students-connect-to-jupyter-notebooks"></a>In che modo gli studenti si connettono ai notebook di Jupyter?
Dopo la pubblicazione del modello, ogni studente avrà accesso a una macchina virtuale fornita con tutti gli elementi preconfigurati per la classe, inclusi i notebook di Jupyter. Le sezioni seguenti illustrano i diversi modi in cui gli studenti si connettono ai notebook di Jupyter. 

### <a name="for-windows-vms"></a>Per macchine virtuali Windows
Se sono stati forniti gli studenti con macchine virtuali Windows, è necessario connettersi alle macchine virtuali e usare i notebook di Jupyter disponibili localmente. 

Per connettersi a una macchina virtuale Windows, uno studente può usare una connessione Desktop remoto (RDP). Per informazioni dettagliate sulla procedura, vedere [come accedere a un Lab per la classe](how-to-use-classroom-lab.md). 

Uno studente che usa un Mac o un Chromebook può seguire le istruzioni degli articoli seguenti per connettersi alla macchina virtuale di Data Science Windows. 

- [Connettersi a una macchina virtuale usando RDP in un Mac](connect-virtual-machine-mac-remote-desktop.md)
- [Connettersi a una macchina virtuale usando RDP in un Chromebook](connect-virtual-machine-chromebook-remote-desktop.md)

### <a name="for-linux-vms"></a>Per macchine virtuali Linux
Se sono stati forniti gli studenti con macchine virtuali Linux, sono disponibili diverse opzioni che gli studenti possono usare per connettersi ai notebook di Jupyter nelle macchine virtuali:

- Accedere a Jupyter notebook in locale dopo la connessione alla macchina virtuale
    - Connettersi tramite SSH alla macchina virtuale per le sessioni terminal
     - Connessione X2Go alla macchina virtuale per le sessioni grafiche
- Usare il tunneling SSH per connettersi dal computer locale dello studente direttamente al server Jupyter nella macchina virtuale. 

Le sezioni seguenti forniscono informazioni dettagliate su questi modi per connettersi ai notebook di Jupyter. 

#### <a name="ssh-to-virtual-machine"></a>Da SSH a macchina virtuale
Gli studenti possono connettersi tramite SSH alle macchine virtuali Linux da una sessione terminal. Per informazioni dettagliate sulla procedura, vedere [come accedere a un Lab per la classe](how-to-use-classroom-lab.md). Se usano un computer client Windows, sarà necessario abilitare un client SSH scaricando [Putty](https://www.putty.org/) o abilitando [OpenSSH in Windows](https://docs.microsoft.com/windows-server/administration/openssh/openssh_install_firstuse) per SSH dal prompt dei comandi. 

1.  Avviare la VM.
2.  Quando la macchina virtuale è in esecuzione, fare clic su **Connetti**. verrà visualizzata una finestra di dialogo che fornisce la stringa di comando ssh, che sarà simile all'esempio seguente:
    
     ```shell
    ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
     ```
3.  Passare al prompt dei comandi o al terminale, incollare il comando e quindi premere **invio**.
4.  Immettere la password per accedere alla macchina virtuale. 

Quando gli studenti sono connessi alle macchine virtuali, possono accedere ed eseguire Jupyter notebook in locale.

#### <a name="x2go-to-virtual-machine"></a>X2Go alla macchina virtuale
Il provisioning dell'immagine **Data Science Virtual Machine-Ubuntu** è già stato eseguito con il server X2GO ed è pronto ad accettare le connessioni client. Per connettersi al desktop con interfaccia grafica del computer Linux, gli studenti devono seguire questi passaggi una sola volta per configurare X2Go nei computer client:

1.  Scaricare e installare il [client x2go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient) per la piattaforma client.
2.  Nel [portale di Azure Lab Services](https://labs.azure.com)verificare che la macchina virtuale Linux a cui si desidera connettersi sia avviata.
3.  Quando la macchina virtuale è in esecuzione, fare clic su **Connetti**. verrà visualizzata una finestra di dialogo che fornisce la stringa di comando ssh, che sarà simile all'esempio seguente:

    ```
     ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
     ```    
4. Quando si hanno queste informazioni, aprire l'app client X2Go e creare una nuova sessione. 
5.  Immettere i valori seguenti nel riquadro **Preferenze sessione** :
    - **Nome sessione**: può essere quello che si vuole, ma è consigliabile usare il nome della macchina virtuale Lab.
     - **Host**: `ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com`
     - **Account di accesso**: Student
     - **Porta SSH**: 12345
     - **Tipo di sessione**: Xfce
6. Selezionare **OK**. 

    > [!NOTE]
     > Quando si crea una nuova sessione di X2Go, assicurarsi di usare la porta SSH e **non** la porta RDP.

A questo punto, per connettersi alla macchina virtuale, seguire questa procedura:    

1.  Nel client X2Go fare doppio clic sulla macchina virtuale a cui ci si vuole connettere. 

    ![Client X2Go](./media/class-type-jupyter-notebook/x2go-client.png)
2. Immettere la password per la connessione alla macchina virtuale. Potrebbe essere necessario concedere a X2Go l'autorizzazione per ignorare il firewall per completare la connessione.
3.  Verrà ora visualizzata l'interfaccia grafica per la Data Science VM Ubuntu.


#### <a name="ssh-tunnel-to-jupyter-server-on-the-vm"></a>Tunnel SSH per il server Jupyter nella macchina virtuale
Alcuni studenti potrebbero voler connettersi direttamente dal computer locale direttamente al server Jupyter all'interno delle proprie macchine virtuali. Il protocollo SSH consente l'invio di porte tra il computer locale e un server remoto (in questo caso, la macchina virtuale Lab dello studente), in modo che un'applicazione in esecuzione su una determinata porta sul server venga sottoposto a **tunneling** sulla porta di mapping del computer locale. Gli studenti devono seguire questa procedura per eseguire il tunneling SSH nel server Jupyter nelle VM del Lab:

1.  Nel [portale di Azure Lab Services](https://labs.azure.com)verificare che la macchina virtuale Linux che si vuole connettere sia avviata.
2.  Quando la macchina virtuale è in esecuzione, fare clic su **Connetti**. verrà visualizzata una finestra di dialogo che fornisce la stringa di comando ssh, che sarà simile alla stringa seguente:

    ```bash
     ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
     ```
3. Nel computer locale, avviare un terminale o un prompt dei comandi e copiare la stringa di connessione SSH. Quindi, aggiungere `-L 8888:localhost:8888` alla stringa di comando, che crea il **tunnel** tra le porte. La stringa finale dovrebbe essere simile alla seguente:

    ```bash
     ssh –L 8888:localhost:8888 -p 12345 student@ml-lab-b720853e-570f-49ac-9cb2-bd0bd2aeec35.eastus.cloudapp.azure.com
     ```
4. Premere **invio** per eseguire il comando. 
5.  Quando richiesto, specificare la password per la connessione alla macchina virtuale Lab. 
6.  Dopo aver eseguito la connessione alla macchina virtuale, avviare il server Jupyter usando questo comando: 

    ```bash
     jupyter notebook
     ```
7. L'esecuzione del comando fornirà un URL nel terminale o nel prompt dei comandi. L'URL dovrebbe essere simile al seguente:

    ```bash
     http://localhost:8888/?token=8c09ecfc93e6a8cbedf9c66dffdae19670a64acc1d37
     ```
8. Incollare l'URL in un browser nel computer locale per connettersi e lavorare all'Jupyter Notebook. 

    > [!NOTE]
    > Visual Studio Code consente inoltre un' [esperienza di modifica Jupyter notebook](https://code.visualstudio.com/docs/python/jupyter-support)eccellente. È possibile seguire le istruzioni su [come connettersi a un server Jupyter remoto](https://code.visualstudio.com/docs/python/jupyter-support#_connect-to-a-remote-jupyter-server) e usare lo stesso URL del passaggio precedente per connettersi da vs code anziché dal browser. 


## <a name="cost-estimate"></a>Stima dei costi
Verrà ora trattata una possibile stima dei costi per questa classe. Verrà usata una classe di 25 studenti. Sono previste 20 ore di tempo della classe pianificata. Ogni studente riceve inoltre una quota di 10 ore per il lavoro o assegnazioni al di fuori dell'orario di classe pianificato. Le dimensioni della macchina virtuale scelte sono Small GPU (Compute), ovvero 139 unità Lab. Se si desidera utilizzare le dimensioni piccole (20 unità Lab) o medie (42 unità Lab), è possibile sostituire la parte dell'unità Lab nell'equazione seguente con il numero corretto.  

Di seguito è riportato un esempio di una possibile stima dei costi per questa classe: 25 studenti * (20 ore pianificate + 10 ore di quota) * 139 unità Lab * 0,01 USD all'ora = 1042,5 USD

Per altre informazioni sui prezzi, vedere [Prezzi di Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusioni
In questo articolo sono stati illustrati i passaggi per creare un Lab per una classe Jupyter Notebooks. È possibile usare una configurazione simile per altre classi di machine learning.

## <a name="next-steps"></a>Passaggi successivi

I passaggi successivi sono comuni per la configurazione di qualsiasi Lab.

- [Creare e gestire un modello](how-to-create-manage-template.md)
- [Aggiungere utenti](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Impostare la quota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Impostare una pianificazione](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Collegamenti per la registrazione tramite posta elettronica agli studenti](how-to-configure-student-usage.md#send-invitations-to-users)
