## <a name="how-apim-proxy-server-responds-with-ssl-certificates-in-the-tls-handshake"></a>Modalità di risposta con i certificati SSL nell'handshake TLS ruoli Proxy Server

### <a name="clients-calling-with-sni-header"></a>Client che chiamano con intestazione SNI
Se il cliente ha uno o più domini personalizzati configurati per il Proxy, ruoli possono rispondere alle richieste HTTPS dai domini personalizzati (ad esempio, contoso.com) e il dominio predefinito (ad esempio, ruoli-servizio-name.azure-api.net). RUOLI in base alle informazioni nell'intestazione di indicazione nome Server (SNI), risponde con il certificato server appropriato.

### <a name="clients-calling-without-sni-header"></a>Client che chiamano senza intestazione SNI
Se il cliente utilizza un client che non invia il [SNI](https://tools.ietf.org/html/rfc6066#section-3) intestazione, ruoli crea le risposte in base alla logica seguente:

* Se il servizio è configurato per il Proxy di un solo dominio personalizzato, il certificato predefinito è il certificato emesso per il dominio personalizzato Proxy.
* Se il servizio è configurato più domini personalizzati per il Proxy (supportato solo nel **Premium** livello), il cliente è possibile designare quali certificati devono essere il certificato predefinito. Per impostare il certificato predefinito, il [defaultSslBinding](https://docs.microsoft.com/rest/api/apimanagement/apimanagementservice/createorupdate#definitions_hostnameconfiguration) proprietà deve essere impostata su true ("defaultSslBinding": "true"). Se il cliente non è impostata la proprietà, il certificato predefinito è il certificato emesso per il dominio di Proxy predefinito ospitato *.azure-api.net.

## <a name="support-for-putpost-request-with-large-payload"></a>Supporto per la richiesta PUT/POST con payload di grandi dimensioni

RUOLI Proxy server supporta la richiesta con payload di grandi dimensioni quando si utilizzano i certificati sul lato client in HTTPS (ad esempio, payload > da 40 KB). Per impedire il blocco di richiesta del server, se è possibile impostare la proprietà ["negotiateClientCertificate": "true"](https://docs.microsoft.com/rest/api/apimanagement/ApiManagementService/CreateOrUpdate#definitions_hostnameconfiguration) sul nome host Proxy. Se la proprietà è impostata su true, il client di certificato è richiesto al momento della connessione SSL/TLS, prima di qualsiasi scambio di richiesta HTTP. Poiché l'impostazione si applica il **nome host Proxy** chiedere un livello, tutte le richieste di connessione per il certificato client. I clienti è possono configurare fino a 20 domini personalizzati per il Proxy (supportato solo nel **Premium** livello) e di lavoro risolvere questa limitazione.

