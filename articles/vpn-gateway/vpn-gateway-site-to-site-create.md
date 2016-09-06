<properties
   pageTitle="Creare una rete virtuale con una connessione gateway VPN da sito a sito con il portale di Azure classico | Microsoft Azure"
   description="Creare una rete virtuale con una connessione di gateway VPN da sito a sito per configurazioni cross-premise e ibride usando il modello di distribuzione classico."
   services="vpn-gateway"
   documentationCenter=""
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="cherylmc"/>  

# Creare una rete virtuale con una connessione da sito a sito usando il portale di Azure classico

> [AZURE.SELECTOR]
- [Portale di Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Portale di Azure classico](vpn-gateway-site-to-site-create.md)
- [PowerShell - Gestione risorse](vpn-gateway-create-site-to-site-rm-powershell.md)


Questo articolo illustra la creazione di una rete virtuale e una connessione VPN da sito a sito alla rete locale con il **modello di distribuzione classica** e il portale classico. Le connessioni da sito a sito possono essere usate per le configurazioni cross-premise e ibride. Attualmente, non è possibile creare una configurazione da sito a sito per il modello di distribuzione classica tramite il portale di Azure.

![Diagramma da sito a sito](./media/vpn-gateway-site-to-site-create/site2site.png "Da sito a sito")  


### Strumenti e modelli di distribuzione per le connessioni da sito a sito

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]

Per connettere più reti virtuali, vedere [Configurare una connessione tra reti virtuali per il modello di distribuzione classica](virtual-networks-configure-vnet-to-vnet-connection.md).
 
## Prima di iniziare

Prima di iniziare la configurazione, verificare che ci siano le condizioni seguenti:

- Un dispositivo VPN compatibile e un utente che sia in grado di configurarlo. Vedere [Informazioni sui dispositivi VPN](vpn-gateway-about-vpn-devices.md). Se non si ha familiarità con la configurazione del dispositivo VPN o con gli intervalli di indirizzi IP disponibili nella configurazione di rete locale, è necessario coordinarsi con qualcuno che possa fornire tali dettagli.

- Un indirizzo IP pubblico esterno per il dispositivo VPN. L'indirizzo IP non può trovarsi dietro un NAT.

