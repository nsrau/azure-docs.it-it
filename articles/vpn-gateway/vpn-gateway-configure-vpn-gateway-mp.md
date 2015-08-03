<properties 
   pageTitle="Configurare un gateway VPN nel portale di gestione | Microsoft Azure"
   description="Questo articolo illustra come configurare la rete virtuale del gateway VPN e modificare il tipo di routing del gateway VPN da statico a dinamico o viceversa."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="jdial"
   editor="tysonn" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/12/2015"
   ms.author="cherylmc" />

# Configurare un gateway VPN nel portale di gestione

Se si desidera creare una connessione cross-premise sicura tra Azure e il percorso locale, sarà necessario configurare un gateway VPN. Esistono diversi tipi di gateway e il tipo di gateway da creare dipende dal piano di sviluppo della rete e dal dispositivo VPN locale che si desidera usare. Alcune opzioni di connettività, come una connessione Point-to-Site, richiedono ad esempio un gateway con routing dinamico. Se si desidera configurare il gateway per supportare connessioni Point-to-Site (P2S) e Site-to-Site (S2S), sarà necessario configurare un gateway di routing dinamico, anche se la connessione Site-to-Site può essere configurata con entrambi i tipi di routing del gateway. È necessario anche assicurarsi che il dispositivo che si desidera usare per la connessione Site-to-Site supporti il tipo di gateway che si intende creare. Vedere [Informazioni sui gateway VPN](vpn-gateway-about-vpngateways.md).

## Panoramica della configurazione

Prima di configurare il gateway, è necessario innanzitutto creare la rete virtuale. Per i passaggi necessari per creare una rete virtuale per la connettività cross-premise, vedere l'articolo relativo alla [configurazione di una rete virtuale con VPN Site-to-Site](vpn-gateway-site-to-site-create.md) o l'articolo relativo alla [configurazione di una rete virtuale con VPN Point-to-Site](vpn-gateway-point-to-site-create.md). Seguire quindi i passaggi descritti di seguito per configurare il gateway VPN e raccogliere le informazioni necessarie per configurare il dispositivo VPN.

