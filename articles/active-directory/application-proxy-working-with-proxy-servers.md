---
title: Usare server proxy locali esistenti e Azure AD | Documentazione Microsoft
description: Tratta l'uso di server proxy locali esistenti.
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: 3eb68cba15e89c455d7d33be1ec0bf596df5f3b7
ms.openlocfilehash: 47b17c0d81e8597d3125949b26906cc356a9af11
ms.contentlocale: it-it
ms.lasthandoff: 09/01/2017

---

# <a name="work-with-existing-on-premises-proxy-servers"></a>Usare server proxy locali esistenti

Questo articolo illustra come configurare i connettori del proxy applicazione di Azure Active Directory (Azure AD) per l'uso di server proxy in uscita. È rivolto ai clienti con ambienti di rete che dispongono di proxy esistenti.

Iniziamo esaminando questi scenari di distribuzione principali:
* Configurare i connettori per ignorare i proxy in uscita locali.
* Configurare i connettori per l'uso di un proxy in uscita per accedere al proxy applicazione di Azure AD.

Per ulteriori informazioni sui connettori, vedere [Informazioni sui connettori proxy di applicazione di Azure AD](application-proxy-understand-connectors.md).

## <a name="bypass-outbound-proxies"></a>Ignorare i proxy in uscita

I connettori sono componenti del sistema operativo sottostanti che creano le richieste in uscita. Questi componenti tentano automaticamente di individuare un server proxy nella rete usando WPAD (Web Proxy Auto-Discovery).

I componenti del sistema operativo provano a individuare un server proxy eseguendo una ricerca DNS per wpad.domainsuffix. Se la ricerca restituisce il DNS, viene quindi inviata una richiesta HTTP all'indirizzo IP per wpad.dat. Questa richiesta diventa lo script di configurazione proxy nell'ambiente. Il connettore usa questo script per selezionare un server proxy in uscita. Il traffico del connettore potrebbe tuttavia non riuscire ancora a passare perché sono necessarie altre impostazioni di configurazione nel proxy.

È possibile configurare il connettore in modo che ignori il proxy locale, per garantire che usi la connettività diretta ai servizi di Azure. Se consentita dai criteri di rete, questa è un'operazione consigliata, perché implica una configurazione in meno da gestire.

Per disabilitare l'uso del proxy in uscita per il connettore, modificare il file C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config e aggiungere la sezione *system.net* indicata nell'esempio di codice seguente:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.net>
    <defaultProxy enabled="false"></defaultProxy>
  </system.net>
  <runtime>
    <gcServer enabled="true"/>
  </runtime>
  <appSettings>
    <add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```
Per assicurarsi che anche il servizio Connector Updater ignori il proxy, apportare una modifica simile al file ApplicationProxyConnectorUpdaterService.exe.config. Questo file si trova in C:\Programmi\Microsoft AAD App Proxy Connector Updater.

Assicurarsi di creare copie dei file originali nel caso sia necessario ripristinare i file .config predefiniti.

## <a name="use-the-outbound-proxy-server"></a>Usare il server proxy in uscita

Alcuni ambienti dei clienti richiedono che tutto il traffico in uscita passi attraverso un proxy in uscita, senza eccezioni. Di conseguenza ignorare il proxy non è possibile.

Si può configurare il traffico del connettore per il passaggio attraverso il proxy in uscita, come illustrato nel diagramma seguente:

 ![Configurazione del traffico del connettore per l'uso di un proxy in uscita per accedere al proxy applicazione di Azure AD](./media/application-proxy-working-with-proxy-servers/configure-proxy-settings.png)

Dato che il traffico è solo in uscita, non è necessario configurare l'accesso in ingresso attraverso firewall.

>[!NOTE]
>Il proxy di applicazione non supporta l'autenticazione in altri proxy. Gli account del servizio di rete del connettore/strumento di aggiornamento dovrebbero essere in grado di connettersi al proxy senza ricevere richieste di autenticazione.

### <a name="step-1-configure-the-connector-and-related-services-to-go-through-the-outbound-proxy"></a>Passaggio 1: Configurare il connettore e i servizi correlati per il passaggio attraverso il proxy in uscita

Se WPAD è abilitato nell'ambiente e configurato in modo corretto, il connettore individua automaticamente il server proxy in uscita e tenta di usarlo. Tuttavia, è possibile configurare in modo esplicito il connettore per il passaggio attraverso un proxy in uscita.

A tale scopo modificare il file C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config e aggiungere la sezione *system.net* indicata nell'esempio di codice seguente. Modificare *proxyserver:8080* in modo che corrisponda al nome o all'indirizzo IP del server proxy locale e alla porta su cui è in ascolto.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.net>  
    <defaultProxy>   
      <proxy proxyaddress="http://proxyserver:8080" bypassonlocal="True" usesystemdefault="True"/>   
    </defaultProxy>  
  </system.net>
  <runtime>
    <gcServer enabled="true"/>
  </runtime>
  <appSettings>
    <add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```

