---
title: Configurare la crittografia TLS end-to-end tramite il portale
titleSuffix: Azure Application Gateway
description: Informazioni su come usare il portale di Azure per creare un gateway applicazione con crittografia TLS end-to-end.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: 6f86f32e64bbbe79ea5a403d04f7d6c29ee6b980
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133006"
---
# <a name="configure-end-to-end-tls-by-using-application-gateway-with-the-portal"></a>Configurare TLS end-to-end tramite il gateway applicazione con il portale

Questo articolo descrive come usare il portale di Azure per configurare la crittografia TLS (Transport Layer Security) end-to-end, precedentemente nota come crittografia SSL (Secure Sockets Layer), tramite lo SKU del gateway applicazione di Azure v1.

> [!NOTE]
> Lo SKU del gateway applicazione v2 richiede certificati radice attendibili per l'abilitazione della configurazione end-to-end.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="before-you-begin"></a>Prima di iniziare

Per configurare TLS end-to-end con un gateway applicazione, è necessario un certificato per il gateway. I certificati sono necessari anche per i server back-end. Il certificato del gateway viene utilizzato per derivare una chiave simmetrica in conformità con la specifica del protocollo TLS. La chiave simmetrica viene quindi utilizzata per crittografare e decrittografare il traffico inviato al gateway. 

Per la crittografia TLS end-to-end, i server back-end corretti devono essere consentiti nel gateway applicazione. Per consentire questo accesso, caricare il certificato pubblico dei server back-end, noto anche come certificati di autenticazione (v1) o certificati radice attendibili (v2), nel gateway applicazione. L'aggiunta del certificato garantisce che il gateway applicazione comunichi solo con istanze back-end note. Questa configurazione protegge ulteriormente le comunicazioni end-to-end.

