---
title: 'Guida introduttiva: configurare la crittografia SSL end-to-end con applicazione Azure gateway-portale di Azure | Microsoft Docs'
description: Informazioni su come usare la portale di Azure per creare un gateway applicazione Azure con la crittografia SSL end-to-end.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/30/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: a37b313bd808ee0441d84ac92050b087eba7ac9d
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097158"
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-the-portal"></a>Configurare SSL end-to-end usando il gateway applicazione con il portale

Questo articolo illustra come usare la portale di Azure per configurare la crittografia SSL end-to-end con uno SKU del gateway applicazione V1.  

> [!NOTE]
> Lo SKU del gateway applicazione V2 richiede certificati radice attendibili per abilitare la configurazione end-to-end.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="before-you-begin"></a>Prima di iniziare

Per configurare SSL end-to-end con un gateway applicazione, è necessario un certificato per il gateway e i certificati per i server back-end. Il certificato del gateway viene usato per derivare una chiave simmetrica in base alla specifica del protocollo SSL. La chiave simmetrica viene quindi usata per crittografare e decrittografare il traffico inviato al gateway. Per la crittografia SSL end-to-end, i server back-end corretti devono essere consentiti nel gateway applicazione. A tale scopo, caricare il certificato pubblico dei server back-end, noti anche come certificati di autenticazione (V1) o certificati radice attendibili (v2), nel gateway applicazione. L'aggiunta del certificato garantisce che il gateway applicazione comunichi solo con istanze di back-end note. proteggendo ulteriormente la comunicazione end-to-end.

