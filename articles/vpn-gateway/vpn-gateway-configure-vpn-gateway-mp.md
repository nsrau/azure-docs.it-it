---
title: 'Configurare un gateway VPN: portale di Azure classico: Azure| Microsoft Docs'
description: Questo articolo illustra come configurare la rete virtuale del gateway VPN e modificare il tipo di routing VPN del gateway. Questi passaggi si applicano al modello di distribuzione classico e al portale classico.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: fbe59ba8-b11f-4d21-9bb1-225ec6c6d351
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/04/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: 2ea4e6bb86b1ba6f7b501b193d0713d3901457af
ms.lasthandoff: 04/06/2017


---
# <a name="configure-a-vpn-gateway-in-the-classic-portal"></a>Configurare un gateway VPN nel portale di Azure classico 
Se si vuole creare una connessione cross-premise sicura tra Azure e il percorso locale, è necessario creare un gateway di rete virtuale. Un gateway VPN è un tipo specifico di gateway di rete virtuale. Nel modello di distribuzione classica, un gateway VPN può essere uno dei due tipi di routing VPN: statico o dinamico. La scelta del tipo di VPN dipende sia dal piano di progettazione della rete che dal dispositivo VPN locale da usare. Per altre informazioni sui dispositivi VPN, vedere [Informazioni sui dispositivi VPN](vpn-gateway-about-vpn-devices.md).

**Informazioni sui modelli di distribuzione di AzureAbout Azure deployment models**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-overview"></a>Panoramica della configurazione
La seguente procedura descrive la configurazione del gateway VPN nel portale classico. Questi passaggi si applicano ai gateway per le reti virtuali create utilizzando il modello di distribuzione classica. Attualmente, non tutte le impostazioni di configurazione per i gateway sono disponibili nel portale di Azure. Quando lo sono, viene creato un nuovo set di istruzioni che si applicano al portale di Azure.

### <a name="before-you-begin"></a>Prima di iniziare
Prima di configurare il gateway, è necessario innanzitutto creare la rete virtuale. Per i passaggi necessari per creare una rete virtuale per la connettività cross-premise, vedere [Configurare una rete virtuale con connessione VPN da sito a sito](vpn-gateway-site-to-site-create.md) o [Configurare una rete virtuale con connessione VPN da punto a sito](vpn-gateway-point-to-site-create.md). Dopodiché, seguire i passaggi descritti di seguito per configurare il gateway VPN e raccogliere le informazioni necessarie per configurare il dispositivo VPN. 

