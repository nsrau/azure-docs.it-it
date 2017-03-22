---
title: Configurare l&quot;ambiente di sviluppo in Mac OS X | Microsoft Docs
description: "Installare il runtime, l&quot;SDK e gli strumenti e creare un cluster di sviluppo locale. Al termine della configurazione, sarà possibile iniziare a creare applicazioni in Mac OS X."
services: service-fabric
documentationcenter: java
author: saysa
manager: raunakp
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/27/2016
ms.author: saysa
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: fc73eedae7ec9664da714567f47a543e625cd023
ms.lasthandoff: 03/11/2017


---
# <a name="set-up-your-development-environment-on-mac-os-x"></a>Configurare l'ambiente di sviluppo in Mac OS X
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

È possibile creare applicazioni di Service Fabric da eseguire in cluster Linux usando Mac OS X. Questo articolo illustra come configurare il computer Mac per lo sviluppo.

## <a name="prerequisites"></a>Prerequisiti
Service Fabric non viene eseguito in modo nativo in OS X. Per eseguire un cluster di Service Fabric locale, viene creata una macchina virtuale Ubuntu preconfigurata con Vagrant e VirtualBox. Prima di iniziare, sono necessari:

* [Vagrant (versione&1;.8.4 o successiva)](http://www.vagrantup.com/downloads.html)
* [VirtualBox](http://www.virtualbox.org/wiki/Downloads)

>[!NOTE]
>  È necessario usare versioni con supporto reciproco di Vagrant e VirtualBox. Vagrant potrebbe non funzionare correttamente in una versione di VirtualBox non supportata.
>

## <a name="create-the-local-vm"></a>Creare la VM locale
Per creare la VM locale contenente un cluster di Service Fabric a 5 nodi, seguire questa procedura:

1. Clonare il repository **Vagrantfile**

    ```bash
    git clone https://github.com/azure/service-fabric-linux-vagrant-onebox.git
    ```
2. Passare al clone locale del repository

    ```bash
    cd service-fabric-linux-vagrant-onebox
    ```
3. (Facoltativo) Modificare le impostazioni predefinite della VM

    Per impostazione predefinita, la VM locale è configurata come segue:

   * 3 GB di memoria allocati
   * Rete host privata configurata all'indirizzo IP 192.168.50.50 che consente il pass-through del traffico dall'host Mac

     È possibile modificare una di queste impostazioni o aggiungere altre opzioni di configurazione della VM in Vagrantfile. Per l'elenco completo delle opzioni di configurazione, vedere la [documentazione di Vagrant](http://www.vagrantup.com/docs) .
4. Creare la VM

    ```bash
    vagrant up
    ```

   In questo passaggio viene scaricata e avviata in locale l'immagine di VM preconfigurata e vi viene quindi configurato un cluster di Service Fabric locale. È probabile che questo passaggio richieda alcuni minuti. Se la configurazione ha esito positivo, nell'output verrà visualizzato un messaggio che indica che è in corso l'avvio del cluster.

    ![Avvio della configurazione del cluster dopo il provisioning della VM][cluster-setup-script]

5. Verificare che il cluster sia stato configurato correttamente passando a Service Fabric Explorer all'indirizzo http://192.168.50.50:19080/Explorer, presupponendo che sia stato mantenuto l'IP predefinito della rete privata.

    ![Visualizzazione di Service Fabric Explorer dal Mac host][sfx-mac]

## <a name="install-the-service-fabric-plugin-for-eclipse-neon"></a>Installare il plug-in Service Fabric per Eclipse Neon

Service Fabric offre un plug-in per **Eclipse Neon per l'ambiente IDE Java** che può semplificare il processo di creazione, compilazione e distribuzione di servizi Java. È possibile seguire la procedura di installazione illustrata in questa [documentazione](service-fabric-get-started-eclipse.md#install-or-update-service-fabric-plugin-on-eclipse-neon) generale sull'installazione e l'aggiornamento del plug-in Service Fabric Eclipse.

## <a name="using-service-fabric-eclipse-plugin-on-mac"></a>Uso del plug-in Service Fabric Eclipse in Mac

Assicurarsi di aver completato la procedura illustrata nella [documentazione del plug-in Service Fabric Eclipse](service-fabric-get-started-eclipse.md). La procedura per la creazione, la compilazione e la distribuzione di applicazioni Java di Service Fabric usando un contenitore guest Vagrant in un host Mac è molto simile a quella illustrata nella documentazione generale, a parte alcuni punti che è necessario tenere presente, come indicato di seguito:
* Dal momento che le librerie di Service Fabric saranno necessarie per la corretta compilazione dell'applicazione Java di Service Fabric, il progetto Eclipse deve essere creato in un percorso condiviso. Per impostazione predefinita, il contenuto nel percorso dell'host in cui esiste ``Vagrantfile`` è condiviso con il percorso ``/vagrant`` nel guest.
* Più semplicemente, se ``Vagrantfile`` si trova nel percorso``~/home/john/allprojects/``, ad esempio, è necessario creare il progetto di Service Fabric ``MyActor`` nel percorso ``~/home/john/allprojects/MyActor`` e il percorso dell'area di lavoro di Eclipse sarà ``~/home/john/allprojects``.

## <a name="next-steps"></a>Passaggi successivi
<!-- Links -->
* [Creare e distribuire la prima applicazione Java di Service Fabric in Linux usando Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Create and deploy your first Service Fabric Java application on Linux using Service Fabric Plugin for Eclipse](service-fabric-get-started-eclipse.md) (Creare e distribuire la prima applicazione Java di Service Fabric in Linux usando il plug-in Service Fabric per Eclipse)
* [Creare un cluster di Service Fabric nel portale di Azure](service-fabric-cluster-creation-via-portal.md)
* [Creare un cluster di Service Fabric con Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
* [Informazioni sul modello applicativo di Service Fabric](service-fabric-application-model.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

