<properties urlDisplayName="Install MongoDB" pageTitle="Installare MongoDB in una macchina virtuale che esegue CentOS Linux in Azure" metaKeywords="Azure, MongoDB" description="Informazioni su come installare Mongo DB in una macchina virtuale in Azure." metaCanonical="" services="" documentationCenter="" title="Installare MongoDB in una macchina virtuale che esegue CentOS Linux in Azure" authors="timlt" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timlt" />

# Installazione di MySQL in una macchina virtuale che esegue OpenSUSE Linux in Azure

[MySQL][MySQL] è un database SQL open source molto diffuso. Dalla raccolta immagini del [portale di gestione di Azure][portale di gestione di Azure], è possibile creare una macchina virtuale che esegue OpenSUSE Linux. Su tale macchina virtuale sarà quindi possibile installare e configurare un database MySQL.

In questa esercitazione si apprenderà come:

-   Usare il portale di gestione per creare una macchina virtuale OpenSUSE Linux dalla raccolta.
-   Connettersi alla macchina virtuale tramite SSH o PuTTY.
-   Installare MySQL nella macchina virtuale.

## Creare una sottoscrizione di Azure

Se non si dispone già di una sottoscrizione di Azure, è possibile iscriversi [gratuitamente][gratuitamente]. Dopo l'iscrizione, eseguire la procedura seguente per continuare con questa esercitazione.

[WACOM.INCLUDE [antares-iaas-signup-iaas](../includes/antares-iaas-signup-iaas.md)]

## Creazione di una macchina virtuale che esegue OpenSUSE Linux

[WACOM.INCLUDE [create-and-configure-opensuse-vm-in-portal](../includes/create-and-configure-opensuse-vm-in-portal.md)]

## Installazione ed esecuzione di MySQL nella macchina virtuale

[WACOM.INCLUDE [install-and-run-mysql-on-opensuse-vm](../includes/install-and-run-mysql-on-opensuse-vm.md)]

## Riepilogo

In questa esercitazione è stato illustrato come creare una macchina virtuale che esegue OpenSUSE Linux e come stabilire una connessione remota a quest'ultima usando SSH o PuTTY. È stato inoltre illustrato come installare e configurare MySQL nella macchina virtuale Linux. Per altre informazioni su MySQL, vedere la [Documentazione di MySQL][Documentazione di MySQL].

  [MySQL]: http://www.mysql.com
  [portale di gestione di Azure]: http://manage.windowsazure.com
  [gratuitamente]: http://azure.microsoft.com
  [Documentazione di MySQL]: http://dev.mysql.com/doc/
