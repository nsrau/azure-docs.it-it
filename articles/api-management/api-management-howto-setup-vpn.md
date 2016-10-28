<properties
	pageTitle="Come configurare connessioni VPN in Gestione API di Azure"
	description="Informazioni su come configurare una connessione VPN in Gestione API di Azure e usarla per accedere ai servizi Web."
	services="api-management"
	documentationCenter=""
	authors="antonba"
	manager="erikre"
	editor=""/> 

<tags
	ms.service="api-management"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/09/2016"
	ms.author="antonba"/> 

# Come configurare connessioni VPN in Gestione API di Azure

Il supporto VPN di Gestione API consente di connettere il gateway di Gestione API a una rete virtuale di Azure (classica). Questo consente ai clienti di Gestione API di connettersi in modo sicuro ai servizi Web back-end locali o altrimenti non accessibili per la rete Internet pubblica.

>[AZURE.NOTE] Gestione API di Azure funziona con reti virtuali classiche. Per informazioni sulla creazione di una rete virtuale classica, vedere [Creare una rete virtuale (classica) usando il portale di Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Per informazioni sulla connessione di reti virtuali classiche a reti virtuali di ARM, vedere [Connessione di reti virtuali classiche a nuove reti virtuali](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

## <a name="enable-vpn"> </a>Abilitare connessioni VPN

>La connettività VPN è disponibile solo nel livello **Premium** e **Developer**. Per passare a questo livello, aprire il servizio Gestione API nel [portale di Azure classico][] e quindi aprire la scheda **Ridimensiona**. Nella sezione **Generale** selezionare il livello Premium e fare clic su Salva.

Per abilitare la connettività VPN, aprire il servizio Gestione API nel [portale di Azure classico][] e passare alla scheda **Configura**.

Nella sezione VPN impostare **Connessione VPN** su **Attivata**.

![Scheda Configura dell'istanza di Gestione API][api-management-setup-vpn-configure]

Verrà ora visualizzato un elenco di tutte le aree in cui viene eseguito il provisioning del servizio Gestione API.

Selezionare un VPN e una subnet per ogni area. L'elenco di VPN viene popolato in base alle reti virtuali disponibili nella sottoscrizione di Azure, impostate nell'area che si sta configurando.

![Selezionare una VPN][api-management-setup-vpn-select]

Fare clic su **Salva** nella parte inferiore della schermata. Durante l'aggiornamento non sarà possibile eseguire altre operazioni nel servizio Gestione API dal portale di Azure classico. Il gateway del servizio rimarrà disponibile e le chiamate di runtime non dovrebbero essere interessate.

Si noti che l'indirizzo VIP del gateway cambierà ogni volta che la VPN verrà abilitata o disabilitata.

## <a name="connect-vpn"> </a>Connettersi a un servizio Web dietro la VPN

Dopo che il servizio Gestione API è stato connesso alla VPN, l'accesso ai servizi Web all'interno della rete virtuale non è diverso dall'accesso a servizi pubblici. Basterà digitare l'indirizzo locale o il nome host (se è stato configurato un server DNS per la rete virtuale di Azure) del servizio Web nel campo **URL del servizio Web** quando si crea una nuova API o se ne modifica una esistente.

![Aggiungere un'API dalla VPN][api-management-setup-vpn-add-api]

## Porte necessarie per il supporto VPN di Gestione API

Quando un'istanza del servizio Gestione API è ospitata in una rete virtuale, vengono usate le porte indicate nella tabella seguente. Se le porte sono bloccate, il servizio potrebbe non funzionare correttamente. Il blocco di una o più di queste porte è il problema di configurazione più comune nell'uso di Gestione API in una rete virtuale.

| Porte | Direzione | Protocollo di trasporto | Scopo | Origine/Destinazione |
|------------------------------|------------------|--------------------|------------------------------------------------------------------|-----------------------------------|
| 80, 443 | In ingresso | TCP | Comunicazione tra client e Gestione API | INTERNET / VIRTUAL\_NETWORK |
| 80,443 | In uscita | TCP | Dipendenza di Gestione API in archiviazione di Azure e bus di servizio di Azure | VIRTUAL\_NETWORK / INTERNET |
| 1433 | In uscita | TCP | Dipendenze di Gestione API in SQL | VIRTUAL\_NETWORK / INTERNET |
| 9350, 9351, 9352, 9353, 9354 | In uscita | TCP | Dipendenze di Gestione API nel bus di servizio | VIRTUAL\_NETWORK / INTERNET |
| 5671 | In uscita | AMQP | Dipendenza di Gestione API per il criterio Registra a Hub eventi | VIRTUAL\_NETWORK / INTERNET |
| 6381, 6382, 6383 | In ingresso/In uscita | UDP | Dipendenze di Gestione API nella cache Redis | VIRTUAL\_NETWORK / VIRTUAL\_NETWORK |
| 445 | In uscita | TCP | Dipendenza di Gestione API nella condivisione file di Azure per GIT | VIRTUAL\_NETWORK / INTERNET |

## <a name="custom-dns"> </a>Configurazione del server DNS personalizzato

Gestione API dipende da diversi servizi di Azure. Quando un'istanza del servizio Gestione API è ospitata in una rete virtuale in cui viene usato un server DNS personalizzato, è necessario che l'istanza possa risolvere i nomi host dei servizi di Azure. Vedere [queste](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) informazioni aggiuntive sulla configurazione del DNS personalizzato.

## <a name="related-content"> </a>Contenuti correlati


* [Creare una rete virtuale con una connessione VPN da sito a sito con il portale di Azure classico][]
* [Come usare Controllo API per tenere traccia delle chiamate in Gestione API di Azure][]

[api-management-setup-vpn-configure]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-configure.png
[api-management-setup-vpn-select]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-add-api.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[portale di Azure classico]: https://manage.windowsazure.com/

[Creare una rete virtuale con una connessione VPN da sito a sito con il portale di Azure classico]: ../vpn-gateway/vpn-gateway-site-to-site-create.md
[Come usare Controllo API per tenere traccia delle chiamate in Gestione API di Azure]: api-management-howto-api-inspector.md

<!---HONumber=AcomDC_0921_2016-->