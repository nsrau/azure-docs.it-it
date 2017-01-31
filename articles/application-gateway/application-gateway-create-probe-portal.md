---
title: Creare un probe personalizzato per un gateway applicazione con il portale | Documentazione Microsoft
description: Informazioni su come creare un probe personalizzato per un gateway applicazione usando il portale
services: application-gateway
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 33fd5564-43a7-4c54-a9ec-b1235f661f97
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/13/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: aaf13418331f29287399621cb911e4b9f5b33dc0
ms.openlocfilehash: 0fafaff220dfb5d224bc5b57b630f8b2912155f7


---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>Creare un probe personalizzato per un gateway applicazione con il portale
> [!div class="op_single_selector"]
> * [Portale di Azure](application-gateway-create-probe-portal.md)
> * [PowerShell per Azure Resource Manager](application-gateway-create-probe-ps.md)
> * [PowerShell per Azure classico](application-gateway-create-probe-classic-ps.md)

[!INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]

## <a name="scenario"></a>Scenario

Lo scenario seguente illustra la creazione di un probe di integrità personalizzato in un gateway applicazione esistente.
Lo scenario presuppone che sia già stata seguita la procedura per [creare un gateway applicazione](application-gateway-create-gateway-portal.md).

## <a name="a-namecreateprobeacreate-the-probe"></a><a name="createprobe"></a>Creare il probe

I probe vengono configurati con un processo in due passaggi nel portale. Il primo passaggio consiste nel creare il probe. Successivamente lo si aggiunge alle impostazioni http back-end del gateway applicazione.

### <a name="step-1"></a>Passaggio 1

Passare al [portale di Azure](http://portal.azure.com) e selezionare un gateway applicazione esistente.

![Panoramica del gateway applicazione][1]

### <a name="step-2"></a>Passaggio 2

Fare clic su **Probe** e quindi su **Aggiungi** per aggiungere un probe.

![Pannello Aggiungi probe con informazioni inserite][2]

### <a name="step-3"></a>Passaggio 3

Inserire le informazioni necessarie per il probe e al termine fare clic su **OK**.

* **Nome**: nome descrittivo del probe accessibile nel portale.
* **Host**: nome host usato per il probe. Applicabile solo quando vengono configurati più siti nel gateway applicazione. In caso contrario, usare "127.0.0.1". Questo valore è diverso dal nome host della VM.
* **Percorso** : parte restante dell'URL completo per il probe personalizzato. Un percorso valido inizia con "/".
* **Intervallo (sec)** : frequenza con cui il probe viene eseguito per controllare l'integrità. Non è consigliabile impostare un valore inferiore a 30 secondi.
* **Timeout (sec)** : durata dell'attesa prima che si verifichi il timeout del probe. L'intervallo di timeout deve essere abbastanza elevato da poter effettuare una chiamata http per assicurarsi che la pagina relativa all'integrità del back-end sia disponibile.
* **Soglia di non integrità** : numero di tentativi non riusciti da considerare come uno stato di non integrità. Se la soglia è 0 significa che, se un controllo integrità ha esito negativo, il back-end verrà immediatamente considerato non integro.

> [!IMPORTANT]
> Il nome host non è uguale al nome del server. Questo valore è il nome dell'host virtuale in esecuzione nel server applicazioni. Il probe viene inviato a http://(name host):(porta da impostazioni HTTP)/percorsoURL

![Impostazioni di configurazione del probe][3]

## <a name="add-probe-to-the-gateway"></a>Aggiungere il probe al gateway

Ora che il probe è stato creato, deve essere aggiunto al gateway. Le impostazioni del probe vengono definite nelle impostazioni HTTP back-end del gateway applicazione.

### <a name="step-1"></a>Passaggio 1

Fare clic su **Impostazioni HTTP** nel gateway applicazione, quindi fare clic sulle impostazioni HTTP back-end correnti nella finestra per visualizzare il pannello di configurazione.

![Finestra delle impostazioni HTTP][4]

### <a name="step-2"></a>Passaggio 2

Nel pannello delle impostazioni **appGatewayBackEndHttp** fare clic su **Usa probe personalizzato** e scegliere il probe creato nella sezione [Creare il probe](#createprobe).
Al termine, fare clic su **OK** per applicare le impostazioni.

![Pannello delle impostazioni appGatewayBackEndHttp][5]

Il probe predefinito controlla l'accesso predefinito all'applicazione Web. Ora che è stato creato un probe personalizzato, il gateway applicazione usa il percorso personalizzato definito per monitorare l'integrità per il back-end selezionato. In base ai criteri definiti, il gateway applicazione controlla il file specificato nel probe. Se la chiamata a host:Porta/percorso non restituisce una risposta di stato HTTP 200 OK, dopo che è stata raggiunta la soglia di non integrità il server viene escluso dalla rotazione. Il probe continua a essere eseguito sull'istanza non integra per determinare quando risulterà di nuovo integra. Quando l'istanza viene nuovamente aggiunta al pool di server integri, il flusso del traffico verso l'istanza riprenderà e il relativo probe verrà eseguito all'intervallo normale specificato dall'utente.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come configurare l'offload SSL con un gateway applicazione di Azure, vedere [Configurare un gateway applicazione per l'offload SSL con il portale](application-gateway-ssl-portal.md)

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png
[3]: ./media/application-gateway-create-probe-portal/figure3.png
[4]: ./media/application-gateway-create-probe-portal/figure4.png
[5]: ./media/application-gateway-create-probe-portal/figure5.png



<!--HONumber=Dec16_HO3-->


