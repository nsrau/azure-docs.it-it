## <a name="what-is-reverse-dns"></a>Che cos'è il DNS inverso?

I record DNS convenzionali consentono un mapping da un nome DNS (ad esempio, "www.contoso.com") a un indirizzo IP (ad esempio, 64.4.6.100).  Il DNS inverso consente la riconversione di un indirizzo IP (64.4.6.100) in un nome ("www.contoso.com").

I record DNS inversi vengono usati in varie situazioni. I record DNS inversi, ad esempio, sono molto usati per combattere l'invio di posta indesiderata verificando il mittente di un messaggio di posta elettronica.  Il server di posta di destinazione recupererà il record DNS inverso dell'indirizzo IP del server di origine e verificherà che l'host sia autorizzato a inviare posta elettronica dal dominio di origine. Si noti tuttavia che [i servizi di calcolo di Azure non supportano l'invio di messaggi di posta elettronica a domini esterni](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/).

## <a name="how-reverse-dns-works"></a>Come funziona il DNS inverso

I record DNS inversi sono ospitati in speciali zone DNS, chiamate zone "ARPA".  Queste zone costituiscono una gerarchia DNS separata parallela alla normale gerarchia che ospita domini come "contoso.com".

Il record DNS "www.contoso.com", ad esempio, viene implementato usando un record "A" DNS con il nome "www" nella zona "contoso.com".  Questo record A fa riferimento all'indirizzo IP corrispondente, in questo caso 64.4.6.100.  La ricerca inversa viene implementata separatamente, usando un record "PTR" denominato "100" nella zona "6.4.64.in-addr.arpa". Si noti che gli indirizzi IP sono inversi nelle zone ARPA.  Questo record PTR, se è stato configurato correttamente, fa riferimento al nome "www.contoso.com".

Quando a un'organizzazione viene assegnato un blocco di indirizzi IP, acquisisce anche il diritto di gestire la zona ARPA corrispondente. Le zone ARPA corrispondenti ai blocchi di indirizzi IP usati da Azure vengono ospitate e gestite da Microsoft. L'ISP può ospitare per l'utente la zona ARPA per gli indirizzi IP o può consentire di ospitare la zona ARPA nel servizio DNS preferito, ad esempio DNS di Azure.

> [!NOTE]
> Le ricerche DNS dirette e le ricerche DNS inverse vengono implementate in gerarchie DNS separate parallele. La ricerca inversa per "www.contoso.com" **non** è ospitata nella zona "contoso.com", ma nella zona ARPA per il blocco di indirizzi IP corrispondente.

Per altre informazioni sul DNS inverso, vedere [Risoluzione DNS inversa](http://en.wikipedia.org/wiki/Reverse_DNS_lookup).

## <a name="azure-support-for-reverse-dns"></a>Supporto di Azure per DNS inverso

Azure supporta due scenari distinti correlati al DNS inverso:

1. Hosting della zona ARPA corrispondente al blocco di indirizzi IP.
2. Consenso di configurare il record DNS inverso per l'indirizzo IP assegnato al servizio di Azure.

Per supportare il primo, DNS di Azure può essere usato per ospitare le zone ARPA e gestire i record PTR per ogni ricerca DNS inversa.  Il processo di creazione della zona ARPA, configurazione della delega e configurazione dei record PTR è uguale a quello delle normali zone DNS.  Le sole differenze consistono nel fatto che la delega deve essere configurata tramite l'ISP invece che tramite il registrar DNS e che deve essere usato solo il tipo di record PTR.

Per supportare il secondo, Azure consente di configurare la ricerca inversa per gli indirizzi IP allocati al servizio.  Questa ricerca inversa viene configurata da Azure come record PTR nella zona ARPA corrispondente.  Queste zone ARPA, corrispondenti a tutti gli intervalli di IP usati da Azure, vengono ospitate da Microsoft. **La parte restante dell'articolo descrive questo scenario in dettaglio.**


<!--HONumber=Nov16_HO3-->


