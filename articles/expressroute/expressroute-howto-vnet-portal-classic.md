---
title: Configurare una rete virtuale e un gateway per ExpressRoute nel portale classico | Documentazione Microsoft
description: Questo articolo illustra come configurare una rete virtuale per ExpressRoute usando il modello di distribuzione classica e il portale classico.
documentationcenter: na
services: expressroute
author: cherylmc
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: 688817d9-51c8-4372-9af8-33fa098c7c5a
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/20/2016
ms.author: cherylmc
ms.openlocfilehash: f62254b2a7df50aa55a2a49009702848a9aecebd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-network-for-expressroute-in-the-classic-portal"></a>Creare una rete virtuale per ExpressRoute nel portale classico
La procedura disponibile in questo articolo illustra come configurare una rete virtuale e un gateway di rete virtuale da usare con ExpressRoute mediante il modello di distribuzione classica e il portale classico.

Se sono necessarie istruzioni per il modello di distribuzione di Resource Manager, è possibile vedere gli articoli seguenti: [Creare una rete virtuale usando PowerShell](../virtual-network/virtual-networks-create-vnet-arm-ps.md) e [Aggiungere un gateway VPN a una VNet di Resource Manager per ExpressRoute](expressroute-howto-add-gateway-resource-manager.md).

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Informazioni sui modelli di distribuzione di AzureAbout Azure deployment models**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="create-a-classic-vnet-and-gateway"></a>Creare una rete virtuale classica e un gateway
La procedura seguente consente di creare una rete virtuale classica e un gateway di rete virtuale. Se è già disponibile una rete virtuale classica, vedere la sezione [Configurare una rete virtuale classica esistente](#config) in questo articolo.

1. Accedere al [portale di Azure classico](http://manage.windowsazure.com).
2. Nell'angolo inferiore sinistro della schermata fare clic su **New**. Nel riquadro di spostamento fare clic su **Servizi di rete** e quindi su **Rete virtuale**. Fare clic su **Creazione personalizzata** per avviare la configurazione guidata.
3. Nella pagina **Dettagli della rete virtuale** immettere quanto segue:
   
   * **Nome** : assegnare un nome alla rete virtuale. Questo nome della rete virtuale viene usato quando si distribuiscono le macchine virtuali e le istanze PaaS e pertanto è consigliabile non specificare un nome troppo complicato.
   * **Località** : la località è direttamente correlata alla posizione fisica (regione) in cui si desidera che risiedano le risorse (macchine virtuali). Ad esempio, se si desidera che le macchine virtuali distribuite nella rete virtuale vengano posizionate fisicamente in Stati Uniti occidentali, selezionare tale posizione. È possibile modificare l’area associata alla rete virtuale dopo averla creata.
4. Nella pagina **DNS Servers and VPN Connectivity** immettere le informazioni seguenti e quindi fare clic sulla freccia Next in basso a destra. 
   
   * **Server DNS** : immettere il nome del server DNS e l'indirizzo IP o selezionare un server DNS registrato in precedenza dal menu di scelta rapida. Questa impostazione non comporta la creazione di un server DNS. Consente di specificare i server DNS da usare per la risoluzione dei nomi per la rete virtuale.
   * **Connettività da sito a sito**: selezionare la casella di controllo **Configura una VPN Site-to-Site**.
   * **ExpressRoute**: selezionare la casella di controllo **Usa ExpressRoute**. Questa opzione viene visualizzata solo se è stata selezionata **Configura una VPN Site-To-Site**.
   * **Rete locale** : è necessario avere un sito di rete locale per ExpressRoute. Tuttavia, nel caso di una connessione ExpressRoute, i prefissi degli indirizzi specificati per il sito di rete locale verranno ignorati. Per il routing verranno invece usati i prefissi degli indirizzi annunciati a Microsoft attraverso il circuito ExpressRoute.<BR>Se è già disponibile una rete locale creata per la connessione ExpressRoute, è possibile selezionarla dall'elenco a discesa. In caso contrario, selezionare **Specificare una nuova rete locale**.
5. Se nel passaggio precedente si è scelto di specificare una nuova rete locale, viene visualizzata la pagina **Connettività da sito a sito** . Per configurare la rete locale, immettere le informazioni seguenti e quindi fare clic sulla freccia avanti. 
   
   * **Nome** : nome da assegnare al sito di rete locale.
   * **Spazio di indirizzi** : inclusi IP iniziale e CIDR (conteggio indirizzi). È possibile specificare qualsiasi intervallo di indirizzi, purché non si sovrapponga all'intervallo di indirizzi della rete virtuale. In genere, vengono specificati gli intervalli di indirizzi per le reti locali, ma nel caso di ExpressRoute queste impostazioni non vengono usate. Tuttavia, questa impostazione è necessaria per creare la rete locale quando si usa il portale classico.
   * **Aggiungi spazio di indirizzi** : questa impostazione non è rilevante per ExpressRoute.
6. Nella pagina **Virtual Network Address Spaces** immettere le informazioni seguenti e quindi fare clic sul segno di spunta in basso a destra per configurare la rete. 
   
   * **Spazio di indirizzi** : inclusi IP iniziale e conteggio indirizzi. Verificare che gli spazi di indirizzi specificati non si sovrappongano agli spazi di indirizzi presenti nella rete locale.
   * **Aggiungi subnet** inclusi IP iniziale e conteggio indirizzi. Non sono necessarie altre subnet.
   * **Aggiungi subnet gateway** : fare clic per aggiungere la subnet del gateway. La subnet del gateway viene usata solo per il gateway di rete virtuale ed è obbligatoria per questa configurazione.<BR>Il CIDR, o conteggio indirizzi, della subnet del gateway per ExpressRoute deve essere /28 o superiore (/ 27/26 e così via). In questo modo nella subnet diventano disponibili indirizzi IP sufficienti per consentire il funzionamento della configurazione. Se nel portale classico è stata selezionata la casella di controllo per l'uso di ExpressRoute, viene specificata automaticamente una subnet del gateway con /28.  Non è possibile modificare il conteggio di indirizzi CIDR nel portale classico. La subnet del gateway verrà visualizzata come **Gateway** nel portale classico, anche se il vero nome della subnet del gateway creato è in effetti **GatewaySubnet**. È possibile visualizzare il nome tramite PowerShell o nel portale di Azure.
7. Fare clic sul segno di spunta nella parte inferiore della pagina per iniziare a creare la rete virtuale. Al termine, nella pagina **Reti** del portale classico verrà visualizzato **Creato** in **Stato**.

## <a name="gw"></a>Creare il gateway
1. Nella pagina **Reti** fare clic sulla rete virtuale appena creata e quindi su **Dashboard** nella parte superiore della pagina.
2. Nella parte inferiore della pagina **Dashboard** fare clic su **Crea gateway** e selezionare **Routing dinamico**. Fare clic su **Sì** per confermare che si vuole creare un gateway.
3. All'avvio della creazione del gateway, verrà visualizzato un messaggio che indica che la procedura è stata avviata. La creazione del gateway può richiedere fino a 45 minuti.
4. Collegare la rete al circuito. Seguire le istruzioni riportate nell'articolo [Come collegare reti virtuali a circuiti ExpressRoute](expressroute-howto-linkvnet-classic.md).

## <a name="config"></a>Configurare una rete virtuale classica esistente per ExpressRoute
Se è già disponibile una rete virtuale classica, è possibile configurarne la connessione a ExpressRoute nel portale classico. Le impostazioni sono le stesse usate nelle sezioni precedenti, quindi vedere quelle sezioni per acquisire familiarità con le impostazioni necessarie. Se si vuole creare una connessione coesistente ExpressRoute/Da sito a sito, vedere la procedura in [questo articolo](expressroute-howto-coexist-classic.md) . I passaggi sono diversi rispetto a questo articolo.

1. È necessario creare la rete locale prima di aggiornare il resto delle impostazioni della rete virtuale. Per creare una nuova rete locale, necessaria quando si configura ExpressRoute dal portale classico, fare clic su **Nuovo** **>** **Servizi di rete** **>** **Rete virtuale** **>** **Aggiungi rete locale**. Seguire i passaggi della procedura guidata per creare la rete locale.
2. Usare la pagina **Configura** per aggiornare il resto delle impostazioni per la rete virtuale e associare la rete virtuale alla rete locale.
3. Dopo aver configurato le impostazioni, vedere la sezione [Creare il gateway](#gw) in questo articolo per completare l'operazione.

## <a name="next-steps"></a>Passaggi successivi
* Se si vuole aggiungere macchine virtuali alla rete virtuale, vedere [Percorsi di apprendimento per le macchine virtuali](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/).
* Per altre informazioni su ExpressRoute, vedere [Panoramica tecnica relativa a ExpressRoute](expressroute-introduction.md).