Se si ha già di un gateway VPN e si vuole modificare il tipo di routing VPN, vedere [Come modificare il tipo di routing VPN per il gateway](#how-to-change-the-vpn-routing-type-for-your-gateway).

## <a name="create-a-vpn-gateway"></a>Creare un gateway VPN
1. Nel [portale di Azure classico](https://manage.windowsazure.com), nella pagina **Reti**, verificare che la colonna di stato relativa alla rete virtuale riporti la dicitura **Creata**.
2. Nella colonna **Nome** fare clic sul nome della rete virtuale.
3. Nella pagina **Dashboard** si noti che la rete virtuale non ha ancora un gateway configurato. Questo stato verrà visualizzato durante l'esecuzione dei passaggi per configurare il gateway.

![Gateway non creato](./media/vpn-gateway-configure-vpn-gateway-mp/IC717025.png)

Fare quindi clic su **Crea gateway** nella parte inferiore della pagina. È possibile selezionare *Routing statico* o *Routing dinamico*. Il tipo di routing VPN da selezionare dipende da diversi fattori, ad esempio dalle funzionalità che il dispositivo VPN deve supportare e dall'eventuale necessità di supportare connessioni Point-to-Site. Per determinare il tipo di routing VPN necessario, vedere [Informazioni sui dispositivi VPN per la connettività di rete virtuale](vpn-gateway-about-vpn-devices.md). Una volta creato il gateway, non è possibile passare da un tipo di routing VPN del gateway a un altro senza eliminare e ricreare il gateway. Quando viene richiesto di confermare che si intende procedere con la creazione del gateway, fare clic su **Sì**.

![Tipo di routing VPN del gateway](./media/vpn-gateway-configure-vpn-gateway-mp/IC717026.png)

Durante la creazione del gateway la relativa rappresentazione grafica nella pagina diventa gialla e viene visualizzato il testo *Creazione del gateway*. La creazione del gateway può richiedere fino a 45 minuti. Attendere il completamento del gateway prima di poter proseguire con altre impostazioni di configurazione.

![Creazione del gateway](./media/vpn-gateway-configure-vpn-gateway-mp/IC717027.png)

Quando per il gateway viene visualizzato *Connessione*, è possibile raccogliere le informazioni che saranno necessarie per il dispositivo VPN.

![Connessione del gateway](./media/vpn-gateway-configure-vpn-gateway-mp/IC717028.png)

## <a name="site-to-site-connections"></a>Connessioni Site-to-site

### <a name="step-1-gather-information-for-your-vpn-device-configuration"></a>Passaggio 1. Raccogliere informazioni per la configurazione del dispositivo VPN
Se si sta creando una connessione da sito a sito, dopo aver creato il gateway raccogliere le informazioni per la configurazione del dispositivo VPN. Tali informazioni sono disponibili nella pagina **Dashboard** della rete virtuale:

1. **Indirizzo IP del gateway**: l'indirizzo IP è incluso nella pagina **Dashboard**, ma verrà visualizzato solo al termine della creazione del gateway.
2. **Chiave condivisa**: fare clic su **Gestisci chiave** nella parte inferiore della schermata. Fare clic sull'icona accanto alla chiave per copiarla negli Appunti, quindi incollare e salvare la chiave. Questo pulsante funziona solo quando è presente un solo tunnel VPN Site-to-Site. Se si dispone di più tunnel VPN S2S, usare l'API *Get Virtual Network Gateway Shared Key* (Ottieni chiave condivisa per il gateway della rete virtuale) o il cmdlet di PowerShell.

![Gestisci chiave](./media/vpn-gateway-configure-vpn-gateway-mp/IC717029.png)

### <a name="step-2--configure-your-vpn-device"></a>Passaggio 2.  Configurare il dispositivo VPN
Le connessioni da sito a sito verso una rete locale richiedono un dispositivo VPN. Anche se non viene specificata la procedura di configurazione per tutti i dispositivi VPN, le informazioni contenute nei collegamenti seguenti potrebbero risultare utili:

- Per informazioni sui dispositivi VPN compatibili, vedere l'articolo relativo ai [dispositivi VPN](vpn-gateway-about-vpn-devices.md). 
- Per collegamenti alle impostazioni di configurazione dei dispositivi, vedere [Dispositivi VPN convalidati](vpn-gateway-about-vpn-devices.md#devicetable). I collegamenti forniti rappresentano i migliori possibili. È sempre consigliabile rivolgersi al produttore del dispositivo per le informazioni di configurazione più aggiornate.
- Per informazioni sulla modifica degli esempi, vedere [Modifica degli esempi di configurazione di dispositivo](vpn-gateway-about-vpn-devices.md#editing).
- Per informazioni sui parametri IPsec/IKE, vedere [Parametri](vpn-gateway-about-vpn-devices.md#ipsec).
- Prima di configurare il dispositivo VPN, verificare eventuali [Problemi noti di compatibilità del dispositivo](vpn-gateway-about-vpn-devices.md#known) per il dispositivo VPN da usare.

Durante la configurazione del dispositivo VPN, sono necessari gli elementi seguenti:

- Indirizzo IP pubblico del gateway di rete virtuale. Per trovarlo, passare al pannello **Panoramica** della rete virtuale usata.
- Chiave condivisa. Si tratta della stessa chiave condivisa che viene specificata durante la creazione della connessione VPN da sito a sito. In questi esempi viene usata una chiave condivisa molto semplice. È necessario generare una chiave più complessa per l'uso effettivo.

Dopo avere configurato il dispositivo VPN, è possibile visualizzare le informazioni di connessione aggiornate nella pagina Dashboard per la rete virtuale.

### <a name="step-3-verify-your-local-network-ranges-and-vpn-gateway-ip-address"></a>Passaggio 3. Verificare gli intervalli della rete locale e l'indirizzo IP del gateway VPN
#### <a name="verify-your-vpn-gateway-ip-address"></a>Verificare l'indirizzo IP del gateway VPN
Per assicurare il corretto funzionamento del gateway, l'indirizzo IP del dispositivo VPN deve essere configurato in modo appropriato per la rete locale specificata per la configurazione cross-premise. In genere, l'indirizzo IP viene impostato durante il processo di configurazione Site-to-Site. Se, tuttavia, la rete locale è stata usata in precedenza con un dispositivo differente, o se l'indirizzo IP per la rete locale è cambiato, modificare le impostazioni per specificare il corretto indirizzo IP del gateway.

1. Per verificare l'indirizzo IP del gateway, fare clic su **Reti** nel riquadro sinistro del portale, quindi selezionare **Reti locali** nella parte superiore della pagina. Verrà visualizzato l'indirizzo del gateway VPN di ogni rete locale creata. Per modificare l'indirizzo IP, selezionare la rete virtuale e fare clic su **Modifica** nella parte inferiore della pagina.
2. Modificare l'indirizzo IP nella pagina **Specificare i dettagli della rete locale** e quindi fare clic sulla freccia Avanti nella parte inferiore della pagina.
3. Nella pagina **Specificare lo spazio di indirizzi** fare clic sul segno di spunta in basso a destra per salvare le impostazioni.

#### <a name="verify-the-address-ranges-for-your-local-networks"></a>Verificare gli intervalli di indirizzi delle reti locali
Per il corretto flusso del traffico attraverso il gateway verso la destinazione locale, è necessario verificare che ogni intervallo di indirizzi IP sia specificato. Ciascun intervallo deve essere elencato nella configurazione delle **reti locali** di Azure. Tale operazione può risultare complessa a seconda della configurazione di rete della posizione locale. Il traffico associato per un indirizzo IP contenuto all'interno degli intervalli elencati verrà quindi inviato tramite il gateway VPN di rete virtuale. Gli intervalli elencati non devono essere intervalli privati, anche se si desidera verificare che la configurazione locale sia in grado di ricevere il traffico in ingresso.

Per aggiungere o modificare intervalli per una rete locale, attenersi alla procedura riportata di seguito:

1. Per modificare gli intervalli di indirizzi IP per una rete locale, fare clic su **Reti** nel riquadro sinistro del portale, quindi selezionare **Reti locali** nella parte superiore della pagina. Nel portale il modo più semplice per visualizzare gli intervalli elencati è mediante la pagina **Modifica** . Per visualizzare gli intervalli, selezionare la rete virtuale e fare clic su **Modifica** nella parte inferiore della pagina.
2. Nella pagina **Specificare i dettagli della rete locale** non apportare alcuna modifica. Fare clic sulla freccia Avanti nella parte inferiore della pagina.
3. Nella pagina **Specificare lo spazio di indirizzi** modificare lo spazio di indirizzi della rete nel modo desiderato. Fare quindi clic sul segno di spunta per salvare la configurazione.

## <a name="how-to-view-gateway-traffic"></a>Come visualizzare il traffico gateway
È possibile visualizzare il gateway e il relativo traffico dalla pagina **Dashboard** della rete virtuale.

Nella pagina **Dashboard** è possibile visualizzare quanto segue:

* La quantità di dati che passano attraverso il gateway, sia in entrata che in uscita.
* I nomi dei server DNS specificati per la rete virtuale.
* La connessione tra il gateway e il dispositivo VPN.
* La chiave condivisa usata per configurare la connessione del gateway al dispositivo VPN.

## <a name="how-to-change-the-vpn-routing-type-for-your-gateway"></a>Come modificare il tipo di routing VPN per il gateway
Poiché alcune configurazioni di connettività sono disponibili solo per determinati tipi di routing dei gateway, potrebbe essere necessario modificare il tipo di routing di un gateway VPN esistente. È ad esempio possibile aggiungere la connettività Point-to-Site a una connessione Site-to-Site già esistente che dispone di un gateway statico. Le connessioni Point-to-Site richiedono un gateway dinamico. Ciò significa che per configurare una connessione P2S, è necessario modificare il tipo di routing VPN gateway da statico a dinamico.

Se si deve modificare il tipo di routing VPN di un gateway, eliminare il gateway esistente e ricrearlo con il nuovo tipo di routing. Non è necessario eliminare l'intera rete virtuale per modificare il tipo di routing del gateway.

Prima di modificare il tipo di routing VPN del gateway, verificare che il dispositivo VPN supporti il tipo di routing che si desidera usare. Per scaricare nuovi esempi di configurazione del routing e verificare i requisiti del dispositivo VPN, vedere [Informazioni sui dispositivi e i gateway VPN per la connettività di rete virtuale](vpn-gateway-about-vpn-devices.md).

> [!IMPORTANT]
> Quando si elimina un gateway VPN di rete virtuale, il relativo indirizzo VIP assegnato viene rilasciato. Quando si ricrea il gateway, a esso viene assegnato un nuovo indirizzo VIP.
> 
> 

1. **Eliminare il gateway VPN esistente.**
   
    Nella pagina **Dashboard** per la rete virtuale scorrere alla parte inferiore e fare clic su **Elimina gateway**. Attendere la notifica dell'eliminazione del gateway. Quando si riceve la notifica sullo schermo che il gateway è stato eliminato, è possibile creare un nuovo gateway.
2. **Creare un nuovo gateway VPN.**
   
    Per creare un nuovo gateway, seguire la procedura riportata nella parte superiore della pagina [Creare un gateway VPN](#create-a-vpn-gateway).

## <a name="next-steps"></a>Passaggi successivi
È possibile aggiungere macchine virtuali nella rete virtuale. Vedere [Come creare una macchina virtuale personalizzata](../virtual-machines/windows/classic/createportal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Se si vuole configurare una connessione VPN da punto a sito, vedere [Configurare una connessione VPN Point-to-Site](vpn-gateway-point-to-site-create.md).


