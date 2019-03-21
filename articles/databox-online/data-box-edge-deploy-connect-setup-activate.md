---
title: Connettersi a un dispositivo Azure Data Box Edge, configurarlo e attivarlo nel portale di Azure | Microsoft Docs
description: La terza esercitazione relativa alla distribuzione di Data Box Edge illustra come connettersi al dispositivo fisico, configurarlo e attivarlo.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 01/09/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 4fd52510abd61c4d319a3fcbc8f722df5edbc476
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58120602"
---
# <a name="tutorial-connect-set-up-and-activate-azure-data-box-edge-preview"></a>Esercitazione: connettere, configurare e attivare Azure Data Box Edge (anteprima) 

Questa esercitazione descrive come è possibile connettersi a un dispositivo Azure Data Box Edge e come è possibile configurarlo e attivarlo usando l'interfaccia utente Web locale. 

Per completare il processo di configurazione e attivazione sono necessari circa 20 minuti. 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Connettersi a un dispositivo fisico
> * Configurare e attivare il dispositivo fisico

> [!IMPORTANT]
> Data Box Edge è disponibile in anteprima. Rivedere le [condizioni del servizio per l'anteprima di Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) prima di ordinare e distribuire la soluzione. 


## <a name="prerequisites"></a>Prerequisiti

Prima di configurare il dispositivo Data Box Edge, assicurarsi di:

* Aver installato il dispositivo fisico come descritto in [Installare un Data Box Edge](data-box-edge-deploy-install.md).
* Disporre della chiave di attivazione dal servizio Data Box Edge creato per gestire il dispositivo Data Box Edge. Per altre informazioni, vedere [Preparare la distribuzione di Azure Data Box Edge](data-box-edge-deploy-prep.md).

## <a name="connect-to-the-local-web-ui-setup"></a>Connettersi all'installazione dell'interfaccia utente Web locale 

1. Configurare la scheda Ethernet nel computer per connettersi al dispositivo Edge con un indirizzo IP statico pari a 192.168.100.5 e una subnet pari a 255.255.255.0.

1. Collegare il computer alla porta 1 sul dispositivo. 

1. Aprire una finestra del browser e accedere all'interfaccia utente Web locale del dispositivo all'indirizzo https://192.168.100.10.  
    È possibile che questa operazione richieda alcuni minuti dopo averla abilitata nel dispositivo. 

    Viene visualizzato un messaggio di errore o di avviso in cui si indica che si è verificato un problema con il certificato di sicurezza del sito Web. 
   
    ![Messaggio di errore del certificato di sicurezza del sito Web](./media/data-box-edge-deploy-connect-setup-activate/image2.png)

1. Selezionare **Continua in questa pagina Web**.  
    Questi passaggi possono variare in base al browser in uso.

1. Accedere all'interfaccia utente Web del dispositivo. La password predefinita è *Password1*. 
   
    ![Pagina di accesso al dispositivo Data Box Edge](./media/data-box-edge-deploy-connect-setup-activate/image3.png)

1. Quando richiesto, modificare la password di amministratore del dispositivo.  
    La password deve avere una lunghezza compresa tra 8 e 16 caratteri e deve contenere tre dei seguenti tipi di carattere: maiuscolo, minuscolo, numerico e speciale.

Viene aperto il dashboard del dispositivo.

## <a name="set-up-and-activate-the-physical-device"></a>Configurare e attivare il dispositivo fisico
 
Il dashboard visualizza le diverse impostazioni necessarie per configurare e registrare il dispositivo fisico con il servizio Data Box Edge. Le impostazioni **Nome del dispositivo**, **Impostazioni di rete**, **Impostazioni proxy Web** e **Impostazioni ora** sono facoltative. Le uniche impostazioni obbligatorie sono **Impostazioni cloud**.
   
![Dashboard del dispositivo Data Box Edge](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-1.png)

1. Nel riquadro sinistro selezionare **Nome del dispositivo** e quindi immettere un nome descrittivo per il dispositivo.  
    Il nome descrittivo deve contenere da 1 a 15 caratteri e includere lettere, numeri e trattini.

    ![Pagina "Nome del dispositivo"](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-2.png)

1. (Facoltativo) Nel riquadro sinistro selezionare **Impostazioni di rete** e quindi configurare le impostazioni.  
    Nel dispositivo fisico sono presenti sei interfacce di rete. PORT 1 e PORT 2 sono interfacce di rete a 1 Gbps, mentre PORT 3, PORT 4, PORT 5 e PORT 6 sono tutte interfacce di rete a 25 Gbps. La porta PORT 1 viene automaticamente configurata come porta di sola gestione, mentre le porte comprese tra PORT 2 e PORT 6 sono tutte porte dati. Di seguito è illustrata la pagina **Impostazioni di rete**.
    
    ![Pagina "Impostazioni di rete"](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-3.png)
   
    Nella configurazione delle impostazioni di rete tenere presenti queste considerazioni:

   - Se nell'ambiente è abilitato il DHCP, le interfacce di rete vengono configurate automaticamente. Di conseguenza, vengono assegnati automaticamente anche un indirizzo IP, una subnet, un gateway e un DNS.
   - Se il protocollo DHCP non è abilitato, è possibile assegnare indirizzi IP statici (se necessario).
   - È possibile configurare l'interfaccia di rete come IPv4.

     >[!NOTE] 
     > È consigliabile non cambiare l'indirizzo IP locale dell'interfaccia di rete da statico a DHCP, a meno che non si abbia un altro indirizzo IP per la connessione al dispositivo. Se si usa un'interfaccia di rete e si passa a DHCP, non sarà possibile determinare l'indirizzo DHCP. Se si vuole passare a un indirizzo DHCP, attendere fino al termine della registrazione del dispositivo con il servizio e quindi eseguire il passaggio. È possibile visualizzare gli indirizzi IP di tutti gli adattatori in **Proprietà dispositivo** nel portale di Azure per il proprio servizio.

1. (Facoltativo) Nel riquadro sinistro selezionare **Impostazioni proxy Web**e quindi configurare il server proxy Web. Sebbene la configurazione del proxy Web sia facoltativa, se si usa un proxy Web è possibile configurarlo solo in questa pagina.
   
   ![Pagina "Impostazioni proxy Web"](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-4.png)
   
   Nella pagina **Impostazioni proxy Web** eseguire queste operazioni:
   
   a. Nella casella **URL proxy Web** immettere l'URL in questo formato: `http://host-IP address or FQDN:Port number`. Gli URL HTTPS non sono supportati.

   b. In **Autenticazione** selezionare **Nessuna** o **NTLM**.

   c. Se si usa l'autenticazione, immettere un nome utente e una password.

   d. Per convalidare e applicare le impostazioni del proxy Web configurate, selezionare **Applica impostazioni**.

1. (Facoltativo) Nel riquadro sinistro selezionare **Impostazioni ora**, quindi configurare il fuso orario e i server NTP primario e secondario per il dispositivo.  
    I server NTP sono obbligatori in quanto il dispositivo deve sincronizzare l'ora e consentire l'autenticazione con i provider del servizio cloud.
    
    ![Pagina "Impostazioni ora"](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-5.png)
    
    Nella pagina **Impostazioni ora** eseguire queste operazioni:
    
    a. Nell'elenco a discesa **Fuso orario** selezionare il fuso orario che corrisponde alla posizione geografica in cui viene distribuito il dispositivo.  
        Il fuso orario predefinito per il dispositivo è PST. Il dispositivo utilizzerà questo fuso orario per tutte le operazioni pianificate.

    b. Nella casella **Server NTP primario** immettere il server primario per il dispositivo o accettare il valore predefinito di time.windows.com.  
        Assicurarsi che la rete consenta il traffico NTP dal data center a Internet.

    c. Facoltativamente, nella casella **Server NTP secondario** immettere un server secondario per il dispositivo.

    d. Per convalidare e applicare le impostazioni ora configurate, selezionare **Applica**.

6. Nel riquadro sinistro selezionare **Impostazioni del cloud**e quindi attivare il dispositivo con il servizio Data Box Edge nel portale di Azure.
    
    a. Nella casella **Chiave di attivazione** immettere la chiave di attivazione ottenuta in [Ottenere la chiave di attivazione](data-box-edge-deploy-prep.md#get-the-activation-key) per Data Box Edge.

    b. Selezionare **Applica**. 
       
    ![Pagina "Impostazioni del cloud"](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-6.png)
    
    Dopo aver attivato il dispositivo, vengono visualizzate le opzioni relative alla modalità di connessione. Queste impostazioni devono essere configurate se si prevede di usare il dispositivo in modalità disconnessa o parzialmente disconnessa. 

    ![Conferma dell'attivazione di "Impostazioni del cloud"](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-7.png)    

La configurazione del dispositivo è completata. È ora possibile aggiungere condivisioni al dispositivo.


## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Connettersi a un dispositivo fisico
> * Configurare e attivare il dispositivo fisico


Per informazioni su come trasferire dati con il dispositivo Data Box Edge, vedere:

> [!div class="nextstepaction"]
> [Trasferire dati con Data Box Edge](./data-box-edge-deploy-add-shares.md).
