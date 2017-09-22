---
title: Creare un gateway applicazione con le regole di routing basato su URL - Interfaccia della riga di comando di Azure 2.0 | Microsoft Docs
description: Questa pagina fornisce istruzioni per la creazione e la configurazione di un gateway applicazione con le regole di routing basato su URL.
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: davidmu
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: c617006bcb122cd3191f5da3ff08191e3c55b04b
ms.contentlocale: it-it
ms.lasthandoff: 09/14/2017

---
# <a name="create-an-application-gateway-by-using-path-based-routing-with-azure-cli-20"></a>Creare un gateway applicazione con il routing basato sul percorso con l'interfaccia della riga di comando di Azure 2.0

> [!div class="op_single_selector"]
> * [Portale di Azure](application-gateway-create-url-route-portal.md)
> * [PowerShell per Azure Resource Manager](application-gateway-create-url-route-arm-ps.md)
> * [Interfaccia della riga di comando di Azure 2.0](application-gateway-create-url-route-cli.md)

Il routing basato sul percorso dell'URL consente di associare le route in base al percorso dell'URL delle richieste HTTP. Verifica se è disponibile una route per il pool back-end configurato per l'URL elencato nel gateway applicazione e quindi invia il traffico di rete al pool definito. In genere il routing basato sul percorso dell'URL viene usato per le richieste di bilanciamento del carico per diversi tipi di contenuto tra vari pool di server back-end.

Per i gateway applicazione di Azure sono disponibili due tipi di regole: di base e basate sul percorso dell'URL. Il tipo di regola di base fornisce il servizio di round robin per i pool back-end. Le regole basate sul percorso, oltre alla distribuzione round-robin, usano anche il modello del percorso dell'URL della richiesta per scegliere il pool back-end appropriato.

## <a name="scenario"></a>Scenario

Nell'esempio seguente il gateway applicazione gestisce il traffico per contoso.com con due pool di server back-end: un pool di server predefinito e un pool di server immagini.

Le richieste per http://contoso.com/image* vengono indirizzate al pool di server immagini (**imagesBackendPool**). Se il modello del percorso non corrisponde, il gateway applicazione seleziona il pool di server predefinito (**appGatewayBackendPool**).

![Route dell'URL](./media/application-gateway-create-url-route-cli/scenario.png)

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Aprire il **prompt dei comandi di Microsoft Azure**ed eseguire l'accesso.

```azurecli
az login -u "username"
```

> [!NOTE]
> È anche possibile usare `az login` senza l'opzione per l'accesso del dispositivo che richiede l'immissione di un codice in aka.ms/devicelogin.

Dopo aver immesso il comando precedente, viene restituito un codice. Passare a https://aka.ms/devicelogin in un browser per continuare la procedura di accesso.

![Comando che illustra l'accesso al dispositivo][1]

Nel browser immettere il codice ricevuto. Questo reindirizza l'utente a una pagina di accesso.

![Browser in cui immettere il codice][2]

Immettere il codice per accedere e quindi chiudere il browser per continuare.

![Accesso eseguito][3]

## <a name="add-a-path-based-rule-to-an-existing-application-gateway"></a>Aggiungere una regola basata su percorso a un gateway applicazione esistente

La procedura seguente mostra come aggiungere una regola basata su percorso a un gateway applicazione esistente.
### <a name="create-a-new-back-end-pool"></a>Creare un nuovo pool back-end

Configurare l'impostazione **imagesBackendPool** del gateway applicazione per il traffico di rete con carico bilanciato nel pool back-end. In questo esempio vengono configurate diverse impostazioni per il nuovo pool back-end. Ogni pool back-end può avere le proprie impostazioni. Le impostazioni HTTP di back-end vengono usate dalle regole basate su percorso per instradare il traffico verso i membri del pool di back-end corretti. Ciò determina il protocollo e la porta usati durante l'invio di traffico ai membri del pool back-end. Anche le sessioni basate sui cookie sono determinate dalle impostazioni HTTP di back-end.  Se abilitata, l'affinità di sessione basata su cookie invia traffico allo stesso back-end sotto forma di richieste precedenti per ogni pacchetto.

```azurecli-interactive
az network application-gateway address-pool create \
--gateway-name AdatumAppGateway \
--name imagesBackendPool  \
--resource-group myresourcegroup \
--servers 10.0.0.6 10.0.0.7
```

### <a name="create-a-new-front-end-port-for-an-application-gateway"></a>Creare una nuova porta front-end per un gateway applicazione

L'oggetto di configurazione della porta front-end viene usato da un listener per definire la porta del gateway applicazione in ascolto del traffico nel listener.

```azurecli-interactive
az network application-gateway frontend-port create --port 82 --gateway-name AdatumAppGateway --resource-group myresourcegroup --name port82
```

### <a name="create-a-new-listener"></a>Creare un nuovo listener

Questo passaggio configura il listener per l'indirizzo IP pubblico e la porta usata per ricevere il traffico di rete in ingresso. L'esempio seguente prende in considerazione la configurazione IP front-end configurata in precedenza, la configurazione della porta front-end e un protocollo (Http o Https, con distinzione tra maiuscole e minuscole) e configura il listener. In questo esempio, il listener è in ascolto per il traffico HTTP sulla porta 82 all'indirizzo IP pubblico che è stato creato in precedenza in questo esempio.

```azurecli-interactive
az network application-gateway http-listener create --name imageListener --frontend-ip appGatewayFrontendIP  --frontend-port port82 --resource-group myresourcegroup --gateway-name AdatumAppGateway
```

### <a name="create-the-url-path-map"></a>Creare il mapping dei percorsi URL

Questo passaggio configura il percorso URL relativo usato dal gateway applicazione per definire il mapping tra il percorso e il pool back-end selezionato per gestire il traffico di rete in ingresso.

> [!IMPORTANT]
> Ogni percorso deve iniziare con una barra rovesciata ("/") e l'unica posizione in cui è consentito l'asterisco è alla fine. Alcuni esempi validi sono /xyz, /xyz* o /xyz/*. La stringa inviata al selettore di percorsi non include alcun testo dopo il primo carattere "?" o "#" e questi caratteri non sono consentiti. 

L'esempio seguente crea una regola per il percorso "/images/*" che instrada il traffico al back-end **imagesBackendPool**. Questa regola assicura che il traffico per ogni set di URL venga indirizzato al back-end. Ad esempio, http://adatum.com/images/figure1.jpg passa a **imagesBackendPool**. In caso di mancata corrispondenza con le regole di percorso predefinite, la configurazione del mapping dei percorsi della regola configura anche un pool di indirizzi back-end predefinito. Ad esempio, http://adatum.com/shoppingcart/test.html passa al **pool1** definito come pool predefinito per il traffico senza corrispondenza.

```azurecli-interactive
az network application-gateway url-path-map create \
--gateway-name AdatumAppGateway \
--name imagespathmap \
--paths /images/* \
--resource-group myresourcegroup2 \
--address-pool imagesBackendPool \
--default-address-pool appGatewayBackendPool \
--default-http-settings appGatewayBackendHttpSettings \
--http-settings appGatewayBackendHttpSettings \
--rule-name images
```

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sull'offload SSL (Secure Sockets Layer), vedere [Configurare un gateway applicazione per l'offload SSL](application-gateway-ssl-cli.md).


[scenario]: ./media/application-gateway-create-url-route-cli/scenario.png
[1]: ./media/application-gateway-create-url-route-cli/figure1.png
[2]: ./media/application-gateway-create-url-route-cli/figure2.png
[3]: ./media/application-gateway-create-url-route-cli/figure3.png

