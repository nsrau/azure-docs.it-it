<properties
   pageTitle="Creare un nome di dominio completo (FQDN) per una VM nel portale di Azure | Microsoft Azure"
   description="Informazioni su come creare un nome di dominio completo o FQDN per un Gestore risorse basato su macchina virtuale nel portale di Azure."
   services="virtual-machines"
   documentationCenter=""
   authors="dsk-2015"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/19/2016"
   ms.author="dkshir"/>

# Creare un nome di dominio completo nel portale di Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modello di distribuzione classica.


Quando si crea una macchina virtuale nel [portale di Azure](https://portal.azure.com) utilizzando il modello di distribuzione **Gestione risorse di**, il portale crea una risorsa IP pubblica per la macchina virtuale. È possibile utilizzare questo indirizzo IP per accedere in remoto alla macchina virtuale. Anche se il portale non consente la creazione di un [nome di dominio completo](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) o FQDN (Fully Qualified Domain Name), per impostazione predefinita, è estremamente semplice crearne uno dopo aver creato la macchina virtuale. Questo articolo illustra i passaggi per creare un nome DNS o un FQDN.

L'articolo presuppone che sia già stata effettuata la connessione alla sottoscrizione nel portale e che sia stata creata una macchina virtuale con le immagini disponibili tramite **Gestione risorse**. Una volta avviata la macchina virtuale, seguire questi passaggi.

1.  Visualizzare le impostazioni della macchina virtuale nel portale e fare clic sull'indirizzo IP pubblico.

    ![individuare la risorsa ip](media/virtual-machines-create-fqdn-on-portal/locatePublicIP.PNG)

2.  Notare che il nome DNS per l'indirizzo IP pubblico è vuoto. Fare clic su **Tutte le impostazioni** per il pannello IP pubblico.

    ![impostazioni ip](media/virtual-machines-create-fqdn-on-portal/settingsIP.PNG)

3.  Aprire la scheda **Configurazione** nelle impostazioni dell'IP pubblico. Immettere l'etichetta del nome DNS desiderato e **salvare** questa configurazione.

    ![immettere l'etichetta del nome dns](media/virtual-machines-create-fqdn-on-portal/dnsNameLabel.PNG)

    La risorsa IP pubblico mostra ora la nuova etichetta DNS nel relativo pannello.

4.  Chiudere i pannelli dell'IP pubblico e tornare al pannello macchina virtuale nel portale. Verificare che il nome DNS o FQDN sia visualizzato accanto all'indirizzo IP per la risorsa IP pubblico.

    ![FQDN creato](media/virtual-machines-create-fqdn-on-portal/fqdnCreated.PNG)


    È ora possibile connettersi in remoto alla macchina virtuale utilizzando questo nome DNS. Ad esempio, utilizzare `SSH adminuser@testdnslabel.centralus.cloudapp.azure.com`, quando ci si connette a una macchina virtuale Linux che ha il nome di dominio completo di `testdnslabel.centralus.cloudapp.azure.com` e il nome utente di `adminuser`.

<!---HONumber=AcomDC_0128_2016-->