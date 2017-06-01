---
title: Usare server proxy locali esistenti e Azure AD | Documentazione Microsoft
description: Tratta l&quot;uso di server proxy locali esistenti.
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
ms.date: 05/22/2017
ms.author: kgremban
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: ea928ba4d13970a32123a8ada8575658cecde5d8
ms.contentlocale: it-it
ms.lasthandoff: 05/31/2017


---

# <a name="work-with-existing-on-premises-proxy-servers"></a>Usare server proxy locali esistenti

Questo articolo illustra come configurare i connettori del proxy applicazione di Azure Active Directory (Azure AD) per l'uso di server proxy in uscita. È rivolto ai clienti con ambienti di rete che dispongono di proxy esistenti.

Iniziamo esaminando questi scenari di distribuzione principali:
* Configurare i connettori per ignorare i proxy in uscita locali.
* Configurare i connettori per l'uso di un proxy in uscita per accedere al proxy applicazione di Azure AD.

Per ulteriori informazioni sui connettori, vedere [Informazioni sui connettori proxy di applicazione di Azure AD](application-proxy-understand-connectors.md).

## <a name="configure-the-outbound-proxy"></a>Configurare il proxy in uscita

Se nell’ambiente è disponibile un proxy in uscita, utilizzare un account con le dovute autorizzazioni per configurare il proxy in uscita. Poiché il programma di installazione viene eseguito nel contesto dell'utente che esegue l'installazione, la configurazione può essere verificata usando Microsoft Edge o altri browser Internet.

Per configurare le impostazioni proxy in Microsoft Edge:

1. Passare a **Impostazioni** > **Visualizza impostazioni avanzate** > **Apri impostazioni proxy** > **Configurazione manuale proxy**.
2. Impostare **Usa server proxy** su **Sì**, selezionare la casella di controllo **Non usare server proxy per indirizzi locali (Intranet)** e quindi cambiare l'indirizzo e la porta in modo che corrispondano al server proxy locale.
3. Specificare le impostazioni proxy necessarie.

   ![Finestra di dialogo per le impostazioni proxy](./media/application-proxy-working-with-proxy-servers/proxy-bypass-local-addresses.png)

## <a name="bypass-outbound-proxies"></a>Ignorare i proxy in uscita

I connettori sono componenti del sistema operativo sottostanti che creano le richieste in uscita. Questi componenti tentano automaticamente di individuare un server proxy in rete. tramite Web Proxy Auto-Discovery (WPAD), se è attivato nell'ambiente.

I componenti del sistema operativo provano a individuare un server proxy eseguendo una ricerca DNS per wpad.domainsuffix. Se viene individuato nel DNS, viene quindi inviata una richiesta HTTP all'indirizzo IP per wpad.dat. Questa richiesta diventa lo script di configurazione proxy nell'ambiente. Il connettore usa questo script per selezionare un server proxy in uscita. Il traffico del connettore potrebbe tuttavia non riuscire ancora a passare perché sono necessarie altre impostazioni di configurazione nel proxy.

È possibile configurare il connettore in modo che ignori il proxy locale, per garantire che usi la connettività diretta ai servizi di Azure. Si tratta di un'operazione consigliata, se i criteri di rete lo consentono, perché implica una configurazione in meno da gestire.

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
Per assicurarsi che anche il servizio Connector Updater ignori il proxy, apportare una modifica simile al file ApplicationProxyConnectorUpdaterService.exe.config che si trova in C:\Programmi\Microsoft AAD App Proxy Connector Updater.

Assicurarsi di creare copie dei file originali nel caso sia necessario ripristinare i file .config predefiniti.

## <a name="use-the-outbound-proxy-server"></a>Usare il server proxy in uscita

Alcuni ambienti dei clienti richiedono che tutto il traffico in uscita passi attraverso un proxy in uscita, senza eccezioni. Di conseguenza ignorare il proxy non è possibile.

