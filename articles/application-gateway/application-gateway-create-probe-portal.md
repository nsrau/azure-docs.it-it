---
title: Creare un probe personalizzato tramite il portaleCreate a custom probe using the portal
titleSuffix: Azure Application Gateway
description: Informazioni su come creare un probe personalizzato per un gateway applicazione usando il portale
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 15daf47a1cb44635932311e60b3690af9ff58677
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74074598"
---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>Creare un probe personalizzato per un gateway applicazione con il portale

> [!div class="op_single_selector"]
> * [Portale di Azure](application-gateway-create-probe-portal.md)
> * [PowerShell per Azure Resource Manager](application-gateway-create-probe-ps.md)
> * [Azure Classic PowerShell](application-gateway-create-probe-classic-ps.md)

In questo articolo si aggiunge un probe di integrità personalizzato a un gateway applicazione esistente tramite il portale di Azure.In this article, you add a custom health probe to an existing application gateway through the Azure portal. Usando i probe di integrità, il gateway applicazione di Azure monitora l'integrità delle risorse nel pool back-end.

## <a name="before-you-begin"></a>Prima di iniziare

Se non si dispone già di un gateway applicazione, vedere [Creare un gateway applicazione con il portale](application-gateway-create-gateway-portal.md) per creare un gateway applicazione da usare.

## <a name="create-probe-for-application-gateway-v2-sku"></a>Creare un probe per lo SKU del gateway applicazione v2Create probe for Application Gateway v2 SKU

I probe vengono configurati con un processo in due passaggi nel portale. Il primo passaggio consiste nell'immettere i valori necessari per la configurazione probe. Nel secondo passaggio si esegue il test dell'integrità del back-end usando questa configurazione del probe e si salva il probe. 

### <a name="enter-probe-properties"></a><a name="createprobe"></a>Immettere le proprietà della sonda