Per altre informazioni, vedere [terminazione SSL e SSL end-to-end](https://docs.microsoft.com/azure/application-gateway/ssl-overview).

## <a name="create-a-new-application-gateway-with-end-to-end-ssl"></a>Creare un nuovo gateway applicazione con SSL end-to-end

Per creare un nuovo gateway applicazione con la crittografia SSL end-to-end, è necessario abilitare prima la terminazione SSL durante la creazione di un nuovo gateway applicazione. In questo modo verrà abilitata la crittografia SSL per la comunicazione tra il client e il gateway applicazione. Sarà quindi necessario inserire i certificati nell'elenco elementi consentiti per i server back-end nelle impostazioni HTTP per abilitare la crittografia SSL per la comunicazione tra il gateway applicazione e i server back-end, ottenendo la crittografia SSL end-to-end.

### <a name="enable-ssl-termination-while-creating-a-new-application-gateway"></a>Abilitare la terminazione SSL durante la creazione di un nuovo gateway applicazione

Vedere questo articolo per informazioni su come [abilitare la terminazione SSL durante la creazione di un nuovo gateway applicazione](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="add-authenticationroot-certificate-of-back-end-servers"></a>Aggiungere l'autenticazione/certificato radice dei server back-end

1. Fare clic su **Tutte le risorse** e quindi selezionare **myAppGateway**.

2. Selezionare **impostazioni http** dal menu a sinistra. Azure ha creato automaticamente un'impostazione HTTP predefinita, **appGatewayBackendHttpSettings**, quando è stato creato il gateway applicazione. 

3. Selezionare **appGatewayBackendHttpSettings**.

4. In **protocollo**selezionare **https**. Verrà visualizzato un riquadro per i **certificati di autenticazione back-end o i certificati radice attendibili** . 

5. Scegliere **Crea nuovo**.

6. Immettere un **nome**appropriato.

7. Selezionare il file del certificato usando la casella **Carica certificato CER** .

   Per i gateway applicazione standard e WAF (V1), è necessario caricare la chiave pubblica del certificato del server back-end in formato CER.

   ![addcert](./media/end-to-end-ssl-portal/addcert.png)

   Per i gateway applicazione Standard_v2 e WAF_v2, è necessario caricare il **certificato radice** del certificato del server back-end in formato CER. Se il certificato back-end viene emesso da un'autorità di certificazione nota, è possibile selezionare la casella "utilizza certificato CA noto" e non è necessario caricare un certificato.

   ![addtrustedrootcert](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

   ![rootcert](./media/end-to-end-ssl-portal/trustedrootcert.png)

8. Selezionare **Salva**.

## <a name="enable-end-to-end-ssl-for-existing-application-gateway"></a>Abilitare SSL end-to-end per il gateway applicazione esistente

Per configurare un gateway applicazione esistente con la crittografia SSL end-to-end, è necessario abilitare prima la terminazione SSL nel listener. In questo modo verrà abilitata la crittografia SSL per la comunicazione tra il client e il gateway applicazione. Sarà quindi necessario inserire i certificati nell'elenco elementi consentiti per i server back-end nelle impostazioni HTTP per abilitare la crittografia SSL per la comunicazione tra il gateway applicazione e i server back-end, ottenendo la crittografia SSL end-to-end.

Per abilitare la terminazione SSL, è necessario usare un listener con il protocollo HTTPS e il certificato. Quindi, è possibile scegliere di usare un listener esistente con il protocollo e il certificato HTTPS oppure creare un nuovo listener. Se si sceglie il primo, è possibile ignorare i passaggi indicati di seguito per **abilitare la terminazione SSL nel gateway applicazione esistente** e passare direttamente a **Aggiungi autenticazione/certificati radice attendibili per i server back-end** . Se si sceglie il secondo, attenersi alla procedura riportata di seguito.

### <a name="enable-ssl-termination-in-existing-application-gateway"></a>Abilitare la terminazione SSL nel gateway applicazione esistente

1. Fare clic su **Tutte le risorse** e quindi selezionare **myAppGateway**.

2. Selezionare **listener** dal menu a sinistra.

3. È possibile scegliere tra il listener di base e **multisito** in **base** alla necessità.

4. In **protocollo**selezionare **https**. Verrà visualizzato un riquadro per il **certificato** .

5. Caricare il certificato PFX che si intende usare per la terminazione SSL tra il client e il gateway applicazione.

   > [!NOTE]
   > A scopo di test, è possibile usare un certificato autofirmato. ma non è consigliabile per i carichi di lavoro di produzione perché sono più difficili da gestire e non sono completamente protetti. Informazioni su come [creare un certificato autofirmato](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal#create-a-self-signed-certificate).

6. Aggiungere altre impostazioni necessarie per il **listener** in base ai requisiti.

7. Selezionare **OK** per salvare.

### <a name="add-authenticationtrusted-root-certificates-of-back-end-servers"></a>Aggiungere l'autenticazione/certificati radice attendibili dei server back-end

1. Fare clic su **Tutte le risorse** e quindi selezionare **myAppGateway**.

2. Selezionare **impostazioni http** dal menu a sinistra. È possibile inserire i certificati nell'elenco elementi consentiti in un'impostazione HTTP back-end esistente o creare una nuova impostazione HTTP. Nel passaggio seguente, il certificato per l'impostazione HTTP predefinita è **appGatewayBackendHttpSettings**.

3. Selezionare **appGatewayBackendHttpSettings**.

4. In **protocollo**selezionare **https**. Verrà visualizzato un riquadro per i **certificati di autenticazione back-end o i certificati radice attendibili** . 

5. Scegliere **Crea nuovo**.

6. Immettere un **nome**appropriato.

7. Selezionare il file del certificato usando la casella **Carica certificato CER** .

   Per i gateway applicazione standard e WAF (V1), è necessario caricare la chiave pubblica del certificato del server back-end in formato CER.

   ![addcert](./media/end-to-end-ssl-portal/addcert.png)

   Per i gateway applicazione Standard_v2 e WAF_v2, è necessario caricare il **certificato radice** del certificato del server back-end in formato CER. Se il certificato back-end viene emesso da un'autorità di certificazione nota, è possibile selezionare la casella "utilizza certificato CA noto" e non è necessario caricare un certificato.

   ![addtrustedrootcert](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

8. Selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Gestire il traffico Web con un gateway applicazione mediante l'interfaccia della riga di comando di Azure](./tutorial-manage-web-traffic-cli.md)
