---
title: Connettersi ad Azure Data Box Gateway, configurarlo e attivarlo nel portale di Azure | Microsoft Docs
description: La terza esercitazione relativa alla distribuzione di Data Box Gateway illustra come connettersi al dispositivo virtuale, quindi configurarlo e attivarlo.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/18/2019
ms.author: alkohli
ms.openlocfilehash: de2ef4908260a62acf28a270dda6ad738a1760b9
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58402330"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-gateway"></a>Esercitazione: La connessione, configurare, attivare Gateway casella dei dati di Azure

## <a name="introduction"></a>Introduzione

Questa esercitazione descrive come connettersi a, impostare e attivare il dispositivo Gateway finestra dati tramite l'interfaccia utente web locale. 

Per completare il processo di configurazione e attivazione sono necessari circa 10 minuti. 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Connettersi a un dispositivo virtuale
> * Configurare e attivare il dispositivo virtuale

## <a name="prerequisites"></a>Prerequisiti

Prima di configurare Data Box Gateway, assicurarsi di:

* Aver effettuato il provisioning di un dispositivo virtuale e aver ottenuto un URL connesso a esso come descritto in dettaglio nel [effettuare il provisioning di un Gateway di finestra di dati in Hyper-V](data-box-gateway-deploy-provision-hyperv.md) oppure [effettuare il provisioning di un Gateway di finestra di dati in VMware](data-box-gateway-deploy-provision-vmware.md).
* Disporre della chiave di attivazione dal servizio Data Box Gateway creato per gestire i dispositivi Data Box Gateway. Per ulteriori informazioni, andare all'articolo sulle [operazioni preliminari alla distribuzione di Azure Data Box Gateway](data-box-gateway-deploy-prep.md).


## <a name="connect-to-the-local-web-ui-setup"></a>Connettersi all'installazione dell'interfaccia utente Web locale 

