<properties
   pageTitle="Creare una rete virtuale con una connessione VPN da sito a sito nel portale di Azure | Microsoft Azure"
   description="Creare una rete virtuale con una connessione VPN Site-to-Site per configurazioni cross-premise e ibride tramite il modello di distribuzione classico."
   services="vpn-gateway"
   documentationCenter=""
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/21/2015"
   ms.author="cherylmc"/>

# Creare una rete virtuale con una connessione VPN da sito a sito nel portale di Azure

> [AZURE.SELECTOR]
- [Azure portal](vpn-gateway-site-to-site-create.md)
- [PowerShell - Resource Manager](vpn-gateway-create-site-to-site-rm-powershell.md)

In questo argomento verrà illustrata la creazione di una rete virtuale e una connessione VPN da sito a sito alla rete locale. Questo articolo si applica al modello di distribuzione classica.

>[AZURE.NOTE]Prima di creare una rete virtuale, è importante comprendere che Azure attualmente funziona con due modelli di distribuzione: Gestione risorse e Classico. Prima di iniziare la configurazione, assicurarsi di comprendere i modelli di distribuzione e gli strumenti. Per informazioni sui modelli di distribuzione, vedere [Modelli di distribuzione Azure](../azure-classic-rm.md).

È possibile selezionare l'articolo per il modello di distribuzione e lo strumento di distribuzione utilizzando le schede sopra riportate. Per esempio, se si desidera creare una connessione gateway VPN da sito a sito utilizzando il modello di gestione risorse di Azure invece del modello classico, fare clic sulla scheda **Gestione risorse di PowerShell** (sopra) per vedere [Creare una connessione VPN da sito a sito utilizzando Gestione risorse di Azure e PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md).

 
## Prima di iniziare

Prima di iniziare la configurazione, verificare che ci siano le condizioni seguenti:

- Un dispositivo VPN compatibile e un utente che sia in grado di configurarlo. Vedere [Informazioni sui dispositivi VPN](vpn-gateway-about-vpn-devices.md). Se non si ha familiarità con la configurazione del dispositivo VPN o non si conoscono gli intervalli di indirizzi IP che si trovano nella configurazione della rete locale, è necessario rivolgersi a un esperto in grado di fornire le informazioni necessarie.

-  Un indirizzo IP pubblico esterno per il dispositivo VPN. L’indirizzo IP non può trovarsi dietro un NAT.

