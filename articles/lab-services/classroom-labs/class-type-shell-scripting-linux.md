---
title: Configurare un lab sulla creazione di scripting per la shell Linux con Azure Lab Services | Microsoft Docs
description: Informazioni su come configurare un lab per insegnare la creazione di script per la shell in Linux.
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
ms.openlocfilehash: 66b325eb1d268fdd5b1052a0da84c603186edf65
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83589500"
---
# <a name="set-up-a-lab-to-teach-shell-scripting-on-linux"></a>Configurare un lab per insegnare la creazione di script per la shell in Linux
Questo articolo illustra come configurare un lab per insegnare a creare script per la shell in Linux. La creazione di script è una parte utile dell'amministrazione del sistema che consente agli amministratori di evitare attività ripetitive. In questo scenario di esempio, la classe illustra gli script bash tradizionali e gli script avanzati. Gli script avanzati sono script che combinano i comandi bash e Ruby. Questo approccio consente a Ruby di passare dati e comandi bash per interagire con la shell. 

Gli studenti che frequentano queste classi di scripting lavorano su una macchina virtuale Linux per apprendere le nozioni di base di Linux e acquisire familiarità con gli script della shell bash. La macchina virtuale Linux viene fornita con accesso tramite Desktop remoto abilitato e con gli editor di testo [gedit](https://help.gnome.org/users/gedit/stable/) e [Visual Studio Code](https://code.visualstudio.com/) installati.

## <a name="lab-configuration"></a>Configurazione del lab
Per configurare questo lab, è necessaria una sottoscrizione di Azure per iniziare. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare. Quando è disponibile una sottoscrizione di Azure, è possibile creare un nuovo account lab in Azure Lab Services o usare un account lab esistente. Per la creazione di un nuovo account lab, vedere l'esercitazione seguente: [Esercitazione per configurare un account lab](tutorial-setup-lab-account.md).

Dopo aver creato l'account lab, abilitare le impostazioni seguenti nell'account lab: 

| Impostazione dell'account lab | Istruzioni |
| ----------- | ------------ |  
| Immagini del Marketplace | Abilitare l'immagine di Ubuntu server 18.04 LTS per l'uso nell'account lab. Per altre informazioni, vedere [Specificare le immagini del Marketplace disponibili per gli autori di lab](specify-marketplace-images.md). | 

Seguire [questa esercitazione](tutorial-setup-classroom-lab.md) per creare un nuovo lab e applicare le impostazioni seguenti:

| Impostazioni del lab | Valore/istruzioni | 
| ------------ | ------------------ |
| Dimensioni della macchina virtuale | Piccolo  |
| Immagine della macchina virtuale | Ubuntu Server 18.04 LTS |
| Abilita Connessione Desktop remoto | Abilita. <p>L'abilitazione di questa impostazione consentirà a docenti e studenti di connettersi alle macchine virtuali usando Desktop remoto (RDP). Per altre informazioni, vedere [Abilitare Desktop remoto per le macchine virtuali Linux in un lab Azure Lab Services](how-to-enable-remote-desktop-linux.md#connect-to-the-template-vm). </p>|


## <a name="install-desktop-and-xrdp"></a>Installare desktop e xrdp
Per impostazione predefinita, l'immagine di Ubuntu Server 18.04 LTS non include l'installazione del server di Desktop remoto. Per installare i pacchetti necessari nel computer modello per la connessione tramite Remote Desktop Protocol, seguire le istruzioni riportate in [Installare e configurare Desktop remoto per connettersi a una VM Linux di Azure](../../virtual-machines/linux/use-remote-desktop.md).

## <a name="install-ruby"></a>Installare Ruby
Ruby è un linguaggio dinamico open source che può essere combinato con script bash. Questa sezione illustra come usare `apt-get` per installare la versione più recente di [Ruby](https://www.ruby-lang.org/).

1. Per installare gli aggiornamenti, eseguire i comandi seguenti:

    ```bash
    sudo apt-get update 
    sudo apt-get upgrade 
    ```
2.  Installare [Ruby](https://www.ruby-lang.org/).  Ruby è un linguaggio dinamico open source che può essere combinato con script bash. 
    
    ```bash
    sudo apt-get install ruby-full
    ```

## <a name="install-development-tools"></a>Installare gli strumenti di sviluppo
Questa sezione illustra come installare un paio di editor di testo. gedit è l'editor di testo predefinito per l'ambiente desktop GNOME. È progettato come editor di testo per utilizzo generico. Visual Studio Code è un editor di testo che include il supporto per il debug e l'integrazione del controllo del codice sorgente.

> [!NOTE]
> Sono disponibili numerosi editor di testo. Visual Studio Code e gedit sono solo due esempi.

1. Installare [gedit](https://help.gnome.org/users/gedit/stable/).

    ```bash
    sudo apt-get install gedit
    ```
1. Installare [Visual Studio Code](https://code.visualstudio.com/).  Visual Studio Code può essere installato tramite Snap Store.  Per le opzioni di installazione alternative, vedere [Download alternativi di Visual Studio Code](https://code.visualstudio.com/#alt-downloads).

    ```bash
    sudo snap install vscode --classic 
    ```

    Il modello è ora aggiornato e include il linguaggio di programmazione e gli strumenti di sviluppo necessari per completare il lab. È ora possibile pubblicare l'immagine modello nel lab. Selezionare il pulsante **Pubblica** nella pagina del modello per pubblicare il modello nel lab.  

## <a name="cost"></a>Costi 
Se si vuole stimare il costo di questo lab, è possibile usare l'esempio seguente:
 
Per una classe di 25 studenti con 20 ore di lezioni pianificate e 10 ore di quota per compiti o assegnazioni, il prezzo del lab sarà dato dalla formula seguente: 

25 studenti * (20 + 10) ore * 139 unità lab * 0,01 USD all'ora = 1042,5 USD

Per altre informazioni sui prezzi, vedere il documento seguente: [Prezzi di Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusioni
In questo articolo è stata illustrata la procedura per creare un lab per le classi di scripting. Anche se questo articolo è incentrato sulla configurazione di strumenti di scripting di Ruby in un computer Linux, è possibile usare la stessa configurazione per altre classi di scripting come Python in Linux.

## <a name="next-steps"></a>Passaggi successivi
I passaggi successivi sono comuni alla configurazione di qualsiasi lab:

- [Aggiungere utenti](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Impostare la quota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Impostare una pianificazione](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Inviare i collegamenti di registrazione agli studenti tramite posta elettronica](how-to-configure-student-usage.md#send-invitations-to-users). 





