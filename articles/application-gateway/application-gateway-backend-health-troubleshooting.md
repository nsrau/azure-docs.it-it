---
title: Risolvere i problemi di integrità back-end in applicazione Azure gateway
description: Questo articolo illustra la risoluzione dei problemi di integrità back-end per applicazione Azure gateway
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: article
ms.date: 08/30/2019
ms.author: surmb
ms.openlocfilehash: 1fd4e9156e29133b1db4fe9ab9a0825eb1aa3b55
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097585"
---
<a name="troubleshoot-backend-health-issues-in-application-gateway"></a>Risolvere i problemi di integrità back-end nel gateway applicazione
==================================================

<a name="overview"></a>Panoramica
--------

Il gateway applicazione, per impostazione predefinita, esegue il probe dei server back-end per verificarne lo stato di integrità e se è pronto per gestire le richieste. Gli utenti possono anche creare Probe personalizzati per citare il nome host, il percorso per il probe e i codici di stato da accettare come integri. In entrambi i casi, se il server back-end non risponde correttamente, il gateway applicazione contrassegna il server come non integro e arresta l'invio della richiesta al server. Una volta che il server inizia a rispondere correttamente, riprenderà a soddisfare le richieste.

### <a name="how-to-check-backend-health"></a>Come controllare l'integrità back-end

