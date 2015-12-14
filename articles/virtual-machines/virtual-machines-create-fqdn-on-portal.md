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
   ms.date="08/21/2015"
   ms.author="dkshir"/>

# Creare un nome di dominio completo nel portale di Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modello di distribuzione classica.


Quando si crea una macchina virtuale nel [portale di Azure](https://portal.azure.com) utilizzando il modello di distribuzione **Gestione risorse di**, il portale crea una risorsa IP pubblica per la macchina virtuale. È possibile utilizzare questo indirizzo IP per accedere in remoto alla macchina virtuale. Tuttavia, il portale non crea un [nome di dominio completo](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) o FQDN, per impostazione predefinita. Poiché un nome di dominio completo sarebbe più facile da ricordare e utilizzare rispetto a un indirizzo IP, in questo articolo viene illustrato come aggiungerne uno ad una macchina virtuale.

L'articolo presuppone che sia già stata effettuata la connessione alla sottoscrizione nel portale e che sia stata creata una macchina virtuale con le immagini disponibili tramite **Gestione risorse**. Una volta avviata la macchina virtuale, seguire questi passaggi.

1.  Visualizzare le impostazioni della macchina virtuale nel portale e fare clic sull'indirizzo IP pubblico.

    ![individuare la risorsa ip](media/virtual-machines-create-fqdn-on-portal/locatePublicIP.PNG)

2.  **Dissociare** l'indirizzo IP pubblico dalla macchina virtuale. Si noti che non è ancora presente un nome di dominio. Dopo aver fatto clic sul pulsante **Sì**, potrebbero essere necessari alcuni secondi affinché la dissociazione venga completata.

    ![dissociare la risorsa ip](media/virtual-machines-create-fqdn-on-portal/dissociateIP.PNG)

    Questo IP pubblico viene associato alla macchina virtuale dopo la procedura seguente. Se l'indirizzo IP pubblico è un _IP pubblico dinamico_, si perde l'indirizzo IPV4 e ne viene assegnato uno nuovo dopo aver configurato il FQDN.

3.  Quando il pulsante**Dissocia** diventa grigio, fare clic sulla sezione **Tutte le impostazioni** dell'indirizzo IP pubblico e aprire la scheda **Configurazione**. Immettere l'etichetta del nome DNS desiderato. **Salvare** questa configurazione.

    ![immettere l'etichetta del nome dns](media/virtual-machines-create-fqdn-on-portal/dnsNameLabel.PNG)

4.  Tornare al pannello macchina virtuale nel portale e fare clic su **Tutte le impostazioni** per la macchina virtuale. Aprire la scheda **Interfacce di rete** e fare clic sulla risorsa di interfaccia di rete associata a questa macchina virtuale. Si apre il pannello **Interfaccia di rete** del portale.

    ![aprire l'interfaccia di rete](media/virtual-machines-create-fqdn-on-portal/openNetworkInterface.PNG)

5.  Si noti che il campo**Indirizzo IP pubblico** per l'interfaccia di rete è vuoto. Fare clic sulla sezione **Tutte le impostazioni** per l'interfaccia di rete e aprire la scheda **Indirizzi IP**. Nel pannello**Indirizzi IP**, fare clic su **Abilitato** per il campo **Indirizzo IP pubblico**. Selezionare la scheda **Impostazioni necessarie per la configurazione dell’indirizzo IP ** e selezionare l'IP predefinito dissociato in precedenza. Fare clic su **Save**. Potrebbero essere necessari alcuni minuti per aggiungere nuovamente la risorsa IP.

    ![configurare la risorsa IP](media/virtual-machines-create-fqdn-on-portal/configureIP.PNG)

6.  Chiudere tutti gli altri pannelli e tornare al pannello**Macchina virtuale**. Fare clic sulla risorsa IP pubblico nelle impostazioni. Tenere presente che il pannello IP pubblico ora visualizza il nome FQDN desiderato come **nome DNS**.

    ![FQDN creato](media/virtual-machines-create-fqdn-on-portal/fqdnCreated.PNG)

    È ora possibile connettersi in remoto alla macchina virtuale utilizzando questo nome DNS. Ad esempio, utilizzare `SSH adminuser@testdnslabel.eastus.cloudapp.azure.com`, quando ci si connette a una macchina virtuale Linux che ha il nome di dominio completo di `testdnslabel.eastus.cloudapp.azure.com` e il nome utente di `adminuser`.

<!---HONumber=AcomDC_1203_2015-->