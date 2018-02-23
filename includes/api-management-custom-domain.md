## <a name="how-apim-proxy-server-responds-with-ssl-certificates-in-the-tls-handshake"></a>Come il server proxy APIM risponde con certificati SSL nell'handshake TLS

### <a name="clients-calling-with-sni-header"></a>Client che chiamano con l'intestazione SNI
Se il cliente ha uno o più domini personalizzati configurati per il proxy, APIM può rispondere alle richieste HTTPS sia dai domini personalizzati (ad esempio, contoso.com) sia dal dominio predefinito (ad esempio, apim-service-name.azure-api.net). In base alle informazioni disponibili nell'intestazione dell'indicazione nome server (SNI, Server Name Indication), APIM risponde con il certificato server appropriato.

### <a name="clients-calling-without-sni-header"></a>Client che chiamano senza intestazione SNI
Se il cliente usa invece un client, che non invia l'intestazione [SNI](https://tools.ietf.org/html/rfc6066#section-3), APIM crea le risposte in base alla logica seguente:

* Se il servizio ha un solo dominio personalizzato configurato per il proxy, il certificato predefinito è quello emesso per il dominio personalizzato del proxy.
* Se il servizio ha più domini personalizzati configurati per il proxy (scenario supportato solo nel livello **Premium**), il cliente può scegliere quale deve essere il certificato predefinito. Per impostare il certificato predefinito, la proprietà [defaultSslBinding](https://docs.microsoft.com/rest/api/apimanagement/apimanagementservice/createorupdate#hostnameconfiguration) deve essere impostata su true ("defaultSslBinding":"true"). Se il cliente non imposta questa proprietà, il certificato predefinito è quello emesso per il dominio predefinito del proxy ospitato in *.azure-api.net.

## <a name="support-for-putpost-request-with-large-payload"></a>Supporto per una richiesta PUT/POST con payload di grandi dimensioni

Il server proxy APIM supporta una richiesta con payload di grandi dimensioni se si usano certificati sul lato client in HTTPS (ad esempio, payload > 40 KB). Per impedire che la richiesta del server si blocchi, i clienti possono impostare la proprietà ["negotiateClientCertificate": "true"](https://docs.microsoft.com/rest/api/apimanagement/ApiManagementService/CreateOrUpdate#hostnameconfiguration) sul nome host del proxy. Se la proprietà è impostata su true, il certificato client viene richiesto al momento della connessione SSL/TLS, prima di qualsiasi scambio di richieste HTTP. Poiché l'impostazione viene applicata al livello del **nome host del proxy**, tutte le richieste di connessione richiedono il certificato client. Per superare questa limitazione, i clienti possono configurare fino a 20 domini personalizzati per il proxy (scenario supportato solo nel livello **Premium**).

