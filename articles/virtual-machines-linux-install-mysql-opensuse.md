<properties linkid="manage-linux-common-task-mysql-virtual-machine" urlDisplayName="Install MySQL" pageTitle="Install MySQL on a Linux virtual machine in Azure" metaKeywords="Azure vm OpenSUSE, Linux vm" description="Learn how to create an Azure virtual machine with OpenSUSE Linux, and then use SSH or PuTTY to install MySQL." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install MySQL on a virtual machine running OpenSUSE Linux in Azure" authors="timlt" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timlt"></tags>

# Installazione di MySQL in una macchina virtuale che esegue OpenSUSE Linux in Azure

[MySQL][MySQL] è un database SQL open source molto diffuso. Dalla raccolta immagini del [portale di gestione di Azure][portale di gestione di Azure], è possibile creare una macchina virtuale che esegue OpenSUSE Linux. Su tale macchina virtuale sarà quindi possibile installare e configurare un database MySQL.

In questa esercitazione si apprenderà come:

-   Utilizzare il portale di gestione per creare una macchina virtuale OpenSUSE Linux dalla raccolta.

-   Connettersi alla macchina virtuale tramite SSH o PuTTY.

-   Installare MySQL nella macchina virtuale.

## Creazione di una macchina virtuale che esegue OpenSUSE Linux

[WACOM.INCLUDE [create-and-configure-opensuse-vm-in-portal][create-and-configure-opensuse-vm-in-portal]]

## Installazione ed esecuzione di MySQL nella macchina virtuale

[WACOM.INCLUDE [install-and-run-mysql-on-opensuse-vm][install-and-run-mysql-on-opensuse-vm]]

## Riepilogo

In questa esercitazione è stato illustrato come creare una macchina virtuale OpenSUSE Linux e come stabilire una connessione remota a quest'ultima utilizzando SSH o PuTTY. È stato inoltre illustrato come installare e configurare MySQL nella macchina virtuale Linux. Per ulteriori informazioni su MySQL, vedere la [Documentazione di MySQL][Documentazione di MySQL].

  [MySQL]: http://www.mysql.com
  [portale di gestione di Azure]: http://manage.windowsazure.com
  [create-and-configure-opensuse-vm-in-portal]: ../includes/create-and-configure-opensuse-vm-in-portal.md
  [install-and-run-mysql-on-opensuse-vm]: ../includes/install-and-run-mysql-on-opensuse-vm.md
  [Documentazione di MySQL]: http://dev.mysql.com/doc/
