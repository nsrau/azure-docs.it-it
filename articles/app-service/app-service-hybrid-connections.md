---
title: Connessioni ibride del Servizio app di Azure | Microsoft Docs
description: Informazioni su come creare e usare Connessioni ibride per accedere alle risorse in reti diverse
services: app-service
documentationcenter: 
author: ccompy
manager: stefsch
editor: 
ms.assetid: 66774bde-13f5-45d0-9a70-4e9536a4f619
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/22/2017
ms.author: ccompy
ms.openlocfilehash: fef9e7b280387934cb093f51b4c8aa134a3b87e7
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="azure-app-service-hybrid-connections"></a>Connessioni ibride del Servizio app di Azure #

## <a name="overview"></a>Panoramica ##

Connessioni ibride è un servizio disponibile in Azure, nonché una funzionalità del Servizio app di Azure.  Come servizio fornisce modalità di utilizzo e funzionalità superiori rispetto a quelle offerte come funzionalità del Servizio app di Azure.  Per altre informazioni su Connessioni ibride e sul suo utilizzo al di fuori del Servizio app di Azure, vedere [Protocollo per le connessioni ibride di inoltro di Azure][HCService].

Nel Servizio app di Azure, la funzionalità Connessioni ibride può essere usata per accedere alle risorse di applicazione presenti in altre reti. Fornisce l'accesso DALLA propria app A un endpoint applicazione.  Non abilita una funzionalità alternativa per accedere all'applicazione.  Quando usata nel Servizio app, ogni connessione ibrida è correlata a una singola combinazione di host e porta TCP.  Ciò significa che l'endpoint della connessione ibrida può trovarsi in qualsiasi sistema operativo e qualsiasi applicazione purché vi sia una porta TCP in ascolto. Connessioni ibride non conosce e né deve conoscere quale sia il protocollo dell'applicazione o a quale risorsa l'utente stia accedendo,  in quanto si limita a fornire l'accesso alla rete.  


## <a name="how-it-works"></a>Funzionamento ##
La funzionalità Connessioni ibride è costituita da due chiamate in uscita a Inoltro del bus di servizio.  Esistono una connessione da una libreria nell'host in cui l'app è in esecuzione nel Servizio app e una connessione da Gestione connessione ibrida a Inoltro del bus di servizio.  Gestione connessione ibrida è un servizio di inoltro che viene distribuito nell'ambito dell'hosting di rete. 

Grazie alle due connessioni congiunte, l'app dispone di un tunnel TCP a una combinazione host:porta fissa sull'altro lato di Gestione connessione ibrida.  La connessione usa TLS 1.2 per la sicurezza e le chiavi SAS per l'autenticazione/autorizzazione.    

![][1]

Quando l'app effettua una richiesta DNS che corrisponde a un endpoint di Connessione ibrida configurato, il traffico TCP in uscita viene reindirizzato lungo la connessione ibrida.  

> [!NOTE]
> Ciò significa che è consigliabile usare sempre un nome DNS per la connessione ibrida.  Alcuni software client non eseguono una ricerca DNS se l'endpoint usa un indirizzo IP al posto del nome DNS.
>
>

Esistono due tipi di connessioni ibride: le nuove connessioni ibride che vengono offerte come servizio durante l'inoltro di Azure e le connessioni ibride BizTalk meno recenti.  Queste ultime sono definite nel portale come Connessioni ibride BizTalk (versione classica).  Per altre informazioni su queste connessioni, vedere più avanti in questo documento.

### <a name="app-service-hybrid-connection-benefits"></a>Vantaggi della funzionalità Connessioni ibride del Servizio app di Azure ###

La funzionalità Connessioni ibride offre numerosi vantaggi tra cui:

- Le app possono accedere in modo sicuro a servizi e sistemi locali
- Non richiede un endpoint accessibile tramite Internet
- È facile e rapida da configurare  
- Ogni connessione ibrida corrisponde a una singola combinazione host:porta, il che rappresenta un aspetto eccellente della sicurezza
- In genere non richiede varchi nei firewall in quanto le connessioni sono tutte in uscita su porte Web standard
- Dal momento che si tratta di una funzionalità a livello di rete, è agnostica alla lingua usata dall'app e alla tecnologia usata dall'endpoint
- Può essere usata per fornire l'accesso a più reti da una singola app. 

