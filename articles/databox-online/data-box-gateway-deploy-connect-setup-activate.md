---
title: Connettersi ad Azure Data Box Gateway, configurarlo e attivarlo nel portale di Azure | Microsoft Docs
description: La terza esercitazione relativa alla distribuzione di Data Box Gateway illustra come connettersi al dispositivo virtuale, quindi configurarlo e attivarlo.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: alkohli
ms.openlocfilehash: 898cb63f8868ce2abaee8784214322edf9a56997
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60000318"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-gateway"></a>Esercitazione: Connettere, configurare e attivare Azure Data Box Gateway

## <a name="introduction"></a>Introduzione

Questa esercitazione descrive come connettersi al dispositivo Data Box Gateway, configurarlo e attivarlo usando l'interfaccia utente Web locale. 

Per completare il processo di configurazione e attivazione sono necessari circa 10 minuti. 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Connettersi a un dispositivo virtuale
> * Configurare e attivare il dispositivo virtuale

## <a name="prerequisites"></a>Prerequisiti

Prima di configurare Data Box Gateway, assicurarsi di:

* Aver eseguito il provisioning di un dispositivo virtuale e aver ottenuto un corrispondente URL connesso, come descritto in [Eseguire il provisioning di un dispositivo Data Box Gateway in Hyper-V](data-box-gateway-deploy-provision-hyperv.md) o [Eseguire il provisioning di un dispositivo Data Box Gateway in VMware](data-box-gateway-deploy-provision-vmware.md).
* Disporre della chiave di attivazione dal servizio Data Box Gateway creato per gestire i dispositivi Data Box Gateway. Per ulteriori informazioni, andare all'articolo sulle [operazioni preliminari alla distribuzione di Azure Data Box Gateway](data-box-gateway-deploy-prep.md).


## <a name="connect-to-the-local-web-ui-setup"></a>Connettersi all'installazione dell'interfaccia utente Web locale 

