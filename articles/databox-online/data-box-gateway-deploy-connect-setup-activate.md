---
title: Connettersi ad Azure Data Box Gateway, configurarlo e attivarlo nel portale di Azure | Microsoft Docs
description: La terza esercitazione relativa alla distribuzione di Data Box Gateway illustra come connettersi al dispositivo virtuale, quindi configurarlo e attivarlo.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 01/09/2019
ms.author: alkohli
ms.openlocfilehash: 5e83eb46a4d62c6aaf0862d4bb1aa046c5d64dde
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2019
ms.locfileid: "54120514"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-gateway-preview"></a>Esercitazione: Connettersi ad Azure Data Box Gateway (anteprima), configurarlo e attivarlo 

## <a name="introduction"></a>Introduzione

Questa esercitazione descrive come connettersi al dispositivo Data Box Gateway, configurarlo e attivarlo usando l'interfaccia utente Web locale. 

Per completare il processo di configurazione e attivazione sono necessari circa 10 minuti. 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Connettersi al dispositivo virtuale
> * Configurare e attivare il dispositivo virtuale

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.


> [!IMPORTANT]
> - Data Box Gateway è in anteprima. Rivedere le [condizioni del servizio per l'anteprima di Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) prima di ordinare e distribuire la soluzione. 


## <a name="prerequisites"></a>Prerequisiti

Prima di configurare Data Box Gateway, assicurarsi di:

* Aver eseguito il provisioning di un dispositivo virtuale e di aver ottenuto un URL connesso a esso, come descritto in [Eseguire il provisioning di un dispositivo Data Box Gateway in Hyper-V](data-box-gateway-deploy-provision-hyperv.md) o [Eseguire il provisioning di un dispositivo Data Box Gateway in VMware](data-box-gateway-deploy-provision-vmware.md).
* Disporre della chiave di attivazione dal servizio Data Box Gateway creato per gestire i dispositivi Data Box Gateway. Per ulteriori informazioni, andare all'articolo sulle [operazioni preliminari alla distribuzione di Azure Data Box Gateway](data-box-gateway-deploy-prep.md).

<!--* If this is the second or subsequent virtual device that you are registering with an existing StorSimple Device Manager service, you should have the service data encryption key. This key was generated when the first device was successfully registered with this service. If you have lost this key, see [Get the service data encryption key](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) for your Data Box Gateway.-->

## <a name="connect-to-the-local-web-ui-setup"></a>Connettersi all'installazione dell'interfaccia utente Web locale 

1. Aprire una finestra del browser e connettersi all'interfaccia utente Web locale. Digitare: 
   
   [https://ip-address-of-network-interface](https://ip-address-of-network-interface)
   
   Usare l'URL di connessione annotato nell'esercitazione precedente. Viene visualizzato un errore in cui si indica che si è verificato un problema con il certificato di sicurezza del sito Web. Fare clic su **Passa a questa pagina Web**. (Questi passaggi variano in base al browser in uso.)
   
    ![](./media/data-box-gateway-deploy-connect-setup-activate/image2.png)

2. Accedere all'interfaccia utente Web del dispositivo virtuale. La password predefinita è *Password1*. 
   
    ![](./media/data-box-gateway-deploy-connect-setup-activate/image3.png)

3. Viene richiesto di modificare la password dell'amministratore del dispositivo. Digitare una nuova password che contenga minimo 8 caratteri e massimo 16. La password deve contenere 3 dei seguenti tipi di caratteri: maiuscolo, minuscolo, numerico e speciale.

    ![](./media/data-box-gateway-deploy-connect-setup-activate/image4.png)

Viene aperto il **Dashboard** del dispositivo.

## <a name="set-up-and-activate-the-virtual-device"></a>Configurare e attivare il dispositivo virtuale
 
1. Dal dashboard è possibile accedere a diverse impostazioni necessarie per configurare e registrare il dispositivo virtuale con il servizio Data Box Gateway. Le **Impostazioni di rete**, le **Impostazioni proxy Web** e le **Impostazioni ora** sono facoltative. Le sole impostazioni obbligatorie sono **Nome dispositivo** e **Impostazioni cloud**.
   
    ![](./media/data-box-gateway-deploy-connect-setup-activate/image5.png)

2. Nella pagina **Nome dispositivo** configurare un nome descrittivo per il dispositivo. Questo nome può includere da 1 a 15 caratteri e può contenere lettere, numeri e trattini.

    ![](./media/data-box-gateway-deploy-connect-setup-activate/image6.png)

3. Facoltativamente configurare le **impostazioni di rete**. Sarà presente almeno un'interfaccia di rete o più a seconda di quante ne sono state configurate nella macchina virtuale sottostante. Di seguito è illustrata la pagina **Impostazioni di rete** di un dispositivo virtuale con un'interfaccia di rete abilitata.
    
    ![](./media/data-box-gateway-deploy-connect-setup-activate/image7.png)
   
    Quando si configurano le impostazioni di rete, tenere presente quanto segue:

    - Se nell'ambiente è abilitato DHCP, le interfacce di rete vengono configurate automaticamente. Di conseguenza, indirizzo IP, subnet, gateway e DNS vengono assegnati automaticamente.
    - Se DHCP non è abilitato, è possibile assegnare indirizzi IP statici se necessario.
    - È possibile configurare l'interfaccia di rete come IPv4.

    >[!NOTE] 
    > È consigliabile non cambiare l'indirizzo IP locale dell'interfaccia di rete da statico a DHCP, a meno che non si abbia un altro indirizzo IP per la connessione al dispositivo. Se si usa un'interfaccia di rete e si passa a DHCP, non sarà possibile determinare l'indirizzo DHCP. Se si vuole passare a un indirizzo DHCP, attendere fino al termine della registrazione del dispositivo con il servizio e quindi eseguire il passaggio. È possibile visualizzare gli indirizzi IP di tutti gli adattatori in **Proprietà dispositivo** nel portale di Azure per il servizio.

4. Configurare il server proxy Web (facoltativo). Sebbene la configurazione del proxy Web sia facoltativa, tenere presente che se si utilizza un proxy Web, è possibile configurarlo solo qui.
   
   ![](./media/data-box-gateway-deploy-connect-setup-activate/image8.png)
   
   Nella pagina **Proxy Web** :
   
   1. Fornire l'**URL proxy Web** in questo formato: *http://&lt;indirizzo IP-host o FDQN&gt;:Numero della porta*. Notare che gli URL HTTPS non sono supportati.
   2. Specificare **Autenticazione** come **Basic** o **Nessuna**.
   3. Se si usa l'autenticazione, è necessario anche fornire **Nome utente** e **Password**.
   4. Fare clic su **Apply**. Le impostazioni proxy Web configurate vengono così convalidate e applicate.

5. Configurare le impostazioni ora per il dispositivo, ad esempio il fuso orario e i server NTP primari e secondari (facoltativo). I server NTP sono obbligatori in quanto il dispositivo deve sincronizzare l'ora e consentire l'autenticazione con i provider del servizio cloud.
    
    ![](./media/data-box-gateway-deploy-connect-setup-activate/image9.png)
    
    Nella pagina **Impostazioni ora** :
    
    1. Nell'elenco a discesa, selezionare il **Fuso orario** in base alla posizione geografica in cui viene distribuito il dispositivo. Il fuso orario predefinito per il dispositivo è PST. Il dispositivo utilizzerà questo fuso orario per tutte le operazioni pianificate.
    2. Specificare un **Server NTP primario** per il dispositivo o accettare il valore predefinito di time.windows.com. Assicurarsi che la rete consenta il traffico NTP dal data center a Internet.
    3. Facoltativamente, specificare un **Server NTP secondario** per il dispositivo.
    4. Fare clic su **Apply**. Le impostazioni ora configurate vengono così convalidate e applicate.

6. Nella pagina **Impostazioni cloud** attivare il dispositivo con il servizio Data Box Gateway nel portale di Azure.
    
    1. Immettere la **chiave di attivazione** ottenuta in [Ottieni chiave di attivazione](data-box-gateway-deploy-prep.md#get-the-activation-key) per Data Box Gateway.

    2. Fare clic su **Attiva**. 
       
         ![](./media/data-box-gateway-deploy-connect-setup-activate/image10.png)
    
    3. Potrebbe essere necessario attendere un minuto per l'attivazione del dispositivo. Dopo l'attivazione, la pagina si aggiorna per indicare che il dispositivo è stato attivato correttamente.


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati presentati argomenti relativi a Data Box Gateway, ad esempio:

> [!div class="checklist"]
> * Connettersi al dispositivo virtuale
> * Configurare e attivare il dispositivo virtuale


Passare all'esercitazione successiva per informazioni su come trasferire dati con Data Box Gateway.

> [!div class="nextstepaction"]
> [Trasferire dati con Data Box Gateway](./data-box-gateway-deploy-add-shares.md).