Per altre informazioni, vedere [Panoramica della terminazione TLS e TLS end-to-end con Gateway applicazione](https://docs.microsoft.com/azure/application-gateway/ssl-overview).

## <a name="create-a-new-application-gateway-with-end-to-end-tls"></a>Creare un nuovo gateway applicazione con TLS end-to-endCreate a new application gateway with end-to-end TLS

Per creare un nuovo gateway applicazione con crittografia TLS end-to-end, è necessario innanzitutto abilitare la terminazione TLS durante la creazione di un nuovo gateway applicazione. Questa azione abilita la crittografia TLS per la comunicazione tra il client e il gateway applicazione. Quindi, è necessario inserire nell'elenco Destinatari attendibili i certificati per i server back-end nelle impostazioni HTTP. Questa configurazione abilita la crittografia TLS per la comunicazione tra il gateway applicazione e i server back-end. Ciò consente la crittografia TLS end-to-end.

### <a name="enable-tls-termination-while-creating-a-new-application-gateway"></a>Abilitare la terminazione TLS durante la creazione di un nuovo gateway applicazioneEnable TLS termination while creating a new application gateway

Per altre informazioni, vedere [Abilitare la terminazione TLS durante la creazione di un nuovo gateway applicazione](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="add-authenticationroot-certificates-of-back-end-servers"></a>Aggiungere certificati di autenticazione/radice dei server back-end

1. Fare clic su **Tutte le risorse** e quindi selezionare **myAppGateway**.

2. Seleziona **Impostazioni HTTP** dal menu a sinistra. Azure ha creato automaticamente un'impostazione HTTP predefinita, **appGatewayBackendHttpSettings**, quando è stato creato il gateway applicazione. 

3. Selezionare **appGatewayBackendHttpSettings**.

4. In **Protocollo**selezionare **HTTPS**. Viene visualizzato un riquadro per i certificati di **autenticazione back-end o i certificati radice attendibili.**

5. Selezionare **Crea nuovo**.

6. Nel campo **Nome** immettere un nome appropriato.

7. Selezionare il file del certificato nella casella **Carica certificato CER.**

   Per i gateway applicazione Standard e WAF (v1), è necessario caricare la chiave pubblica del certificato del server back-end in formato CER.

   ![Aggiungere il certificato](./media/end-to-end-ssl-portal/addcert.png)

   Per Standard_v2 e WAF_v2 gateway applicazione, è necessario caricare il certificato radice del certificato del server back-end in formato CER. Se il certificato back-end viene emesso da un'autorità di certificazione (CA) nota, è possibile selezionare la casella di controllo **Usa certificato CA noto** e quindi non è necessario caricare un certificato.

   ![Aggiungere un certificato radice attendibile](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

   ![Certificato radice](./media/end-to-end-ssl-portal/trustedrootcert.png)

8. Selezionare **Salva**.

## <a name="enable-end-to-end-tls-for-an-existing-application-gateway"></a>Abilitare TLS end-to-end per un gateway applicazione esistenteEnable end-to-end TLS for an existing application gateway

Per configurare un gateway applicazione esistente con crittografia TLS end-to-end, è innanzitutto necessario abilitare la terminazione TLS nel listener. Questa azione abilita la crittografia TLS per la comunicazione tra il client e il gateway applicazione. Quindi, inserire tali certificati per i server back-end nelle impostazioni HTTP nell'elenco Destinatari attendibili . Questa configurazione abilita la crittografia TLS per la comunicazione tra il gateway applicazione e i server back-end. Ciò consente la crittografia TLS end-to-end.

È necessario utilizzare un listener con il protocollo HTTPS e un certificato per abilitare la terminazione TLS. È possibile utilizzare un listener esistente che soddisfi tali condizioni o creare un nuovo listener. Se si sceglie l'opzione precedente, è possibile ignorare la sezione "Abilitare la terminazione TLS in un gateway applicazione esistente" e passare direttamente alla sezione "Aggiungere certificati radice attendibili/autenticazione per i server back-end".

Se si sceglie quest'ultima opzione, applicare i passaggi della procedura seguente.
### <a name="enable-tls-termination-in-an-existing-application-gateway"></a>Abilitare la terminazione TLS in un gateway applicazione esistenteEnable TLS termination in an existing application gateway

1. Fare clic su **Tutte le risorse** e quindi selezionare **myAppGateway**.

2. Selezionare **Listener dal** menu a sinistra.

3. Selezionare **Listener di base** o **multisito** in base alle proprie esigenze.

4. In **Protocollo**selezionare **HTTPS**. Viene visualizzato un riquadro per **Certificato.**

5. Caricare il certificato PFX che si intende utilizzare per la terminazione TLS tra il client e il gateway applicazione.

   > [!NOTE]
   > A scopo di test, è possibile utilizzare un certificato autofirmato. Tuttavia, questo non è consigliato per i carichi di lavoro di produzione, perché sono più difficili da gestire e non sono completamente sicuri. Per ulteriori informazioni, vedere [creare un certificato autofirmato.](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal#create-a-self-signed-certificate)

6. Aggiungere altre impostazioni necessarie per il **listener,** a seconda delle esigenze.

7. Selezionare **OK** per salvare.

### <a name="add-authenticationtrusted-root-certificates-of-back-end-servers"></a>Aggiungere certificati radice attendibili/di autenticazione dei server back-end

1. Fare clic su **Tutte le risorse** e quindi selezionare **myAppGateway**.

2. Seleziona **Impostazioni HTTP** dal menu a sinistra. È possibile inserire i certificati in un'impostazione HTTP back-end esistente nell'elenco Destinatari attendibili oppure creare una nuova impostazione HTTP. Nel passaggio successivo, il certificato per l'impostazione HTTP predefinita, **appGatewayBackendHttpSettings**, viene aggiunto all'elenco Destinatari attendibili.

3. Selezionare **appGatewayBackendHttpSettings**.

4. In **Protocollo**selezionare **HTTPS**. Viene visualizzato un riquadro per i certificati di **autenticazione back-end o i certificati radice attendibili.** 

5. Selezionare **Crea nuovo**.

6. Nel campo **Nome** immettere un nome appropriato.

7. Selezionare il file del certificato nella casella **Carica certificato CER.**

   Per i gateway applicazione Standard e WAF (v1), è necessario caricare la chiave pubblica del certificato del server back-end in formato CER.

   ![Aggiungere il certificato](./media/end-to-end-ssl-portal/addcert.png)

   Per Standard_v2 e WAF_v2 gateway applicazione, è necessario caricare il certificato radice del certificato del server back-end in formato CER. Se il certificato back-end viene emesso da un'autorità di certificazione nota, è possibile selezionare la casella di controllo **Usa certificato CA noto** e quindi non è necessario caricare un certificato.

   ![Aggiungere un certificato radice attendibile](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

8. Selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Gestire il traffico Web con un gateway applicazione usando l'interfaccia della riga di comando di Azure](./tutorial-manage-web-traffic-cli.md)