- Una sottoscrizione di Azure. Se non è disponibile una sottoscrizione di Azure, è possibile attivare i [vantaggi dell'abbonamento a MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).


## Creare la rete virtuale

1. Accedere al [portale di Azure classico](https://manage.windowsazure.com/).

2. Nell'angolo inferiore sinistro della schermata fare clic su **Nuovo**. Nel riquadro di spostamento fare clic su **Servizi di rete** e quindi su **Rete virtuale**. Fare clic su **Custom Create** per avviare la configurazione guidata.

3. Per creare la rete virtuale, inserire le informazioni nelle pagine seguenti.

## Pagina Dettagli della rete virtuale

Immettere le seguenti informazioni.

- **Nome**: assegnare un nome alla rete virtuale. Ad esempio, *EastUSVNet*. Questo nome della rete virtuale viene usato quando si distribuiscono le macchine virtuali e le istanze PaaS, quindi è consigliabile non specificare un nome troppo complicato.
- **Indirizzo**: la località è direttamente correlata alla località fisica (area) in cui si vuole che risiedano le risorse (macchine virtuali). Se ad esempio si vuole che le macchine virtuali distribuite nella rete virtuale siano collocate fisicamente nell'area *Stati Uniti occidentali*, selezionare tale località. È possibile modificare il percorso associato alla rete virtuale dopo averla creata.

## Pagina Server DNS e connettività VPN

Immettere le informazioni riportate di seguito e quindi fare clic sulla freccia Avanti in basso a destra.

- **Server DNS**: immettere il nome del server DNS e l'indirizzo IP o selezionare un server DNS registrato in precedenza dal menu di scelta rapida. Questa impostazione non comporta la creazione di un server DNS. Consente di specificare i server DNS da usare per la risoluzione dei nomi per la rete virtuale.
- **Configura una VPN Site-to-Site**: selezionare la casella di controllo per **configurare una VPN Site-to-Site**.
- **Rete locale**: una rete locale rappresenta la località fisica locale. È possibile selezionare una rete locale creata in precedenza oppure crearne una nuova. Tuttavia, se si sceglie di usare una rete locale creata in precedenza, vedere la pagina di configurazione **Reti locali** e verificare che l'indirizzo IP (indirizzo IPv4 pubblico) per il dispositivo VPN sia corretto.

## Pagina Connettività da sito a sito

Se si crea una nuova rete locale, verrà visualizzata la pagina **Connettività da sito a sito**. Se si desidera usare una rete locale creata in precedenza, questa pagina non verrà visualizzata nella procedura guidata e sarà possibile passare alla sezione successiva.

Immettere le informazioni riportate di seguito e quindi fare clic sulla freccia Avanti.

- 	**Nome**: nome da assegnare al sito di rete locale.
- 	**Indirizzo IP dispositivo VPN**: indirizzo IPv4 pubblico del dispositivo VPN locale usato per la connessione ad Azure. Il dispositivo VPN non può trovarsi dietro un NAT.
- 	**Spazio di indirizzi**: includono l'indirizzo IP iniziale e CIDR (conteggio indirizzi). È possibile specificare gli intervalli di indirizzi da inviare tramite il gateway di rete virtuale al percorso locale. Se un indirizzo IP di destinazione rientra negli intervalli specificati qui, verrà instradato tramite il gateway di rete virtuale.
- 	**Aggiungi spazio di indirizzi**: se si vogliono inviare più intervalli di indirizzi tramite il gateway di rete virtuale, specificare i singoli intervalli di indirizzi aggiuntivi. È possibile aggiungere o rimuovere gli intervalli in un secondo momento nella pagina **Rete locale**.

## Spazi di indirizzi della rete virtuale

Specificare l'intervallo di indirizzi che si desidera usare per la rete virtuale. Questi sono gli indirizzi IP dinamici (DIP) che verranno assegnati alle macchine virtuali e alle istanze di altri ruoli distribuiti nella rete virtuale.

È particolarmente importante selezionare un intervallo che non si sovrapponga con gli intervalli utilizzati per la rete locale. È necessario coordinarsi con l'amministratore di rete. L'amministratore di rete dovrà selezionare un intervallo di indirizzi IP dallo spazio di indirizzi della rete locale da usare per la rete virtuale.

Immettere le seguenti informazioni, quindi fare clic sul segno di spunta in basso a destra per configurare la rete.

- **Spazio di indirizzi**: sono inclusi l'indirizzo IP iniziale e il conteggio indirizzi. Verificare che gli spazi di indirizzi specificati non si sovrappongano agli spazi di indirizzi presenti nella rete locale.
- **Aggiungi subnet**: sono inclusi l'indirizzo IP iniziale e il conteggio indirizzi. Non sono necessarie altre subnet, ma è possibile crearne una separata per le macchine virtuali che avranno DIP statici. Oppure è possibile decidere di collocare le macchine virtuali in una subnet separata dalle istanze di altri ruoli.
- **Aggiungi subnet gateway**: fare clic per aggiungere la subnet del gateway. La subnet gateway viene usata solo per il gateway di rete virtuale ed è obbligatoria per la configurazione.

Fare clic sul segno di spunta nella parte inferiore della pagina per iniziare a creare la rete virtuale. Al termine della creazione della rete virtuale, in **Stato** verrà visualizzato **Creato** nella pagina **Reti** del portale di Azure classico. Dopo aver creato la rete virtuale, è quindi possibile configurare il gateway di rete virtuale.

[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## Configurare il gateway di rete virtuale

Configurare il gateway di rete virtuale per creare una connessione da sito a sito sicura. Vedere [Configurare un gateway VPN per il modello di distribuzione classica](vpn-gateway-configure-vpn-gateway-mp.md).

## Passaggi successivi

Dopo aver completato la connessione, è possibile aggiungere macchine virtuali alle reti virtuali. Per altre informazioni, vedere [Macchine virtuali - Documentazione](https://azure.microsoft.com/documentation/services/virtual-machines/).

<!---HONumber=AcomDC_0831_2016-->