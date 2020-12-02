---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/27/2020
ms.author: alkohli
ms.openlocfilehash: fa65a7354112a2b220686372459b348d45832dd9
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467372"
---
Eseguire i passaggi seguenti nell'interfaccia utente Web locale del dispositivo. Questo passaggio richiede circa 15 minuti, incluso il caricamento del file di configurazione VPN (o il file di tag del servizio). 

1. Passare a **Configuration > VPN**. Selezionare **Configura**.

    ![Configura interfaccia utente locale 1](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-1.png)

2. Nel pannello **Configura VPN** :

    - Abilitare **le impostazioni VPN**.
    - Fornire il **segreto condiviso VPN**. Si tratta della chiave condivisa fornita durante la creazione della risorsa di connessione VPN di Azure.
    - Fornire l'indirizzo **IP del gateway VPN** . Si tratta dell'indirizzo IP del gateway di rete locale di Azure.
    - Per **Gruppo PFS** selezionare **nessuno**. 
    - Per il **gruppo DH** selezionare **group2**.
    - Per **metodo di integrità IPSec** selezionare **SHA256**.
    - Per le **costanti di trasformazione crittografia IPSec** selezionare **GCMAES256**.
    - Per le **costanti di trasformazione dell'autenticazione IPSec** selezionare **GCMAES256**.
    - Per **metodo di crittografia IKE** selezionare **AES256**.
    - Selezionare **Applica**.

        ![Configurare l'interfaccia utente locale 2](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-2.png)

    Per altre informazioni sugli algoritmi di crittografia supportati, vedere [informazioni sui requisiti crittografici e sui gateway VPN di Azure](../articles/vpn-gateway/vpn-gateway-about-compliance-crypto.md#ipsecike-policy-faq). 

3. Per caricare il file di configurazione della route VPN, selezionare **carica**. 

    ![Configura interfaccia utente locale 3](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-3.png)

    - Passare al file *JSON* dei tag di servizio scaricato nel sistema locale nel passaggio precedente.
    - Selezionare l'area geografica dell'area di Azure associata al dispositivo, alla rete virtuale e ai gateway.
    - Selezionare **Applica**.

        ![Configura interfaccia utente locale 4](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-4.png)
    
    Il caricamento richiede circa 7-8 minuti sul dispositivo.

4. Per aggiungere route specifiche del client, configurare gli intervalli di indirizzi IP a cui accedere solo usando la VPN. 

    - In **intervalli di indirizzi IP a cui accedere solo tramite VPN**, selezionare **Configura**.
    - Fornire un intervallo IPv4 valido e selezionare **Aggiungi**. Ripetere i passaggi per aggiungere altri intervalli.
    - Selezionare **Applica**.

        ![Configura interfaccia utente locale 5](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-5.png)

