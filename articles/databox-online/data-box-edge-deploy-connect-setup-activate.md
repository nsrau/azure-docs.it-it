---
title: Esercitazione per connettersi a un dispositivo Azure Data Box Edge, configurarlo e attivarlo nel portale di Azure | Microsoft Docs
description: Esercitazione su come distribuire Data Box Edge, connettersi al dispositivo fisico, configurarlo e attivarlo.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: cf2aa9bc1234f8bc92829b107d1a788b75d56a6b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67075078"
---
# <a name="tutorial-connect-set-up-and-activate-azure-data-box-edge"></a>Esercitazione: Connettere, configurare e attivare Azure Data Box Edge 

Questa esercitazione descrive come è possibile connettersi a un dispositivo Azure Data Box Edge e come è possibile configurarlo e attivarlo usando l'interfaccia utente Web locale.

Per completare il processo di configurazione e attivazione sono necessari circa 20 minuti.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Connettersi a un dispositivo fisico
> * Configurare e attivare il dispositivo fisico

## <a name="prerequisites"></a>Prerequisiti

Prima di configurare il dispositivo Data Box Edge, assicurarsi di:

* Aver installato il dispositivo fisico come descritto in [Installare un Data Box Edge](data-box-edge-deploy-install.md).
* Disporre della chiave di attivazione dal servizio Data Box Edge creato per gestire il dispositivo Data Box Edge. Per altre informazioni, vedere [Preparare la distribuzione di Azure Data Box Edge](data-box-edge-deploy-prep.md).

## <a name="connect-to-the-local-web-ui-setup"></a>Connettersi all'installazione dell'interfaccia utente Web locale 

1. Configurare la scheda Ethernet nel computer per connettersi al dispositivo Data Box Edge con l'indirizzo IP statico 192.168.100.5 e la subnet 255.255.255.0.

2. Collegare il computer alla porta 1 sul dispositivo. Usare la figura seguente per identificare PORT 1 sul dispositivo.

    ![Backplane del dispositivo cablato](./media/data-box-edge-deploy-install/backplane-cabled.png)


3. Aprire una finestra del browser e accedere all'interfaccia utente Web locale del dispositivo all'indirizzo `https://192.168.100.10`.  
    È possibile che questa operazione richieda alcuni minuti dopo averla abilitata nel dispositivo. 

    Viene visualizzato un messaggio di errore o di avviso in cui si indica che si è verificato un problema con il certificato di sicurezza del sito Web. 
   
    ![Messaggio di errore del certificato di sicurezza del sito Web](./media/data-box-edge-deploy-connect-setup-activate/image2.png)

4. Selezionare **Continua in questa pagina Web**.  
    Questi passaggi possono variare in base al browser in uso.

5. Accedere all'interfaccia utente Web del dispositivo. La password predefinita è *Password1*. 
   
    ![Pagina di accesso al dispositivo Data Box Edge](./media/data-box-edge-deploy-connect-setup-activate/image3.png)

6. Quando richiesto, modificare la password di amministratore del dispositivo.  
    La nuova password deve contenere da 8 a 16 caratteri e deve contenere tre dei seguenti tipi di carattere: maiuscolo, minuscolo, numerico e speciale.

Viene aperto il dashboard del dispositivo.

## <a name="set-up-and-activate-the-physical-device"></a>Configurare e attivare il dispositivo fisico
 
Il dashboard visualizza le diverse impostazioni necessarie per configurare e registrare il dispositivo fisico con il servizio Data Box Edge. Le impostazioni **Nome del dispositivo**, **Impostazioni di rete**, **Impostazioni proxy Web** e **Impostazioni ora** sono facoltative. Le uniche impostazioni obbligatorie sono **Impostazioni cloud**.
   
