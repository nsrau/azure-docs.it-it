<properties
   pageTitle="Reimpostare un gateway VPN di Azure | Microsoft Azure"
   description="Questo articolo illustra in modo dettagliato la reimpostazione del gateway VPN di Azure. Questo articolo è applicabile ai gateway VPN creati usando il modello di distribuzione classica."
   services="vpn-gateway"
   documentationCenter="na"
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
   ms.date="08/16/2016"
   ms.author="cherylmc"/>

# Reimpostare un gateway VPN di Azure mediante PowerShell


Questo articolo illustra in modo dettagliato la reimpostazione del gateway VPN di Azure con i cmdlet di PowerShell. Queste istruzioni si applicano al modello di distribuzione classica. Attualmente, i gateway di rete virtuale creati nel modello di distribuzione Resource Manager non possono essere reimpostati.

La reimpostazione del gateway VPN di Azure è utile se si perde la connettività VPN cross-premise in uno o più tunnel VPN da sito a sito. In questa situazione tutti i dispositivi VPN funzionano correttamente, ma non sono in grado di stabilire tunnel IPsec con i gateway VPN di Azure. Quando si usa il cmdlet *Reset-AzureVNetGateway*, il gateway viene riavviato e quindi le configurazioni cross-premise vengono riapplicate al gateway. Il gateway mantiene l'indirizzo IP pubblico già disponibile. Non sarà quindi necessario aggiornare la configurazione del router VPN con un nuovo indirizzo IP pubblico per il gateway VPN di Azure.


Prima di reimpostare il gateway, verificare gli elementi chiave elencati di seguito per ogni tunnel VPN da sito a sito (S2S) IPsec. Eventuali mancate corrispondenze negli elementi provocherà la disconnessione dei tunnel VPN S2S. La verifica e la correzione delle configurazioni per i gateway locali e VPN di Azure eviterà riavvii non necessari e interruzioni per le altre connessioni funzionanti nei gateway.

Verificare gli elementi seguenti prima di reimpostare il gateway.

- Gli indirizzi IP virtuali per il gateway VPN di Azure e il gateway VPN locale devono essere configurati correttamente nei criteri VPN di Azure e locali.
- La chiave precondivisa deve essere uguale nei gateway VPN di Azure e locali.
- Se si applica una configurazione IPsec/IKE specifica, ad esempio la crittografia, gli algoritmi hash e PFS (Perfect Forward Secrecy), assicurarsi che i gateway VPN di Azure e locali abbiano le stesse configurazioni.


## Reimpostare un gateway VPN mediante PowerShell

Il cmdlet PowerShell per la reimpostazione di un gateway VPN di Azure è *Reset-AzureVNetGateway*. Ogni gateway VPN di Azure è costituito da due istanze di VM in esecuzione in una configurazione di standby attivo. Dopo l'emissione del comando, l'istanza attualmente attiva del gateway VPN di Azure verrà riavviata immediatamente. Si verificherà un breve intervallo durante il failover dall'istanza attiva (in fase di riavvio) all'istanza di standby. L'intervallo dovrebbe essere inferiore a un minuto.

L'esempio seguente reimposta il gateway VPN di Azure per la rete virtuale denominata "ContosoVNet".
 
		Reset-AzureVNetGateway –VnetName “ContosoVNet” 

	 	Error          :
	 	HttpStatusCode : OK
	 	Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
	 	Status         : Successful
		RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
		StatusCode     : OK


Se la connessione non viene ripristinata dopo il primo riavvio, emettere di nuovo lo stesso comando per riavviare la seconda istanza della VM, ovvero il nuovo gateway attivo. Se vengono richiesti due riavvii uno dopo l'altro, il periodo necessario per il riavvio di entrambe le istanze della VM (attiva e di standby) sarà leggermente più lungo. Si verificherà quindi un intervallo più lungo nella connettività VPN, dai 2 ai 4 minuti, in attesa del completamento dei riavvii.

Dopo due riavvii, se si verificano ancora problemi di connettività tra più sedi locali, aprire un ticket di supporto dal portale di Azure classico per contattare il supporto tecnico di Microsoft Azure.

## Passaggi successivi
	
Per altre informazioni su questo cmdlet, vedere [Riferimenti PowerShell](https://msdn.microsoft.com/library/azure/mt270366.aspx).

<!---HONumber=AcomDC_0824_2016-->