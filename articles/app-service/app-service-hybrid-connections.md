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
ms.date: 10/20/2017
ms.author: ccompy
ms.openlocfilehash: 677642e4e97523ed71ff5857ae27263743dca535
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2017
---
# <a name="azure-app-service-hybrid-connections"></a>Connessioni ibride del Servizio app di Azure #

Connessioni ibride è un servizio disponibile in Azure e una funzionalità del Servizio app di Azure. Come servizio, fornisce modalità d'uso e funzionalità superiori rispetto a quelle usate nel Servizio app. Per altre informazioni su Connessioni ibride e sul suo uso al di fuori del Servizio app, vedere [Protocollo per le connessioni ibride di inoltro di Azure][HCService].

Nel Servizio app la funzionalità Connessioni ibride può essere usata per accedere alle risorse di applicazione presenti in altre reti. Fornisce l'accesso dalla propria app a un endpoint applicazione. Non abilita una funzionalità alternativa per accedere all'applicazione. Quando usata in Servizio app, ogni connessione ibrida è correlata a una singola combinazione di host e porta TCP. Ciò significa che l'endpoint della connessione ibrida può trovarsi in qualsiasi sistema operativo e in qualsiasi applicazione, a condizione che si acceda a una porta TCP in ascolto. La funzionalità Connessioni ibride non conosce né deve conoscere quale sia il protocollo dell'applicazione o a quale risorsa l'utente stia accedendo, in quanto si limita a fornire l'accesso alla rete.  


## <a name="how-it-works"></a>Funzionamento ##
La funzionalità Connessioni ibride è costituita da due chiamate in uscita a Inoltro del bus di servizio di Azure. Esistono una connessione da una libreria nell'host in cui l'app è in esecuzione nel Servizio app e una connessione da Gestione connessione ibrida a Inoltro del bus di servizio. Gestione connessione ibrida è un servizio di inoltro che viene distribuito nell'ambito della rete che ospita la risorsa a cui si prova ad accedere. 

Grazie alle due connessioni congiunte, l'app dispone di un tunnel TCP a una combinazione host:porta fissa sull'altro lato di Gestione connessione ibrida. La connessione usa TLS 1.2 per la sicurezza e le chiavi di firma di accesso condiviso per l'autenticazione e l'autorizzazione.    

![Diagramma del flusso di alto livello delle connessioni ibride][1]

Quando l'app esegue una richiesta DNS che corrisponde a un endpoint di Connessione ibrida configurato, il traffico TCP in uscita viene reindirizzato lungo la connessione ibrida.  

> [!NOTE]
> Ciò significa che è consigliabile usare sempre un nome DNS per la connessione ibrida. Alcuni software client non eseguono una ricerca DNS se l'endpoint usa un indirizzo IP al posto del nome DNS.
>
>

La funzionalità Connessioni ibride presenta due tipi di connessioni: le connessioni ibride che vengono offerte come servizio nell'ambito di Inoltro del bus di servizio e le connessioni ibride meno recenti di Servizi BizTalk di Azure. Queste ultime sono definite nel portale come Connessioni ibride (versione classica). Per altre informazioni su queste connessioni, vedere più avanti in questo articolo.

### <a name="app-service-hybrid-connection-benefits"></a>Vantaggi della funzionalità Connessioni ibride di Servizio app di Azure ###

La funzionalità Connessioni ibride offre numerosi vantaggi tra cui:

- Le app possono accedere in modo sicuro a servizi e sistemi locali.
- La funzionalità non richiede un endpoint accessibile tramite Internet.
- È facile e rapida da configurare. 
- Ogni connessione ibrida corrisponde a una singola combinazione host:porta, utile per la sicurezza.
- In genere non richiede varchi nei firewall, in quanto le connessioni sono tutte in uscita su porte Web standard.
- Dal momento che si tratta di una funzionalità a livello di rete, è indipendente dalla lingua usata dall'app e dalla tecnologia usata dall'endpoint.
- Può essere usata per fornire l'accesso a più reti da una singola app. 

