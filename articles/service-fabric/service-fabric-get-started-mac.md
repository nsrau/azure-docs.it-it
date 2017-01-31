---
title: Configurare l&quot;ambiente di sviluppo in Mac OS X | Documentazione Microsoft
description: "Installare il runtime, l&quot;SDK e gli strumenti e creare un cluster di sviluppo locale. Al termine della configurazione, sarà possibile iniziare a creare applicazioni in Mac OS X."
services: service-fabric
documentationcenter: .net
author: seanmck
manager: timlt
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/27/2016
ms.author: seanmck
translationtype: Human Translation
ms.sourcegitcommit: d5a04e34a3f46097b3ad1b04ac6b2b845fc946af
ms.openlocfilehash: beb869fd5c1fcc19101b16149244187c37a6d6a3


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

* [Vagrant (versione 1.8.4 o successiva)](http://www.vagrantup.com/downloads.html)
* [VirtualBox](http://www.virtualbox.org/wiki/Downloads)

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

## <a name="install-the-service-fabric-plugin-for-eclipse-neon-optional"></a>Installare il plug-in Service Fabric per Eclipse Neon (facoltativo)
Service Fabric offre un plug-in per l'IDE di Eclipse Neon che può semplificare il processo di creazione e distribuzione di servizi Java.

1. In Eclipse assicurarsi che sia installata la versione 1.0.17 di Buildship o una versione successiva. Per controllare le versioni dei componenti installati, scegliere **? > Dettagli installazione**. È possibile aggiornare Buildship seguendo le istruzioni riportate [qui][buildship-update].
2. Per installare il plug-in Service Fabric, scegliere **Help > Install New Software...** (? > Installa nuovo software...).
3. Nella casella di testo "Work with" (Usa) immettere: http://dl.windowsazure.com/eclipse/servicefabric.
4. Fare clic su Aggiungi.

    ![Plug-in Eclipse Neon per Service Fabric][sf-eclipse-plugin-install]
5. Scegliere il plug-in Service Fabric e fare clic su Next (Avanti).
6. Procedere con l'installazione e accettare il contratto di licenza con l'utente finale.

## <a name="next-steps"></a>Passaggi successivi
<!-- Links -->

* [Creare la prima applicazione di Service Fabric per Linux](service-fabric-create-your-first-linux-application-with-java.md)
* [Creare un cluster di Service Fabric nel portale di Azure](service-fabric-cluster-creation-via-portal.md)
* [Creare un cluster di Service Fabric con Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
* [Informazioni sul modello applicativo di Service Fabric](service-fabric-application-model.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship



<!--HONumber=Jan17_HO1-->