Si può configurare il traffico del connettore per il passaggio attraverso il proxy in uscita, come illustrato nel diagramma seguente.

 ![Configurazione del traffico del connettore per l'uso di un proxy in uscita per accedere al proxy applicazione di Azure AD](./media/application-proxy-working-with-proxy-servers/configure-proxy-settings.png)

Dato che il traffico è solo in uscita, non è necessario configurare l'accesso in ingresso attraverso firewall.

### <a name="step-1-configure-the-connector-and-related-services-to-go-through-the-outbound-proxy"></a>Passaggio 1: Configurare il connettore e i servizi correlati per il passaggio attraverso il proxy in uscita

Come illustrato in precedenza, se WPAD è abilitato nell'ambiente e configurato in modo appropriato, il connettore individuerà automaticamente il server proxy in uscita e proverà a usarlo. Tuttavia, è possibile configurare in modo esplicito il connettore per il passaggio attraverso un proxy in uscita.

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

Configurare quindi il servizio Connector Updater in modo che usi il proxy, apportando una modifica simile al file che si trova in C:\Program Files\Microsoft AAD App Proxy Connector Updater\ApplicationProxyConnectorUpdaterService.exe.config.

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

I canali di controllo del bus di servizio sottostanti usati dal servizio connettore richiedono anche la connettività a specifici indirizzi IP. Fino a quando il bus di servizio non viene spostato in un FQDN, sono disponibili due opzioni:

* Consentire al connettore l'accesso in uscita a tutte le destinazioni.
* Consentire al connettore l'accesso in uscita agli [intervalli di indirizzi IP del data center di Azure](https://www.microsoft.com/en-gb/download/details.aspx?id=41653).

>[!NOTE]
>Il problema con l'elenco di intervalli di indirizzi IP del data center di Azure è che viene aggiornato ogni settimana. È necessario implementare un processo per assicurare che le regole di accesso vengano aggiornate di conseguenza.
>

#### <a name="proxy-authentication"></a>Autenticazione proxy

L'autenticazione proxy non è attualmente supportata. Il nostro consiglio è di consentire l'accesso anonimo del connettore alle destinazioni su Internet.

#### <a name="proxy-ports"></a>Porte proxy

Il connettore esegue le connessioni SSL in uscita usando il metodo CONNECT. Questo metodo crea essenzialmente un tunnel attraverso il proxy in uscita. Alcuni server proxy consentono per impostazione predefinita il tunneling in uscita solo alle porte SSL standard, ad esempio la 443. In questo caso, il server proxy deve essere configurato per consentire il tunneling a porte aggiuntive.

Configurare il server proxy per consentire il tunneling alle porte SSL non standard 8080, 9090, 9091 e 10100-10120.

>[!NOTE]
>Quando il bus di servizio viene eseguito su HTTPS, usa la porta 443. Per impostazione predefinita, il bus di servizio prova tuttavia a stabilire connessioni TCP dirette ed esegue il fallback su HTTPS solo se si verifica un errore di connettività diretta.
>

Per garantire che anche il traffico del bus di servizio venga inviato tramite il server proxy in uscita, verificare che il connettore non possa connettersi direttamente ai servizi di Azure per le porte 9350, 9352 e 5671.

#### <a name="ssl-inspection"></a>Ispezione SSL
Non usare l'ispezione SSL per il traffico del connettore perché causerà problemi per tale traffico.

## <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>Risolvere i problemi relativi al connettore proxy e alla connettività del servizio
A questo punto si dovrebbe vedere tutto il traffico che passa attraverso il proxy. Se si verificano problemi, potrebbero essere utili le seguenti informazioni per la risoluzione dei problemi.

Il modo migliore per identificare e risolvere i problemi di connettività del connettore è eseguire un'acquisizione di rete sul servizio del connettore durante l'avvio del servizio stesso. Questa può essere un'attività molto complessa, quindi vediamo alcuni semplici suggerimenti sull'acquisizione e il filtraggio delle tracce di rete.

È possibile usare lo strumento di monitoraggio preferito. Ai fini di questo articolo è stato usato Microsoft Network Monitor 3.4. È possibile [scaricarlo da Microsoft](https://www.microsoft.com/download/details.aspx?id=4865).

Gli esempi e i filtri usati nelle sezioni seguenti sono specifici di Network Monitor, ma i principi sono applicabili a qualsiasi strumento di analisi.

### <a name="take-a-capture-by-using-network-monitor"></a>Eseguire un'acquisizione con Network Monitor

Per avviare un'acquisizione:

1. Aprire Network Monitor e fare clic su **New Capture** (Nuova acquisizione).
2. Fare clic sul pulsante **Start**.

   ![Finestra di Network Monitor](./media/application-proxy-working-with-proxy-servers/network-capture.png)

Dopo aver completato un'acquisizione, fare clic sul pulsante **Stop** per terminarla.

### <a name="take-a-capture-of-connector-traffic"></a>Eseguire un'acquisizione del traffico del connettore

Per iniziare la risoluzione dei problemi, procedere come segue:

1. Da services.msc arrestare il servizio connettore del proxy applicazione di Azure AD.
2. Avviare l'acquisizione di rete.
3. Avviare il servizio connettore del proxy applicazione di Azure AD.
4. Arrestare l'acquisizione di rete.

   ![Servizio connettore proxy applicazione di Azure AD in services.msc](./media/application-proxy-working-with-proxy-servers/services-local.png)

### <a name="look-at-the-requests-from-the-connector-to-the-proxy-server"></a>Esaminare le richieste del connettore al server proxy

Ora che è disponibile un'acquisizione di rete, è possibile filtrarla. Per l'analisi della traccia è fondamentale sapere come filtrare l'acquisizione.

Un filtro è il seguente, dove 8080 è la porta del servizio proxy:

**(http.Request or http.Response) and tcp.port==8080**

Se si immette questo filtro nella finestra di **visualizzazione del filtro** e si seleziona **Applica**, il traffico acquisito sarà filtrato in base al filtro.

Il filtro precedente consente di visualizzare solo le richieste e risposte HTTP da e verso la porta del proxy. Per l'avvio di un connettore in cui il connettore è configurato per l'uso di un server proxy, il filtro sarà simile al seguente:

 ![Elenco di esempio di richieste e risposte HTTP filtrate](./media/application-proxy-working-with-proxy-servers/http-requests.png)

Si cercano ora in modo specifico le richieste CONNECT che indicano la comunicazione con il server proxy. Al completamento dell'operazione si otterrà una risposta HTTP affermativa (200).

Se vengono visualizzati altri codici di risposta, ad esempio 407 o 502, il proxy richiede l'autenticazione o non consente il traffico per altri motivi. A questo punto ci si rivolge al team di supporto del server proxy.

### <a name="identify-failed-tcp-connection-attempts"></a>Identificare tentativi di connessione TCP non riusciti

L'altro scenario comune a cui si può essere interessati è quando il connettore prova a connettersi direttamente, ma non riesce.

Un altro filtro di Network Monitor che contribuisce a identificare facilmente questo problema è il seguente:

**property.TCPSynRetransmit**

Un pacchetto SYN è il primo pacchetto inviato per stabilire una connessione TCP. Se questo pacchetto non restituisce una risposta, il SYN viene ritentato. È possibile usare il filtro precedente per visualizzare qualsiasi SYN ritrasmesso. È quindi possibile vedere se questi SYN corrispondono a traffico correlato al connettore.

L'esempio seguente illustra un tentativo di connessione non riuscito alla porta 9352 del bus di servizio:

 ![Risposta di esempio per un tentativo di connessione non riuscito](./media/application-proxy-working-with-proxy-servers/failed-connection-attempt.png)

Se viene visualizzata una risposta simile alla precedente, il connettore sta provando a comunicare direttamente con il servizio del bus di servizio di Azure. Se si prevede che il connettore esegua connessioni dirette ai servizi di Azure, questa risposta è una chiara indicazione del fatto che si è verificato un problema di rete o di firewall.

>[!NOTE]
>Se il sistema è configurato per l'uso di un server proxy, questa risposta può significare che il bus di servizio sta provando a eseguire una connessione TCP diretta prima di passare al tentativo di connessione su HTTPS.
>

L'analisi delle tracce di rete non è per tutti. Può essere tuttavia uno strumento utile per ottenere rapidamente informazioni su quanto accade nella rete.

Se si verificano continuamente problemi di connettività del connettore, creare un ticket con il team di supporto Microsoft. Il team può offrire assistenza nella risoluzione del problema.

Per informazioni sulla risoluzione degli errori con il connettore del proxy applicazione, vedere [Risolvere i problemi del proxy applicazione](https://azure.microsoft.com/documentation/articles/active-directory-application-proxy-troubleshoot).

## <a name="next-steps"></a>Passaggi successivi

[Comprendere i connettori del proxy applicazione Azure AD](application-proxy-understand-connectors.md)<br>
[Come eseguire un'installazione invisibile all'utente del connettore del proxy applicazione Azure AD](active-directory-application-proxy-silent-installation.md)