Configurare quindi il servizio Connector Updater in modo che usi il proxy, apportando una modifica simile al file C:\Programmi\Microsoft AAD App Proxy Connector Updater\ApplicationProxyConnectorUpdaterService.exe.config.

### <a name="step-2-configure-the-proxy-to-allow-traffic-from-the-connector-and-related-services-to-flow-through"></a>Passaggio 2: configurare il proxy per consentire il passaggio del traffico proveniente dal connettore e dai servizi correlati

Esistono quattro aspetti da considerare per il proxy in uscita:
* Regole in uscita del proxy
* Autenticazione proxy
* Porte proxy
* Ispezione SSL

#### <a name="proxy-outbound-rules"></a>Regole in uscita del proxy
Consentire l'accesso agli endpoint seguenti per l'accesso al servizio connettore:

* *.msappproxy.net
* *.servicebus.windows.net

Per la registrazione iniziale consentire l'accesso agli endpoint seguenti:

* login.windows.net
* login.microsoftonline.com

Se non è possibile consentire la connettività in base al nome di dominio completo ed è necessario specificare invece intervalli IP, usare queste opzioni:

* Consentire al connettore l'accesso in uscita a tutte le destinazioni.
* Consentire al connettore l'accesso in uscita a tutti gli [intervalli di indirizzi IP del data center di Azure](https://www.microsoft.com/en-gb/download/details.aspx?id=41653). Il problema con l'elenco di intervalli di indirizzi IP del data center di Azure è che viene aggiornato ogni settimana. È necessario implementare un processo per assicurare che le regole di accesso vengano aggiornate di conseguenza. L'uso di un solo subset di indirizzi IP può provocare errori di configurazione.

#### <a name="proxy-authentication"></a>Autenticazione proxy

L'autenticazione proxy non è attualmente supportata. Il nostro consiglio è di consentire l'accesso anonimo del connettore alle destinazioni su Internet.

#### <a name="proxy-ports"></a>Porte proxy

Il connettore esegue le connessioni SSL in uscita usando il metodo CONNECT. Questo metodo crea essenzialmente un tunnel attraverso il proxy in uscita. Configurare il server proxy per consentire il tunneling alle porte 443 e 80.

>[!NOTE]
>Quando il bus di servizio viene eseguito su HTTPS, usa la porta 443. Per impostazione predefinita, il bus di servizio prova tuttavia a stabilire connessioni TCP dirette ed esegue il fallback su HTTPS solo se si verifica un errore di connettività diretta.

#### <a name="ssl-inspection"></a>Ispezione SSL
Non usare l'ispezione SSL per il traffico del connettore perché causa problemi per tale traffico. Il connettore usa un certificato client per l'autenticazione al servizio proxy di applicazione e il certificato può essere perso durante l'analisi SSL. 

## <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>Risolvere i problemi relativi al connettore proxy e alla connettività del servizio
A questo punto si dovrebbe vedere tutto il traffico che passa attraverso il proxy. Se si verificano problemi, potrebbero essere utili le seguenti informazioni per la risoluzione dei problemi.

