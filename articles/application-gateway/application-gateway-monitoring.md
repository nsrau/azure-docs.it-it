<properties 
   pageTitle="Configurare un gateway applicazione per l'uso di probe personalizzati tramite Gestione risorse di Azure | Microsoft Azure"
   description="Questa pagina fornisce istruzioni per la configurazione di probe personalizzati nel gateway applicazione tramite Gestione risorse di Azure."
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn"/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="11/24/2015"
   ms.author="joaoma"/>

# Monitoraggio dell'integrità e probe personalizzati 


Il gateway applicazione di Azure monitora l'integrità di tutte le risorse nel pool back-end e rimuove automaticamente dal pool le risorse considerate non integre.

È possibile configurare il monitoraggio dell'integrità usando due tipi di probe: il probe di integrità predefinito e il probe personalizzato.

## Probe di integrità predefinito

Un gateway applicazione configura automaticamente un probe di integrità predefinito quando non si configura nessuna configurazione di probe personalizzato. Il comportamento di monitoraggio consiste nell'invio di una richiesta HTTP agli indirizzi IP configurati per il pool back-end e alla porta configurata nelle impostazioni HTTP back-end per il gateway applicazione.

Ad esempio, si configura il gateway applicazione per usare i server back-end A, B e C per ricevere il traffico di rete HTTP sulla porta 80. Il monitoraggio dell'integrità predefinito testa i tre server ogni 30 secondi per ottenere una risposta HTTP integra. Una risposta HTTP integra ha un [codice di stato](https://msdn.microsoft.com/library/aa287675.aspx) compreso tra 200 e 399.

Se il controllo probe predefinito non riesce per il server A, il gateway applicazione lo rimuove dal pool back-end e il flusso del traffico di rete a questo server viene arrestato. Il probe predefinito continua tuttavia a controllare il server A ogni 30 secondi. Quando il server A risponde correttamente a una richiesta di un probe di integrità predefinito, viene aggiunto di nuovo come integro al pool back-end e il flusso del traffico verso il server riprenderà.

Il probe predefinito usa solo gli indirizzi IP per controllare lo stato. Per verificare l'integrità testando la connettività a un URL è necessario usare il probe personalizzato.


## Probe personalizzato 

I probe personalizzati consentono un controllo più granulare sul monitoraggio dell'integrità. Quando si usano i probe personalizzati, è possibile configurare l'intervallo di probe, l'URL e il percorso da testare e il numero accettabile di risposte non riuscite prima di contrassegnare l'istanza del pool back-end come non integra.


Impostazioni del probe personalizzato:

- **Intervallo probe**: configura i controlli dell'intervallo di probe.
- **Timeout probe**: definisce il timeout del probe per il controllo di una richiesta HTTP.
- **Soglia di non integrità**: specifica il numero di richieste non riuscite necessario per contrassegnare l'istanza come non integra.  
- **Percorso e nome host**: se il sito Web o una Web farm non ha una risposta HTTP solo per l'indirizzo IP, è necessario configurare un percorso e un nome host del probe per ottenere una risposta HTTP integra valida. Ad esempio, è disponibile un sito Web http://contoso.com/ ma non produce una risposta HTTP valida. È necessario configurare un percorso e nome host per fornire una risposta HTTP integra valida e confermare l'integrità dell'istanza del server Web. In questo caso il probe personalizzato può essere configurato per "http://contoso.com/path/custompath.htm" in modo che i controlli del probe ottengano una risposta HTTP corretta. 



>[AZURE.NOTE] I probe personalizzati possono essere configurati esclusivamente con PowerShell.

<!---HONumber=AcomDC_0128_2016-->