Se si dispone già di un gateway VPN e si desidera modificare il tipo di routing, vedere la sezione [Come modificare il tipo di gateway VPN](#how-to-change-your-vpn-gateway-type).

1. [Creare un gateway VPN](#create-a-vpn-gateway)

1. [Raccogliere informazioni per la configurazione del dispositivo VPN](#gather-information-for-your-vpn-device-configuration)

1. [Configurare il dispositivo VPN](#configure-your-vpn-device)

1. [Verificare gli intervalli della rete locale e l'indirizzo IP del gateway VPN](#verify-your-local-network-ranges-and-vpn-gateway-ip-address)

## Creare un gateway VPN

1. Nella pagina **Reti** verificare che la colonna di stato per la rete virtuale sia impostata su **Creato**.

1. Nella colonna **Nome** fare clic sul nome della rete virtuale.

1. Nella pagina **Dashboard** si noti che la rete virtuale non ha ancora un gateway configurato. Questo stato verrà visualizzato durante l'esecuzione dei passaggi per configurare il gateway.

![Gateway non creato](./media/vpn-gateway-configure-vpn-gateway-mp/IC717025.png)


Fare quindi clic su **Crea gateway** nella parte inferiore della pagina. È possibile selezionare *Routing statico* o *Routing dinamico*. Il tipo di routing da selezionare dipende da diversi fattori, ad esempio dalle funzionalità che il dispositivo VPN deve supportare e dall'eventuale necessità di supportare connessioni Point-to-Site. Per determinare il tipo di routing necessario, fare riferimento a [Informazioni sui dispositivi e i gateway VPN per la connettività di rete virtuale](http://go.microsoft.com/fwlink/p/?LinkId=615934). Una volta creato il gateway, non è possibile passare da un tipo di gateway a un altro senza eliminare e ricreare il gateway. Quando viene richiesto di confermare che si desidera procedere con la creazione del gateway, fare clic su **Sì**.

![Tipo di gateway](./media/vpn-gateway-configure-vpn-gateway-mp/IC717026.png)

Durante la creazione del gateway la relativa rappresentazione grafica nella pagina diventa gialla e viene visualizzato il testo *Creazione del gateway*. La creazione del gateway può richiedere fino a 25 minuti. Sarà necessario attendere il completamento del gateway prima di poter proseguire con altre impostazioni di configurazione.

![Creazione del gateway](./media/vpn-gateway-configure-vpn-gateway-mp/IC717027.png)

Quando per il gateway viene visualizzato *Connessione*, è possibile raccogliere le informazioni che saranno necessarie per il dispositivo VPN.

![Connessione del gateway](./media/vpn-gateway-configure-vpn-gateway-mp/IC717028.png)

## Raccogliere informazioni per la configurazione del dispositivo VPN

Dopo avere creato il gateway, raccogliere le informazioni per la configurazione del dispositivo VPN. Tali informazioni sono disponibili nella pagina **Dashboard** della rete virtuale:

1. **Indirizzo IP del gateway**: l'indirizzo IP è incluso nella pagina **Dashboard**, ma verrà visualizzato solo al termine della creazione del gateway.

1. **Chiave condivisa**: fare clic su **Gestisci chiave** nella parte inferiore della schermata. Fare clic sull'icona accanto alla chiave per copiarla negli Appunti, quindi incollare e salvare la chiave. Si noti che questo pulsante funziona solo quando è presente un solo tunnel VPN Site-to-Site. Se si dispone di più tunnel VPN Site-to-Site, usare l'API per ottenere la chiave condivisa del gateway di rete virtuale o il cmdlet di PowerShell.

![Gestisci chiave](./media/vpn-gateway-configure-vpn-gateway-mp/IC717029.png)


## Configurare il dispositivo VPN

Dopo avere completato i passaggi precedenti, l'utente o l'amministratore di rete deve configurare il dispositivo VPN per creare la connessione. Per altre informazioni sui dispositivi VPN, vedere [Informazioni sui dispositivi e i gateway VPN per la connettività di rete virtuale](http://go.microsoft.com/fwlink/p/?LinkID=615934).

Dopo avere configurato il dispositivo VPN, è possibile visualizzare le informazioni di connessione aggiornate nella pagina Dashboard per la rete virtuale.

Per verificare la connessione è anche possibile eseguire uno dei comandi seguenti:

| | Cisco ASA | Cisco ISR/ASR | Juniper SSG/ISG | Juniper SRX/J |
|----------------------|-----------------------|-----------------------|-----------------|------------------------------------------|
| **Check main mode SAs** | show crypto isakmp sa | show crypto isakmp sa | get ike cookie | show security ike security-association |
| **Check quick mode SAs** | show crypto ipsec sa | show crypto ipsec sa | get sa | show security ipsec security-association |


## Verificare gli intervalli della rete locale e l'indirizzo IP del gateway VPN

### Verificare l'indirizzo IP del gateway VPN

Per assicurare il corretto funzionamento del gateway, l'indirizzo IP del dispositivo VPN deve essere configurato in modo appropriato per la rete locale specificata per la configurazione cross-premise. In genere, l'indirizzo IP viene impostato durante il processo di configurazione Site-to-Site. Se, tuttavia, la rete locale è stata usata in precedenza con un dispositivo differente, o se l'indirizzo IP per la rete locale è cambiato, modificare le impostazioni per specificare il corretto indirizzo IP del gateway.

1. Per verificare l'indirizzo IP del gateway, fare clic su **Reti** nel riquadro sinistro del portale e quindi selezionare **Reti locali** nella parte superiore della pagina. Verrà visualizzato l'indirizzo del gateway VPN di ogni rete locale creata. Per modificare l'indirizzo IP, selezionare la rete virtuale e fare clic su **Modifica** nella parte inferiore della pagina.

1. Modificare l'indirizzo IP nella pagina **Specificare i dettagli della rete locale** e quindi fare clic sulla freccia Avanti nella parte inferiore della pagina.

1. Nella pagina **Specificare lo spazio di indirizzi** fare clic sul segno di spunta in basso a destra per salvare le impostazioni.

### Verificare gli intervalli di indirizzi delle reti locali

Affinché il traffico possa attraversare il gateway fino al percorso locale, sarà necessario verificare che siano elencati anche tutti gli intervalli di indirizzi IP che si desidera includere nella configurazione della rete locale. A seconda della configurazione di rete della posizione locale, questa attività può risultare complessa, dal momento che è necessario elencare ciascun intervallo nella configurazione delle **Reti locali** di Azure. Il traffico associato per un indirizzo IP contenuto all'interno degli intervalli elencati verrà quindi inviato tramite il gateway VPN di rete virtuale. Gli intervalli di indirizzi IP elencati non devono essere intervalli privati, anche se si desidera verificare che la configurazione locale sia in grado di ricevere il traffico in ingresso.

Per aggiungere o modificare intervalli per una rete locale, seguire la procedura riportata di seguito.

1. Per modificare gli intervalli di indirizzi IP per una rete locale, fare clic su **Reti** nel riquadro sinistro del portale e quindi selezionare **Reti locali** nella parte superiore della pagina. Nel portale il modo più semplice per visualizzare gli intervalli elencati è mediante la pagina **Modifica**. Per visualizzare gli intervalli, selezionare la rete virtuale e fare clic su **Modifica** nella parte inferiore della pagina.

1. Nella pagina **Specificare i dettagli della rete locale** non apportare alcuna modifica. Fare clic sulla freccia Avanti nella parte inferiore della pagina.

1. Nella pagina **Specificare lo spazio di indirizzi** modificare lo spazio di indirizzi della rete nel modo desiderato. Fare quindi clic sul segno di spunta per salvare la configurazione.

## Come visualizzare il traffico gateway

È possibile visualizzare il gateway e il relativo traffico dalla pagina **Dashboard** della rete virtuale.

Nella pagina **Dashboard** è possibile visualizzare quanto segue:

- La quantità di dati che passano attraverso il gateway, sia in entrata che in uscita.

- I nomi dei server DNS specificati per la rete virtuale.

- La connessione tra il gateway e il dispositivo VPN.

- La chiave condivisa usata per configurare la connessione del gateway al dispositivo VPN.


## Come modificare il tipo di gateway VPN

Poiché alcune configurazioni di connettività sono disponibili solo per determinati tipi di gateway, potrebbe essere necessario modificare il tipo di gateway di un gateway VPN esistente. È ad esempio possibile aggiungere la connettività Point-to-Site a una connessione Site-to-Site già esistente che dispone di un gateway statico. La connettività Point-to-Site richiede un gateway dinamico, pertanto per configurarla sarà necessario modificare il tipo di gateway da statico a dinamico.

Se è necessario modificare il tipo di routing di un gateway VPN, eliminare il gateway esistente e quindi ricrearlo con il nuovo tipo di routing. Non è necessario eliminare l'intera rete virtuale per modificare il tipo di routing del gateway.

Prima di modificare il tipo di gateway, verificare che il dispositivo VPN supporti il tipo di routing che si desidera usare. Per scaricare nuovi esempi di configurazione del routing e verificare i requisiti del dispositivo VPN, vedere [Informazioni sui dispositivi e i gateway VPN per la connettività di rete virtuale](http://go.microsoft.com/fwlink/p/?LinkID=615934).

>[AZURE.IMPORTANT]Quando si elimina un gateway VPN di rete virtuale, il relativo indirizzo VIP assegnato viene rilasciato. Quando si ricrea il gateway, a esso verrà assegnato un nuovo indirizzo VIP.

1. **Eliminare il gateway VPN esistente.**

	Nella pagina **Dashboard** per la rete virtuale passare nella parte inferiore e scegliere **Elimina gateway**. Attendere la notifica dell'eliminazione del gateway. Quando si riceve la notifica sullo schermo che il gateway è stato eliminato, è possibile creare un nuovo gateway.

1. **Creare un nuovo gateway VPN.**

	Per creare un nuovo gateway, seguire la procedura riportata nella parte superiore della pagina [Creare un gateway VPN](#create-a-vpn-gateway).


## Passaggi successivi

Per altre informazioni sulla connettività cross-premise di rete virtuale, vedere l'articolo [Informazioni sulla connettività cross-premise protetta della rete virtuale](http://go.microsoft.com/fwlink/p/?LinkID=532884).

È possibile aggiungere macchine virtuali nella rete virtuale. Vedere [Come creare una macchina virtuale personalizzata](../virtual-machines/virtual-machines-create-custom.md).

Se si desidera configurare una connessione VPN Point-to-Site, vedere l'articolo relativo alla [configurazione di una connessione VPN Point-to-Site](vpn-gateway-point-to-site-create.md).

 

<!---HONumber=July15_HO4-->