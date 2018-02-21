---
title: Creare un probe personalizzato - Gateway applicazione di Azure - Portale di Azure | Microsoft Docs
description: Informazioni su come creare un probe personalizzato per un gateway applicazione usando il portale
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 33fd5564-43a7-4c54-a9ec-b1235f661f97
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: davidmu
ms.openlocfilehash: bb77c9b39e1aa89f6411de8ec3b1fca41e954bf2
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>Creare un probe personalizzato per un gateway applicazione con il portale

> [!div class="op_single_selector"]
> * [Azure portal](application-gateway-create-probe-portal.md)
> * [PowerShell per Azure Resource Manager](application-gateway-create-probe-ps.md)
> * [PowerShell per Azure classico](application-gateway-create-probe-classic-ps.md)

In questo articolo viene aggiunto un probe personalizzato a un gateway applicazione esistente tramite il portale di Azure. I probe personalizzati sono utili per le applicazioni che dispongono di una pagina di controllo dell'integrità specifica o per quelle che non rispondono in modo corretto all'applicazione Web predefinita.

## <a name="before-you-begin"></a>Prima di iniziare

Se non si dispone già di un gateway applicazione, vedere [Creare un gateway applicazione con il portale](application-gateway-create-gateway-portal.md) per creare un gateway applicazione da usare.

## <a name="createprobe"></a>Creare il probe

I probe vengono configurati con un processo in due passaggi nel portale. Il primo passaggio consiste nel creare il probe. Nel secondo passaggio lo si aggiunge alle impostazioni http back-end del gateway applicazione.

1. Accedere al [Portale di Azure](https://portal.azure.com). Se non si dispone già di un account, è possibile iscriversi per ottenere una [versione di valutazione gratuita della durata di un mese](https://azure.microsoft.com/free).

1. Nel riquadro Preferiti del portale di Azure fare clic su Tutte le risorse. Fare clic su gateway applicazione nel pannello Tutte le risorse. Se nella sottoscrizione selezionata sono già presenti risorse, è possibile immettere partners.contoso.net nella casella Filtra per nome per accedere con facilità al gateway applicazione.

1. Fare clic su **Probe** e quindi su **Aggiungi** per aggiungere un probe.

  ![Pannello Aggiungi probe con informazioni inserite][1]

1. Nel pannello **Aggiungi probe integrità** specificare le informazioni necessarie per il probe e al termine fare clic su **OK**.

  |**Impostazione** | **Valore** | **Dettagli**|
  |---|---|---|
  |**Nome**|customProbe|Nome descrittivo del probe accessibile nel portale.|
  |**Protocollo**|HTTP o HTTPS | Protocollo usato per il probe di integrità.|
  |**Host**|vale a dire contoso.com|Nome host usato per il probe. Applicabile solo quando vengono configurati più siti nel gateway applicazione. In caso contrario, usare "127.0.0.1". Questo valore è diverso dal nome host della VM.|
  |**Percorso**|/ o un altro percorso|Parte restante dell'URL completo per il probe personalizzato. Un percorso valido inizia con "/". Per il percorso predefinito http://contoso.com è sufficiente usare "/" |
  |**Intervallo (sec)**|30|Frequenza con cui viene eseguito il probe per controllare l'integrità. Non è consigliabile impostare un valore inferiore a 30 secondi.|
  |**Timeout (secondi)**|30|Durata dell'attesa prima che si verifichi il timeout del probe. L'intervallo di timeout deve essere abbastanza elevato da poter effettuare una chiamata http per assicurarsi che la pagina relativa all'integrità del back-end sia disponibile.|
  |**Soglia non integra**|3|Numero di tentativi non riusciti prima che venga stabilito uno stato di non integrità. Se la soglia è 0 significa che, se un controllo integrità ha esito negativo, il back-end verrà immediatamente considerato non integro.|

  > [!IMPORTANT]
  > Il nome host non è uguale al nome del server. Questo valore è il nome dell'host virtuale in esecuzione nel server applicazioni. Il probe viene inviato a http://(name host):(porta da impostazioni HTTP)/percorsoURL

## <a name="add-probe-to-the-gateway"></a>Aggiungere il probe al gateway

Ora che il probe è stato creato, deve essere aggiunto al gateway. Le impostazioni del probe vengono definite nelle impostazioni HTTP back-end del gateway applicazione.

1. Fare clic su **Impostazioni HTTP** nel gateway applicazione e quindi fare clic sulle impostazioni HTTP back-end correnti presenti nella finestra per visualizzare il pannello di configurazione.

  ![Finestra delle impostazioni HTTP][2]

1. Nel pannello delle impostazioni **appGatewayBackEndHttpSettings** selezionare la casella di controllo **Usa probe personalizzato** e scegliere il probe creato nella sezione [Creare il probe](#createprobe) nell'elenco a discesa **Probe personalizzato**.
Al termine fare clic su **Salva** per applicare le impostazioni.

Il probe predefinito controlla l'accesso predefinito all'applicazione Web. Ora che è stato creato un probe personalizzato, il gateway applicazione usa il percorso personalizzato definito per monitorare l'integrità per i server back-end. In base ai criteri definiti, il gateway applicazione controlla il percorso specificato nel probe. Se la chiamata a host:Porta/percorso non restituisce una risposta di stato HTTP 200-399, dopo che è stata raggiunta la soglia di non integrità il server viene escluso dalla rotazione. Il probe continua a essere eseguito sull'istanza non integra per determinare quando risulterà di nuovo integra. Quando l'istanza viene nuovamente aggiunta al pool di server integri, il flusso del traffico verso l'istanza riprenderà e il relativo probe verrà eseguito all'intervallo normale specificato dall'utente.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come configurare l'offload SSL con un gateway applicazione di Azure, vedere [Configurare un gateway applicazione per l'offload SSL con il portale](application-gateway-ssl-portal.md)

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png

