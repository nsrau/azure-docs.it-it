---
title: Creare un gateway applicazione - Portale di Azure| Microsoft Docs
description: Informazioni su come creare un gateway applicazione usando il portale
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 54dffe95-d802-4f86-9e2e-293f49bd1e06
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: davidmu
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: d3c39cfe3159cd4059a81f966fb551175188278b
ms.contentlocale: it-it
ms.lasthandoff: 08/01/2017

---
# <a name="create-an-application-gateway-with-the-portal"></a>Creare un gateway applicazione con il portale

Il [gateway applicazione](application-gateway-introduction.md) è un'appliance virtuale dedicata che offre un servizio di controller per la distribuzione di applicazioni (ADC, Application Delivery Controller) e varie funzionalità di bilanciamento del carico di livello 7 per l'applicazione. Questo articolo illustra la procedura necessaria per creare un gateway applicazione usando il portale di Azure e aggiungendo un server esistente come membro di back-end.

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo [http://portal.azure.com](http://portal.azure.com)

## <a name="create-application-gateway"></a>Creare il gateway applicazione

Per creare un gateway applicazione, seguire questa procedura. Il gateway applicazione richiede una propria subnet. Quando si crea una rete virtuale, assicurarsi di lasciare uno spazio indirizzi sufficiente per più subnet. Dopo aver distribuito il gateway applicazione in una subnet, è possibile aggiungere ad essa solo altri gateway applicazione.

1. Nel riquadro Preferiti del portale fare clic su **Nuovo**
1. Nel pannello **Nuovo** fare clic su **Rete**. Nel pannello **Rete** fare clic su **Gateway applicazione**, come mostrato nell'immagine seguente:

    ![Creazione di un gateway applicazione][1]

1. Nel pannello **Informazioni di base** visualizzato immettere i valori seguenti e quindi fare clic su **OK**:

   | **Impostazione** | **Valore** | **Dettagli**|
   |---|---|---|
   |**Nome**|AdatumAppGateway|Il nome del gateway applicazione|
   |**Livello**|Standard|I valori disponibili sono Standard e WAF. Per altre informazioni su WAF, visitare [firewall applicazione Web](application-gateway-web-application-firewall-overview.md).|
   |**Dimensioni SKU**|Media|Opzioni disponibili quando si sceglie il livello Standard: Small, Medium e Large. Quando si sceglie il livello WAF, le opzioni sono solo Medium e Large.|
   |**Numero di istanze**|2|Numero di istanze del gateway applicazione per la disponibilità elevata. I numeri di istanze di 1 devono essere usati solo a scopo di test.|
   |**Sottoscrizione**|[Sottoscrizione]|Selezionare una sottoscrizione in cui creare il gateway applicazione.|
   |**Gruppo di risorse**|**Crea nuovo:** AdatumAppGatewayRG|Creare un gruppo di risorse. Il nome del gruppo di risorse deve essere univoco all'interno della sottoscrizione selezionata. Per altre informazioni sui gruppi di risorse, vedere l'articolo [Panoramica di Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#resource-groups).|
   |**Posizione**|Stati Uniti occidentali||

1. Nel pannello **Impostazioni** che viene visualizzato in **Rete virtuale**, fare clic su **Scegliere una rete virtuale**. Viene visualizzato il pannello **Scegli rete virtuale**.  Fare clic su **Crea nuovo** per aprire il pannello **Crea rete virtuale**.

   ![scegliere una rete virtuale][2]

1. Nel pannello **Crea rete virtuale** immettere i valori seguenti e quindi fare clic su **OK**. I pannelli **Crea rete virtuale** e **Scegli rete virtuale** vengono chiusi. Con questa operazione, il campo **Subnet** nel pannello **Impostazioni** viene popolato con la subnet selezionata.

   | **Impostazione** | **Valore** | **Dettagli**|
   |---|---|---|
   |**Nome**|AdatumAppGatewayVNET|Nome del gateway applicazione|
   |**Spazio di indirizzi:**|10.0.0.0/16|È lo spazio degli indirizzi per la rete virtuale|
   |**Nome della subnet**|AppGatewaySubnet|Nome della subnet per il gateway applicazione|
   |**Intervallo di indirizzi subnet**|10.0.0.0/28|Questa subnet consente subnet aggiuntive nella rete virtuale per i membri del pool back-end|

1. Nel pannello **Impostazioni** in **Configurazione IP front-end** scegliere **Pubblico** in **Tipo di indirizzo IP**

1. Nel pannello **Impostazioni** in **Indirizzo IP pubblico** fare clic su **Scegliere un indirizzo IP pubblico**. Nel pannello **Scegliere un indirizzo IP pubblico** visualizzato fare clic su **Crea nuovo**.

   ![scegliere un indirizzo IP pubblico][3]

1. Nel pannello **Crea indirizzo IP pubblico** accettare il valore predefinito e fare clic su **OK**. Questa operazione determina la chiusura del pannello e l'inserimento dell'indirizzo IP pubblico scelto nel campo **Indirizzo IP pubblico**.

1. Nel pannello **Impostazioni** in **Configurazione listener** fare clic su **HTTP** in **Protocollo**. Immettere la porta da usare nel campo **Porta**.

2. Fare clic su **OK** nel pannello **Impostazioni** per continuare.

1. Rivedere le impostazioni nel pannello **Riepilogo** e fare clic su **OK** per avviare la creazione del gateway applicazione. La creazione di un gateway applicazione è un'attività a esecuzione prolungata che può richiedere molto tempo per essere completata.

## <a name="add-servers-to-backend-pools"></a>Aggiungere server ai pool back-end

Dopo aver creato il gateway applicazione, è necessario aggiungere i sistemi che ospitano l'applicazione per cui si richiede il bilanciamento del carico. Gli indirizzi IP, il nome di dominio completo o i NIC di questi server vengono aggiunti ai pool di indirizzi back-end.

### <a name="ip-address-or-fqdn"></a>Indirizzo IP o nome di dominio completo

1. Dopo la creazione del gateway applicazione, nel riquadro **Preferiti** del portale di Azure fare clic su **Tutte le risorse**. Fare clic sul gateway applicazione **AdatumAppGateway** nel pannello Tutte le risorse. Se nella sottoscrizione selezionata sono già presenti delle risorse, è possibile immettere **AdatumAppGateway** nella casella **Filtra per nome** per accedere con facilità al gateway applicazione.

1. Il gateway applicazione creato verrà visualizzato. Fare clic su **Pool back-end** e selezionare il pool di back-end corrente **appGatewayBackendPool**. Verrà aperto il pannello **appGatewayBackendPool**.

   ![Pool back-end del gateway applicazione][4]

1. Fare clic su **Aggiungi destinazione** per aggiungere indirizzi IP ai valori del nome di dominio completo. Scegliere **Indirizzo IP o FQDN** come il **tipo** e immettere l'indirizzo IP o il nome di dominio completo. Ripetere questo passaggio per gli altri membri del pool di back-end. Al termine, fare clic su **Salva**.

### <a name="virtual-machine-and-nic"></a>Macchina virtuale e scheda di interfaccia di rete

È anche possibile aggiungere le schede di interfaccia di rete delle macchine virtuali come membri del pool back-end. Solo le macchine virtuali all'interno della stessa rete virtuale in cui si trova il gateway applicazione sono disponibili nell'elenco a discesa.

1. Dopo la creazione del gateway applicazione, nel riquadro **Preferiti** del portale di Azure fare clic su **Tutte le risorse**. Fare clic sul gateway applicazione **AdatumAppGateway** nel pannello Tutte le risorse. Se nella sottoscrizione selezionata sono già presenti delle risorse, è possibile immettere **AdatumAppGateway** nella casella **Filtra per nome** per accedere con facilità al gateway applicazione.

1. Il gateway applicazione creato verrà visualizzato. Fare clic su **Pool back-end** e selezionare il pool di back-end corrente **appGatewayBackendPool**. Verrà aperto il pannello **appGatewayBackendPool**.

   ![Pool back-end del gateway applicazione][5]

1. Fare clic su **Aggiungi destinazione** per aggiungere indirizzi IP ai valori del nome di dominio completo. Scegliere **Macchina virtuale** come il **tipo** e selezionare la macchina virtuale e il NIC da usare. Al termine, fare clic su **Salva**.

   > [!NOTE]
   > Solo le macchine virtuali nella stessa rete virtuale del gateway applicazione sono disponibili nella casella di riepilogo a discesa.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non serve più, eliminare il gruppo di risorse, il gateway applicazione e tutte le risorse correlate. A questo scopo, selezionare il gruppo di risorse nel pannello del gateway applicazione e fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questo scenario è stato distribuito un gateway applicazione ed è stato aggiunto un server al back-end. I passaggi successivi consistono nel configurare il gateway applicazione modificando le impostazioni e adattando le regole nel gateway. Per avere informazioni su questi passaggi, vedere gli articoli seguenti:

Per informazioni su come creare probe di integrità personalizzati, vedere [Creare un probe personalizzato per un gateway applicazione con il portale](application-gateway-create-probe-portal.md)

Per informazioni su come configurare l'offload SSL ed evitare costose attività di decrittografia SSL nei server Web, vedere [Configurare un gateway applicazione per l'offload SSL con Azure Resource Manager](application-gateway-ssl-portal.md)

Informazioni su come proteggere i siti Web con [Firewall applicazione Web](application-gateway-webapplicationfirewall-overview.md), una funzionalità del gateway applicazione.

<!--Image references-->
[1]: ./media/application-gateway-create-gateway-portal/figure1.png
[2]: ./media/application-gateway-create-gateway-portal/figure2.png
[3]: ./media/application-gateway-create-gateway-portal/figure3.png
[4]: ./media/application-gateway-create-gateway-portal/figure4.png
[5]: ./media/application-gateway-create-gateway-portal/figure5.png
[scenario]: ./media/application-gateway-create-gateway-portal/scenario.png