### <a name="things-you-cannot-do-with-hybrid-connections"></a>Operazioni che non è possibile eseguire con la funzionalità Connessioni ibride ###

Esistono alcune operazioni che non è possibile eseguire con le connessioni ibride tra cui:

- Montaggio di un'unità
- Utilizzo del protocollo UDP
- Accesso a servizi basati su TCP che usano porte dinamiche, ad esempio la modalità FTP passiva o la modalità passiva estesa
- Supporto LDAP, in quanto talvolta richiede il protocollo UDP
- Supporto di Active Directory.

## <a name="adding-and-creating-a-hybrid-connection-in-your-app"></a>Aggiunta e creazione di una connessione ibrida nell'app ##

È possibile creare connessioni ibride tramite il portale delle app o dal portale del servizio Connessioni ibride.  È consigliabile usare il portale delle app per creare le connessioni ibride per la propria app.  Per creare una connessione ibrida, andare al [portale di Azure][portal] e accedere all'interfaccia utente della propria app.  Selezionare **Rete > Configurare gli endpoint della connessione ibrida**.  Da qui è possibile vedere le connessioni ibride configurate con l'app.  

![][2]

Per aggiungere una nuova connessione ibrida, fare clic su Aggiungi connessione ibrida.  Verrà visualizzato l'elenco delle connessioni ibride che sono già state create.  Per aggiungere una o più connessioni all'app, selezionare quelle desiderate e fare clic su **Aggiungi connessione ibrida selezionata**.  

![][3]

Se si intende creare una nuova connessione ibrida, fare clic su **Crea nuova connessione ibrida**.  Qui occorre specificare gli elementi seguenti: 

- Nome dell'endpoint
- Nome host dell'endpoint
- Porta dell'endpoint
- Spazio dei nomi del bus di servizio che si intende usare.

![][4]

Ogni connessione ibrida è associata a uno spazio dei nomi del bus di servizio e ogni spazio dei nomi del bus di servizi si trova in un'area di Azure.  È importante provare a usare uno spazio dei nomi del bus di servizio nella stessa area dell'app in modo da evitare la latenza indotta dalla rete.

Se si vuole rimuovere la connessione ibrida dall'app, fare clic sulla connessione e selezionare **Disconnetti**.  

È possibile visualizzare i dettagli di una connessione ibrida aggiunta all'app Web facendo semplicemente clic sulla connessione.  

![][5]

## <a name="hybrid-connections-and-app-service-plans"></a>Connessioni ibride e piani di servizio app ##

È ora possibile creare esclusivamente le nuove connessioni ibride.  Sono disponibili solo per le SKU di prezzo Basic, Standard, Premium e per ambiente isolato.  Esistono limiti legati al piano tariffario.  

| Piano tariffario | Numero di connessioni ibride a disposizione nel piano |
|----|----|
| Basic | 5 |
| Standard | 25 |
| Premium | 200 |
| Isolato | 200 |

Le restrizioni previste dal piano di servizio app sono indicate anche nell'interfaccia utente del piano di servizio app che mostra quante connessioni ibride sono in uso e da quali app.  

![][6]

Esattamente come nella visualizzazione dell'app, è possibile vedere i dettagli sulla connessione ibrida facendo clic sulla connessione.  Nella sezione delle proprietà mostrata di seguito si possono vedere tutte le informazioni disponibili nella visualizzazione dell'app, in più si possono vedere quante altre app nello stesso piano di servizio app usano la connessione ibrida selezionata.

![][7]

Sebbene esista un limite al numero di endpoint della connessione ibrida che possono essere usati in un piano di servizio app, ogni connessione ibrida usata può essere associata a un numero qualsiasi di app nel piano di servizio app specifico.  Per fare un esempio, se si dispone di 1 connessione ibrida usata in 5 app distinte nel piano di servizio app, si tratta comunque di 1 connessione ibrida.

