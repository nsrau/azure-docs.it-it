---
title: Abilitare Desktop remoto grafico per Linux in Azure Lab Services | Microsoft Docs
description: Informazioni su come abilitare Desktop remoto per le macchine virtuali Linux in un lab Azure Lab Services.
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
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: cc59cd40701957591faba3fb91c2596bc92e21e3
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83701750"
---
# <a name="enable-graphical-remote-desktop-for-linux-virtual-machines-in-azure-lab-services"></a>Abilitare Desktop remoto grafico per le macchine virtuali Linux in Azure Lab Services
Questo articolo illustra come eseguire le attività seguenti:

- Abilitare sessioni di Desktop remoto grafico per una macchina virtuale Linux
- Come connettersi a una macchina virtuale Linux mediante i client Desktop remoto RDP (Remote Desktop Protocol) o X2Go

## <a name="set-up-graphical-remote-desktop-solution"></a>Configurare una soluzione Desktop remoto grafico
Quando viene creato un lab da un'immagine **Linux**, l'accesso **SSH** (Secure Shell) viene configurato automaticamente in modo che il docente possa connettersi alla macchina virtuale modello dalla riga di comando tramite SSH.  Analogamente, quando viene pubblicata la macchina virtuale modello, gli studenti possono connettersi alle macchine virtuali tramite SSH.

Per connettersi a una macchina virtuale Linux mediante **GUI** (interfaccia utente grafica), è consigliabile usare **RDP** o **X2Go**.  Per entrambe le opzioni è necessario che l'insegnante esegua altre operazioni di configurazione nella macchina virtuale modello:

### <a name="rdp-setup"></a>Configurazione di RDP
Per usare RDP, il docente deve:
  - Abilitare una Connessione Desktop remoto. Questa operazione è necessaria in modo specifico per aprire la porta della macchina virtuale per RDP.
  - Installare il server Desktop remoto RDP.
  - Installare un ambiente desktop grafico Linux, ad esempio MATE, XFCE e così via.

### <a name="x2go-setup"></a>Configurazione di X2Go
Per usare X2Go, il docente deve:
- Installare il server Desktop remoto X2Go.
- Installare un ambiente desktop grafico Linux, ad esempio MATE, XFCE e così via.

X2Go usa la stessa porta già abilitata per SSH.  Di conseguenza, non è necessaria alcuna configurazione aggiuntiva per aprire una porta sulla macchina virtuale per X2Go.

> [!NOTE]
> In alcuni casi, ad esempio con Ubuntu LTS 18.04, X2Go offre prestazioni migliori.  Se si usa RDP e si nota latenza durante l'interazione con l'ambiente desktop grafico, provare con X2Go, in quanto potrebbe migliorare le prestazioni.

> [!IMPORTANT]
>  In alcune immagini del marketplace sono già installati un ambiente desktop grafico e un server Desktop remoto.  Ad esempio, in [Data Science Virtual Machine per Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804), l'[ambiente XFCE e il server X2Go sono già installati e configurati per accettare connessioni client](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro#x2go).

## <a name="enable-remote-desktop-connection-for-rdp"></a>Abilitare Connessione desktop remoto per RDP

Questo passaggio è necessario solo per la connessione tramite RDP.  Se invece si prevede di usare X2Go, è possibile passare alla sezione successiva, poiché X2Go usa la porta SSH.

