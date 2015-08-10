<properties
   pageTitle="Creare una rete virtuale con una connessione VPN da sito a sito nel portale di gestione | Microsoft Azure"
   description="Creare una rete virtuale con una connessione VPN Site-to-Site per configurazioni cross-premise e ibride."
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
   ms.date="07/23/2015"
   ms.author="cherylmc"/>

# Creare una rete virtuale con una connessione VPN da sito a sito nel portale di gestione

> [AZURE.SELECTOR]
- [Azure Portal](vpn-gateway-site-to-site-create.md)
- [PowerShell - Resource Manager](vpn-gateway-create-site-to-site-rm-powershell.md)

È possibile connettere il percorso locale con una rete virtuale creando una connessione VPN Site-to-Site. Questa procedura illustra come creare una rete virtuale e una connessione VPN Site-to-Site tra la rete virtuale appena creata e il percorso locale.

Azure dispone attualmente di due modalità di gestione: gestione dei servizi di Azure e gestione delle risorse di Azure (ARM). Il programma di installazione del sito a sito è diverso, a seconda della modalità in cui la rete virtuale è stata creata. Queste istruzioni si applicano alla gestione dei servizi Azure. Se invece si desidera creare una connessione VPN da sito a sito di gestione risorse di Azure, vedere[creare una connessione VPN da sito a sito utilizzando Gestione risorse di Azure e PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md).


## Prima di iniziare

