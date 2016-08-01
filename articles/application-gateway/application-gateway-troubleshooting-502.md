<properties
   pageTitle="Risolvere gli errori (502) del gateway non valido nel gateway applicazione | Microsoft Azure"
   description="Informazioni su come risolvere gli errori 502 del gateway applicazione"
   services="application-gateway"
   documentationCenter="na"
   authors="amitsriva"
   manager="rossort"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/15/2016"
   ms.author="amitsriva" />

#Risoluzione degli errori del gateway non valido nel gateway applicazione

##Panoramica
Dopo aver configurato un gateway applicazione di Azure, uno degli errori che gli utenti possono incontrare è "Errore del server: 502 - Risposta non valida ricevuta dal server Web in funzione come server proxy o gateway". Ciò può verificarsi a causa dei principali motivi seguenti:

- Il pool back-end del gateway applicazione di Azure non è configurato o è vuoto.
- Nessuna delle macchine virtuali o istanze nel set di scalabilità di macchine virtuali è integra.
- Le macchine virtuali o le istanze back-end del set di scalabilità di macchine virtuali non rispondono al probe di integrità predefinito.
- Configurazione non valida o inappropriata dei probe di integrità personalizzati.
- Problemi di timeout della richiesta o di connettività con le richieste degli utenti.


##BackendAddressPool vuoto
###Causa
Se il gateway applicazione non dispone di macchine virtuali o set di scalabilità di macchine virtuali configurati nel pool di indirizzi back-end, non può indirizzare le richieste del cliente e genera un errore di gateway non valido.

###Soluzione
Verificare che il pool di indirizzi back-end non sia vuoto. Questa operazione può essere eseguita tramite PowerShell, l'interfaccia della riga di comando o il portale.

	
	Get-AzureRmApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"

L'output del cmdlet precedente non dovrebbe contenere pool di indirizzi back-end vuoti. Di seguito è riportato un esempio in cui vengono restituiti due pool configurati con indirizzi IP o FQDN (nome di dominio completo) per le macchine virtuali back-end. Lo stato del provisioning di BackendAddressPool deve essere "Succeeded".
	
		BackendAddressPoolsText: 
				[{
					"BackendAddresses": [{
						"ipAddress": "10.0.0.10",
						"ipAddress": "10.0.0.11"
					}],
					"BackendIpConfigurations": [],
					"ProvisioningState": "Succeeded",
					"Name": "Pool01",
					"Etag": "W/"00000000-0000-0000-0000-000000000000"",
					"Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool01"
				}, {
					"BackendAddresses": [{
						"Fqdn": "xyx.cloudapp.net",
						"Fqdn": "abc.cloudapp.net"
					}],
					"BackendIpConfigurations": [],
					"ProvisioningState": "Succeeded",
					"Name": "Pool02",
					"Etag": "W/"00000000-0000-0000-0000-000000000000"",
					"Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool02"
				}]
	
	
## Istanze non integre in BackendAddressPool

###Causa
Se tutte le istanze di BackendAddressPool non sono integre, il gateway applicazione non avrà back-end su cui instradare la richiesta dell'utente. Questo potrebbe anche verificarsi quando le istanze back-end sono integre ma non dispongono dell'applicazione richiesta distribuita.

###Soluzione
Verificare che le istanze siano integre e che l'applicazione sia configurata correttamente. Controllare se le istanze back-end siano in grado di rispondere a un ping da un'altra macchina virtuale nella stessa rete virtuale. Se configurato con un endpoint pubblico, verificare che sia valida la richiesta del browser per l'applicazione Web.


##Problemi con il probe di integrità predefinito
###Causa
Gli errori 502 possono anche indicare che il probe di integrità predefinito non è in grado di raggiungere le macchine virtuali back-end. Quando viene eseguito il provisioning di un'istanza del gateway applicazione, viene automaticamente configurato il probe di integrità predefinito per ogni BackendAddressPool tramite le proprietà del BackendHttpSetting. Per impostare il probe non è necessaria alcuna azione da parte dell'utente. In particolare, quando viene configurata una regola di bilanciamento del carico, viene creata un'associazione tra BackendHttpSetting e BackendAddressPool. Il probe predefinito viene configurato per ognuna di queste associazioni e il gateway applicazione avvia una connessione di controllo di integrità periodica su ogni istanza nel BackendAddressPool, sulla porta specificata nell'elemento BackendHttpSetting. La tabella seguente elenca i valori associati al probe di integrità predefinito.


|Proprietà probe | Valore | Descrizione|
|---|---|---|
| URL probe| http://127.0.0.1/ | Percorso URL |
| Interval | 30 | Intervallo di probe in secondi |
| Timeout | 30 | Timeout di probe in secondi |
| Soglia non integra | 3 | Numero di tentativi di probe. Il server back-end viene contrassegnato come inattivo dopo che il numero di errori di probe consecutivi ha raggiunto una soglia non integra. |