![Pagina "Dashboard" dell'interfaccia utente Web locale](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-1.png)

1. Nel riquadro sinistro selezionare **Nome del dispositivo** e quindi immettere un nome descrittivo per il dispositivo.  
    Il nome descrittivo deve contenere da 1 a 15 caratteri e deve includere lettere, numeri e trattini.

    ![Pagina "Nome del dispositivo" dell'interfaccia utente Web locale](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-2.png)

2. (Facoltativo) Nel riquadro sinistro selezionare **Impostazioni di rete** e quindi configurare le impostazioni.  
    Nel dispositivo fisico sono presenti sei interfacce di rete. PORT 1 e PORT 2 sono interfacce di rete a 1 Gbps, PORT 3, PORT 4, PORT 5 e PORT 6 sono tutte interfacce di rete a 25 Gbps che possono servire anche da interfacce di rete a 10 Gbps. La porta PORT 1 viene automaticamente configurata come porta di sola gestione, mentre le porte comprese tra PORT 2 e PORT 6 sono tutte porte dati. Di seguito è illustrata la pagina **Impostazioni di rete**.
    
    ![Pagina "Impostazioni di rete" dell'interfaccia utente Web locale](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-3.png)
   
    Nella configurazione delle impostazioni di rete tenere presenti queste considerazioni:

   - Se nell'ambiente è abilitato il DHCP, le interfacce di rete vengono configurate automaticamente. Di conseguenza, vengono assegnati automaticamente anche un indirizzo IP, una subnet, un gateway e un DNS.
   - Se il protocollo DHCP non è abilitato, è possibile assegnare indirizzi IP statici (se necessario).
   - È possibile configurare l'interfaccia di rete come IPv4.

     >[!NOTE] 
     > È consigliabile non cambiare l'indirizzo IP locale dell'interfaccia di rete da statico a DHCP, a meno che non si abbia un altro indirizzo IP per la connessione al dispositivo. Se si usa un'interfaccia di rete e si passa a DHCP, non sarà possibile determinare l'indirizzo DHCP. Se si vuole passare a un indirizzo DHCP, attendere fino al termine della registrazione del dispositivo con il servizio e quindi eseguire il passaggio. È possibile visualizzare gli indirizzi IP di tutti gli adattatori in **Proprietà dispositivo** nel portale di Azure per il proprio servizio.

3. (Facoltativo) Nel riquadro sinistro selezionare **Impostazioni proxy Web**e quindi configurare il server proxy Web. Sebbene la configurazione del proxy Web sia facoltativa, se si usa un proxy Web è possibile configurarlo solo in questa pagina.
   
   ![Pagina "Impostazioni proxy Web" dell'interfaccia utente Web locale](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-4.png)
   
   Nella pagina **Impostazioni proxy Web** eseguire queste operazioni:
   
   a. Nella casella **URL proxy Web** immettere l'URL in questo formato: `http://host-IP address or FQDN:Port number`. Gli URL HTTPS non sono supportati.

   b. In **Autenticazione** selezionare **Nessuna** o **NTLM**.

   c. Se si usa l'autenticazione, immettere un nome utente e una password.

   d. Per convalidare e applicare le impostazioni del proxy Web configurate, selezionare **Applica impostazioni**.

4. (Facoltativo) Nel riquadro sinistro selezionare **Impostazioni ora**, quindi configurare il fuso orario e i server NTP primario e secondario per il dispositivo.  
    I server NTP sono obbligatori in quanto il dispositivo deve sincronizzare l'ora e consentire l'autenticazione con i provider del servizio cloud.
       
    Nella pagina **Impostazioni ora** eseguire queste operazioni:
    
    1. Nell'elenco a discesa **Fuso orario** selezionare il fuso orario che corrisponde alla posizione geografica in cui viene distribuito il dispositivo.
        Il fuso orario predefinito per il dispositivo è PST. Il dispositivo utilizzerà questo fuso orario per tutte le operazioni pianificate.

    2. Nella casella **Server NTP primario** immettere il server primario per il dispositivo o accettare il valore predefinito di time.windows.com.  
        Assicurarsi che la rete consenta il traffico NTP dal data center a Internet.

    3. Facoltativamente, nella casella **Server NTP secondario** immettere un server secondario per il dispositivo.

    4. Per convalidare e applicare le impostazioni dell'ora configurate, selezionare **Applica impostazioni**.

        ![Pagina "Impostazioni ora" dell'interfaccia utente Web locale](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-5.png)

5. (Facoltativo) Nel riquadro sinistro selezionare **Impostazioni di archiviazione** per configurare la resilienza della risorsa di archiviazione nel dispositivo. Questa funzionalità è attualmente in anteprima. Per impostazione predefinita, la risorsa di archiviazione del dispositivo non è resiliente e in caso di errore del disco dati si verifica una perdita di dati. Se si abilita l'opzione della resilienza, la risorsa di archiviazione del dispositivo verrà riconfigurata e in caso di errore del disco dati non si verificherà una perdita di dati. La configurazione della resilienza della risorsa di archiviazione riduce la capacità utilizzabile del dispositivo.

    > [!IMPORTANT] 
    > La resilienza può essere configurata solo prima di attivare il dispositivo. 

    ![Pagina "Impostazioni di archiviazione" dell'interfaccia utente Web locale](./media/data-box-edge-deploy-connect-setup-activate/storage-settings.png)

6. Nel riquadro sinistro selezionare **Impostazioni del cloud**e quindi attivare il dispositivo con il servizio Data Box Edge nel portale di Azure.
    
    1. Nella casella **Chiave di attivazione** immettere la chiave di attivazione ottenuta in [Ottenere la chiave di attivazione](data-box-edge-deploy-prep.md#get-the-activation-key) per Data Box Edge.
    2. Selezionare **Applica**.
       
        ![Pagina "Impostazioni del cloud" dell'interfaccia utente Web locale](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-6.png)

    3. Il dispositivo viene prima di tutto attivato. Viene quindi analizzato per verificare la disponibilità di aggiornamenti critici che, se presenti, vengono applicati automaticamente. Verrà visualizzata una notifica in merito.

        La finestra di dialogo include anche una chiave di ripristino che è necessario copiare e conservare in un posto sicuro. Questa chiave viene usata per ripristinare i dati nel caso non sia possibile avviare il dispositivo.

        ![Pagina "Impostazioni del cloud" dell'interfaccia utente Web locale aggiornata](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-7.png)

    4. Può essere necessario attendere diversi minuti dopo il completamento dell'aggiornamento. La pagina viene aggiornata per indicare che il dispositivo è stato attivato correttamente.

        ![Pagina "Impostazioni del cloud" dell'interfaccia utente Web locale aggiornata](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-8.png)

La configurazione del dispositivo è completata. È ora possibile aggiungere condivisioni al dispositivo.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Connettersi a un dispositivo fisico
> * Configurare e attivare il dispositivo fisico

Per informazioni su come trasferire dati con il dispositivo Data Box Edge, vedere:

> [!div class="nextstepaction"]
> [Trasferire dati con Data Box Edge](./data-box-edge-deploy-add-shares.md).