- Verificare che il dispositivo VPN che si desidera usare soddisfi i requisiti necessari per la creazione di una connessione di rete virtuale cross-premise. Per altre informazioni, vedere [Informazioni sui dispositivi e i gateway VPN per la connettività di rete virtuale](http://go.microsoft.com/fwlink/p/?LinkId=615099).

- Ottenere un IP IPv4 accessibile pubblicamente per il dispositivo VPN. Tale indirizzo IP è necessario per una configurazione Site-to-Site e viene usato per il dispositivo VPN, che non può trovarsi dietro un dispositivo NAT.

>[AZURE.IMPORTANT]Se non si ha familiarità con la configurazione del dispositivo VPN o non si conoscono gli intervalli di indirizzi IP che si trovano nella configurazione della rete locale, è necessario rivolgersi a un esperto in grado di fornire le informazioni necessarie.

## Creare la rete virtuale

1. Accedere al **portale di gestione**.

2. Nell'angolo inferiore sinistro della schermata fare clic su **New**. Nel riquadro di spostamento, fare clic su **Servizi di rete**, quindi fare clic su **Rete virtuale**. Fare clic su **Custom Create** per avviare la configurazione guidata.

3. Inserire le informazioni nelle pagine seguenti per creare la rete virtuale.

## Pagina Dettagli della rete virtuale

Immettere le informazioni riportate di seguito.

- **Nome**: assegnare un nome alla rete virtuale. Ad esempio, *EastUSVNet*. Questo nome della rete virtuale viene usato quando si distribuiscono le macchine virtuali e le istanze PaaS e pertanto è consigliabile non specificare un nome troppo complicato.
- **Indirizzo**: la località è direttamente correlata alla posizione fisica (regione) in cui si desidera che le risorse (macchine virtuali) devono risiedere. Se ad esempio si desidera che le macchine virtuali distribuite nella rete virtuale vengano posizionate fisicamente nell'area *Stati Uniti occidentali*, selezionare tale posizione. È possibile modificare il percorso associato alla rete virtuale dopo averla creata.

## Pagina Server DNS e connettività VPN
Immettere le informazioni riportate di seguito e quindi fare clic sulla freccia Avanti in basso a destra.

- **Server DNS**:immettere il nome del server DNS e l'indirizzo IP o selezionare un server DNS registrato in precedenza dall'elenco a discesa. Questa impostazione non crea un server DNS, consente di specificare i server DNS che si desidera usare per la risoluzione dei nomi per la rete virtuale.
- **Configura una VPN Site-to-Site**: selezionare la casella di controllo per **configurare una VPN Site-to-Site**.
- **Rete locale**: una rete locale rappresenta la posizione fisica locale. È possibile selezionare una rete locale creata in precedenza oppure crearne una nuova. Tuttavia, se si sceglie di usare una rete locale creata in precedenza, è opportuno visitare la pagina di configurazione **Reti locali** e verificare che l'indirizzo IP (indirizzo IPv4 pubblico) per il dispositivo VPN che si usa per la connessione sia corretto.

## Pagina Connettività Site-to-Site
Se si crea una nuova rete locale, verrà visualizzata la pagina **Connettività Site-to-Site**. Se si desidera usare una rete locale creata in precedenza, questa pagina non verrà visualizzata nella procedura guidata e sarà possibile passare alla sezione successiva.

Immettere le informazioni riportate di seguito e quindi fare clic sulla freccia Avanti.

- 	**Nome**: nome da assegnare al sito di rete locale.
- 	**Indirizzo IP dispositivo VPN**: indirizzo IPv4 pubblico del dispositivo VPN locale che verrà usato per la connessione ad Azure. Il dispositivo VPN non può trovarsi dietro un NAT.
- 	**Spazio di indirizzi**: includono l'indirizzo IP iniziale e CIDR (conteggio indirizzi). Qui è possibile specificare l'intervallo o gli intervalli di indirizzi che si desidera inviare tramite il gateway di rete virtuale al percorso locale. Se un indirizzo IP di destinazione rientra negli intervalli qui specificati, verrà instradato tramite il gateway della rete virtuale.
- 	**Aggiungi spazio di indirizzi**: se si dispone di più intervalli di indirizzi che si desidera inviare tramite il gateway di rete virtuale, tramite questa opzione è possibile specificare tutti gli intervalli di indirizzi aggiuntivi. È possibile aggiungere o rimuovere gli intervalli in un secondo momento nella pagina Rete locale.

## Spazi di indirizzi della rete virtuale
Specificare l'intervallo di indirizzi che si desidera usare per la rete virtuale. Si tratta di indirizzi IP dinamici (DIP) che verranno assegnati per le macchine virtuali e altre istanze del ruolo da distribuire a questa rete virtuale.

È particolarmente importante selezionare un intervallo che non si sovrapponga con gli intervalli utilizzati per la rete locale. Sarà necessario coordinarsi con l'amministratore di rete. L'amministratore di rete dovrà selezionare un intervallo di indirizzi IP dallo spazio di indirizzi della rete locale da usare per la rete virtuale.

Immettere le informazioni seguenti e quindi fare clic sul segno di spunta in basso a destra per configurare la rete.

- **Spazio di indirizzi**: includono indirizzo IP iniziale e conteggio indirizzi. Verificare che gli spazi di indirizzi specificati non si sovrappongano agli spazi di indirizzi presenti nella rete locale.
- **Aggiungi subnet**: includono indirizzo IP iniziale e conteggio indirizzi. Non sono necessarie altre subnet, ma è possibile creare una subnet separata per le macchine virtuali che avranno DIP statici. In alternativa, è possibile collocare le macchine virtuali in una subnet separata dalle istanze del ruolo.
- **Aggiungi subnet gateway**: fare clic per aggiungere la subnet del gateway. La subnet del gateway viene usata solo per il gateway di rete virtuale ed è obbligatoria per la configurazione.

Fare clic sul segno di spunta nella parte inferiore della pagina per iniziare a creare la rete virtuale. Al termine della creazione della rete virtuale, nella colonna **Stato** della pagina **Reti** del portale di gestione verrà visualizzata la dicitura **Creato**. Dopo aver creato la rete virtuale, è quindi possibile configurare il gateway di rete virtuale.

## Configurare il gateway di rete virtuale

Configurare quindi il **Gateway di rete virtuale** per creare una connessione Site-to-Site sicura. Vedere [Configurare un gateway di rete virtuale nel portale di gestione](vpn-gateway-configure-vpn-gateway-mp.md).

## Passaggi successivi

Per altre informazioni sulla connettività cross-premise di rete virtuale, vedere l'articolo [Informazioni sulla connettività cross-premise protetta della rete virtuale](https://msdn.microsoft.com/library/azure/dn133798.aspx).

Se si desidera configurare una connessione VPN Point-to-Site, vedere l'articolo relativo alla [configurazione di una connessione VPN Point-to-Site](vpn-gateway-point-to-site-create.md).

È possibile aggiungere macchine virtuali nella rete virtuale. Vedere [Come creare una macchina virtuale personalizzata](../virtual-machines/virtual-machines-create-custom.md).

Se si vuole configurare una connessione di rete virtuale con RRAS, vedere [Configurare una VPN da sito a sito mediante RRAS (Routing and Remote Access Service, servizio Routing e accesso remoto) di Windows Server 2012](https://msdn.microsoft.com/library/dn636917.aspx)
 

<!---HONumber=July15_HO5-->