---
title: Configurare la crittografia TLS end-to-end tramite il portale
titleSuffix: Azure Application Gateway
description: Informazioni su come usare la portale di Azure per creare un gateway applicazione con la crittografia TLS end-to-end.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/14/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: 33240d1f44d2f26569791f72a3d5fc3a6656a757
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84808026"
---
# <a name="configure-end-to-end-tls-by-using-application-gateway-with-the-portal"></a>Configurare TLS end-to-end usando il gateway applicazione con il portale

Questo articolo descrive come usare la portale di Azure per configurare la crittografia end-to-end Transport Layer Security (TLS), precedentemente nota come crittografia Secure Sockets Layer (SSL), tramite lo SKU applicazione Azure gateway V1.

> [!NOTE]
> Lo SKU del gateway applicazione V2 richiede certificati radice attendibili per abilitare la configurazione end-to-end.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="before-you-begin"></a>Prima di iniziare

Per configurare TLS end-to-end con un gateway applicazione, è necessario un certificato per il gateway. I certificati sono necessari anche per i server back-end. Il certificato del gateway viene usato per derivare una chiave simmetrica in conformità con la specifica del protocollo TLS. La chiave simmetrica viene quindi usata per crittografare e decrittografare il traffico inviato al gateway. 

Per la crittografia TLS end-to-end, i server back-end corretti devono essere consentiti nel gateway applicazione. Per consentire l'accesso, caricare il certificato pubblico dei server back-end, noti anche come certificati di autenticazione (V1) o certificati radice attendibili (v2), nel gateway applicazione. L'aggiunta del certificato garantisce che il gateway applicazione comunichi solo con istanze di back-end note. Questa configurazione protegge ulteriormente la comunicazione end-to-end.

