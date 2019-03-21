---
title: Guida introduttiva - configurare la crittografia SSL end-to-end con il Gateway applicazione di Azure - portale di Azure | Microsoft Docs
description: Informazioni su come usare il portale di Azure per creare un Gateway applicazione di Azure con crittografia SSL end-to-end.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 3/19/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: e47a3e1231701f3339057e25ee4388aff0c9fbd7
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58227597"
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-the-portal"></a>Configurare SSL end-to-end tramite il Gateway applicazione con il portale

Questo articolo illustra come usare il portale di Azure per configurare la crittografia SSL end-to-end con un gateway applicazione lo SKU v1.  

> [!NOTE]
> SKU v2 del Gateway applicazione richiede i certificati radice trusted per abilitare la configurazione end-to-end. Non è ancora disponibile il supporto del portale per l'aggiunta di certificati radice attendibili. Pertanto, in caso di SKU V2 osserverà [configurare SSL end-to-end tramite PowerShell](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="before-you-begin"></a>Prima di iniziare

Per configurare SSL end-to-end con un gateway applicazione, sono necessari un certificato per il gateway e i certificati per i server back-end. Il certificato del gateway viene usato per derivare una chiave simmetrica in base alla specifica del protocollo SSL. La chiave simmetrica viene quindi usata per crittografare e decrittografare il traffico inviato al gateway. Per la crittografia SSL end-to-end, il back-end deve essere incluso nell'elenco elementi consentiti con il gateway applicazione. A tale scopo, caricare il certificato pubblico dei server back-end, noto anche come certificati di autenticazione, il gateway applicazione. L'aggiunta del certificato garantisce che il gateway applicazione comunichi solo con istanze di back-end note, proteggendo ulteriormente la comunicazione end-to-end.

