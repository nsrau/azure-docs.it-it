---
title: Configurare un lab di hacking etico con Azure Lab Services . Documenti Microsoft
description: Informazioni su come configurare un lab usando Azure Lab Services per insegnare l'hacking etico.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2019
ms.author: spelluru
ms.openlocfilehash: 2b600edc4c360a2b2990be34e44bb8fbd1c8f721
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74133187"
---
# <a name="set-up-a-lab-to-teach-ethical-hacking-class"></a>Creare un laboratorio per insegnare la classe di hacking etico 
Questo articolo illustra come impostare una classe incentrata sul lato forense dell'hacking etico. I test di penetrazione, una pratica usata dalla community di hacking etico, si verificano quando un utente tenta di accedere al sistema o alla rete per dimostrare le vulnerabilità che possono essere sfruttate da un utente malintenzionato. 

In un corso di hacking etico gli studenti possono apprendere tecniche moderne per la difesa dalle vulnerabilità. Ogni studente ottiene una macchina virtuale host Windows Server con due macchine virtuali annidate, una con l'immagine [Metasploitable3](https://github.com/rapid7/metasploitable3) e l'altra con l'immagine [Kali Linux](https://www.kali.org/). La macchina virtuale Metasploitable viene usata a scopo di exploit, mentre la macchina virtuale Kali fornisce l'accesso agli strumenti necessari per eseguire attività forensi.

Questo articolo ha due sezioni principali. La prima sezione illustra come creare il lab della classe. Nella seconda sezione viene illustrato come creare la macchina modello con la virtualizzazione annidata abilitata e con gli strumenti e le immagini necessari. In questo caso, un'immagine metasploitable e un'immagine Kali Linux in un computer con Hyper-V abilitato per ospitare le immagini.

## <a name="lab-configuration"></a>Configurazione del lab
Per configurare questo lab, è necessaria una sottoscrizione di Azure per iniziare. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare. Dopo aver ottenuto una sottoscrizione di Azure, è possibile creare un nuovo account lab in Azure Lab Services o usare un account esistente. Per la creazione di un nuovo account lab, vedere l'esercitazione seguente: [Esercitazione per l'installazione](tutorial-setup-lab-account.md)di un account lab.

Seguire [questa esercitazione](tutorial-setup-classroom-lab.md) per creare un nuovo lab e quindi applicare le impostazioni seguenti:Follow this tutorial to create a new lab and then apply the following settings:

| Dimensioni della macchina virtuale | Immagine |
| -------------------- | ----- | 
| Medio (virtualizzazione annidata) | Windows Server 2019 Datacenter |

## <a name="template-machine"></a>Macchina modello 

Dopo aver creato il computer modello, avviare la macchina e connettersi ad essa per completare le tre attività principali seguenti. 
 