Oltre alla possibilità di essere usate solo nelle SKU Basic, Standard, Premium o per ambiente isolato, esiste un costo aggiuntivo per le connessioni ibride.  Per altre informazioni sui prezzi delle connessioni ibride, vedere [Prezzi di Bus di servizio][sbpricing].

## <a name="hybrid-connection-manager"></a>Gestione connessione ibrida ##

Per il corretto funzionamento delle connessioni ibride, è necessario disporre di un agente di inoltro nella rete che ospita l'endpoint della connessione ibrida.  L'agente di inoltro viene chiamato Gestione connessione ibrida.  È possibile scaricare questo strumento scegliendo **Rete > Configurare gli endpoint della connessione ibrida** nell'interfaccia utente dell'app nel [portale di Azure][portal].  

Questo strumento viene eseguito in Windows Server 2008 R2 e versioni successive di Windows.  Dopo che è stato installato, lo strumento Gestione connessione ibrida viene eseguito come servizio.  Questo servizio si connette al servizio di inoltro del bus di servizio di Azure in base agli endpoint configurati.  Le connessioni da Gestione connessione ibrida sono in uscita sulle porte 80 e 443.    

Gestione connessione ibrida dispone di un'interfaccia utente che ne consente la configurazione.  Dopo avere installato Gestione connessione ibrida, è possibile visualizzarne l'interfaccia utente eseguendo il file HybridConnectionManagerUi.exe che risiede nella directory di installazione dell'agente di inoltro.  L'interfaccia può essere richiamata anche direttamente da Windows 10 digitando *interfaccia utente Gestione connessione ibrida* nella casella di ricerca.  

Quando l'interfaccia utente di Gestione connessione ibrida viene avviata, la prima cosa che si vede è una tabella nella quale sono elencate tutte le connessioni ibride che sono configurate con questa istanza di Gestione connessione ibrida.  Per apportare eventuali modifiche, è necessario eseguire l'autenticazione con Azure. 

Per aggiungere una o più connessioni ibride a Gestione connessione ibrida:

1. Avviare l'interfaccia utente di Gestione connessione ibrida.
1. Fare clic su Configure another hybrid connection (Configura un'altra connessione ibrida)![][8].

1. Accedere con l'account Azure.
1. Scegliere una sottoscrizione.
1. Fare clic sulle connessioni ibride che si desidera vengano inoltrare tramite Gestione connessione ibrida. ![][9]

1. Fare clic su Salva.

Verranno visualizzate a questo punto le connessioni ibride che sono state aggiunte.  È inoltre possibile fare clic sulla connessione ibrida configurata e visualizzarne i dettagli.

![][10]

Affinché Gestione connessione ibrida supporti le connessioni ibride con cui è configurato, deve disporre di quanto segue:

- Accesso TCP ad Azure sulle porte 80 e 443
- Accesso TCP all'endpoint della connessione ibrida
- Possibilità di eseguire ricerche DNS nell'host endpoint e nello spazio dei nomi del bus di servizio di Azure.

Gestione connessione ibrida supporta le nuove connessioni ibride nonché le connessioni ibride BizTalk meno recenti.

### <a name="redundancy"></a>Ridondanza ###

Ogni istanza di Gestione connessione ibrida può supportare più connessioni ibride.  Ogni connessione ibrida specificata può inoltre essere supportata da più istanze di Gestione connessione ibrida.  Il comportamento predefinito consiste nell'eseguire il round robin sul traffico tra le istanze configurate di Gestione connessione ibrida per un endpoint specificato.  Se si intende usufruire della disponibilità elevata nelle connessioni ibride dalla rete, è sufficiente creare istanze di Gestione connessione ibrida in computer separati. 

### <a name="manually-adding-a-hybrid-connection"></a>Aggiunta manuale di una connessione ibrida ###

Se si vuole che un'entità esterna alla propria sottoscrizione ospiti un'istanza di Gestione connessione ibrida per una connessione ibrida specificata, è possibile condividere con tale entità la stringa di connessione gateway per la connessione ibrida.  Questa stringa è visibile nelle proprietà di una connessione ibrida nel [portale di Azure][portal]. Per usare tale stringa, fare clic sul pulsante **Configure manually** (Configura manualmente) in Gestione connessione ibrida e incollare la stringa di connessione gateway.


## <a name="troubleshooting"></a>Risoluzione dei problemi ##

Quando la connessione ibrida è configurata con un'applicazione in esecuzione ed esiste almeno un'istanza di Gestione connessione ibrida che dispone di tale connessione ibrida configurata, lo stato nel portale è **Connesso**.  Se lo stato non è **Connesso**, significa che l'app non funziona o che Gestione connessione ibrida non riesce a connettersi ad Azure tramite la porta 80 o 443.  

Il motivo principale per cui i client non riescono a connettersi al relativo endpoint è perché l'endpoint è stato specificato usando un indirizzo IP anziché un nome DNS.  Se l'app non riesce a raggiungere l'endpoint desiderato ed è stato specificato un indirizzo IP, usare un nome DNS valido nell'host in cui Gestione connessione ibrida è in esecuzione.  È necessario controllare anche che il nome DNS venga risolto correttamente nell'host in cui Gestione connessione ibrida è in esecuzione e che vi sia connettività tra l'host in cui Gestione connessione ibrida è in esecuzione e l'endpoint della connessione ibrida.  

Nel Servizio app è disponibile uno strumento denominato tcpping, richiamabile dalla console.  Questo strumento indica se si dispone dell'accesso a un endpoint TCP, ma non se si dispone dell'accesso all'endpoint di una connessione ibrida.  Se usato nella console per rilevare l'endpoint di una connessione ibrida, un ping con esito positivo indicherà solo che si dispone di una connessione ibrida configurata per l'app che usa la combinazione host:porta specificata.  

## <a name="biztalk-hybrid-connections"></a>Connessioni ibride BizTalk ##

Non è più possibile usare la funzionalità Connessioni ibride BizTalk precedente per la creazione di connessioni ibride BizTalk.  È possibile continuare a usare le connessioni ibride BizTalk preesistenti, ma è consigliabile migrare al nuovo servizio.  Rispetto alla versione di BizTalk, il nuovo servizio offre vari vantaggi tra cui:

- Non è richiesto alcun account BizTalk aggiuntivo
- La versione di TLS è 1.2 anziché 1.0 come in Connessioni ibride BizTalk
- La comunicazione avviene sulle porte 80 e 443 usando un nome DNS per raggiungere Azure anziché gli indirizzi IP e una gamma di altre porte aggiuntive.  

Per aggiungere una connessione ibrida BizTalk alla propria app, passare all'app nel [portale di Azure][portal] e fare clic su **Rete > Configurare gli endpoint della connessione ibrida**.  Nella tabella Connessioni ibride (versione classica) fare clic su **Aggiungi connessione ibrida classica**.  Verrà visualizzato un elenco delle connessioni ibride BizTalk in uso.  


<!--Image references-->
[1]: ./media/app-service-hybrid-connections/hybridconn-connectiondiagram.png
[2]: ./media/app-service-hybrid-connections/hybridconn-portal.png
[3]: ./media/app-service-hybrid-connections/hybridconn-addhc.png
[4]: ./media/app-service-hybrid-connections/hybridconn-createhc.png
[5]: ./media/app-service-hybrid-connections/hybridconn-properties.png
[6]: ./media/app-service-hybrid-connections/hybridconn-aspproperties.png
[7]: ./media/app-service-hybrid-connections/hybridconn-hcm.png
[8]: ./media/app-service-hybrid-connections/hybridconn-hcmadd.png
[9]: ./media/app-service-hybrid-connections/hybridconn-hcmadded.png
[10]: ./media/app-service-hybrid-connections/hybridconn-hcmdetails.png

<!--Links-->
[HCService]: http://docs.microsoft.com/azure/service-bus-relay/relay-hybrid-connections-protocol/
[portal]: http://portal.azure.com/
[oldhc]: http://docs.microsoft.com/azure/biztalk-services/integration-hybrid-connection-overview/
[sbpricing]: http://azure.microsoft.com/pricing/details/service-bus/