1. Aprire una finestra del browser e accedere interfaccia utente del dispositivo al web locale:
   
   [https://ip-address-of-network-interface](https://ip-address-of-network-interface)
   
   Usare l'URL di connessione annotato nell'esercitazione precedente. Viene visualizzato un messaggio di errore o di avviso in cui si indica che si è verificato un problema con il certificato di sicurezza del sito Web.

2. Selezionare **Continua in questa pagina Web**. Questi passaggi possono variare in base al browser in uso.
   
    ![Messaggio di errore del certificato di sicurezza del sito Web](./media/data-box-gateway-deploy-connect-setup-activate/image2.png)

3. Accedere all'interfaccia utente Web del dispositivo virtuale. La password predefinita è *Password1*. 
   
    ![Accedere all'interfaccia utente web locale](./media/data-box-gateway-deploy-connect-setup-activate/image3.png)

4. Al prompt di modificare la password del dispositivo. La nuova password deve contenere tra 8 e 16 caratteri. Deve contenere 3 dei seguenti: caratteri maiuscoli, minuscoli, numerici e speciali.

    ![Modificare la password del dispositivo](./media/data-box-gateway-deploy-connect-setup-activate/image4.png)

Viene aperto il **Dashboard** del dispositivo.

## <a name="set-up-and-activate-the-virtual-device"></a>Configurare e attivare il dispositivo virtuale
 
Il dashboard Visualizza le varie impostazioni necessarie per configurare e registrare il dispositivo virtuale con il servizio Gateway di dati finestra. Le impostazioni **Nome del dispositivo**, **Impostazioni di rete**, **Impostazioni proxy Web** e **Impostazioni ora** sono facoltative. Le uniche impostazioni obbligatorie sono **Impostazioni cloud**.
   
!["Dashboard" interfaccia utente web locale pagina](./media/data-box-gateway-deploy-connect-setup-activate/image5.png)

1. Nel riquadro di sinistra, selezionare **nome dispositivo**, quindi immettere un nome descrittivo per il dispositivo. Il nome descrittivo deve contenere da 1 a 15 caratteri e contenere lettere, numeri e trattini.

    ![Pagina "Nome del dispositivo" dell'interfaccia utente web locale](./media/data-box-gateway-deploy-connect-setup-activate/image6.png)

2. (Facoltativo) Nel riquadro di sinistra, selezionare **impostazioni di rete** e quindi configurare le impostazioni. Nel dispositivo virtuale, si noterà almeno un'interfaccia di rete e così via a seconda di quanti è configurato nella macchina virtuale sottostante. Di seguito è illustrata la pagina **Impostazioni di rete** di un dispositivo virtuale con un'interfaccia di rete abilitata.
    
    ![Pagina "Impostazioni di rete" dell'interfaccia utente web locale](./media/data-box-gateway-deploy-connect-setup-activate/image7.png)
   
    Durante la configurazione delle impostazioni di rete, tenere presenti:

    - Se nell'ambiente è abilitato il DHCP, le interfacce di rete vengono configurate automaticamente. Di conseguenza, vengono assegnati automaticamente anche un indirizzo IP, una subnet, un gateway e un DNS.
    - Se il protocollo DHCP non è abilitato, è possibile assegnare indirizzi IP statici (se necessario).
    - È possibile configurare l'interfaccia di rete come IPv4.

     >[!NOTE] 
     > È consigliabile non cambiare l'indirizzo IP locale dell'interfaccia di rete da statico a DHCP, a meno che non si abbia un altro indirizzo IP per la connessione al dispositivo. Se si usa un'interfaccia di rete e si passa a DHCP, non sarà possibile determinare l'indirizzo DHCP. Se si vuole passare a un indirizzo DHCP, attendere fino al termine della registrazione del dispositivo con il servizio e quindi eseguire il passaggio. È possibile visualizzare gli indirizzi IP di tutti gli adattatori in **Proprietà dispositivo** nel portale di Azure per il proprio servizio.

3. (Facoltativo) configurare il server proxy Web. Sebbene la configurazione del proxy web sia facoltativa, se si usa un proxy web, è possibile configurarlo solo in questa pagina.
   
   ![Pagina "Impostazioni del proxy Web" dell'interfaccia utente web locale](./media/data-box-gateway-deploy-connect-setup-activate/image8.png)
   
   Nel **proxy Web** pagina, effettuare le operazioni seguenti:
   
   1. Nella casella **URL proxy Web** immettere l'URL in questo formato: `http://&lt;host-IP address or FQDN&gt;:Port number`. Gli URL HTTPS non sono supportati.
   2. In **Autenticazione** selezionare **Nessuna** o **NTLM**.
   3. Se si usa l'autenticazione, immettere un **nomeutente** e **Password**.
   4. Per convalidare e applicare le impostazioni del proxy web configurate, selezionare **applica**.

4. (Facoltativo) Nel riquadro sinistro selezionare **Impostazioni ora**, quindi configurare il fuso orario e i server NTP primario e secondario per il dispositivo. 

    I server NTP sono obbligatori in quanto il dispositivo deve sincronizzare l'ora e consentire l'autenticazione con i provider del servizio cloud.
    
    ![Pagina "Impostazioni di tempo" dell'interfaccia utente web locale](./media/data-box-gateway-deploy-connect-setup-activate/image9.png)
    
    Nel **impostazioni ora** pagina, effettuare le operazioni seguenti:
    
    1. Nel **fuso orario** elenco a discesa selezionare il fuso orario che corrisponde alla posizione geografica in cui viene distribuito il dispositivo.
        Il fuso orario predefinito per il dispositivo è PST. Il dispositivo utilizzerà questo fuso orario per tutte le operazioni pianificate.

    2. Specificare una **server NTP primario** per il dispositivo o accettare il valore predefinito di `time.windows.com`.   
        Assicurarsi che la rete consenta il traffico NTP dal data center a Internet.

    3. Facoltativamente, nella **server NTP secondario** immettere un server secondario per il dispositivo.

    4. Per convalidare e applicare le impostazioni ora configurate, selezionare **Applica**.

6. Nel riquadro sinistro, selezionare **impostazioni di Cloud**e quindi attivare il dispositivo con il servizio Gateway di dati finestra nel portale di Azure.
    
    1. Nel **chiave di attivazione** immettere il **chiave di attivazione** ottenuto [ottenere la chiave di attivazione](data-box-gateway-deploy-prep.md#get-the-activation-key) per il Gateway di finestra di dati.

    2. Selezionare **Attiva**.
       
         ![Pagina "Impostazioni di Cloud" dell'interfaccia utente web locale](./media/data-box-gateway-deploy-connect-setup-activate/image10a.png)
    
    3. Il dispositivo viene attivato e gli aggiornamenti critici, se disponibile, vengono applicati automaticamente. Viene visualizzata una notifica in tal senso. Monitorare lo stato di aggiornamento tramite il portale di Azure.

        ![Pagina "Impostazioni di Cloud" dell'interfaccia utente web locale](./media/data-box-gateway-deploy-connect-setup-activate/image12.png)
        
        **La finestra di dialogo ha anche una chiave di ripristino che è necessario copiare e salvare in un luogo sicuro. Questa chiave viene usata per recuperare i dati nel caso in cui il dispositivo non può avviarsi.**


    4. Si potrebbe essere necessario attendere alcuni minuti completare correttamente l'aggiornamento. Dopo l'aggiornamento è completato, accedere al dispositivo. Il **impostazioni di Cloud** pagina aggiornato per indicare che il dispositivo viene attivato correttamente.

        ![Pagina "Impostazioni di Cloud" dell'interfaccia utente web locale aggiornato](./media/data-box-gateway-deploy-connect-setup-activate/image13.png)

La configurazione del dispositivo è completata. È ora possibile aggiungere condivisioni al dispositivo.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Connettersi a un dispositivo virtuale
> * Configurare e attivare il dispositivo virtuale

Per informazioni su come trasferire dati con il Gateway di finestra di dati, vedere:

> [!div class="nextstepaction"]
> [Trasferire dati con Data Box Gateway](./data-box-gateway-deploy-add-shares.md).
