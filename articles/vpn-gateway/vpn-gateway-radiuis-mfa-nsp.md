---
title: Autenticazione RADIUS del gateway VPN protetto di Azure con il server NPS per l'autenticazione a più fattori | Microsoft Docs
description: Descrive come integrare l'autenticazione RADIUS del gateway di Azure con server NPS per l'autenticazione a più fattori.
services: vpn-gateway
documentationcenter: na
author: ahmadnyasin
manager: willchen
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 4e11b1bc16f874f892288f9677a71023f483de7c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60458129"
---
# <a name="integrate-azure-vpn-gateway-radius-authentication-with-nps-server-for-multi-factor-authentication"></a>Integrare l'autenticazione RADIUS del gateway VPN di Azure con server NPS per l'autenticazione a più fattori 

L'articolo descrive come integrare il server di criteri di rete (NPS) con l'autenticazione RADIUS del gateway VPN di Azure per fornire un'autenticazione a più fattori per le connessioni VPN point-to-site. 

## <a name="prerequisite"></a>Prerequisito

Per abilitare l'autenticazione a più fattori, gli utenti devono usare Azure Active Directory (Azure AD), che deve essere sincronizzata dall'ambiente locale o cloud. L'utente deve anche avere già completato il processo di registrazione automatica per l'autenticazione a più fattori.  Per altre informazioni, vedere come [Configurare l'account per la verifica in due passaggi](../active-directory/user-help/multi-factor-authentication-end-user-first-time.md)

## <a name="detailed-steps"></a>Procedura dettagliata

### <a name="step-1-create-a-virtual-network-gateway"></a>Passaggio 1: Creare un gateway di rete virtuale

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nella rete virtuale che ospiterà il gateway di rete virtuale, selezionare **Subnet**, quindi selezionare **Subnet gateway** per creare una subnet. 

    ![Immagine che illustra come aggiungere una subnet gateway](./media/vpn-gateway-radiuis-mfa-nsp/gateway-subnet.png)
3. Creare un gateway di rete virtuale specificando le impostazioni seguenti:

    - **Tipo di gateway**: Selezionare **VPN**.
    - **Tipo di VPN**: Selezionare **Basato su route**.
    - **SKU**: Selezionare un tipo di SKU in base alle esigenze.
    - **Rete virtuale**: Selezionare la rete virtuale in cui è stata creata la subnet del gateway.

        ![Immagine che illustra le impostazioni del gateway di rete virtuale](./media/vpn-gateway-radiuis-mfa-nsp/create-vpn-gateway.png)


 
### <a name="step-2-configure-the-nps-for-azure-mfa"></a>Passaggio 2 - Configurare i criteri di rete per Azure MFA

1. Sul server NPS, [installare l'estensione NPS per Multi-Factor Authentication di Azure](../active-directory/authentication/howto-mfa-nps-extension.md#install-the-nps-extension).
2. Aprire la console NSP, fare clic con il tasto destro del mouse su **Client RADIUS**, quindi selezionare **Nuovo**. Creare il client RADIUS specificando le impostazioni seguenti:

    - **Nome descrittivo**: Digitare un nome qualsiasi.
    - **Indirizzo (IP o DNS)**: Digitare la subnet del gateway creata nel passaggio 1.
    - **Segreto condiviso**: digitare qualsiasi chiave privata e annotarla per un uso successivo.

      ![Immagine delle impostazioni del client RADIUS](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client1.png)

 
3.  Nella scheda **Avanzate**, impostare il nome del fornitore su **RADIUS Standard** e verificare che la casella di controllo **Opzioni aggiuntive** sia deselezionata.

    ![Immagine delle impostazioni Avanzate del client RADIUS](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client2.png)

4. Passare a **Criteri** > **Criteri di rete**, fare doppio clic sul criterio **Connessioni al server di Routing e accesso remoto Microsoft**, selezionare **Concedi accesso**, quindi fare clic su **OK**.

### <a name="step-3-configure-the-virtual-network-gateway"></a>Passaggio 3: configurare il gateway di rete virtuale

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Aprire il gateway di rete virtuale creato. Assicurarsi che il tipo di gateway sia impostato su **VPN** e che il tipo di VPN sia **Basato su route**.
3. Fare clic su **Configurazione point-to-site** > **Configura ora**, quindi specificare le impostazioni seguenti:

    - **Pool di indirizzi**: Digitare la subnet del gateway creata nel passaggio 1.
    - **Tipo di autenticazione**: Selezionare **autenticazione RADIUS**.
    - **Indirizzo IP del server**: Digitare l'indirizzo IP del server NPS.

      ![Immagine delle impostazioni point-to-site](./media/vpn-gateway-radiuis-mfa-nsp/configure-p2s.png)

## <a name="next-steps"></a>Passaggi successivi

- [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md)
- [Integrare l'infrastruttura NPS esistente con Azure Multi-Factor Authentication](../active-directory/authentication/howto-mfa-nps-extension.md)