1. Accedere al [portale](https://portal.azure.com)di Azure . Se non hai già un account, puoi iscriverti per una prova gratuita di [un mese](https://azure.microsoft.com/free)

2. Nel riquadro Preferiti del portale di Azure fare clic su Tutte le risorse. Fare clic su gateway applicazione nel pannello Tutte le risorse. Se nella sottoscrizione selezionata sono già presenti risorse, è possibile immettere partners.contoso.net nella casella Filtra per nome per accedere con facilità al gateway applicazione.

3. Selezionare **Probe** di integrità e quindi **selezionare Aggiungi** per aggiungere un nuovo probe di integrità.

   ![Aggiungi nuova sonda][4]

4. Nella pagina **Aggiungi probe** di integrità immettere le informazioni necessarie per il probe e, al termine, selezionare **OK**.

   |**Impostazione** | **Valore** | **Dettagli**|
   |---|---|---|
   |**Nome**|customProbe|Questo valore è un nome descrittivo assegnato al probe accessibile nel portale.|
   |**Protocollo**|HTTP o HTTPS | Protocollo usato per il probe di integrità. |
   |**Host**|vale a dire contoso.com|Questo valore è il nome dell'host virtuale (diverso dal nome host della macchina virtuale) in esecuzione nel server applicazioni. Il probe viene inviato a (protocol)://(nome host):(port da httpsetting)/urlPath.  Ciò è applicabile quando multisito è configurato nel gateway applicazione. Se il gateway applicazione è configurato per un singolo sito, immettere '127.0.0.1'.|
   |**Selezionare il nome host dalle impostazioni HTTP back-end**|Sì o No|Imposta l'intestazione *host* nel probe sul nome host della risorsa back-end nel pool back-end associato all'impostazione HTTP a cui è associato il probe. Particolarmente necessario in caso di back-end multi-tenant, ad esempio il servizio app di Azure.Special required in case of multi-tenant backends such as Azure app service. [Scopri di più](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-back-end-address)|
   |**Percorso**|/ o un altro percorso|Parte restante dell'URL completo per il probe personalizzato. Un percorso valido inizia con "/". Per il percorso predefinito\/di http: /contoso.com è sufficiente utilizzare '/' |
   |**Intervallo (sec)**|30|Frequenza con cui viene eseguito il probe per controllare l'integrità. Non è consigliabile impostare un valore inferiore a 30 secondi.|
   |**Timeout (secondi)**|30|La quantità di tempo di attesa del probe prima del timeout. Se non viene ricevuta una risposta valida entro questo periodo di timeout, il probe viene contrassegnato come non riuscito. L'intervallo di timeout deve essere abbastanza elevato da poter effettuare una chiamata http per assicurarsi che la pagina relativa all'integrità del back-end sia disponibile. Si noti che il valore di timeout non deve essere maggiore del valore 'Interval' utilizzato in questa impostazione del probe o del valore 'Timeout richiesta' nell'impostazione HTTP che verrà associata a questo probe.|
|**Soglia non sana**|3|Numero di tentativi consecutivi non riusciti da considerare non integri. La soglia può essere impostata su 1 o più.|
   |**Usare le condizioni di corrispondenza della sondaUse probe matching**|Sì o No|Per impostazione predefinita, una risposta HTTP(S) con codice di stato compreso tra 200 e 399 è considerata integra. È possibile modificare l'intervallo accettabile di codice di risposta back-end o corpo della risposta back-end. [Scopri di più](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching)|
   |**Impostazioni HTTP**|selezione dal menu a discesa|Probe verrà associato alle impostazioni HTTP selezionate qui e pertanto, monitorerà l'integrità del pool back-end associato all'impostazione HTTP selezionata. Verrà utilizzata la stessa porta per la richiesta probe di quella utilizzata nell'impostazione HTTP selezionata. È possibile scegliere solo le impostazioni HTTP che non sono associate ad altri probe personalizzati. <br>Si noti che solo le impostazioni HTTP sono disponibili per l'associazione che hanno lo stesso protocollo del protocollo scelto in questa configurazione probe e hanno lo stesso stato per l'opzione di *impostazione di impostazione HTTP Seleziona nome host dal back-end.*|
   
   > [!IMPORTANT]
   > Il probe monitorerà l'integrità del back-end solo quando è associato a una o più impostazioni HTTP. Monitorerà le risorse back-end dei pool back-end associati alle impostazioni HTTP a cui è associato il probe. La richiesta probe verrà inviata a http://(host name):(port from httpsetting)/urlPath.

### <a name="test-backend-health-with-the-probe"></a>Testare l'integrità del back-end con il probe

Dopo aver immesso le proprietà del probe, è possibile testare l'integrità delle risorse back-end per verificare che la configurazione del probe sia corretta e che le risorse back-end funzionino come previsto.

1. Selezionare **Test** e annotare il risultato della sonda. Il gateway applicazione verifica l'integrità di tutte le risorse back-end nei pool back-end associati alle impostazioni HTTP utilizzate per questo probe. 

   ![Testare l'integrità del back-endTest backend health][5]

2. Se sono presenti risorse back-end non integre, controllare la colonna **Dettagli** per comprendere il motivo dello stato non integro della risorsa. Se la risorsa è stata contrassegnata come non integra a causa di una configurazione probe non corretta, selezionare il collegamento **Torna indietro per sondare** e modificare la configurazione probe. In caso contrario, se la risorsa è stata contrassegnata come non integra a causa di un problema con il back-end, risolvere i problemi con la risorsa back-end e quindi testare nuovamente il back-end selezionando il collegamento **Torna indietro per sondare** e selezionare **Test**.

   > [!NOTE]
   > È possibile scegliere di salvare il probe anche con risorse back-end non integre, ma non è consigliabile. Ciò è dovuto al fatto che il gateway applicazione rimuove le risorse back-end dal pool back-end che sono determinate come non integre dal probe. Nel caso in cui non siano presenti risorse integre in un pool back-end, non sarà possibile accedere all'applicazione e verrà visualizzato un errore 502.

   ![Visualizzare il risultato della sonda][6]

3. Selezionare **Aggiungi** per salvare il probe. 

## <a name="create-probe-for-application-gateway-v1-sku"></a>Creare un probe per lo SKU del gateway applicazione v1Create probe for Application Gateway v1 SKU

I probe vengono configurati con un processo in due passaggi nel portale. Il primo passaggio consiste nel creare il probe. Nel secondo passaggio lo si aggiunge alle impostazioni http back-end del gateway applicazione.

### <a name="create-the-probe"></a><a name="createprobe"></a>Creare il probe

1. Accedere al [portale](https://portal.azure.com)di Azure . Se non hai già un account, puoi iscriverti per una prova gratuita di [un mese](https://azure.microsoft.com/free)

2. Nel riquadro Preferiti del portale di Azure selezionare **Tutte le risorse**. Selezionare il gateway applicazione nella pagina **Tutte le risorse.** Se nella sottoscrizione selezionata sono già presenti risorse, è possibile immettere partners.contoso.net nella casella Filtra per nome per accedere con facilità al gateway applicazione.

3. Selezionare **Sonde,** quindi **Aggiungi** per aggiungere una sonda.

   ![Pannello Aggiungi probe con informazioni inserite][1]

4. Nel pannello **Aggiungi probe** di integrità immettere le informazioni necessarie per il probe e, al termine, selezionare **OK**.

   |**Impostazione** | **Valore** | **Dettagli**|
   |---|---|---|
   |**Nome**|customProbe|Questo valore è un nome descrittivo assegnato al probe accessibile nel portale.|
   |**Protocollo**|HTTP o HTTPS | Protocollo usato per il probe di integrità. |
   |**Host**|vale a dire contoso.com|Questo valore è il nome dell'host virtuale (diverso dal nome host della macchina virtuale) in esecuzione nel server applicazioni. Il probe viene inviato a (protocol)://(nome host):(port da httpsetting)/urlPath.  Ciò è applicabile quando multisito è configurato nel gateway applicazione. Se il gateway applicazione è configurato per un singolo sito, immettere '127.0.0.1'.|
   |**Selezionare il nome host dalle impostazioni HTTP back-end**|Sì o No|Imposta l'intestazione *host* nel probe sul nome host della risorsa back-end nel pool back-end associato all'impostazione HTTP a cui è associato il probe. Particolarmente necessario in caso di back-end multi-tenant, ad esempio il servizio app di Azure.Special required in case of multi-tenant backends such as Azure app service. [Scopri di più](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-back-end-address)|
   |**Percorso**|/ o un altro percorso|Parte restante dell'URL completo per il probe personalizzato. Un percorso valido inizia con "/". Per il percorso predefinito\/di http: /contoso.com è sufficiente utilizzare '/' |
   |**Intervallo (sec)**|30|Frequenza con cui viene eseguito il probe per controllare l'integrità. Non è consigliabile impostare un valore inferiore a 30 secondi.|
   |**Timeout (secondi)**|30|La quantità di tempo di attesa del probe prima del timeout. Se non viene ricevuta una risposta valida entro questo periodo di timeout, il probe viene contrassegnato come non riuscito. L'intervallo di timeout deve essere abbastanza elevato da poter effettuare una chiamata http per assicurarsi che la pagina relativa all'integrità del back-end sia disponibile. Si noti che il valore di timeout non deve essere maggiore del valore 'Interval' utilizzato in questa impostazione del probe o del valore 'Timeout richiesta' nell'impostazione HTTP che verrà associata a questo probe.|
|**Soglia non sana**|3|Numero di tentativi consecutivi non riusciti da considerare non integri. La soglia può essere impostata su 1 o più.|
   |**Usare le condizioni di corrispondenza della sondaUse probe matching**|Sì o No|Per impostazione predefinita, una risposta HTTP(S) con codice di stato compreso tra 200 e 399 è considerata integra. È possibile modificare l'intervallo accettabile di codice di risposta back-end o corpo della risposta back-end. [Scopri di più](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching)|

   > [!IMPORTANT]
   > Il nome host non è uguale al nome del server. Questo valore è il nome dell'host virtuale in esecuzione nel server applicazioni. Il probe viene inviato a http://(name host):(porta da impostazioni HTTP)/percorsoURL

### <a name="add-probe-to-the-gateway"></a>Aggiungere il probe al gateway

Ora che il probe è stato creato, deve essere aggiunto al gateway. Le impostazioni del probe vengono definite nelle impostazioni HTTP back-end del gateway applicazione.

1. Fare clic su **Impostazioni HTTP** nel gateway applicazione e quindi fare clic sulle impostazioni HTTP back-end correnti presenti nella finestra per visualizzare il pannello di configurazione.

   ![Finestra delle impostazioni HTTP][2]

2. Nella pagina delle impostazioni **appGatewayBackEndHttpSettings** selezionare la casella di controllo **Usa probe personalizzato** e scegliere il probe creato nella sezione Create the [probe](#createprobe) nell'elenco a discesa **Custom probe.**
   Al termine fare clic su **Salva** per applicare le impostazioni.

## <a name="next-steps"></a>Passaggi successivi

Visualizzare l'integrità delle risorse back-end come determinato dal probe utilizzando la [visualizzazione dell'integrità back-end](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health).

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png
[4]: ./media/application-gateway-create-probe-portal/figure4.png
[5]: ./media/application-gateway-create-probe-portal/figure5.png
[6]: ./media/application-gateway-create-probe-portal/figure6.png
