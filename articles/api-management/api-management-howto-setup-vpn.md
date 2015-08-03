<properties
	pageTitle="Come configurare connessioni VPN in Gestione API di Azure"
	description="Informazioni su come configurare una connessione VPN in Gestione API di Azure e usarla per accedere ai servizi Web."
	services="api-management"
	documentationCenter=""
	authors="antonba"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="api-management"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/24/2015"
	ms.author="antonba"/>

# Come configurare connessioni VPN in Gestione API di Azure

Il supporto VPN di Gestione API consente di connettere il proxy di Gestione API a una rete virtuale di Azure. Questo consente ai clienti di Gestione API di connettersi in modo sicuro ai servizi Web back-end locali o altrimenti non accessibili per la rete Internet pubblica.

## <a name="enable-vpn"> </a>Abilitare connessioni VPN

>La connettività VPN è disponibile solo nel livello **Premium**. Per passare a questo livello, aprire il servizio Gestione API nel [portale di gestione][] e quindi aprire la scheda **Scalabilità**. Nella sezione **Generale** selezionare il livello Premium e fare clic su Salva.

Per abilitare la connettività VPN, aprire il servizio Gestione API nel [portale di gestione][] e passare alla scheda **Configura**.

Nella sezione VPN impostare **Connessione VPN** su **Attivata**.

![Scheda Configura dell'istanza di Gestione API][api-management-setup-vpn-configure]

Verrà ora visualizzato un elenco di tutte le aree in cui viene eseguito il provisioning del servizio Gestione API.

Selezionare un VPN e una subnet per ogni area. L'elenco di VPN viene popolato in base alle reti virtuali disponibili nella sottoscrizione di Azure, impostate nell'area che si sta configurando.

![Selezionare una VPN][api-management-setup-vpn-select]

Fare clic su **Salva** nella parte inferiore della schermata. Non sarà possibile eseguire altre operazioni nel servizio Gestione API dal portale di gestione di Azure durante l'aggiornamento. Il proxy del servizio rimarrà disponibile e le chiamate di runtime non dovrebbero essere interessate.

Si noti che l'indirizzo VIP del proxy cambierà ogni volta che la VPN verrà abilitata o disabilitata.

## <a name="connect-vpn"> </a>Connettersi a un servizio Web dietro la VPN

Dopo che il servizio Gestione API è stato connesso alla VPN, l'accesso ai servizi Web all'interno della rete virtuale non è diverso dall'accesso a servizi pubblici. Basterà digitare l'indirizzo locale o il nome host (se è stato configurato un server DNS per la rete virtuale di Azure) del servizio Web nel campo **URL del servizio Web** quando si crea una nuova API o se ne modifica una esistente.

![Aggiungere un'API dalla VPN][api-management-setup-vpn-add-api]


## <a name="related-content"> </a>Contenuti correlati


 * [Esercitazione: Creazione di una rete virtuale cross-premise per la connettività da sito a sito][]
 * [Come usare Controllo API per tenere traccia delle chiamate in Gestione API di Azure][]

[api-management-setup-vpn-configure]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-configure.png
[api-management-setup-vpn-select]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-add-api.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[portale di gestione]: https://manage.windowsazure.com/

[Esercitazione: Creazione di una rete virtuale cross-premise per la connettività da sito a sito]: ../virtual-networks-create-site-to-site-cross-premises-connectivity
[Come usare Controllo API per tenere traccia delle chiamate in Gestione API di Azure]: api-management-howto-api-inspector.md
 

<!---HONumber=July15_HO4-->