Per altre informazioni, vedere [Panoramica della terminazione TLS e della TLS end-to-end con il gateway applicazione](https://docs.microsoft.com/azure/application-gateway/ssl-overview).

## <a name="create-a-new-application-gateway-with-end-to-end-tls"></a>Creare un nuovo gateway applicazione con TLS end-to-end

Per creare un nuovo gateway applicazione con la crittografia TLS end-to-end, è necessario abilitare prima la terminazione TLS durante la creazione di un nuovo gateway applicazione. Questa azione Abilita la crittografia TLS per la comunicazione tra il client e il gateway applicazione. Sarà quindi necessario inserire i certificati per i server back-end nelle impostazioni HTTP per l'elenco dei destinatari sicuri. Questa configurazione consente la crittografia TLS per la comunicazione tra il gateway applicazione e i server back-end. Che consente di eseguire la crittografia TLS end-to-end.

### <a name="enable-tls-termination-while-creating-a-new-application-gateway"></a>Abilitare la terminazione TLS durante la creazione di un nuovo gateway applicazione

Per altre informazioni, vedere [abilitare la terminazione TLS durante la creazione di un nuovo gateway applicazione](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="add-authenticationroot-certificates-of-back-end-servers"></a>Aggiungere l'autenticazione/certificati radice dei server back-end

1. Fare clic su **Tutte le risorse** e quindi selezionare **myAppGateway**.

2. Selezionare **impostazioni http** dal menu a sinistra. Azure ha creato automaticamente un'impostazione HTTP predefinita, **appGatewayBackendHttpSettings**, quando è stato creato il gateway applicazione. 

3. Selezionare **appGatewayBackendHttpSettings**.

4. In **protocollo**selezionare **https**. Viene visualizzato un riquadro per i **certificati di autenticazione back-end o i certificati radice attendibili** .

5. Selezionare **Crea nuovo**.

6. Nel campo **nome** immettere un nome appropriato.

7. Selezionare il file del certificato nella casella **Carica certificato CER** .

   Per i gateway applicazione standard e WAF (V1), è necessario caricare la chiave pubblica del certificato del server back-end in formato CER.

   ![Aggiunta del certificato](./media/end-to-end-ssl-portal/addcert.png)

   Per Standard_v2 e WAF_v2 gateway applicazione, è necessario caricare il certificato radice del certificato del server back-end in formato CER. Se il certificato back-end viene emesso da un'autorità di certificazione (CA) Nota, è possibile selezionare la casella di controllo **Usa certificato CA noto** , quindi non è necessario caricare un certificato.

   ![Aggiungi certificato radice attendibile](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

   ![Certificato radice](./media/end-to-end-ssl-portal/trustedrootcert.png)

8. Selezionare **Salva**.

## <a name="enable-end-to-end-tls-for-an-existing-application-gateway"></a>Abilitare TLS end-to-end per un gateway applicazione esistente

Per configurare un gateway applicazione esistente con la crittografia TLS end-to-end, è necessario abilitare prima la terminazione TLS nel listener. Questa azione Abilita la crittografia TLS per la comunicazione tra il client e il gateway applicazione. Quindi, inserire i certificati per i server back-end nelle impostazioni HTTP nell'elenco dei destinatari sicuri. Questa configurazione consente la crittografia TLS per la comunicazione tra il gateway applicazione e i server back-end. Che consente di eseguire la crittografia TLS end-to-end.

Per abilitare la terminazione TLS è necessario usare un listener con il protocollo HTTPS e un certificato. È possibile usare un listener esistente che soddisfi tali condizioni o creare un nuovo listener. Se si sceglie l'opzione precedente, è possibile ignorare la sezione "abilitare la terminazione TLS in un gateway applicazione esistente" e passare direttamente alla sezione "aggiungere i certificati radice attendibili per i server back-end".

Se si sceglie la seconda opzione, applicare i passaggi descritti nella procedura seguente.
### <a name="enable-tls-termination-in-an-existing-application-gateway"></a>Abilitare la terminazione TLS in un gateway applicazione esistente

1. Fare clic su **Tutte le risorse** e quindi selezionare **myAppGateway**.

2. Selezionare **listener** dal menu a sinistra.

3. Selezionare listener di **base** o **multisito** a seconda dei requisiti.

4. In **protocollo**selezionare **https**. Viene visualizzato un riquadro per il **certificato** .

5. Caricare il certificato PFX che si intende usare per la terminazione TLS tra il client e il gateway applicazione.

   > [!NOTE]
   > A scopo di test, è possibile usare un certificato autofirmato. Tuttavia, questa operazione non è consigliata per i carichi di lavoro di produzione, perché sono più difficili da gestire e non sono completamente protetti. Per altre informazioni, vedere [creare un certificato autofirmato](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal#create-a-self-signed-certificate).

6. Aggiungere altre impostazioni necessarie per il **listener**, a seconda dei requisiti.

7. Selezionare **OK** per salvare.

### <a name="add-authenticationtrusted-root-certificates-of-back-end-servers"></a>Aggiungere l'autenticazione/certificati radice attendibili dei server back-end

1. Fare clic su **Tutte le risorse** e quindi selezionare **myAppGateway**.

2. Selezionare **impostazioni http** dal menu a sinistra. È possibile inserire i certificati in un'impostazione HTTP back-end esistente nell'elenco dei destinatari sicuri oppure creare una nuova impostazione HTTP. Nel passaggio successivo, il certificato per l'impostazione HTTP predefinita, **appGatewayBackendHttpSettings**, viene aggiunto all'elenco dei destinatari sicuri.

3. Selezionare **appGatewayBackendHttpSettings**.

4. In **protocollo**selezionare **https**. Viene visualizzato un riquadro per i **certificati di autenticazione back-end o i certificati radice attendibili** . 

5. Selezionare **Crea nuovo**.

6. Nel campo **nome** immettere un nome appropriato.

7. Selezionare il file del certificato nella casella **Carica certificato CER** .

   Per i gateway applicazione standard e WAF (V1), è necessario caricare la chiave pubblica del certificato del server back-end in formato CER.

   ![Aggiunta del certificato](./media/end-to-end-ssl-portal/addcert.png)

   Per Standard_v2 e WAF_v2 gateway applicazione, è necessario caricare il certificato radice del certificato del server back-end in formato CER. Se il certificato back-end viene emesso da un'autorità di certificazione nota, è possibile selezionare la casella di controllo **Usa certificato CA noto** , quindi non è necessario caricare un certificato.

   ![Aggiungi certificato radice attendibile](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

8. Selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Gestire il traffico Web con un gateway applicazione mediante l'interfaccia della riga di comando di Azure](./tutorial-manage-web-traffic-cli.md)
