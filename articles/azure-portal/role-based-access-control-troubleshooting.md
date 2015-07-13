<properties 
	pageTitle="Risoluzione dei problemi del controllo degli accessi in base al ruolo" 
	description="Uso di diversi tipi di risorse per il controllo dell'accesso basato sui ruoli." 
	services="azure-portal"
	documentationCenter="na" 
	authors="stepsic-microsoft-com" 
	writer="" 
	manager="terrylan" 
	editor=""/>

<tags 
	ms.service="azure-portal" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/25/2015" 
	ms.author="stepsic"/>

# Risoluzione dei problemi del controllo di accesso basato sui ruoli

## Introduzione

[Controllo degli accessi in base al ruolo](../role-based-access-control-configure.md) è uno strumento efficace che consente di delegare con estrema precisione l'accesso alle risorse in Azure. Ciò significa che si può accordare a una determinata persona l'accesso alle risorse specifiche di cui ha bisogno. Tuttavia, a volte il modello di risorse di Azure può essere complicato e può risultare difficile comprendere esattamente quali autorizzazioni si stanno concedendo.

Questo documento illustra gli effetti prodotti dall'uso di alcuni dei nuovi ruoli nel portale di Azure. Esistono tre ruoli predefiniti inclusi in questa versione: * Proprietario * Collaboratore * Lettore

Proprietari e collaboratori hanno accesso completo all'esperienza di gestione, con la differenza che il contributore non può concedere l'accesso ad altri utenti o gruppi. Il ruolo di lettore è maggiormente articolato e verrà quindi esaminato in maniera più approfondita. [Vedere questo articolo](../role-based-access-control-configure.md) per maggiori dettagli sulle modalità di concessione dell'accesso.

## Carichi di lavoro del servizio app

### Con accesso in sola lettura 

Se si concede a un utente l'accesso in sola lettura a un'unica app Web, o si dispone personalmente di tale tipo di accesso, è possibile che alcune funzionalità siano disattivate. Per le funzionalità di gestione seguenti è necessario disporre dell'accesso **in scrittura** a un'app Web, come Collaboratore o Proprietario: tali funzionalità non saranno disponibili in uno scenario di sola lettura.
 
1. Comandi, ad esempio avvio, interruzione e così via.
2. Modifica di impostazioni quali la configurazione generale, le impostazioni di scalabilità, di backup e di monitoraggio.
3. Accesso a credenziali di pubblicazione e altri segreti, quali le impostazioni delle app e le stringhe di connessione.
4. Streaming dei log
5. Configurazione dei log di diagnostica
6. Console (prompt dei comandi)
7. Distribuzioni attive e recenti, per la distribuzione continua del Git locale
8. Spesa prevista
9. Test Web
10. Rete virtuale, visibile per un lettore solo se in precedenza era già stata configurata una rete virtuale da un utente con accesso in scrittura.
 
Se non è possibile accedere a nessuno di questi titoli, è necessario disporre dell'accesso come Collaboratore per l'app Web.

### Gestione delle risorse correlate
 
La complessità delle app Web è accentuata dalle interazioni tra alcune risorse diverse. Di seguito è illustrato un tipico gruppo di risorse con alcuni siti Web:

![Gruppo di risorse per app Web](./media/role-based-access-control-troubleshooting/Website-resource-model.png)

Di conseguenza, se si concede l'accesso al solo sito Web, molte funzionalità del blade del sito Web saranno completamente disabilitate.
 
1. Gli elementi seguenti richiedono l'accesso al **piano del servizio app** corrispondente al sito Web in questione:  
    * Visualizzazione del livello di prezzo dell'app Web (ad esempio Free o Standard).
    * Configurazione di scala (ossia numero di istanze, dimensione della macchina virtuale, impostazioni di scalabilità automatica).
    * Quote, ad esempio memoria, larghezza di banda, CPU.
2. I seguenti elementi richiedono l'accesso all'intero **Gruppo di risorse** che contiene il sito Web:  
    * Certificati e associazioni SSL: i certificati SSL possono infatti essere condivisi tra siti appartenenti allo stesso gruppo di risorse e area geografica.
    * Regole di avviso
    * Impostazioni di scalabilità automatica
    * Componenti di Application Insights
    * Test Web

## Carichi di lavoro delle macchine virtuali

Analogamente a quanto accade con le app Web, alcune funzionalità del blade della macchina virtuale richiedono l'accesso in scrittura alla macchina virtuale o ad altre risorse del gruppo di risorse.

Le risorse correlate delle macchine virtuali sono: * Nomi di dominio * Reti virtuali * Account di archiviazione * Regole di avviso

1. I seguenti elementi richiedono l'accesso **in scrittura** alla Macchina virtuale:  
    * Endpoint
    * Indirizzi IP
    * Dischi
    * Estensioni
2. I seguenti elementi richiedono l'accesso in scrittura sia alla Macchina virtuale che al **Gruppo di risorse**, così come al nome di dominio, a cui appartiene:  
    * Set di disponibilità
    * Set con carico bilanciato
    * Regole di avviso
    
Se non è possibile accedere a nessuno di questi titoli, è necessario richiedere all'amministratore l'accesso come Collaboratore per il gruppo di risorse.

<!---HONumber=62-->