1. Aprire una finestra del browser e accedere all'interfaccia utente Web locale del dispositivo all'indirizzo:
   
   [https://ip-address-of-network-interface](https://ip-address-of-network-interface)
   
   Usare l'URL di connessione annotato nell'esercitazione precedente. Viene visualizzato un messaggio di errore o di avviso in cui si indica che si è verificato un problema con il certificato di sicurezza del sito Web.

2. Selezionare **Continua in questa pagina Web**. Questi passaggi possono variare in base al browser in uso.
   
    ![Messaggio di errore del certificato di sicurezza del sito Web](./media/data-box-gateway-deploy-connect-setup-activate/image2.png)

3. Accedere all'interfaccia utente Web del dispositivo virtuale. La password predefinita è *Password1*. 
   
    ![Accedere all'interfaccia utente Web locale](./media/data-box-gateway-deploy-connect-setup-activate/image3.png)

4. Quando richiesto, cambiare la password del dispositivo. La nuova password deve contenere da 8 a 16 caratteri e deve includere 3 dei seguenti elementi: lettere maiuscole, minuscole, numeri e caratteri speciali.

    ![Modificare la password del dispositivo](./media/data-box-gateway-deploy-connect-setup-activate/image4.png)

Viene aperto il **Dashboard** del dispositivo.

## <a name="set-up-and-activate-the-virtual-device"></a>Configurare e attivare il dispositivo virtuale
 
Il dashboard visualizza le diverse impostazioni necessarie per configurare e registrare il dispositivo virtuale con il servizio Data Box Gateway. Le impostazioni **Nome del dispositivo**, **Impostazioni di rete**, **Impostazioni proxy Web** e **Impostazioni ora** sono facoltative. Le uniche impostazioni obbligatorie sono **Impostazioni cloud**.
   
![Pagina "Dashboard" dell'interfaccia utente Web locale](./media/data-box-gateway-deploy-connect-setup-activate/image5.png)

1. Nel riquadro sinistro selezionare **Nome del dispositivo** e quindi immettere un nome descrittivo per il dispositivo. Il nome descrittivo deve contenere da 1 a 15 caratteri e deve includere lettere, numeri e trattini.

    ![Pagina "Nome del dispositivo" dell'interfaccia utente Web locale](./media/data-box-gateway-deploy-connect-setup-activate/image6.png)

2. (Facoltativo) Nel riquadro sinistro selezionare **Impostazioni di rete** e quindi configurare le impostazioni. Nel dispositivo virtuale sarà visibile almeno un'interfaccia di rete a seconda di quante ne sono state configurate nella macchina virtuale sottostante. Di seguito è illustrata la pagina **Impostazioni di rete** di un dispositivo virtuale con un'interfaccia di rete abilitata.
    
    ![Pagina "Impostazioni di rete" dell'interfaccia utente Web locale](./media/data-box-gateway-deploy-connect-setup-activate/image7.png)
   
    Quando si configurano le impostazioni di rete, tenere presente quanto segue:

    - Se nell'ambiente è abilitato il DHCP, le interfacce di rete vengono configurate automaticamente. Di conseguenza, vengono assegnati automaticamente anche un indirizzo IP, una subnet, un gateway e un DNS.
    - Se il protocollo DHCP non è abilitato, è possibile assegnare indirizzi IP statici (se necessario).
    - È possibile configurare l'interfaccia di rete come IPv4.

     >[!NOTE] 
     > È consigliabile non cambiare l'indirizzo IP locale dell'interfaccia di rete da statico a DHCP, a meno che non si abbia un altro indirizzo IP per la connessione al dispositivo. Se si usa un'interfaccia di rete e si passa a DHCP, non sarà possibile determinare l'indirizzo DHCP. Se si vuole passare a un indirizzo DHCP, attendere fino al termine della registrazione del dispositivo con il servizio e quindi eseguire il passaggio. È possibile visualizzare gli indirizzi IP di tutti gli adattatori in **Proprietà dispositivo** nel portale di Azure per il proprio servizio.

3. (Facoltativo) configurare il server proxy Web. Sebbene la configurazione del proxy Web sia facoltativa, se si usa un proxy Web è possibile configurarlo solo in questa pagina.
   
   ![Pagina "Impostazioni proxy Web" dell'interfaccia utente Web locale](./media/data-box-gateway-deploy-connect-setup-activate/image8.png)
   
   Nella pagina **Proxy Web** procedere come segue:
   
   1. Nella casella **URL proxy Web** immettere l'URL in questo formato: `http://&lt;host-IP address or FQDN&gt;:Port number`. Gli URL HTTPS non sono supportati.
   2. In **Autenticazione** selezionare **Nessuna** o **NTLM**.
   3. Se si usa l'autenticazione, immettere un **nome utente** e una **password**.
   4. Per convalidare e applicare le impostazioni del proxy Web configurate, selezionare **Applica**.

4. (Facoltativo) Nel riquadro sinistro selezionare **Impostazioni ora**, quindi configurare il fuso orario e i server NTP primario e secondario per il dispositivo. 

    I server NTP sono obbligatori in quanto il dispositivo deve sincronizzare l'ora e consentire l'autenticazione con i provider del servizio cloud.
    
    ![Pagina "Impostazioni ora" dell'interfaccia utente Web locale](./media/data-box-gateway-deploy-connect-setup-activate/image9.png)
    
    Nella pagina **Impostazioni ora** procedere come segue:
    
    1. Nell'elenco a discesa **Fuso orario** selezionare il fuso orario che corrisponde all'area geografica in cui viene distribuito il dispositivo.
        Il fuso orario predefinito per il dispositivo è PST. Il dispositivo utilizzerà questo fuso orario per tutte le operazioni pianificate.

    2. Specificare un **Server NTP primario** per il dispositivo o accettare il valore predefinito `time.windows.com`.   
        Assicurarsi che la rete consenta il traffico NTP dal data center a Internet.

    3. Facoltativamente, nella casella **Server NTP secondario** immettere un server secondario per il dispositivo.

    4. Per convalidare e applicare le impostazioni ora configurate, selezionare **Applica**.

6. Nel riquadro sinistro selezionare **Impostazioni del cloud**e quindi attivare il dispositivo con il servizio Data Box Gateway nel portale di Azure.
    
    1. Nella casella **Chiave di attivazione** immettere la **chiave di attivazione** ottenuta in [Ottenere la chiave di attivazione per Data Box Gateway](data-box-gateway-deploy-prep.md#get-the-activation-key).

    2. Selezionare **Attiva**.
       
         ![Pagina "Impostazioni del cloud" dell'interfaccia utente Web locale](./media/data-box-gateway-deploy-connect-setup-activate/image10a.png)
    
    3. Il dispositivo viene attivato e gli aggiornamenti critici, se disponibili, vengono applicati automaticamente. Verrà visualizzata una notifica in merito. Monitorare lo stato dell'aggiornamento tramite il portale di Azure.

        ![Pagina "Impostazioni del cloud" dell'interfaccia utente Web locale](./media/data-box-gateway-deploy-connect-setup-activate/image12.png)
        
        **La finestra di dialogo include anche una chiave di ripristino che è necessario copiare e conservare in un posto sicuro. Questa chiave viene usata per ripristinare i dati nel caso non sia possibile avviare il dispositivo.**


    4. Può essere necessario attendere diversi minuti prima che l'aggiornamento venga completato. Al termine, accedere al dispositivo. La pagina **Impostazioni del cloud** viene aggiornata per indicare che il dispositivo è stato attivato correttamente.

        ![Pagina "Impostazioni del cloud" dell'interfaccia utente Web locale aggiornata](./media/data-box-gateway-deploy-connect-setup-activate/image13.png)

La configurazione del dispositivo è completata. È ora possibile aggiungere condivisioni al dispositivo.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Connettersi a un dispositivo virtuale
> * Configurare e attivare il dispositivo virtuale

Per informazioni su come trasferire dati con il dispositivo Data Box Gateway, vedere:

> [!div class="nextstepaction"]
> [Trasferire dati con Data Box Gateway](./data-box-gateway-deploy-add-shares.md).
