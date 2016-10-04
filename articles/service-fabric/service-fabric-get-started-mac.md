<properties
   pageTitle="Configurare l'ambiente di sviluppo in Mac OS X | Microsoft Azure"
   description="Installare il runtime, l'SDK e gli strumenti e creare un cluster di sviluppo locale. Al termine della configurazione, sarà possibile iniziare a creare applicazioni in Mac OS X."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/25/2016"
   ms.author="seanmck"/>

# Configurare l'ambiente di sviluppo in Mac OS X

> [AZURE.SELECTOR]
-[ Windows](service-fabric-get-started.md)
- [Linux](service-fabric-get-started-linux.md)
- [OSX](service-fabric-get-started-mac.md)

È possibile creare applicazioni di Service Fabric da eseguire in cluster Linux usando Mac OS X. Questo articolo illustra come configurare il computer Mac per lo sviluppo.

## Prerequisiti

Service Fabric non viene eseguito in modo nativo in OS X. Per eseguire un cluster di Service Fabric locale, viene creata una macchina virtuale Ubuntu preconfigurata con Vagrant e VirtualBox. Prima di iniziare, sono necessari:

- [Vagrant (versione 1.8.4 o successiva)](http://wwww.vagrantup.com/downloads)
- [VirtualBox](http://www.virtualbox.org/wiki/Downloads)

## Creare la VM locale

Per creare la VM locale contenente un cluster di Service Fabric a 5 nodi, eseguire queste operazioni:

1. Clonare il repository Vagrantfile

    ```bash
    git clone https://github.com/azure/service-fabric-linux-vagrant-onebox.git
    ```

2. Passare al clone locale del repository

    ```bash
    cd service-fabric-linux-vagrant-onebox
    ```

3. (Facoltativo) Modificare le impostazioni predefinite della VM

    Per impostazione predefinita, la VM locale è configurata come segue:

    - 3 GB di memoria allocati
    - Rete host privata configurata all'indirizzo IP 192.168.50.50 che consente il pass-through del traffico dall'host Mac

    È possibile modificare una di queste impostazioni o aggiungere altre opzioni di configurazione della VM in Vagrantfile. Per l'elenco completo delle opzioni di configurazione, vedere la [documentazione di Vagrant](http://www.vagrantup.com/docs).

4. Creare la VM

    ```bash
    vagrant up
    ```

    In questo passaggio viene scaricata e avviata in locale l'immagine di VM preconfigurata e vi viene quindi configurato un cluster di Service Fabric locale. È probabile che questo passaggio richieda alcuni minuti. Se la configurazione ha esito positivo, nell'output verrà visualizzato un messaggio che indica che è in corso l'avvio del cluster.

    ![Avvio della configurazione del cluster dopo il provisioning della VM][cluster-setup-script]

5. Verificare che il cluster sia stato configurato correttamente passando a Service Fabric Explorer all'indirizzo http://192.168.50.50:19080/Explorer (presupponendo che sia stato mantenuto l'IP predefinito della rete privata).

    ![Visualizzazione di Service Fabric Explorer dal Mac host][sfx-mac]


## Installare il plug-in Service Fabric per Eclipse Neon (facoltativo)

Service Fabric offre un plug-in per l'IDE di Eclipse Neon che può semplificare il processo di creazione e distribuzione di servizi Java.

1. In Eclipse assicurarsi che sia installata la versione 1.0.17 di Buildship o una versione successiva. Per controllare le versioni dei componenti installati, scegliere **Help > Installation Details** (? > Dettagli installazione). È possibile aggiornare Buildship seguendo le istruzioni riportate [qui][buildship-update].

2. Per installare il plug-in Service Fabric, scegliere **Help > Install New Software** (? > Installa nuovo software).

3. Nella casella di testo "Work with" (Usa) immettere http://dl.windowsazure.com/eclipse/servicefabric.

4. Fare clic su Aggiungi.

    ![Plug-in Eclipse Neon per Service Fabric][sf-eclipse-plugin-install]

5. Scegliere il plug-in Service Fabric e fare clic su Next (Avanti).

6. Procedere con l'installazione e accettare il contratto di licenza con l'utente finale.

## Passaggi successivi

- [Creare la prima applicazione di Service Fabric per Linux](service-fabric-create-your-first-linux-application-with-java.md)

<!-- Links -->

- [Creare un cluster di Service Fabric nel portale di Azure](service-fabric-cluster-creation-via-portal.md)
- [Creare un cluster di Service Fabric con Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
- [Informazioni sul modello applicativo di Service Fabric](service-fabric-application-model.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!---HONumber=AcomDC_0928_2016-->