1. Configurare la macchina per la virtualizzazione annidata. Abilita tutte le funzionalità di Windows appropriate, ad esempio Hyper-V, e configura la rete per le immagini Hyper-V per essere in grado di comunicare tra loro e con Internet.
2. Configurare l'immagine [Kali](https://www.kali.org/) Linux. Kali è una distribuzione Linux che include strumenti per test di penetrazione e controllo di sicurezza.
3. Impostare l'immagine metasploitable. Per questo esempio, verrà utilizzata l'immagine [Metasploitable3.](https://github.com/rapid7/metasploitable3) Questa immagine è stata creata per avere intenzionalmente vulnerabilità di sicurezza.

Uno script che automatizza le attività descritte in precedenza è disponibile all'indirizzo [Lab Services Ethical Hacking Scripts](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/EthicalHacking).

### <a name="prepare-template-machine-for-nested-virtualization"></a>Preparare la macchina modello per la virtualizzazione annidataPrepare template machine for nested virtualization
Seguire le istruzioni in [questo articolo](how-to-enable-nested-virtualization-template-vm.md) per preparare la macchina virtuale modello per la virtualizzazione annidata. 

### <a name="set-up-a-nested-virtual-machine-with-kali-linux-image"></a>Configurare una macchina virtuale nidificata con immagine Linux di KaliSet up a nested virtual machine with Kali Linux Image
Kali è una distribuzione Linux che include strumenti per test di penetrazione e controllo di sicurezza.

1. Scarica immagine [https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/](https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/)da .  
    1. Scarica **Il bit Hyper-V 64 di Kali Linux** per Hyper-V.
    1. Estrarre il file .7z.  Se non si dispone già di 7 [https://www.7-zip.org/download.html](https://www.7-zip.org/download.html)zip, scaricarlo da . Ricorda il percorso della cartella estratta come ti servirà in un secondo momento.
2. Aprire la console di gestione di **Hyper-V** da Strumenti di amministrazione.
1. Selezionare **Azione**, quindi **Importa macchina virtuale**. 
1. Nella pagina **Individua cartella** della procedura guidata Importa **macchina virtuale** scegliere il percorso della cartella estratta che contiene l'immagine Kali Linux.

    ![Finestra di dialogo Individua cartella](../media/class-type-ethical-hacking/locate-folder.png)
1. Nella pagina Seleziona macchina virtuale selezionare l'immagine Kali Linux.On the **Select Virtual Machine** page, select the Kali Linux image.  In questo caso, l'immagine è **kali-linux-2019.3-hyperv**.

    ![Selezionare l'immagine Kali](../media/class-type-ethical-hacking/select-kali-image.png)
1.  Nella pagina **Scegli tipo di importazione** selezionare Copia macchina virtuale **(creare un nuovo ID univoco)**.

    ![Scegliere il tipo di importazione](../media/class-type-ethical-hacking/choose-import-type.png)
1. Accettare le impostazioni predefinite nelle pagine **Scegli cartelle per i file della macchina virtuale** e Scegli cartelle per l'archiviazione dei **dischi rigidi virtuali,** quindi selezionare **Avanti**.
1. Nella pagina **Connetti rete** scegliere **LabServicesSwitch** creato in precedenza nella sezione Preparare il modello per la **virtualizzazione annidata** di questo articolo e quindi selezionare **Avanti**.

    ![Pagina Connetti rete](../media/class-type-ethical-hacking/connect-network.png)
1. Selezionare Fine nella pagina **Riepilogo.Select** **Finish** on the Summary page. Attendere il completamento delle operazioni di copia e importazione. La macchina virtuale Kali Linux sarà ora disponibile in Hyper-V.
1. Dalla **console di gestione di Hyper-V**scegliere**Inizio** **azione** -> , quindi **Connessione azione** -> **per** connettersi alla macchina virtuale.  
12. Il nome utente predefinito è `root` e la password è `toor`. 

    > [!NOTE]
    > Se è necessario sbloccare l'immagine, premere il tasto CTRL e trascinare il mouse verso l'alto.

## <a name="set-up-a-nested-vm-with-metasploitable-image"></a>Configurare una macchina virtuale annidata con Immagine metasploitableSet up a nested VM with Metasploitable Image  
L'immagine Metasploitable Rapid7 è un'immagine appositamente configurata con vulnerabilità della sicurezza. Utilizzerai questa immagine per testare e individuare i problemi. Le istruzioni seguenti mostrano come utilizzare un'immagine metasploibile creata in precedenza. Tuttavia, se è necessaria una versione più recente dell'immagine metasploibile, vedere [https://github.com/rapid7/metasploitable3](https://github.com/rapid7/metasploitable3).

1. Passare [https://information.rapid7.com/download-metasploitable-2017.html](https://information.rapid7.com/download-metasploitable-2017.html)a . Compila il modulo per scaricare l'immagine e seleziona il pulsante **Invia.**
1. Selezionare il pulsante Scarica metasploitable Now .Select the **Download Metasploitable Now** button.
1. Quando il file zip viene scaricato, estrarre il file zip e ricordare la posizione.
1. Convertire il file vmdk estratto in un file vhdx in modo da poterlo utilizzare con Hyper-V. A tale scopo, aprire PowerShell con privilegi amministrativi e passare alla cartella in cui risiede il file vmdk e seguire queste istruzioni:To do so, open PowerShell with administrative privileges and navigate to the folder where the vmdk file reside, and follow these instructions:
    1. Scaricare [Microsoft Virtual Machine Converter](https://www.microsoft.com/download/details.aspx?id=42497)ed eseguire il file mvmc_setup.msi quando richiesto.
    1. Importare il modulo PowerShell.  Il percorso predefinito in cui è installato il modulo è C:

        ```powershell
        Import-Module 'C:\Program Files\Microsoft Virtual Machine Converter\MvmcCmdlet.psd1'
        ```
    1. Convertire il vmdk in un file vhd che può essere utilizzato da Hyper-V. Questa operazione potrebbe richiedere alcuni minuti.
    
        ```powershell
        ConvertTo-MvmcVirtualHardDisk -SourceLiteralPath .\Metasploitable.vmdk -DestinationLiteralPath .\Metasploitable.vhdx -VhdType DynamicHardDisk -VhdFormat vhdx
        ```
    1. Copiare il file metasploitable.vhdx appena creato in C: 
1. Creare una nuova macchina virtuale Hyper-V.Create a new Hyper-V virtual machine.
    1. Aprire la console di gestione di **Hyper-V**.
    1. Scegliere **Azione** -> **nuova** -> macchina**virtuale**.
    1. Nella pagina **Prima di iniziare** della Creazione guidata nuova macchina **virtuale**fare clic su **Avanti**.
    1. Nella pagina **Specifica nome e percorso** immettere **Metasploitable** come **nome**e selezionare **Avanti**.

        ![Creazione guidata nuova immagine VM](../media/class-type-ethical-hacking/new-vm-wizard-1.png)
    1. Nella pagina **Specifica generazione** accettare le impostazioni predefinite e selezionare **Avanti**.
    1. Nella pagina **Assegna memoria** immettere **512 MB** per la memoria di **avvio**e selezionare **Avanti**. 

        ![Pagina Assegnazione memoria](../media/class-type-ethical-hacking/assign-memory-page.png)
    1. Nella pagina **Configura rete** lasciare la connessione **non connessione**. La scheda di rete verrà configurata in un secondo momento.
    1. Nella pagina **Connetti disco rigido virtuale** selezionare Usa un disco rigido virtuale **esistente.** Individuare il percorso del file **metasploitable.vhdx** creato nel passaggio precedente e selezionare **Avanti**. 

        ![Connetti pagina disco di rete virtuale](../media/class-type-ethical-hacking/connect-virtual-network-disk.png)
    1. Nella pagina **Completamento della Creazione guidata macchina virtuale** selezionare **Fine**.
    1. Dopo aver creato la macchina virtuale, selezionarla nella console di gestione di Hyper-V.Once the virtual machine is created, select it in the Hyper-V Manager. Non accendere ancora la macchina.  
    1. Scegliere**Impostazioni** **azione** -> .
    1. Nella finestra di dialogo **Impostazioni per Metasploitable** per selezionare **Aggiungi hardware**. 
    1. Selezionare **Scheda di rete legacy**e selezionare **Aggiungi**.

        ![Pagina Scheda di rete](../media/class-type-ethical-hacking/network-adapter-page.png)
    1. Nella pagina **Scheda di rete legacy** selezionare **LabServicesSwitch** per l'impostazione **Commutatore virtuale** e quindi **OK**. LabServicesSwitch è stato creato durante la preparazione del computer modello per Hyper-V nella sezione Preparare il modello per la **virtualizzazione annidata.**

        ![Pagina Scheda di rete legacy](../media/class-type-ethical-hacking/legacy-network-adapter-page.png)
    1. L'immagine metasploitable è ora pronta per l'uso. Dalla **console di gestione di Hyper-V**scegliere**Inizio** **azione** -> , quindi **Connessione azione** -> **per** connettersi alla macchina virtuale.  Il nome utente predefinito è **msfadmin** e la password è **msfadmin**. 


Il modello è ora aggiornato e ha le immagini necessarie per una classe di test di penetrazione di hacking etico, un'immagine con strumenti per fare il test di penetrazione e un'altra immagine con vulnerabilità di sicurezza da scoprire. L'immagine del modello può ora essere pubblicata nella classe. Selezionare il pulsante **Pubblica** nella pagina del modello per pubblicare il modello nel lab.
  

## <a name="cost"></a>Costi  
Se si desidera stimare il costo di questo lab, è possibile utilizzare l'esempio seguente: 
 
Per una classe di 25 studenti con 20 ore di tempo programmato e 10 ore di quota per compiti a casa o compiti, il prezzo per il laboratorio sarebbe: 

25 studenti ( 20 x 10) ore , 55 unità di laboratorio , 0,01 USD all'ora, 412,50 USD. 

Per altre informazioni sui prezzi, vedere Prezzi di [Servizi di laboratorio di Azure.](https://azure.microsoft.com/pricing/details/lab-services/)

## <a name="conclusion"></a>Conclusioni
Questo articolo ti ha seguito i passaggi per creare un laboratorio per la classe di hacking etico. Include i passaggi per configurare la virtualizzazione annidata per la creazione di due macchine virtuali all'interno della macchina virtuale host per i test penetranti.

## <a name="next-steps"></a>Passaggi successivi
I passaggi successivi sono comuni alla configurazione di qualsiasi lab:Next steps are common to setting up any lab:

- [Aggiungi utenti](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Imposta quota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Impostare una pianificazione](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Link di registrazione via e-mail agli studenti](how-to-configure-student-usage.md#send-invitations-to-users). 

