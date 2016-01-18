

<properties 
   pageTitle="Panoramica del monitoraggio dell'integrità per il gateway applicazione di Azure | Microsoft Azure"
   description="Informazioni sulle funzionalità di monitoraggio nel gateway applicazione di Azure"
   services="application-gateway"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/17/2015"
   ms.author="joaoma" />

# Panoramica del monitoraggio dell'integrità del gateway applicazione 


Per impostazione predefinita, il gateway applicazione di Azure monitora l'integrità di tutte le risorse nel pool back-end e rimuove automaticamente dal pool le risorse considerate non integre. Il gateway applicazione continua a monitorare le istanze non integre e le riaggiunge al pool back-end integro, dopo che sono diventate disponibili e rispondono ai probe di integrità.

Oltre al monitoraggio del probe di integrità predefinito, è anche possibile personalizzare il probe di integrità in base ai requisiti dell'applicazione. In questo articolo verranno illustrati i probe di integrità predefiniti e personalizzati.

## Probe di integrità predefinito

Un gateway applicazione configura automaticamente un probe di integrità predefinito quando non si configura nessuna configurazione di probe personalizzato. Il comportamento del monitoraggio prevede l'invio di una richiesta HTTP agli indirizzi IP configurati per il pool back-end.

Ad esempio, si configura il gateway applicazione per usare i server back-end A, B e C per ricevere il traffico di rete HTTP sulla porta 80. Il monitoraggio dell'integrità predefinito testa i tre server ogni 30 secondi per ottenere una risposta HTTP integra. Una risposta HTTP integra ha un [codice di stato](https://msdn.microsoft.com/library/aa287675.aspx) compreso tra 200 e 399.

Se il controllo probe predefinito non riesce per il server A, il gateway applicazione lo rimuove dal pool back-end e il flusso del traffico di rete a questo server viene arrestato. Il probe predefinito continua tuttavia a controllare il server A ogni 30 secondi. Quando il server A risponde correttamente a una richiesta di un probe di integrità predefinito, viene aggiunto di nuovo come integro al pool back-end e il flusso del traffico verso il server riprenderà.

Il probe predefinito esamina solo http://127.0.0.1:<port> per determinare lo stato dell'integrità. Se si deve configurare il probe di integrità per passare a un URL personalizzato o modificare un'altra impostazione, è necessario usare i probe personalizzati come descritto di seguito.

### Impostazioni del probe di integrità predefinito

|Proprietà probe | Valore | Descrizione|
|---|---|---|
| URL probe| http://127.0.0.1/ | Percorso URL |
| Interval | 30 | Intervallo di probe in secondi |
| Timeout | 30 | Timeout del probe in secondi |
| Soglia non integra | 3 | Numero di tentativi di probe. Il server back-end viene contrassegnato come inattivo dopo che il numero di errori di probe consecutivi ha raggiunto una soglia non integra |


## Probe di integrità personalizzato 

I probe personalizzati consentono un controllo più granulare sul monitoraggio dell'integrità. Quando si usano i probe personalizzati, è possibile configurare l'intervallo di probe, l'URL e il percorso da testare e il numero di risposte non riuscite da accettare prima di contrassegnare l'istanza del pool back-end come non integra.


### Impostazioni del probe di integrità personalizzato

|Proprietà probe| Descrizione|
|---|---|
| Nome | Nome del probe Questo nome viene usato per fare riferimento al probe nelle impostazioni Http back-end |
| Protocollo | Protocollo usato per inviare il probe. Http è il solo protocollo valido |
| Host | Nome host per inviare il probe |
| Path | Percorso relativo del probe. Il percorso valido inizia da "/". Il probe viene inviato a <protocol>://<host>:<port><path> |
| Interval | Intervallo di probe in secondi. Si tratta dell'intervallo di tempo tra due probe consecutivi| 
| Timeout | Timeout del probe in secondi. Il probe viene contrassegnato come non riuscito se non viene ricevuta una risposta valida entro questo periodo di timeout |
| Soglia non integra | Numero di tentativi di probe. Il server back-end viene contrassegnato come inattivo dopo che il numero di errori di probe consecutivi ha raggiunto una soglia non integra |

## Passaggi successivi

Dopo avere acquisito familiarità con il monitoraggio dell'integrità del gateway applicazione, è possibile configurare un [probe di integrità personalizzato](application-gateway-create-probe-ps.md) per Gestione risorse di Azure o un [probe di integrità personalizzato](application-gateway-create-probe-classic-ps.md) per il modello di distribuzione classica di Azure.

<!---HONumber=AcomDC_0107_2016-->