Per altre informazioni, vedere [SSL SSL end-to-end e chiusura](https://docs.microsoft.com/azure/application-gateway/ssl-overview).

## <a name="create-a-new-application-gateway-with-end-to-end-ssl"></a>Creare un nuovo gateway applicazione con SSL end-to-end

Per creare un nuovo gateway applicazione con la crittografia SSL end-to-end, è necessario abilitare innanzitutto la terminazione SSL durante la creazione di un nuovo gateway applicazione. Abilita la crittografia SSL per la comunicazione tra il client e il gateway applicazione. Quindi, dovrai elenco elementi consentiti certificati per i server back-end nelle impostazioni HTTP per abilitare la crittografia SSL per la comunicazione tra il server gateway e back-end dell'applicazione, quindi eseguire la crittografia SSL end-to-end.

### <a name="enable-ssl-termination-while-creating-a-new-application-gateway"></a>Abilitare la terminazione SSL durante la creazione di un nuovo gateway applicazione

Fare riferimento a questo articolo per comprendere come [abilitare la terminazione SSL durante la creazione di un nuovo gateway applicazione](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="whitelist-certificates-for-backend-servers"></a>Elenco elementi consentiti certificati per server back-end

1. Fare clic su **Tutte le risorse** e quindi selezionare **myAppGateway**.

2. Selezionare **delle impostazioni HTTP** nel menu a sinistra. Azure ha creato automaticamente un'impostazione HTTP predefinito, **appGatewayBackendHttpSettings**, quando è stato creato il gateway applicazione. 

3. Select **appGatewayBackendHttpSettings**.

4. Sotto **Protocol**, selezionare **HTTPS**. Un riquadro per **certificati di autenticazione back-end** verranno visualizzati. 

5. Sotto **certificati di autenticazione back-end**, scegliere **Crea nuovo**.

6. Immettere adatto **nome**.

7. Caricare il certificato usando il **certificato CER caricare** casella.![ addcert](./media/end-to-end-ssl-portal/addcert.png)

   > [!NOTE]
   > Il certificato fornito in questo passaggio deve essere la chiave pubblica del certificato PFX presente nel back-end. Esportare il certificato (non il certificato radice) installato nel server back-end in formato CER e usarlo in questo passaggio. Questo passaggio mostra come aggiungere il back-end all'elenco elementi consentiti con il gateway applicazione.

8. Selezionare **Salva**.

## <a name="enable-end-to-end-ssl-for-existing-application-gateway"></a>Abilitare SSL end-to-end per il gateway applicazione esistente

Per configurare un gateway applicazione esistente con la crittografia SSL end-to-end, è necessario prima della terminazione SSL Abilita nel listener. Abilita la crittografia SSL per la comunicazione tra il client e il gateway applicazione. Quindi, dovrai elenco elementi consentiti certificati per i server back-end nelle impostazioni HTTP per abilitare la crittografia SSL per la comunicazione tra il server gateway e back-end dell'applicazione, quindi eseguire la crittografia SSL end-to-end.

È necessario usare un listener con il protocollo HTTPS e il certificato per l'abilitazione della terminazione SSL. È possibile modificare il protocollo di un listener esistente. Pertanto, è possibile scegliere di usare un listener esistente con certificato e il protocollo HTTPS o creare un nuovo listener. Nel caso in cui si sceglie il primo, è possibile ignorare i passaggi a indicati di seguito **chiusura abilita SSL nel gateway applicazione esistente** e passare direttamente alla **elenco elementi consentiti certificati per server back-end** sezione. Se si sceglie quest'ultimo, seguire questi passaggi. 

### <a name="enable-ssl-termination-in-existing-application-gateway"></a>Abilitare la terminazione SSL nel gateway applicazione esistente

1. Fare clic su **Tutte le risorse** e quindi selezionare **myAppGateway**.

2. Selezionare **listener** nel menu a sinistra.

3. Scegliere tra **base** e **multisito** listener in base alle proprie esigenze.

4. Sotto **Protocol**, selezionare **HTTPS**. Un riquadro per **certificato** verranno visualizzati.

5. Caricare il certificato PFX che si intende usare per la terminazione SSL tra il client e il gateway applicazione.

   > [!NOTE]
   > A scopo di test, è possibile usare un certificato autofirmato. Non si utilizzino certificati autofirmati per carichi di lavoro di produzione. Informazioni su come [creare un certificato autofirmato](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal#create-a-self-signed-certificate).

6. Aggiungere altre impostazioni necessarie per la **Listener** in base alle proprie esigenze.

7. Selezionare **OK** per salvare.

### <a name="whitelist-certificates-for-backend-servers"></a>Elenco elementi consentiti certificati per server back-end

1. Fare clic su **Tutte le risorse** e quindi selezionare **myAppGateway**.

2. Selezionare **delle impostazioni HTTP** nel menu a sinistra. È possibile entrambi i certificati elenco elementi consentiti in un back-end HTTP esistente impostando o creare una nuova impostazione HTTP. Nel seguente passaggio, si eseguirà il certificato elenco elementi consentiti per l'impostazione predefinita HTTP **appGatewayBackendHttpSettings**.

3. Select **appGatewayBackendHttpSettings**.

4. Sotto **Protocol**, selezionare **HTTPS**. Un riquadro per **certificati di autenticazione back-end** verranno visualizzati. 

5. Sotto **certificati di autenticazione back-end**, scegliere **Crea nuovo**.

6. Immettere adatto **nome**.

7. Caricare il certificato usando il **certificato CER caricare** casella.![ addcert](./media/end-to-end-ssl-portal/addcert.png)

   > [!NOTE]
   > Il certificato fornito in questo passaggio deve essere la chiave pubblica del certificato PFX presente nel back-end. Esportare il certificato (non il certificato radice) installato nel server back-end in formato CER e usarlo in questo passaggio. Questo passaggio mostra come aggiungere il back-end all'elenco elementi consentiti con il gateway applicazione.

8. Selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Gestire il traffico Web con un gateway applicazione mediante l'interfaccia della riga di comando di Azure](./tutorial-manage-web-traffic-cli.md)
