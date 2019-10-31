---
title: Configurare un Lab di scripting della shell Linux con Azure Lab Services | Microsoft Docs
description: Informazioni su come configurare un Lab per insegnare lo script della shell in Linux.
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
ms.openlocfilehash: 9502a81149df11ffa378bf8ffc2c377f4bb22d7f
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73177108"
---
# <a name="set-up-a-lab-to-teach-shell-scripting-on-linux"></a>Configurare un Lab per insegnare lo script della shell in Linux
Questo articolo illustra come configurare un Lab per insegnare lo script della shell in Linux. Lo scripting è una parte utile dell'amministrazione del sistema che consente agli amministratori di evitare attività ripetitive. In questo scenario di esempio, la classe illustra gli script bash tradizionali e gli script avanzati. Gli script avanzati sono script che combinano i comandi bash e Ruby. Questo approccio consente a Ruby di passare dati e comandi bash per interagire con la shell. 

Gli studenti che frequentano queste classi di scripting lavorano su una macchina virtuale Linux per apprendere le nozioni di base di Linux e acquisire familiarità con gli script della shell bash. La macchina virtuale Linux viene fornita con accesso tramite Desktop remoto abilitato e con gli editor di testo [gedit](https://help.gnome.org/users/gedit/stable/) e [Visual Studio Code](https://code.visualstudio.com/) installati.

## <a name="lab-configuration"></a>Configurazione del lab
Per configurare questo Lab, è necessaria una sottoscrizione di Azure per iniziare. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare. Quando si dispone di una sottoscrizione di Azure, è possibile creare un nuovo account Lab in Azure Lab Services o usare un account lab esistente. Vedere l'esercitazione seguente per la creazione di un nuovo account Lab: [esercitazione per la configurazione di un account Lab](tutorial-setup-lab-account.md).

Dopo aver creato l'account Lab, abilitare le impostazioni seguenti nell'account Lab: 

| Impostazione dell'account Lab | Istruzioni |
| ----------- | ------------ |  
| Immagini del Marketplace | Abilitare l'immagine di [Ubuntu Server 18,04 LTS](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.0001-com-ubuntu-server-bionic) per l'uso nell'account Lab. Per altre informazioni, vedere [specificare le immagini del Marketplace disponibili per gli autori del Lab](tutorial-setup-lab-account.md#specify-marketplace-images-available-to-lab-creators) | 

Seguire [questa esercitazione](tutorial-setup-classroom-lab.md) per creare un nuovo Lab e applicare le impostazioni seguenti:

| Impostazioni Lab | Valore/istruzioni | 
| ------------ | ------------------ |
| Dimensioni delle macchine virtuali (VM) | Small  |
| Immagine VM | [Ubuntu server 18,04 LTS](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.0001-com-ubuntu-server-bionic) |
| Abilita connessione Desktop remoto | Abilita. <p>L'abilitazione di questa impostazione consentirà agli insegnanti e agli studenti di connettersi alle macchine virtuali tramite Desktop remoto (RDP). Per ulteriori informazioni, vedere [Enable Remote Desktop for Linux Virtual Machines in a Lab in Azure Lab Services](how-to-enable-remote-desktop-linux.md#connect-to-the-template-vm). </p>|


## <a name="install-desktop-and-xrdp"></a>Installare desktop e xrdp
Per impostazione predefinita, per l'immagine di [Ubuntu Server 18,04 LTS](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.0001-com-ubuntu-server-bionic) non è installato Server Desktop remoto. Seguire le istruzioni riportate nell'articolo [installare e configurare Desktop remoto per connettersi a una VM Linux in Azure](../../virtual-machines/linux/use-remote-desktop.md) per installare i pacchetti necessari nel computer modello per la connessione tramite Remote Desktop Protocol.

## <a name="install-ruby"></a>Installare Ruby
Ruby è un linguaggio dinamico open source che può essere combinato con script bash. Questa sezione illustra come usare `apt-get` per installare la versione più recente di [Ruby](https://www.ruby-lang.org/).

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
In questa sezione viene illustrato come installare un paio di editor di testo. Gedit è l'editor di testo predefinito per l'ambiente desktop GNOME. È progettato come editor di testo per utilizzo generico. Visual Studio Code è un editor di testo che include il supporto per il debug e l'integrazione del controllo del codice sorgente.

> [!NOTE]
> Sono disponibili diversi editor di testo. Visual Studio Code e gedit sono solo due esempi.

1. Installare [gedit](https://help.gnome.org/users/gedit/stable/).

    ```bash
    sudo apt-get install gedit
    ```
1. Installare [Visual Studio Code](https://code.visualstudio.com/).  Visual Studio Code può essere installato usando l'archivio snap.  Per le opzioni di installazione alternative, vedere [Visual Studio Code Download alternativi](https://code.visualstudio.com/#alt-downloads).

    ```bash
    sudo snap install vscode --classic 
    ```

    Il modello è stato aggiornato e include il linguaggio di programmazione e gli strumenti di sviluppo necessari per completare il Lab. È ora possibile pubblicare l'immagine modello nel Lab. Selezionare il pulsante **pubblica** nella pagina modello per pubblicare il modello nel Lab.  

## <a name="cost"></a>Costo 
Se si desidera stimare il costo di questo Lab, è possibile utilizzare l'esempio seguente:
 
Per una classe di 25 studenti con 20 ore di tempo di classe pianificata e 10 ore di quota per il lavoro o le assegnazioni, il prezzo del Lab sarà: 

25 studenti * (20 + 10) ore * 20 unità Lab * 0,01 USD all'ora = 150 USD

Per ulteriori informazioni sui prezzi, vedere il documento seguente: [Azure Lab Services prezzi](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusione
Questo articolo illustra la procedura per creare un Lab per le classi di scripting. Sebbene questo articolo sia incentrato sulla configurazione di strumenti di scripting Ruby nel computer Linux, è possibile usare la stessa configurazione per altre classi di scripting come Python in Linux.

## <a name="next-steps"></a>Passaggi successivi
I passaggi successivi sono comuni alla configurazione di qualsiasi Lab:

- [Aggiungere utenti](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Imposta quota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Impostare una pianificazione](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Collegamenti per la registrazione tramite posta elettronica agli studenti](how-to-configure-student-usage.md#send-invitations-to-users). 