###Soluzione
- Verificare che sia stato configurato un sito predefinito e che sia in ascolto su 127.0.0.1.
- Se BackendHttpSetting specifica una porta diversa da 80, il sito predefinito deve essere configurato per ascoltare tale porta.
- La chiamata a http://127.0.0.1:port deve restituire un codice risultato HTTP di 200. Questo valore deve essere restituito entro il periodo di timeout di 30 secondi.
- Verificare che la porta configurata sia aperta e che non esistano regole del firewall o gruppi di sicurezza di rete di Azure che bloccano il traffico in ingresso o in uscita sulla porta configurata.
- Se le macchine virtuali classiche di Azure o il servizio cloud vengono usati con indirizzi FQDN o IP pubblici, verificare che l'[endpoint](../virtual-machines/virtual-machines-windows-classic-setup-endpoints.md) corrispondente sia aperto.
- Se la macchina virtuale è stata configurata tramite Azure Resource Manager e si trova all'esterno della rete virtuale in cui è distribuito il gateway applicazione, è necessario configurare un [Gruppo di sicurezza di rete](../virtual-network/virtual-networks-nsg.md) per consentire l'accesso alla porta desiderata.


##Problemi con il probe di integrità personalizzato
###Causa
Il probe di integrità personalizzato consente una maggiore flessibilità per la ricerca di comportamenti predefiniti del probe. Quando si usano i probe personalizzati, gli utenti possono configurare l'intervallo di probe, l'URL e il percorso da testare, nonché il numero di risposte non riuscite da accettare prima di contrassegnare l'istanza del pool back-end come non integra. Vengono aggiunte le seguenti proprietà aggiuntive.


|Proprietà probe| Descrizione|
|---|---|
| Nome | Nome del probe. Questo nome viene usato per fare riferimento al probe nelle impostazioni HTTP back-end |
| Protocollo | Protocollo usato per inviare il probe. HTTP è il solo protocollo valido |
| Host | Nome host per inviare il probe. Applicabile solo quando vengono configurati più siti nel gateway applicazione. Questo nome è diverso dal nome host della macchina virtuale. |
| Path | Percorso relativo del probe. Il percorso valido inizia da "/". Il probe viene inviato a <protocollo>://<host>:<porta><percorso> |
| Interval | Intervallo di probe in secondi. Si tratta dell'intervallo di tempo tra due probe consecutivi.|
| Timeout | Timeout del probe in secondi. Il probe viene contrassegnato come non riuscito se non viene ricevuta una risposta valida entro questo periodo di timeout |
| Soglia non integra | Numero di tentativi di probe. Il server back-end viene contrassegnato come inattivo dopo che il numero di errori di probe consecutivi ha raggiunto una soglia non integra. |


###Soluzione
Verificare che il probe di integrità personalizzato sia configurato correttamente in base alla tabella riportata sopra. Oltre alle procedure di risoluzione dei problemi indicate in alto, verificare anche quanto segue.

- Verificare che il protocollo sia impostato solo su HTTP. HTTP non è attualmente supportato.
- Verificare che il probe sia specificato correttamente secondo le istruzioni della [guida](application-gateway-create-probe-ps.md).
- Se il gateway applicazione è configurato per un singolo sito, per impostazione predefinita il nome dell'host deve essere specificato come "127.0.0.1", se non diversamente configurato nel probe personalizzato.
- Verificare che la chiamata a http://\< host >: < porta >< percorso > restituisca un codice risultato HTTP di 200.
- Assicurarsi che Intervallo, Timeout e Soglia non integra siano compresi in intervalli accettabili.


##Timeout della richiesta
###Causa
Quando viene ricevuta una richiesta dell'utente, il gateway applicazione applica le regole configurate per la richiesta e indirizza la richiesta su un'istanza del pool back-end. Attende per un intervallo di tempo configurabile la risposta dall'istanza back-end. Per impostazione predefinita, l'intervallo è impostato su **30 secondi**. Se il gateway applicazione non riceve una risposta dall'applicazione back-end in questo intervallo, la richiesta dell'utente visualizza l'errore 502.

###Soluzione
Il gateway applicazione consente agli utenti di configurare questa impostazione tramite BackendHttpSetting, applicabile a pool diversi. I diversi pool back-end possono avere BackendHttpSetting differenti e quindi una diversa configurazione per la richiesta di timeout.

	New-AzureRmApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60

##Passaggi successivi

Se i passaggi precedenti non risolvono il problema, aprire un [ticket al supporto tecnico](https://azure.microsoft.com/support/options/).

<!---HONumber=AcomDC_0720_2016-->