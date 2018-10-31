---
title: Connettersi ad Azure Data Box Edge, configurarlo e attivarlo nel portale di Azure | Microsoft Docs
description: La terza esercitazione relativa alla distribuzione di Data Box Edge illustra come connettersi al dispositivo fisico, configurarlo e attivarlo.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/08/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 3d6742bec54e612b5dca6d9ef6c4f67a33929448
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2018
ms.locfileid: "49166577"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-edge-preview"></a>Esercitazione: Connettersi ad Azure Data Box Edge (Anteprima), configurarlo e attivarlo 

Questa esercitazione descrive come connettersi al dispositivo Data Box Edge, configurarlo e attivarlo usando l'interfaccia utente Web locale. 

Per completare il processo di configurazione e attivazione sono necessari circa 20 minuti. 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Connettersi al dispositivo fisico
> * Configurare e attivare il dispositivo fisico

> [!IMPORTANT]
> Data Box Edge è disponibile in anteprima. Rivedere le [condizioni del servizio per l'anteprima di Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) prima di ordinare e distribuire la soluzione. 


## <a name="prerequisites"></a>Prerequisiti

Prima di configurare Data Box Edge, assicurarsi di:

* Aver installato il dispositivo fisico come descritto in [Installare un Data Box Edge](data-box-edge-deploy-install.md).
* Disporre della chiave di attivazione dal servizio Data Box Edge creato per gestire il dispositivo Data Box Edge. Per altre informazioni, vedere [Preparare la distribuzione di Azure Data Box Edge](data-box-edge-deploy-prep.md).

## <a name="connect-to-the-local-web-ui-setup"></a>Connettersi all'installazione dell'interfaccia utente Web locale 

1. Configurare la scheda Ethernet nel computer usato per connettersi al dispositivo Edge impostando l'indirizzo IP statico 192.168.100.5 e la subnet 255.255.255.0.
2. Collegare il computer alla porta 1 sul dispositivo. 
3. Aprire una finestra del browser e accedere all'interfaccia utente Web locale del dispositivo all'indirizzo https://192.168.100.10. È possibile che questa operazione richieda alcuni minuti dopo averla abilitata nel dispositivo. 
4. Viene visualizzato un messaggio di errore o di avviso in cui si indica che si è verificato un problema con il certificato di sicurezza del sito Web. Fare clic su **Passa a questa pagina Web**. (Questi passaggi variano in base al browser in uso.)
   
    ![](./media/data-box-edge-deploy-connect-setup-activate/image2.png)

2. Accedere all'interfaccia utente Web del dispositivo. La password predefinita è *Password1*. 
   
    ![](./media/data-box-edge-deploy-connect-setup-activate/image3.png)

3. Viene richiesto di modificare la password dell'amministratore del dispositivo. Digitare una nuova password che contenga minimo 8 caratteri e massimo 16. La password deve contenere tre dei seguenti tipi di carattere: maiuscolo, minuscolo, numerico e speciale.

Viene aperto il **Dashboard** del dispositivo.

## <a name="set-up-and-activate-the-physical-device"></a>Configurare e attivare il dispositivo fisico
 
1. Dal dashboard è possibile accedere a diverse impostazioni necessarie per configurare e registrare il dispositivo fisico con il servizio Data Box Edge. Le impostazioni **Nome del dispositivo**, **Impostazioni di rete**, **Impostazioni proxy Web** e **Impostazioni ora** sono facoltative. Le uniche impostazioni obbligatorie sono **Impostazioni cloud**.
   
    ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-1.png)

2. Nella pagina **Nome dispositivo** configurare un nome descrittivo per il dispositivo. Questo nome può includere da 1 a 15 caratteri e può contenere lettere, numeri e trattini.

    ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-2.png)

3. Facoltativamente configurare le **impostazioni di rete**. Nel dispositivo fisico sono disponibili sei interfacce di rete. PORT 1 e PORT 2 sono interfacce di rete a 1 Gbps, mentre PORT 3, PORT 4, PORT 5 e PORT 6 sono tutte interfacce di rete a 25 Gbps. PORT 1 viene automaticamente configurata come porta di sola gestione, mentre le porte comprese tra PORT 2 e PORT 6 sono tutte porte dati. Di seguito è illustrata la pagina **Impostazioni di rete**.
    
    ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-3.png)
   
    Quando si configurano le impostazioni di rete, tenere presente quanto segue:

    - Se nell'ambiente è abilitato il DHCP, le interfacce di rete vengono configurate automaticamente. Di conseguenza, vengono assegnati automaticamente anche un indirizzo IP, una subnet, un gateway e un DNS.
    - Se il protocollo DHCP non è abilitato, è possibile assegnare indirizzi IP statici (se necessario).
    - È possibile configurare l'interfaccia di rete come IPv4.
   
4. Configurare il server proxy Web (facoltativo). Sebbene la configurazione del proxy Web sia facoltativa, se si usa un proxy Web, è possibile configurarlo solo qui.
   
   ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-4.png)
   
   Nella pagina **Proxy Web** :
   
   1. Specificare l'**URL proxy Web** in questo formato: `http://host-IP address or FDQN:Port number`. Gli URL HTTPS non sono supportati.
   2. Specificare **Autenticazione** come **Basic** o **Nessuna**.
   3. Se si usa l'autenticazione, è necessario specificare anche **Nome utente** e **Password**.
   4. Fare clic su **Applica** per convalidare e applicare le impostazioni proxy Web configurate.

5. Configurare le impostazioni ora per il dispositivo, ad esempio il fuso orario e i server NTP primari e secondari (facoltativo). I server NTP sono obbligatori in quanto il dispositivo deve sincronizzare l'ora e consentire l'autenticazione con i provider del servizio cloud.
    
    ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-5.png)
    
    Nella pagina **Impostazioni ora** :
    
    1. Nell'elenco a discesa, selezionare il **Fuso orario** in base alla posizione geografica in cui viene distribuito il dispositivo. Il fuso orario predefinito per il dispositivo è PST. Il dispositivo utilizzerà questo fuso orario per tutte le operazioni pianificate.
    2. Specificare un **Server NTP primario** per il dispositivo o accettare il valore predefinito di time.windows.com. Assicurarsi che la rete consenta il traffico NTP dal data center a Internet.
    3. Facoltativamente, specificare un **Server NTP secondario** per il dispositivo.
    4. Fare clic su **Applica** per convalidare e applicare le impostazioni ora configurate.

6. Nella pagina **Impostazioni cloud** attivare il dispositivo con il servizio Data Box Edge nel portale di Azure.
    
    1. Immettere la **chiave di attivazione** ottenuta in [Ottieni chiave di attivazione](data-box-edge-deploy-prep.md#get-the-activation-key) per Data Box Edge.

    2. Fare clic su **Apply**. 
       
         ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-6.png)
    
    3. Dopo aver attivato il dispositivo, vengono visualizzate le opzioni relative alla modalità di connessione. Queste impostazioni devono essere configurate se si prevede di usare il dispositivo in modalità disconnessa o parzialmente disconnessa. 

        ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-7.png)    

La configurazione del dispositivo è completata. È ora possibile aggiungere condivisioni al dispositivo.


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati presentati argomenti relativi a Data Box Edge, ad esempio:

> [!div class="checklist"]
> * Connettersi al dispositivo fisico
> * Configurare e attivare il dispositivo fisico


Passare all'esercitazione successiva per informazioni su come trasferire dati con Data Box Edge.

> [!div class="nextstepaction"]
> [Trasferire dati con Data Box Edge](./data-box-edge-deploy-add-shares.md).