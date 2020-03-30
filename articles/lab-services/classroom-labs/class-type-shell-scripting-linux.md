---
title: Configurare un laboratorio di scripting della shell di Linux con Azure Lab Services . Documenti Microsoft
description: Informazioni su come configurare un lab per insegnare gli script della shell su Linux.Learn how to set up a lab to teach shell scripting on Linux.
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
ms.date: 09/30/2019
ms.author: spelluru
ms.openlocfilehash: 100a485588c77f6977001dae984b30ebcb1de557
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77443551"
---
# <a name="set-up-a-lab-to-teach-shell-scripting-on-linux"></a>Configurare un lab per insegnare gli script della shell su LinuxSet up a lab to teach shell scripting on Linux
Questo articolo illustra come configurare un lab per insegnare gli script della shell su Linux.This article shows you how to set up a lab to teach shell scripting on Linux. Lo scripting è una parte utile dell'amministrazione del sistema che consente agli amministratori di evitare attività ripetitive. In questo scenario di esempio, la classe illustra gli script bash tradizionali e gli script avanzati. Gli script avanzati sono script che combinano i comandi bash e Ruby. Questo approccio consente a Ruby di passare dati e comandi bash per interagire con la shell. 

Gli studenti che frequentano queste classi di scripting lavorano su una macchina virtuale Linux per apprendere le nozioni di base di Linux e acquisire familiarità con gli script della shell bash. La macchina virtuale Linux viene fornita con accesso tramite Desktop remoto abilitato e con gli editor di testo [gedit](https://help.gnome.org/users/gedit/stable/) e [Visual Studio Code](https://code.visualstudio.com/) installati.

## <a name="lab-configuration"></a>Configurazione del lab
Per configurare questo lab, è necessaria una sottoscrizione di Azure per iniziare. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare. Dopo aver creato una sottoscrizione di Azure, è possibile creare un nuovo account lab in Azure Lab Services o usare un account lab esistente. Per la creazione di un nuovo account lab, vedere l'esercitazione seguente: [Esercitazione sull'installazione di un account lab](tutorial-setup-lab-account.md).

Dopo aver creato l'account lab, abilitare le impostazioni seguenti nell'account lab: 

| Impostazione dell'account lab | Istruzioni |
| ----------- | ------------ |  
| Immagini del Marketplace | Abilitare l'immagine [LTS di Ubuntu Server 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.0001-com-ubuntu-server-bionic) per l'utilizzo all'interno dell'account lab. Per ulteriori informazioni, consultate [Specificare le immagini Marketplace disponibili per i creatori di laboratori.](specify-marketplace-images.md) | 

Seguire [questa esercitazione](tutorial-setup-classroom-lab.md) per creare un nuovo lab e applicare le impostazioni seguenti:Follow this tutorial to create a new lab and apply the following settings:

| Impostazioni lab | Valore/istruzioni | 
| ------------ | ------------------ |
| Dimensioni della macchina virtuale (VM)Virtual machine (VM) size | Piccolo  |
| Immagine della macchina virtualeVM image | [Ubuntu Server 18.04 LTS](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.0001-com-ubuntu-server-bionic) |
| Abilitare la connessione desktop remoto | Abilita. <p>L'abilitazione di questa impostazione consentirà a insegnanti e studenti di connettersi alle proprie macchine virtuali usando il desktop remoto (RDP). Per altre informazioni, vedere [Abilitare il desktop remoto per le macchine virtuali Linux in un lab in Azure Lab Services.For more](how-to-enable-remote-desktop-linux.md#connect-to-the-template-vm)information, see Enable remote desktop for Linux virtual machines in a lab in Azure Lab Services . </p>|


## <a name="install-desktop-and-xrdp"></a>Installare desktop e xrdp
L'immagine [Di Ubuntu Server 18.04 LTS](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.0001-com-ubuntu-server-bionic) non dispone di server desktop remoto installato per impostazione predefinita. Seguire le istruzioni nell'articolo [Installare e configurare Desktop remoto per connettersi a una macchina virtuale Linux in Azure](../../virtual-machines/linux/use-remote-desktop.md) per installare i pacchetti necessari nel computer modello per la connessione tramite il protocollo di desktop remoto.

## <a name="install-ruby"></a>Installare Ruby
Ruby è un linguaggio dinamico open source che può essere combinato con script bash. In questa sezione `apt-get` viene illustrato come utilizzare per installare la versione più recente di [Ruby](https://www.ruby-lang.org/).

1. Installare gli aggiornamenti eseguendo i comandi seguenti:

    ```bash
    sudo apt-get update 
    sudo apt-get upgrade 
    ```
2.  Installare [Ruby](https://www.ruby-lang.org/).  Ruby è un linguaggio dinamico open source che può essere combinato con script bash. 
    
    ```bash
    sudo apt-get install ruby-full
    ```

## <a name="install-development-tools"></a>Installare gli strumenti di sviluppo
In questa sezione viene illustrato come installare un paio di editor di testo. Gedit è l'editor di testo predefinito per l'ambiente desktop gnome. È progettato come un editor di testo generico. Visual Studio Code è un editor di testo che include il supporto per il debug e l'integrazione del controllo del codice sorgente.

> [!NOTE]
> Sono disponibili diversi editor di testo. Codice di Visual Studio e gedit sono solo due esempi.

1. Installare [gedit](https://help.gnome.org/users/gedit/stable/).

    ```bash
    sudo apt-get install gedit
    ```
1. Installare [Visual Studio Code](https://code.visualstudio.com/).  Il codice di Visual Studio può essere installato utilizzando Snap Store.  Per ulteriori opzioni di installazione, vedere [Download alternativi di Visual Studio Code](https://code.visualstudio.com/#alt-downloads).

    ```bash
    sudo snap install vscode --classic 
    ```

    Il modello è stato aggiornato e dispone sia del linguaggio di programmazione che degli strumenti di sviluppo necessari per completare il lab. L'immagine del modello può ora essere pubblicata nel lab. Selezionare il pulsante **Pubblica** nella pagina del modello per pubblicare il modello nel lab.  

## <a name="cost"></a>Costi 
Se si desidera stimare il costo di questo lab, è possibile utilizzare l'esempio seguente:
 
Per una classe di 25 studenti con 20 ore di tempo programmato e 10 ore di quota per compiti a casa o compiti, il prezzo per il laboratorio sarebbe: 

25 studenti : 20 ore 10 : 20 Unità lab , 0,01 USD all'ora, 150 USD

Per altre informazioni sui prezzi, vedere il documento seguente: Prezzi di [Servizi di laboratorio](https://azure.microsoft.com/pricing/details/lab-services/)di Azure .

## <a name="conclusion"></a>Conclusioni
In questo articolo sono stati illustrati i passaggi per creare un laboratorio per le classi di scripting. Mentre questo articolo si è concentrato sulla configurazione di strumenti di scripting Ruby su computer Linux, la stessa configurazione può essere utilizzata per altre classi di scripting come Python su Linux.

## <a name="next-steps"></a>Passaggi successivi
I passaggi successivi sono comuni alla configurazione di qualsiasi lab:Next steps are common to setting up any lab:

- [Aggiungi utenti](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Imposta quota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Impostare una pianificazione](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Link di registrazione via e-mail agli studenti](how-to-configure-student-usage.md#send-invitations-to-users). 