1.  Durante la creazione del Lab, il docente ha la possibilità di **abilitare Connessione Desktop remoto**.  Il docente deve **abilitare** questa opzione per aprire la porta nella macchina virtuale Linux necessaria per una sessione Desktop remoto RDP.  In caso contrario, se questa opzione rimane **disabilitata**, viene aperta solo la porta per SSH.
  
    ![Abilitare Connessione desktop remoto per un'immagine Linux](../media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

2. Nella finestra di messaggio **Abilitazione di Connessione Desktop remoto** selezionare **Continua con Desktop remoto**. 

    ![Abilitare Connessione desktop remoto per un'immagine Linux](../media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

## <a name="install-rdp-or-x2go"></a>Installare RDP o X2Go

Dopo la creazione del lab, il docente deve assicurarsi che nella macchina virtuale modello siano installati un ambiente desktop grafico e un server Desktop remoto.  I docenti devono innanzitutto connettersi alla macchina virtuale modello mediante SSH per installare i pacchetti per:
- Il server Desktop remoto RDP o X2Go.
- Un ambiente desktop grafico, ad esempio MATE, XFCE e così via.

Al termine della configurazione, il docente può connettersi alla macchina virtuale modello usando il client **Desktop remoto Microsoft (RDP)** o **X2Go**.

Attenersi ai passaggi seguenti per configurare la macchina virtuale modello:

1. Se si visualizza **Personalizza modello** sulla barra degli strumenti, selezionarlo. Selezionare quindi **Continua** nella finestra di dialogo **Personalizza modello**. Questa azione avvia la macchina virtuale modello.  

    ![Personalizzare il modello](../media/how-to-enable-remote-desktop-linux/customize-template.png)
1. Dopo l'avvio della macchina virtuale modello, è possibile selezionare **Connetti modello** e poi **Connettiti tramite SSH** sulla barra degli strumenti. 

    ![Connettersi al modello tramite RDP dopo la creazione del lab](../media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 
1. Si visualizza la finestra di dialogo **Connetti alla tua macchina virtuale** seguente. Selezionare il pulsante **Copia** accanto alla casella di testo per copiarlo negli Appunti. Salvare le informazioni di connessione SSH. Usare queste informazioni di connessione da un terminale SSH, ad esempio [PuTTY](https://www.putty.org/), per connettersi alla macchina virtuale.
 
    ![Stringa di connessione SSH](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

4. Installare RDP o X2Go insieme all'ambiente desktop grafico di propria scelta.  Fare riferimento alle istruzioni seguenti:
    - [Installare e configurare RDP](https://docs.microsoft.com/azure/virtual-machines/linux/use-remote-desktop)
    - [Installare e configurare X2Go](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/X2GoRemoteDesktop)

## <a name="connect-to-the-template-vm-via-the-gui"></a>Connettersi alla macchina virtuale modello mediante GUI

Al termine della configurazione della macchina virtuale modello, il docente può connettersi mediante GUI usando il client **Desktop remoto Microsoft (RDP)** o **X2Go**.  Il client usato dipende dal server Desktop remoto (RDP o X2Go) configurato nella macchina virtuale modello.  

### <a name="microsoft-remote-desktop-rdp-client"></a>Client Desktop remoto Microsoft (RDP)

Il client Desktop remoto Microsoft (RDP) viene usato per connettersi a una macchina virtuale modello con RDP configurato.  Il client Desktop remoto può essere usato su sistemi Windows, Chromebook, Mac e altro ancora.  Per altre informazioni, fare riferimento all'articolo sui [client Desktop remoto](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients).

Attenersi alla procedura seguente in base al tipo di computer usato per connettersi alla macchina virtuale modello:

- Windows
  1. Fare clic su **Connetti al modello** sulla barra degli strumenti del lab e selezionare **Connetti tramite RDP** per connettersi alla macchina virtuale modello. 
  1. Salvare il file RDP e usarlo per connettersi alla macchina virtuale modello tramite il client Desktop remoto. 
  1. In genere, il client Desktop remoto è già installato e configurato in Windows.  Di conseguenza, è sufficiente fare clic sul file RDP per aprirlo e avviare la sessione remota.

- Mac
  1. Fare clic su **Connetti al modello** sulla barra degli strumenti del lab e selezionare **Connetti tramite RDP** per salvare il file RDP.  
  1. Fare quindi riferimento all'articolo sulla procedura per [connettersi a una macchina virtuale mediante RDP su un Mac](connect-virtual-machine-mac-remote-desktop.md).

- Chromebook
  1. Fare clic su **Connetti al modello** sulla barra degli strumenti del lab e selezionare **Connetti tramite RDP** per salvare il file RDP.  
  1. Fare quindi riferimento all'articolo sulla procedura per [connettersi a una macchina virtuale mediante RDP su un Chromebook](connect-virtual-machine-chromebook-remote-desktop.md).

### <a name="x2go-client"></a>Client X2Go

Il client X2Go viene usato per connettersi a una macchina virtuale modello con X2Go configurato.  Usando le informazioni di connessione SSH della macchina virtuale modello, seguire le indicazioni nell'articolo sulla procedura per [connettersi a una macchina virtuale mediante X2Go](how-to-use-remote-desktop-linux-student.md#connect-to-the-student-vm-using-x2go).

## <a name="next-steps"></a>Passaggi successivi
Dopo che un docente ha configurato RDP o X2Go nella macchina virtuale modello ed esegue la pubblicazione, gli studenti possono connettersi alle macchine virtuali tramite la GUI del Desktop remoto o SSH.

Per altre informazioni, vedere:
 - [Connect to a Linux VM](how-to-use-remote-desktop-linux-student.md) (Connettersi a una macchina virtuale Linux)