Per verificare l'integrità del pool back-end, è possibile usare la pagina "integrità back-end" nel portale di Azure. In alternativa, è possibile usare [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhealth?view=azps-2.6.0), l' [interfaccia](https://docs.microsoft.com/cli/azure/network/application-gateway?view=azure-cli-latest#az-network-application-gateway-show-backend-health)della riga di comando o l' [API REST](https://docs.microsoft.com/rest/api/application-gateway/applicationgateways/backendhealth) per ottenere lo stato di integrità. Per ottenere altre informazioni, vedere l'articolo collegato rispetto a ogni metodo.

Lo stato recuperato da qualsiasi metodo menzionato in precedenza può essere di tre tipi, ovvero:

1.  Integra

2.  Non integro

3.  Sconosciuto

Se lo stato di integrità back-end per un server è integro, significa che il gateway applicazione inoltrerà le richieste a tale server. Tuttavia, se l'integrità back-end per tutti i server in un pool back-end non è integra o è sconosciuta, è possibile che si verifichino problemi durante l'accesso all'applicazione. Questo documento descrive il sintomo, la ragione e la soluzione di ciascuno degli errori visualizzati quando i server back-end potrebbero non essere integri o sconosciuti.

<a name="backend-health-status-unhealthy"></a>Stato di integrità back-end non integro
-------------------------------

Se lo stato di integrità back-end viene visualizzato come non integro, verrà visualizzato nel portale come lo screenshot seguente:

![Integrità back-end del gateway applicazione-non integro](./media/application-gateway-backend-health-troubleshooting/appgwunhealthy.png)

In alternativa, se si usa Azure PowerShell, l'interfaccia della riga di comando o la query dell'API REST di Azure, verrà visualizzata una risposta simile a quella riportata di seguito:
```azurepowershell
PS C:\Users\testuser\> Get-AzApplicationGatewayBackendHealth -Name "appgw1" -ResourceGroupName "rgOne"
BackendAddressPools :
{Microsoft.Azure.Commands.Network.Models.PSApplicationGatewayBackendHealthPool}
BackendAddressPoolsText : [
{
                              "BackendAddressPool": {
                                "Id": "/subscriptions/536d30b8-665b-40fc-bd7e-68c65f816365/resourceGroups/rgOne/providers/Microsoft.Network/applicationGateways/appgw1/b
                          ackendAddressPools/appGatewayBackendPool"
                              },
                              "BackendHttpSettingsCollection": [
                                {
                                  "BackendHttpSettings": {
                                    "TrustedRootCertificates": [],
                                    "Id": "/subscriptions/536d30b8-665b-40fc-bd7e-68c65f816365/resourceGroups/rgOne/providers/Microsoft.Network/applicationGateways/appg
                          w1/backendHttpSettingsCollection/appGatewayBackendHttpSettings"
                                  },
                                  "Servers": [
                                    {
                                      "Address": "10.0.0.5",
                                      "Health": "Healthy"
                                    },
                                    {
                                      "Address": "10.0.0.6",
                                      "Health": "Unhealthy"
                                    }
                                  ]
                                }
                              ]
                            }
                        ]
```
Una volta visualizzato lo stato del server back-end come non integro per tutti i server in un pool back-end, le richieste non verranno inviate e il gateway applicazione restituirà 502 Errore del gateway errato al client richiedente. Per risolvere il problema, controllare la colonna dettagli della scheda integrità back-end.

Il messaggio visualizzato nella colonna dettagli della scheda integrità back-end fornisce informazioni dettagliate sul problema e, in base a tali informazioni, è possibile iniziare la risoluzione del problema.

> [!NOTE]
> La richiesta di probe predefinita viene inviata nel formato \<protocollo\>://127.0.0.1:\<porta\>/, ad esempio, <http://127.0.0.1/> per un probe http sulla porta 80 e considera solo una risposta dei codici di stato HTTP 200-399 come risposta integro. Il protocollo e la porta di destinazione vengono ereditati dalle impostazioni HTTP. Se si vuole che il gateway applicazione cerchi un protocollo, un nome host o un percorso diverso e accetti un codice di stato diverso come integro, configurare un probe personalizzato e associarlo alle impostazioni HTTP.

<a name="error-messages"></a>Messaggi di errore
------------------------
#### <a name="backend-server-timeout"></a>Timeout server back-end

**Messaggio:** Il tempo impiegato dal back-end per rispondere al\'Probe di integrità del gateway applicazione è superiore alla soglia di timeout nell'impostazione del probe.

**Causa:** Quando il gateway applicazione invia una richiesta di probe HTTP (S) al server back-end, attende la risposta dal server back-end per un determinato periodo di tempo configurato. Se il server back-end non risponde entro il valore di timeout, viene contrassegnato come non integro fino a quando non viene riavviata la risposta entro il timeout.

**Risoluzione:** Controllare nel server back-end o nell'applicazione il motivo per cui non risponde entro il timeout configurato e controllare anche le dipendenze dell'applicazione, ad esempio se il database presenta problemi che potrebbero causare un ritardo nella risposta. Se si è in grado di riconoscere il comportamento dell'applicazione e deve rispondere solo dopo il valore di timeout, aumentare il valore di timeout dalle impostazioni del probe personalizzato.
È necessario disporre di un probe personalizzato per modificare il valore del timeout. Per informazioni su come configurare un probe personalizzato, [vedere la pagina della documentazione](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal).

Per aumentare il timeout, attenersi alla procedura seguente:

1.  Accedere direttamente al server back-end e controllare il tempo impiegato dal server per rispondere a tale pagina. È possibile usare qualsiasi strumento per accedere a, incluso un browser con gli strumenti di sviluppo.

2.  Una volta individuato il tempo richiesto per l'applicazione per rispondere, fare clic sulla scheda Probe integrità e selezionare il probe associato alle impostazioni HTTP.

3.  Immettere un valore di timeout superiore al tempo di risposta dell'applicazione, in secondi.

4.  Salvare le impostazioni del probe personalizzato e controllare l'integrità back-end se Visualizza ora integro.

#### <a name="dns-resolution-error"></a>Errore di risoluzione DNS

**Messaggio:** Il gateway applicazione non è riuscito a creare un probe per questo back-end. Questo problema si verifica in genere quando il nome di dominio completo del back-end non è stato immesso correttamente. 

**Causa:** Se il pool back-end è di tipo indirizzo IP/FQDN o servizio app, il gateway applicazione viene risolto nell'indirizzo IP del nome di dominio completo (FQDN) immesso usando il server DNS (personalizzato o predefinito di Azure) e tenta di connettersi al server sulla porta TCP indicata nelle impostazioni HTTP. Tuttavia, se questo messaggio viene visualizzato, suggerisce che il gateway applicazione non è stato in grado di risolvere correttamente l'indirizzo IP del FQDN immesso.

**Risoluzione:**

1.  Verificare che il nome FQDN immesso nel pool back-end sia corretto e che si tratta di un dominio pubblico, provare a risolverlo dal computer locale.

2.  Se è possibile risolvere l'indirizzo IP, potrebbe essersi verificato un errore nella configurazione DNS in VNet.

3.  Controllare se la VNet è configurata con un server DNS personalizzato. In caso affermativo, controllare sul server DNS il motivo per cui non è in grado di risolvere l'indirizzo IP del nome di dominio completo specificato.

4.  Se è DNS predefinito di Azure, rivolgersi al registrar se è stato eseguito il mapping di un record o di un record CNAME.

5.  Se il dominio è privato/interno, provare a risolverlo da una macchina virtuale nella stessa VNet e se è possibile risolverlo, riavviare il gateway applicazione e verificare di nuovo. Per riavviare il gateway applicazione, è necessario [arrestare](https://docs.microsoft.com/powershell/module/azurerm.network/stop-azurermapplicationgateway?view=azurermps-6.13.0) e [iniziare](https://docs.microsoft.com/powershell/module/azurerm.network/start-azurermapplicationgateway?view=azurermps-6.13.0) a usare i comandi di PowerShell specificati nei rispettivi collegamenti al documento.

#### <a name="tcp-connect-error"></a>Errore di connessione TCP

**Messaggio:** Il gateway applicazione non è riuscito a connettersi al back-end.
Verificare che il back-end risponda sulla porta usata per il probe.
Controllare anche se NSG/UDR/firewall blocca l'accesso all'IP e alla porta di questo back-end

**Causa:** Dopo la fase di risoluzione DNS, il gateway applicazione tenta di connettersi al server back-end sulla porta TCP configurata nelle impostazioni HTTP. Se il gateway applicazione non è in grado di stabilire una sessione TCP sulla porta specificata, il probe viene contrassegnato come non integro con questo messaggio.

**Soluzione:** Se viene visualizzato questo errore, controllare quanto segue:

1.  Controllare se è possibile connettersi al server back-end sulla porta indicata nelle impostazioni HTTP usando un browser o usando PowerShell, ad esempio, è possibile usare il comando: Test-NetConnection-ComputerName www.bing.com-porta 443

2.  Se la porta indicata non è la porta desiderata, immettere il numero di porta corretto per la connessione del gateway applicazione al server back-end

3.  Se non è possibile connettersi alla porta anche dal computer locale, eseguire le operazioni seguenti:

    a.  Controllare le impostazioni NSG della scheda di interfaccia di rete e della subnet del server back-end e verificare se sono consentite le connessioni in ingresso alla porta configurata. Se non sono consentite, creare una nuova regola per consentire le connessioni. Per informazioni su come creare regole NSG, [vedere la pagina della documentazione](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic#create-security-rules).

    b.  Controllare le impostazioni NSG della subnet del gateway applicazione se il traffico pubblico e privato in uscita è consentito, in modo che sia possibile effettuare la connessione. Controllare il documento collegato (a) per altre informazioni su come creare regole NSG.
    ```azurepowershell
            $vnet = Get-AzVirtualNetwork -Name "vnetName" -ResourceGroupName "rgName"
            Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
    ```

    c.  Controllare le impostazioni UDR della subnet del gateway applicazione e del server back-end per eventuali anomalie di routing. Verificare che UDR non stia indirizzando il traffico dalla subnet back-end. Ad esempio, verificare la presenza di route per le appliance virtuali di rete o le route predefinite annunciate alla subnet del gateway applicazione tramite ExpressRoute/VPN.

    d.  Per verificare le route e le regole valide per una scheda di interfaccia di rete, è possibile usare i comandi di PowerShell seguenti.
    ```azurepowershell
            Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
            Get-AzEffectiveRouteTable -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
    ```
4.  Se non si riscontrano problemi con NSG o UDR, controllare il server back-end per individuare problemi relativi all'applicazione perché i client non possono stabilire una sessione TCP sulle porte configurate. Ecco alcuni aspetti da controllare:

    e.  Aprire il prompt dei comandi (Win +\> R-cmd) e immettere netstat e premere INVIO.

    f.  Controllare se il server è in ascolto sulla porta configurata.
        Ad esempio:
    ```
            Proto Local Address Foreign Address State PID
            TCP 0.0.0.0:80 0.0.0.0:0 LISTENING 4
    ```
    g.  In caso contrario, controllare le impostazioni del server Web, ad esempio i binding del sito in IIS, il blocco server in NGINX e l'host virtuale in Apache

    h.  Controllare le impostazioni del firewall del sistema operativo per assicurarsi che sia consentito il traffico in ingresso verso la porta

#### <a name="http-status-code-mismatch"></a>Mancata corrispondenza del codice di stato HTTP

**Messaggio:** Il codice di stato della\'risposta http back-end non corrisponde all'impostazione del probe. Previsto: {HTTPStatusCode0} ricevuto: {HTTPStatusCode1}.

**Causa:** Una volta stabilita la connessione TCP e l'handshake SSL (se SSL è abilitato), il gateway applicazione invierà il probe come richiesta HTTP GET al server back-end. Come indicato in precedenza, il probe predefinito sarà \<il\>protocollo://127.0.0.1\<:\>Port/e considera i codici di stato della risposta in Rage 200-399 come integri. Se il server restituisce un altro codice di stato, verrà contrassegnato come non integro con questo messaggio.

**Soluzione:** A seconda del codice di risposta del server back-end, è possibile eseguire i passaggi seguenti. Di seguito sono elencati alcuni codici di stato comuni:

| **Error (Errore) (Error (Errore)e)** | **Actions** |
| --- | --- |
| Mancata corrispondenza del codice di stato Probe: Ricevuto 401 | Verificare che il server back-end richieda l'autenticazione. I probe del gateway applicazione non possono passare le credenziali per l'autenticazione a questo punto. Consentire \"HTTP 401\" in una corrispondenza del codice di stato probe o eseguire il probe in un percorso in cui il server non richiede l'autenticazione. | |
| Mancata corrispondenza del codice di stato Probe: Ricevuto 403 | Accesso non consentito. Controllare se l'accesso al percorso è consentito nel server back-end. | |
| Mancata corrispondenza del codice di stato Probe: Ricevuto 404 | Pagina non trovata. Controllare il percorso del nome host se è accessibile nel server back-end. Modificare il nome host o il parametro Path in un valore accessibile. | |
| Mancata corrispondenza del codice di stato Probe: Ricevuto 405 | Le richieste probe del gateway applicazione usano il metodo HTTP GET. Controllare se il server lo consente. | |
| Mancata corrispondenza del codice di stato Probe: Ricevuto 500 | Errore interno del server. Controllare l'integrità del server back-end e se i servizi sono in esecuzione. | |
| Mancata corrispondenza del codice di stato Probe: Ricevuto 503 | Servizio non disponibile. Controllare l'integrità del server back-end e se i servizi sono in esecuzione. | |

In alternativa, se si ritiene che la risposta sia legittima e si desidera che il gateway applicazione accetti altri codici di stato come integri, è possibile creare un probe personalizzato. La modifica di questa opzione è utile nelle situazioni in cui il sito Web back-end richiede l'autenticazione e poiché le richieste di probe non contengono credenziali utente, avranno esito negativo e verrà restituito un codice di stato HTTP 401 dal server back-end.

Per creare un probe personalizzato, seguire i passaggi elencati di [seguito](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal).

#### <a name="http-response-body-mismatch"></a>Mancata corrispondenza del corpo della risposta HTTP

**Messaggio:** Il corpo della risposta\'http back-end non corrisponde all'impostazione del probe. Il corpo della risposta ricevuta non contiene {String}.

**Causa:** Quando si crea un probe personalizzato, è possibile contrassegnare un server back-end integro associando una stringa dal corpo della risposta. Ad esempio, è possibile configurare il gateway applicazione in modo che accetti "non autorizzato" come stringa per la corrispondenza. Se la risposta del server back-end per la richiesta del probe contiene la stringa "non autorizzata", verrà contrassegnata come integro.
In caso contrario, verrà contrassegnato come non integro con questo messaggio.

**Soluzione:** È possibile risolvere questo problema attenendosi alla procedura seguente:

1.  Accedere al server back-end localmente o da un computer client nel percorso Probe e verificare il corpo della risposta.

2.  Controllare la configurazione del probe personalizzato del gateway applicazione per verificare se il corpo della risposta corrisponde a quello configurato.

3.  Se non corrispondono, modificare la configurazione del probe con il valore stringa corretto da accettare.

Per altre informazioni sulla corrispondenza dei probe del gateway applicazione, vedere [qui](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching).

#### <a name="backend-server-certificate-invalid-ca"></a>CA del certificato del server back-end non valida

**Messaggio:** Il certificato server usato dal back-end non è firmato da un'autorità di certificazione (CA) Nota. Inserire nell'elenco elementi consentiti il back-end nel gateway applicazione caricando il certificato radice del certificato del server usato dal back-end.

**Causa:** SSL end-to-end con il gateway applicazione V2 richiede che il certificato del server back-end sia verificato per considerare integro il server.
Affinché un certificato SSL venga considerato attendibile, il certificato del server back-end deve essere stato emesso da un'autorità di certificazione inclusa nell'archivio attendibile del gateway applicazione. Se il certificato non è stato emesso da un'autorità di certificazione attendibile, ad esempio i certificati autofirmati, gli utenti devono caricare il certificato dell'autorità emittente nel gateway applicazione.

**Soluzione:** Attenersi alla procedura seguente per esportare e caricare il certificato radice attendibile nel gateway applicazione. i passaggi indicati di seguito sono per i client Windows.

1.  Accedere al computer in cui è ospitata l'applicazione

2.  Aprire Esegui premendo Win + R o facendo clic con il pulsante destro del mouse sul pulsante Start e selezionando Esegui.

3.  Immettere certmgr. msc e premere INVIO. È anche possibile cercare gestione certificati nel menu Start.

4.  Individuare il certificato, in genere \'in certificati-certificati\\\'personali\\dell'utente corrente e aprire il certificato

5.  In Proprietà certificato selezionare la scheda percorso certificazione.

6.  Selezionare il certificato radice e selezionare l'opzione "Visualizza certificato"

7.  In Proprietà certificato passare alla scheda Dettagli

8.  Nella scheda Dettagli selezionare l'opzione "copia su file" e salvare il file nel file X. 509 con codifica base 64 (. Formato CER)

9.  Una volta salvato, aprire la pagina Impostazioni HTTP del gateway applicazione nella portale di Azure

10. Aprire le impostazioni HTTP e fare clic su "Aggiungi certificato" e individuare il file di certificato salvato di recente

11. Fare clic su Salva per salvare le impostazioni HTTP

In alternativa, è possibile esportare il certificato radice da un computer client accedendo direttamente al server (ignorando il gateway applicazione) usando un browser ed esportando il certificato radice dal browser.

Per i passaggi dettagliati per l'estrazione e il caricamento dei certificati radice attendibili nel gateway applicazione, vedere [qui](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku).

#### <a name="trusted-root-certificate-mismatch"></a>Mancata corrispondenza del certificato radice attendibile

**Messaggio:** Il certificato radice del certificato del server usato dal back-end non corrisponde al certificato radice attendibile aggiunto al gateway applicazione. Assicurarsi di aggiungere il certificato radice corretto all'elenco elementi consentiti del back-end

**Causa:** SSL end-to-end con il gateway applicazione V2 richiede che il certificato del server back-end sia verificato per considerare integro il server.
Affinché un certificato SSL venga considerato attendibile, il certificato del server back-end deve essere stato emesso da un'autorità di certificazione inclusa nell'archivio attendibile del gateway applicazione. Se il certificato non è stato emesso da un'autorità di certificazione attendibile, ad esempio i certificati autofirmati, gli utenti devono caricare il certificato dell'autorità emittente nel gateway applicazione.

Il certificato caricato nelle impostazioni HTTP del gateway applicazione deve corrispondere al certificato radice del certificato del server back-end.

**Soluzione:** Se viene visualizzato il messaggio di errore menzionato in precedenza, significa che esiste una mancata corrispondenza tra il certificato che è stato caricato nel gateway applicazione e quello caricato nel server back-end.

Seguire i passaggi 1-11 indicati in precedenza per caricare il certificato radice attendibile corretto nel gateway applicazione.

Per i passaggi dettagliati per l'estrazione e il caricamento dei certificati radice attendibili nel gateway applicazione, vedere [qui](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku).
> [!NOTE]
> L'errore indicato potrebbe verificarsi anche se il server back-end non scambia la catena completa del certificato, inclusa la foglia > intermedia (se applicabile), > foglia durante l'handshake TLS. Per verificare, è possibile usare i comandi OpenSSL da qualsiasi client e connettersi al server back-end usando le impostazioni configurate nel probe del gateway applicazione.

Ad esempio,
```
OpenSSL> s_client -connect 10.0.0.4:443 -servername www.example.com -showcerts
```
Se l'output non Mostra la catena completa del certificato restituito, esportare di nuovo il certificato con la catena completa, incluso il certificato radice, e configurarlo nel server back-end. 

CONNESSIONE STABILITA (00000188) \
Depth = 0 ou = controllo del dominio convalidato, \*CN =. example.com \
verifica errore: num = 20: Impossibile ottenere il certificato dell'autorità emittente locale \
Verifica restituzione: 1 \
Depth = 0 ou = controllo del dominio convalidato, \*CN =. example.com \
verifica errore: num = 21: Impossibile verificare il primo certificato \
Verifica restituzione: 1 \
\-\-\-\
Catena di certificati \
 0 s:/OU = controllo dominio convalidato/CN = *. example. com \
   i:/C = US/St = Arizona/L = Scottsdale/O = GoDaddy. com, Inc./ou =http://certs.godaddy.com/repository//CN=Go Daddy Secure Certificate Authority-G2 \
\-----BEGIN CERTIFICATE-----\
xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx\
\------CERTIFICATO----END

#### <a name="backend-certificate-invalid-common-name-cn"></a>Nome comune (CN) del certificato back-end non valido

**Messaggio:** Il nome comune (CN) del certificato back-end non corrisponde all'intestazione host del probe.

**Causa:** Il gateway applicazione convalida se il nome host specificato nell'impostazione http back-end corrisponde a quello del nome comune (CN) presentato dal certificato SSL del server back-end. Si tratta solo di un comportamento SKU Standard_v2 e WAF_v2. Il SNI dello SKU standard e WAF viene impostato come FQDN nell'indirizzo del pool back-end.

Nello SKU V2, se è presente un probe predefinito (nessun Probe personalizzato configurato/associato), SNI verrà impostato dal nome host indicato nelle impostazioni HTTP o se "pick hostname from backend Address" è indicato nelle impostazioni HTTP, dove il pool di indirizzi back-end contiene FQDN valido.

Se è presente un probe personalizzato associato alle impostazioni HTTP, SNI verrà impostato dal nome host indicato nella configurazione del probe personalizzato o se il probe personalizzato viene archiviato "pick hostname from backend HTTP Settings", verrà impostato dal nome host indicato in HTTP Impostazioni.

Se si imposta "pick hostname from back-end Address" nelle impostazioni HTTP, il pool di indirizzi back-end deve contenere un FQDN valido.

Se viene visualizzato il messaggio di errore menzionato in precedenza, significa che il nome comune (CN) del certificato back-end non corrisponde al nome host configurato nel Probe personalizzato o nelle impostazioni HTTP (nel caso in cui si scelga "pick hostname from backend HTTP Settings"). Se si usa un probe predefinito, il nome host viene impostato come "127.0.0.1". Se non si tratta di un valore desiderato, è necessario creare un probe personalizzato e associarlo alle impostazioni HTTP.

**Soluzione:**

Per risolvere il problema, attenersi alla procedura seguente:

Per Windows:

1.  Accedere al computer in cui è ospitata l'applicazione

2.  Aprire Esegui premendo Win + R o facendo clic con il pulsante destro del mouse sul pulsante Start e selezionando Esegui.

3.  Immettere certmgr. msc e premere INVIO. È anche possibile cercare gestione certificati nel menu Start.

4.  Individuare il certificato, in genere \'in certificati-certificati\\\'personali\\dell'utente corrente e aprire il certificato

5.  Nella scheda dettagli controllare l'oggetto del certificato.

6.  Verificare il CN del certificato dai dettagli e immettere lo stesso nel campo hostname del probe personalizzato o nelle impostazioni HTTP (nel caso in cui si scelga "pick hostname from backend HTTP Settings"). Se questo non è il nome host desiderato per il sito Web, è necessario ottenere un certificato per tale dominio oppure immettere il nome host corretto nella configurazione dell'impostazione Probe/HTTP personalizzata.

Per Linux con OpenSSL

1.  Eseguire questo comando in OpenSSL 
```
openssl x509 -in certificate.crt -text -noout
```

2.  Dalle proprietà visualizzate, trovare il CN del certificato e immettere lo stesso nel campo hostname delle impostazioni http. Se questo non è il nome host desiderato per il sito Web, è necessario ottenere un certificato per tale dominio oppure immettere il nome host corretto nella configurazione dell'impostazione Probe/HTTP personalizzata.

#### <a name="backend-certificate-is-invalid"></a>Il certificato back-end non è valido

**Messaggio:** Il certificato back-end non è valido. La \"data corrente non è compresa nell'intervallo\" valido \"da e\" valido a data del certificato.

**Causa:** Ogni certificato viene specificato con una validità e la connessione HTTPS non sarà protetta, a meno che il certificato SSL del server non sia valido.
I dati correnti devono essere compresi nell'intervallo valido da e valido a. In caso contrario, il certificato verrà considerato non valido e sarà un problema per la sicurezza. A questo punto, il gateway applicazione contrassegna il server back-end come non integro.

**Soluzione:** Se il certificato SSL è scaduto, rinnovare il certificato con il fornitore e aggiornare le impostazioni del server con il nuovo certificato. Se si tratta di un certificato autofirmato, è necessario generare un certificato valido e caricare il certificato radice nelle impostazioni HTTP del gateway applicazione. A tale scopo, attenersi alla procedura seguente:

1.  Aprire le impostazioni HTTP del gateway applicazione nel portale

2.  Selezionare le impostazioni HTTP con il certificato scaduto, selezionare Aggiungi certificato e aprire il nuovo file di certificato.

3.  Rimuovere il certificato precedente usando l'icona di eliminazione accanto al certificato e fare clic su Salva.

#### <a name="certificate-verification-failed"></a>Verifica del certificato non riuscita

**Messaggio:** Non è stato possibile verificare la validità del certificato back-end. Per individuare il motivo, controllare la diagnostica SSL aperta per il messaggio associato al codice di errore {errorCode}

**Causa:** Questo errore si verifica quando il gateway applicazione non è in grado di verificare la validità del certificato.

**Soluzione:** Per risolvere il problema, controllare il certificato nel server se è stato creato correttamente. Ad esempio, è possibile usare [openssl](https://www.openssl.org/docs/man1.0.2/man1/verify.html) per verificare il certificato e le relative proprietà e provare a ricaricare il certificato nelle impostazioni http del gateway applicazione.

<a name="backend-health-status-unknown"></a>Stato di integrità back-end sconosciuto
-------------------------------
Se l'integrità back-end viene visualizzata come sconosciuta, verrà visualizzata nel portale come lo screenshot seguente:

![Stato back-end del gateway applicazione-sconosciuto](./media/application-gateway-backend-health-troubleshooting/appgwunknown.png)

Se l'integrità back-end viene visualizzata come sconosciuta, il problema potrebbe essere dovuto a uno o più dei seguenti motivi:

1.  NSG nella subnet del gateway applicazione blocca l'accesso in ingresso alle porte 65503-65534 (SKU V1) o 65200-65535 (SKU v2) da "Internet"
2.  UDR nella subnet del gateway applicazione con route predefinita (0.0.0.0/0) con hop successivo non come Internet
3.  Route predefinita annunciata dalla connessione ExpressRoute/VPN al VNet tramite BGP
4.  Il server DNS personalizzato è configurato in VNet che non è in grado di risolvere i nomi di dominio pubblici
5.  Il gateway applicazione si trova in uno stato non integro

**Soluzione:**

1.  Controllare se il NSG blocca l'accesso alle porte 65503-65534 (SKU V1) o 65200-65535 (SKU v2) da "Internet"

    a.  Nella scheda "panoramica" del gateway applicazione selezionare il collegamento rete virtuale/subnet

    b.  Nella scheda subnet della rete virtuale selezionare la subnet in cui è stato distribuito il gateway applicazione

    c.  Controllare se sono presenti NSG configurati

    d.  Se ne esiste uno, cercare la risorsa NSG nella scheda Cerca o in tutte le risorse

    e.  Nella sezione regole in ingresso aggiungere una regola in ingresso per consentire l'intervallo di porte di destinazione 65503-65534 per lo SKU V1 o lo SKU 65200-65535 V2 con origine come qualsiasi o Internet

    f.  Fare clic su Salva e verificare se è possibile visualizzare il back-end integro correttamente

    g.  In alternativa, è possibile eseguire questa operazione tramite [PowerShell/interfaccia](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group) della riga di comando

2.  Controllare se il UDR ha una route predefinita (0.0.0.0/0) con hop successivo non come Internet
    
    a.  Seguire i passaggi 1. a e 1. b per determinare la subnet

    b.  Controllare se sono stati configurati UDR. Se ne esiste uno, cercare la risorsa nella barra di ricerca o in tutte le risorse

    c.  Controllare se sono presenti route predefinite (0.0.0.0/0) con hop successivo non come Internet. Se si tratta di un'appliance virtuale o di un gateway di rete virtuale, è necessario assicurarsi che l'appliance virtuale o il dispositivo locale sia in grado di instradare correttamente il pacchetto alla destinazione Internet senza modificare il pacchetto.

    d.  In caso contrario, modificare l'hop successivo su Internet, fare clic su Salva e verificare l'integrità back-end

3.  Route predefinita annunciata dalla connessione ExpressRoute/VPN al VNet tramite BGP

    a.  Se si dispone di una connessione VPN/ExpressRoute al VNet su BGP e se si sta annunciando una route predefinita, è necessario assicurarsi che il pacchetto venga indirizzato di nuovo alla destinazione Internet senza modificarlo.

    b.  È possibile verificare usando l'opzione "risoluzione dei problemi di connessione" nel portale del gateway applicazione

    c.  Scegliere la destinazione manualmente come qualsiasi indirizzo IP instradabile su Internet, come "1.1.1.1" e la porta di destinazione, come qualsiasi elemento e controllare la connettività.

    d.  Se l'hop successivo è il gateway di rete virtuale, potrebbe essere presente una route predefinita annunciata tramite ExpressRoute o VPN

4.  Se in VNet è configurato un server DNS personalizzato, verificare che i server possano risolvere i domini pubblici. La risoluzione dei nomi di dominio pubblico potrebbe essere necessaria negli scenari in cui il gateway applicazione deve raggiungere domini esterni come server OCSP o controllare lo stato di revoca del certificato e così via.

5.  Per verificare se il gateway applicazione è integro e in esecuzione, passare a Integrità risorse opzione nel portale e verificare se è "integro". Se viene visualizzato uno stato non integro o danneggiato, contattare il [supporto tecnico](https://azure.microsoft.com/support/options/).

<a name="next-steps"></a>Fasi successive
----------

Per altre informazioni sulla diagnostica e sulla registrazione del gateway applicazione, vedere [qui](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).
