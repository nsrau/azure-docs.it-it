<properties 
   pageTitle="Come gestire le proprietà della rete virtuale (VNet)"
   description="Informazioni su come visualizzare e modificare le impostazioni della rete virtuale"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/02/2015"
   ms.author="telmos" />

# Come gestire le proprietà della rete virtuale (VNet)
È possibile visualizzare e modificare le impostazioni di una rete virtuale usando il portale di Azure.

## Visualizzare le impostazioni della rete virtuale

1. Accedere al portale di gestione.

1. Nel pannello di navigazione fare clic su **Reti** e quindi sul nome della rete virtuale nella colonna **Nome**.

1. Fare clic su **Dashboard** per visualizzare le informazioni seguenti:

	- **Rete virtuale:** vengono visualizzate la rete virtuale e le subnet insieme alla rete locale e al server DNS.

	- **Dati in entrata e in uscita:** visibili quando si dispone della connettività cross-premise. Si tratta dei dati in entrata e in uscita dalla rete virtuale.

	- **Indirizzo IP del gateway:** visibile quando è configurato un gateway. Si tratta dell'indirizzo IP del gateway ed è l'indirizzo IP che verrà usato per configurare il dispositivo VPN per la connettività cross-premise.

	- **Risorse:** sono le risorse di Azure connesse alla rete virtuale.

	- **Riepilogo rapido:** area in cui è possibile trovare rapidamente le informazioni seguenti:

		- **Scarica pacchetto VPN client:** questo pacchetto viene usato durante la creazione di connessioni VPN cross-premise da punto a sito. Scaricare il pacchetto relativo al client.

		- **Scarica script dispositivo VPN:** questi modelli vengono usati per configurare i dispositivi VPN per le connessioni cross-premise da sito a sito.

		- **Stato**

		- **ID sottoscrizione**
		
		- **ID rete virtuale**
		
		- **Località**

1. Fare clic su **Configura** per visualizzare le informazioni seguenti:

	- **Server DNS:** si tratta dei server DNS e del relativo indirizzo IP corrispondente che verranno usati dai client nella rete virtuale per la risoluzione dei nomi.

	- **Connettività Point-to-Site:** visibile quando la rete virtuale è configurata per le connessioni da punto a sito.

	- **Connettività Site-to-Site:** visibile quando la rete virtuale è configurata per le connessioni da sito a sito.

	- **Rete locale:** viene visualizzata se si dispone della connettività da sito a sito. Si tratta della rete locale selezionata che rappresenta il sito locale. Le impostazioni per tale rete locale possono essere configurate in **Reti** nella pagina **Reti locali**.
	
	- **Spazi di indirizzi della rete virtuale:** spazio degli indirizzi e subnet della rete virtuale.

1. Fare clic su **Certificati** per visualizzare le informazioni seguenti:

	- **Certificato radice caricato:** per le configurazioni VPN da punto a sito
	
	- **Identificazione digitale del certificato**
	
	- **Scadenza del certificato**
	
	- **Carica:** carica un certificato radice
	
	- **Elimina:** elimina un certificato radice selezionato

## Modificare le impostazioni della rete virtuale

Dopo aver distribuito la rete virtuale, è possibile modificare solo alcune impostazioni. La procedura seguente consente di modificare le impostazioni di configurazione della rete virtuale usando il portale di gestione.

1. Accedere al portale di gestione.

1. Nel pannello di navigazione fare clic su **Reti** e quindi sul nome della rete virtuale nella colonna **Nome**.

1. Fare clic su **Configura**.

	- In **Server DNS** è possibile aggiungere altri server DNS per la risoluzione dei nomi. Se si aggiungono altri server DNS dopo aver distribuito macchine virtuali nella rete virtuale, sarà necessario riavviare ognuna di tali macchine in modo che rilevi il nuovo server DNS aggiuntivo.
	
	- Nella sezione relativa alla **connettività Point-to-Site** è possibile configurare lo spazio degli indirizzi che si vuole designare per le connessioni da punto a sito.
	
	- Nella sezione relativa alla **connettività Site-to-Site** è possibile configurare le impostazioni da sito a sito.
	
	- Nella sezione relativa allo **spazio di indirizzi della rete virtuale** è possibile aggiungere un ulteriore spazio degli indirizzi e altre subnet. Per aggiungere subnet, fare clic su **Aggiungi subnet** e configurare lo spazio degli indirizzi che si vuole usare. Per aggiungere una subnet del gateway per la connettività cross-premise, fare clic su **Aggiungi subnet gateway**. È possibile aggiungere solo una subnet del gateway per ogni rete virtuale.

1. Fare clic su **SALVA** nella parte inferiore dello schermo per salvare le modifiche apportate alla configurazione.

## Passaggi successivi

[Come gestire i server DNS usati da una rete virtuale (VNet)](../virtual-networks-manage-dns-in-vnet)

[Come usare gli indirizzi IP pubblici in una rete virtuale](../virtual-networks-public-ip-within-vnet)

[Come eliminare una rete virtuale (VNet)](../virtual-networks-delete-vnet)

<!---HONumber=Sept15_HO2-->