- Una sottoscrizione di Azure. Se non si dispone già di una sottoscrizione di Azure, è possibile attivare i [benefici della sottoscrizione MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere una [versione di valutazione gratuita](http://azure.microsoft.com/pricing/free-trial/).


## Creare la rete virtuale

1. Accedere al **Portale di Azure**.

2. Nell'angolo inferiore sinistro della schermata fare clic su **Nuovo**. Nel riquadro di spostamento, fare clic su **Servizi di rete**, quindi fare clic su **Rete virtuale**. Fare clic su **Custom Create** per avviare la configurazione guidata.

3. Inserire le informazioni nelle pagine seguenti per creare la rete virtuale.

## Pagina Dettagli della rete virtuale

Immettere le seguenti informazioni.

- **Nome**: assegnare un nome alla rete virtuale. Ad esempio, *EastUSVNet*. Questo nome della rete virtuale viene usato quando si distribuiscono le macchine virtuali e le istanze PaaS e pertanto è consigliabile non specificare un nome troppo complicato.
- **Indirizzo**: la località è direttamente correlata alla posizione fisica (regione) in cui si desidera che le risorse (macchine virtuali) devono risiedere. Se ad esempio si desidera che le macchine virtuali distribuite nella rete virtuale vengano posizionate fisicamente nell'area *Stati Uniti occidentali*, selezionare tale posizione. È possibile modificare il percorso associato alla rete virtuale dopo averla creata.

## Pagina Server DNS e connettività VPN
Immettere le informazioni riportate di seguito e quindi fare clic sulla freccia Avanti in basso a destra.

- **Server DNS**: immettere il nome del server DNS e l'indirizzo IP o selezionare un server DNS registrato in precedenza dal menu di scelta rapida. Questa impostazione non crea un server DNS, ma consente di specificare i server DNS da usare per la risoluzione dei nomi per la rete virtuale.
- **Configura una VPN Site-to-Site**: selezionare la casella di controllo per **configurare una VPN Site-to-Site**.
- **Rete locale**: una rete locale rappresenta la posizione fisica locale. È possibile selezionare una rete locale creata in precedenza oppure crearne una nuova. Tuttavia, se si sceglie di usare una rete locale creata in precedenza, è opportuno visitare la pagina di configurazione **Reti locali** e verificare che l'indirizzo IP (indirizzo IPv4 pubblico) per il dispositivo VPN che si usa per la connessione sia corretto.

## Pagina Connettività Site-to-Site
Se si crea una nuova rete locale, verrà visualizzata la pagina **Connettività Site-to-Site**. Se si desidera usare una rete locale creata in precedenza, questa pagina non verrà visualizzata nella procedura guidata e sarà possibile passare alla sezione successiva.

Immettere le informazioni riportate di seguito e quindi fare clic sulla freccia Avanti.

- 	**Nome**: nome da assegnare al sito di rete locale.
- 	**Indirizzo IP dispositivo VPN**: indirizzo IPv4 pubblico del dispositivo VPN locale che verrà usato per la connessione ad Azure. Il dispositivo VPN non può trovarsi dietro un NAT.
- 	**Spazio di indirizzi**: includono l'indirizzo IP iniziale e CIDR (conteggio indirizzi). Si tratta dell'opzione mediante la quale è possibile specificare il o gli intervalli di indirizzi che si desidera inviare tramite il gateway della rete virtuale al percorso locale. Se un indirizzo IP di destinazione rientra negli intervalli specificati qui, verrà instradato tramite il gateway della rete virtuale.
- 	**Aggiungi spazio di indirizzi**: se si dispone di più intervalli di indirizzi che si desidera inviare tramite il gateway di rete virtuale, tramite questa opzione è possibile specificare tutti gli intervalli di indirizzi aggiuntivi. È possibile aggiungere o rimuovere gli intervalli in un secondo momento nella pagina **Rete locale**.

## Spazi di indirizzi della rete virtuale
Specificare l'intervallo di indirizzi che si desidera usare per la rete virtuale. Questi sono gli indirizzi IP dinamici (DIP) che verranno assegnati alle macchine virtuali e alle istanze di altri ruoli distribuiti nella rete virtuale.

È particolarmente importante selezionare un intervallo che non si sovrapponga con gli intervalli utilizzati per la rete locale. Sarà necessario coordinarsi con l'amministratore di rete. L'amministratore di rete dovrà selezionare un intervallo di indirizzi IP dallo spazio di indirizzi della rete locale da usare per la rete virtuale.

Immettere le seguenti informazioni, quindi fare clic sul segno di spunta in basso a destra per configurare la rete.

- **Spazio di indirizzi**: includono indirizzo IP iniziale e conteggio indirizzi. Verificare che gli spazi di indirizzi specificati non si sovrappongano agli spazi di indirizzi presenti nella rete locale.
- **Aggiungi subnet**: includono indirizzo IP iniziale e conteggio indirizzi. Non sono necessarie altre subnet, ma è possibile crearne una separata per le macchine virtuali che avranno DIP statici. Oppure è possibile decidere di collocare le macchine virtuali in una subnet separata dalle istanze di altri ruoli.
- **Aggiungi subnet gateway**: fare clic per aggiungere la subnet del gateway. La subnet gateway viene usata solo per il gateway di rete virtuale ed è obbligatoria per la configurazione.

Fare clic sul segno di spunta nella parte inferiore della pagina per avviare la creazione della rete virtuale. Al termine della creazione della rete virtuale, in **Stato** verrà visualizzato **Creato** nella pagina **Reti** del portale di Azure. Dopo aver creato la rete virtuale, è quindi possibile configurare il gateway di rete virtuale.

[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## Configurare il gateway di rete virtuale

Configurare quindi il gateway di rete virtuale per creare una connessione da sito a sito sicura. Vedere [Configurare un gateway di rete virtuale nel portale di Azure](vpn-gateway-configure-vpn-gateway-mp.md).

## Passaggi successivi

Per altre informazioni sulla connettività di rete virtuale cross-premise, vedere l'articolo [Informazioni sulla connettività protetta cross-premise per le reti virtuali](vpn-gateway-cross-premises-options.md).

Se si vuole configurare una connessione VPN da punto a sito, vedere [Configurare una connessione VPN Point-to-Site](vpn-gateway-point-to-site-create.md).

È possibile aggiungere macchine virtuali nella rete virtuale. Vedere [Come creare una macchina virtuale personalizzata](../virtual-machines/virtual-machines-create-custom.md).

Se si vuole configurare una connessione tra la rete virtuale classica e una rete virtuale creata usando la modalità Gestione risorse di Azure, vedere [Connessione di reti virtuali classiche a reti virtuali di Gestione risorse di Azure](../virtual-network/virtual-networks-arm-asm-s2s-howto.md).

<!---HONumber=Oct15_HO4-->