### <a name="things-you-cannot-do-with-hybrid-connections"></a>Operazioni che non è possibile eseguire con la funzionalità Connessioni ibride ###

Esistono alcune operazioni che non è possibile eseguire con le connessioni ibride tra cui:

- Montaggio di un'unità.
- Uso del protocollo UDP.
- Accesso a servizi basati su TCP che usano porte dinamiche, ad esempio la modalità FTP passiva o la modalità passiva estesa.
- Supporto di LDAP, in quanto talvolta richiede il protocollo UDP.
- Supporto di Active Directory.

## <a name="add-and-create-hybrid-connections-in-your-app"></a>Aggiungere e creare connessioni ibride nell'app ##

È possibile creare connessioni ibride tramite l'app del Servizio app nel portale di Azure o dal Servizio di inoltro di Azure nel portale di Azure. È consigliabile creare connessioni ibride tramite l'app del Servizio app che si vuole usare con la connessione ibrida. Per creare una connessione ibrida, accedere al [portale di Azure][portal] e selezionare l'app. Selezionare **Rete** > **Configurare gli endpoint della connessione ibrida**. Da qui è possibile vedere le connessioni ibride configurate per l'app.  

![Schermata dell'elenco delle connessioni ibride][2]

Per aggiungere una nuova connessione ibrida, selezionare **Aggiungi connessione ibrida**.  Verrà visualizzato un elenco delle connessioni ibride che sono già state create. Per aggiungere una o più connessioni all'app, selezionare quelle desiderate e quindi selezionare **Aggiungi connessione ibrida selezionata**.  

![Schermata del portale delle connessioni ibride][3]

Se si intende creare una nuova connessione ibrida, selezionare **Crea nuova connessione ibrida**. Specificare le informazioni seguenti: 

- Nome dell'endpoint.
- Nome host dell'endpoint.
- Porta dell'endpoint.
- Spazio dei nomi del bus di servizio che si vuole usare.

![Schermata della finestra di dialogo Crea nuova connessione ibrida][4]

Ogni connessione ibrida è associata a uno spazio dei nomi del bus di servizio e ogni spazio dei nomi del bus di servizio si trova in un'area di Azure. È importante provare a usare uno spazio dei nomi del bus di servizio nella stessa area dell'app, in modo da evitare la latenza indotta dalla rete.

Se si vuole rimuovere la connessione ibrida dall'app, fare clic con il pulsante destro del mouse sulla connessione e selezionare **Disconnetti**.  

È possibile visualizzare i dettagli di una connessione ibrida aggiunta all'app semplicemente selezionandola. 

![Schermata dei dettagli delle connessioni ibride][5]

### <a name="create-a-hybrid-connection-in-the-azure-relay-portal"></a>Creare una connessione ibrida nel portale del Servizio di inoltro di Azure ###

Oltre all'esperienza del portale offerta all'interno dell'app, è possibile creare connessioni ibride all'interno del portale del Servizio di inoltro di Azure. Affinché una connessione ibrida possa essere usata dal Servizio app, deve:

* Richiedere l'autorizzazione client.
* Includere un elemento di metadati, denominato endpoint, che contenga una combinazione host:porta come valore.

## <a name="hybrid-connections-and-app-service-plans"></a>Connessioni ibride e piani di servizio app ##

La funzionalità Connessioni ibride è disponibile solo per gli SKU con piano tariffario Basic, Standard, Premium e isolato. Esistono limiti legati al piano tariffario.  

> [!NOTE] 
> È possibile creare solo nuove connessioni ibride basate sul servizio di inoltro di Azure. Non è possibile creare nuove connessioni ibride BizTalk.
>

| Piano tariffario | Numero di connessioni ibride a disposizione nel piano |
|----|----|
| Basic | 5 |
| Standard | 25 |
| Premium | 200 |
| Isolato | 200 |

Si noti che il piano di servizio app mostra quante connessioni ibride sono in uso e da quali app.  

![Schermata delle proprietà del piano di servizio app][6]

Selezionare la connessione ibrida per visualizzarne i dettagli. È possibile vedere tutte le informazioni disponibili nella visualizzazione dell'app, oltre al numero di app nello stesso piano che usano la connessione ibrida selezionata.

È previsto un limite al numero di endpoint della connessione ibrida che possono essere usati in un piano di servizio app. Tuttavia, ogni connessione ibrida usata può essere associata a un numero qualsiasi di app nel piano di servizio app specifico. Ad esempio, una singola connessione ibrida usata in cinque app distinte in un piano di servizio app viene conteggiata come una connessione ibrida.

È previsto un costo aggiuntivo per l'uso delle connessioni ibride. Per i dettagli, vedere [Prezzi di Bus di servizio][sbpricing].

## <a name="hybrid-connection-manager"></a>Gestione connessione ibrida ##

La funzionalità Connessioni ibride richiede un agente di inoltro nella rete che ospita l'endpoint della connessione ibrida. L'agente di inoltro viene chiamato Gestione connessione ibrida. Per scaricare Gestione connessione ibrida, dall'app nel [portale di Azure][portal] selezionare **Rete** > **Configurare gli endpoint della connessione ibrida**.  

Questo strumento viene eseguito in Windows Server 2012 e versioni successive. Quando è installato, Gestione connessione ibrida viene eseguito come servizio che si connette a Inoltro del bus di servizio in base agli endpoint configurati. Le connessioni da Gestione connessione ibrida sono in uscita in Azure sulla porta 443.    

Dopo avere installato Gestione connessione ibrida, è possibile eseguire HybridConnectionManagerUi.exe per usare l'interfaccia utente per lo strumento. Questo file è contenuto nella directory di installazione di Gestione connessione ibrida. In Windows 10 è sufficiente digitare *interfaccia utente Gestione connessione ibrida* nella casella di ricerca.  

![Schermata di Gestione connessione ibrida][7]

Quando si avvia l'interfaccia utente di Gestione connessione ibrida, la prima cosa che si vede è una tabella nella quale sono elencate tutte le connessioni ibride configurate con questa istanza di Gestione connessione ibrida. Per apportare eventuali modifiche, eseguire prima l'autenticazione con Azure. 

Per aggiungere una o più connessioni ibride a Gestione connessione ibrida:

1. Avviare l'interfaccia utente di Gestione connessione ibrida.
1. Selezionare **Configure another Hybrid Connection** (Configura un'altra connessione ibrida).
![Schermata Configure New Hybrid Connections (Configura nuove connessioni ibride)][8]

1. Accedere con l'account Azure.
1. Scegliere una sottoscrizione.
1. Selezionare le connessioni ibride che si vuole vengano inoltrate tramite Gestione connessione ibrida.
![Schermata delle connessioni ibride][9]

1. Selezionare **Salva**.

Ora è possibile visualizzare le connessioni ibride che sono state aggiunte. È anche possibile selezionare la connessione ibrida configurata per visualizzarne i dettagli.

![Schermata Hybrid Connection Details (Dettagli connessioni ibride)][10]

Per supportare le connessioni ibride con cui è configurato, Gestione connessione ibrida richiede quanto segue:

- Accesso TCP ad Azure sulle porte 80 e 443.
- Accesso TCP all'endpoint della connessione ibrida.
- Possibilità di eseguire ricerche DNS nell'host endpoint e nello spazio dei nomi del bus di servizio.

Gestione connessione ibrida supporta sia le nuove connessioni ibride che le connessioni ibride BizTalk.

> [!NOTE]
> Il servizio di inoltro di Azure si affida a WebSocket per la connettività. Questa funzionalità è disponibile solo in Windows Server 2012 e versioni successive. Per questo motivo, Gestione connessione ibrida non è supportato nelle versioni precedenti a Windows Server 2012.
>

### <a name="redundancy"></a>Ridondanza ###

Ogni istanza di Gestione connessione ibrida può supportare più connessioni ibride. Ogni connessione ibrida specificata può anche essere supportata da più istanze di Gestione connessione ibrida. Il comportamento predefinito consiste nell'indirizzare il traffico tra le istanze configurate di Gestione connessione ibrida per un endpoint specificato. Se si intende usufruire della disponibilità elevata nelle connessioni ibride dalla rete, eseguire più istanze di Gestione connessione ibrida in computer separati. 

### <a name="manually-add-a-hybrid-connection"></a>Aggiungere manualmente una connessione ibrida ###

Per consentire a un'entità esterna alla propria sottoscrizione di ospitare un'istanza di Gestione connessione ibrida per una connessione ibrida specificata, condividere con tale entità la stringa di connessione gateway per la connessione ibrida. Questa stringa è visibile nelle proprietà di una connessione ibrida nel [portale di Azure][portal]. Per usare tale stringa, selezionare **Immetti manualmente** in Gestione connessione ibrida e incollare la stringa di connessione gateway.


## <a name="troubleshooting"></a>Risoluzione dei problemi ##

Lo stato "Connesso" indica che almeno un'istanza di Gestione connessione ibrida è configurata con quella specifica connessione ed è in grado di raggiungere Azure. Se lo stato della connessione ibrida non è **Connesso**, la connessione ibrida non è configurata in alcuna istanza di Gestione connessione ibrida con accesso ad Azure.

Il motivo principale per cui i client non riescono a connettersi al relativo endpoint è perché l'endpoint è stato specificato usando un indirizzo IP anziché un nome DNS. Se l'app non riesce a raggiungere l'endpoint desiderato ed è stato specificato un indirizzo IP, usare un nome DNS valido nell'host in cui Gestione connessione ibrida è in esecuzione. È necessario controllare anche che il nome DNS venga risolto correttamente nell'host in cui Gestione connessione ibrida è in esecuzione e che vi sia connettività tra l'host in cui Gestione connessione ibrida è in esecuzione e l'endpoint della connessione ibrida.  

Nel Servizio app è possibile richiamare lo strumento tcpping dalla console Strumenti avanzati (Kudu). Questo strumento indica se si dispone dell'accesso a un endpoint TCP, ma non se si dispone dell'accesso all'endpoint di una connessione ibrida. Quando lo strumento viene usato nella console per rilevare l'endpoint di una connessione ibrida, viene confermato solo che usa una combinazione host:porta.  

## <a name="biztalk-hybrid-connections"></a>Connessioni ibride BizTalk ##

Non è più possibile usare la funzionalità Connessioni ibride BizTalk precedente per la creazione di nuove connessioni ibride BizTalk. È possibile continuare a usare le connessioni ibride BizTalk esistenti con le app in uso, ma è consigliabile eseguire la migrazione alle nuove connessioni ibride che usano il Servizio di inoltro di Azure. Rispetto alla versione di BizTalk, il nuovo servizio offre vari vantaggi tra cui:

- Non è richiesto alcun account BizTalk aggiuntivo.
- La versione di TLS è 1.2 anziché 1.0.
- La comunicazione avviene sulle porte 80 e 443 e usa un nome DNS per raggiungere Azure, anziché gli indirizzi IP e una gamma di porte aggiuntive.  

Per aggiungere una connessione ibrida BizTalk esistente alla propria app, passare all'app nel [portale di Azure][portal] e selezionare **Rete** > **Configurare gli endpoint della connessione ibrida**. Nella tabella Connessioni ibride (versione classica) selezionare **Aggiungi connessione ibrida classica**. Verrà visualizzato un elenco delle connessioni ibride BizTalk in uso.  


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