Il modo migliore per identificare e risolvere i problemi di connettività del connettore è eseguire un'acquisizione di rete durante l'avvio del servizio del connettore. Ecco alcuni semplici suggerimenti per acquisire e filtrare le tracce di rete.

È possibile usare lo strumento di monitoraggio preferito. Ai fini di questo articolo, è stato usato Microsoft Message Analyzer. È possibile [scaricarlo da Microsoft](https://www.microsoft.com/download/details.aspx?id=44226).

Gli esempi seguenti sono specifici di Message Analyzer, ma i principi possono essere applicati a qualsiasi strumento di analisi.

### <a name="take-a-capture-of-connector-traffic"></a>Eseguire un'acquisizione del traffico del connettore

Per iniziare la risoluzione dei problemi, procedere come segue:

1. Da services.msc arrestare il servizio connettore del proxy applicazione di Azure AD.

   ![Servizio connettore proxy applicazione di Azure AD in services.msc](./media/application-proxy-working-with-proxy-servers/services-local.png)

2. Eseguire Message Analyzer come amministratore.
3. Selezionare l'opzione per l'avvio della **traccia locale**.

   ![Avviare l'acquisizione di rete](./media/application-proxy-working-with-proxy-servers/start-local-trace.png)

3. Avviare il servizio connettore del proxy applicazione di Azure AD.
4. Arrestare l'acquisizione di rete.

   ![Arrestare l'acquisizione di rete](./media/application-proxy-working-with-proxy-servers/stop-trace.png)

### <a name="check-if-the-connector-traffic-bypasses-outbound-proxies"></a>Controllare se il traffico del connettore ignora i proxy in uscita

Se il connettore del proxy di applicazione è stato configurato per ignorare i server proxy e connettersi direttamente al servizio proxy di applicazione, è necessario individuare nell'acquisizione di rete i tentativi di connessione TCP non riusciti. 

Usare il filtro di Message Analyzer per identificare questi tentativi. Immettere `property.TCPSynRetransmit` nella casella del filtro e selezionare **Applica**. 

Un pacchetto SYN è il primo pacchetto inviato per stabilire una connessione TCP. Se questo pacchetto non restituisce una risposta, il SYN viene ritentato. È possibile usare il filtro precedente per visualizzare qualsiasi SYN ritrasmesso. È quindi possibile vedere se questi SYN corrispondono a traffico correlato al connettore.

Se si prevede che il connettore esegua connessioni dirette ai servizi di Azure, le risposte SynRetransmit sulla porta 443 indicano la presenza di un problema di rete o di firewall.

### <a name="check-if-the-connector-traffic-uses-outbound-proxies"></a>Controllare se il traffico del connettore usa proxy in uscita

Se il traffico del connettore del proxy di applicazione è stato configurato per passare attraverso i server proxy, è necessario individuare le connessioni HTTPS non riuscite nel proxy. 

Per filtrare l'acquisizione di rete in modo da individuare questi tentativi di connessione, immettere `(https.Request or https.Response) and tcp.port==8080` nel filtro di Message Analyzer, sostituendo 8080 con la porta del servizio proxy. Selezionare **Applica** per visualizzare i risultati del filtro. 

Il filtro precedente mostra solo le richieste e le risposte HTTPS da e verso la porta del proxy. Cercare le richieste CONNECT che indicano la comunicazione con il server proxy. Al completamento dell'operazione si ottiene una risposta HTTP affermativa (200).

Se vengono visualizzati altri codici di risposta, ad esempio 407 o 502, significa che il proxy richiede l'autenticazione o non consente il traffico per altri motivi. A questo punto ci si rivolge al team di supporto del server proxy.

## <a name="next-steps"></a>Passaggi successivi

- [Comprendere i connettori del proxy applicazione di Azure AD](application-proxy-understand-connectors.md)

- In caso di problemi di connettività del connettore, porre una domanda nel [forum di Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD&forum=WindowsAzureAD) o creare un ticket per il team di supporto Microsoft.

