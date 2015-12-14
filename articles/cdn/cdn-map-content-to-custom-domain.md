<properties 
	 pageTitle="Come eseguire il mapping del contenuto della rete per la distribuzione di contenuti (CDN) a un dominio personalizzato" 
	 description="Questo argomento descrive come eseguire il mapping del contenuto della rete CDN a un dominio personalizzato." 
	 services="cdn" 
	 documentationCenter="" 
	 authors="camsoper" 
	 manager="dwrede" 
	 editor=""/>
<tags 
	 ms.service="cdn" 
	 ms.workload="media" 
	 ms.tgt_pltfrm="na" 
	 ms.devlang="na" 
	 ms.topic="article" 
	 ms.date="12/02/2015" 
	 ms.author="casoper"/>

#Come eseguire il mapping di un dominio personalizzato all'endpoint della rete per la distribuzione di contenuti (rete CDN)

È possibile eseguire il mapping di un dominio personalizzato a un endpoint della rete CDN per usare il proprio nome di dominio negli URL del contenuto memorizzato nella cache anziché l'endpoint della rete CDN fornito automaticamente. A tale scopo, verrà creato un record CNAME con il registrar di dominio ed eseguito il mapping del dominio personalizzato e del sottodominio all'endpoint della rete CDN. Un record CNAME è una funzionalità DNS tramite cui viene eseguito il mapping di un dominio di origine a uno di destinazione. In questo caso, il dominio di origine corrisponde al dominio personalizzato e al relativo sottodominio (il sottodominio è sempre obbligatorio). Il dominio di destinazione è l'endpoint della rete CDN.

> [AZURE.NOTE]- Per eseguire il mapping del dominio all'endpoint della rete CDN, è necessario creare un record CNAME con il registrar di dominio. I record CNAME eseguono il mapping di sottodomini specifici come www.mydomain.com o myblog.mydomain.com. Non è possibile eseguire il mapping di un record CNAME a un dominio radice, come ad esempio mydomain.com. -Un sottodominio può essere associato solo a un endpoint della rete CDN. Il record CNAME creato instrada all'endpoint specificato tutto il traffico indirizzato al sottodominio. Ad esempio, se si associa www.mydomain.com all'endpoint della rete CDN, non è possibile associarlo ad altri endpoint di Azure, come un endpoint dell'account di archiviazione o del servizio cloud. Tuttavia, è possibile usare più sottodomini dello stesso dominio per endpoint di servizio diversi. È anche possibile eseguire il mapping di sottodomini diversi allo stesso endpoint della rete CDN.

Le procedure disponibili in questo argomento descrivono come:

-	[Registrare un dominio personalizzato per un endpoint della rete CDN di Azure](#subheading1)
-	[Verificare che il sottodominio personalizzato faccia riferimento all'endpoint della rete CDN](#subheading3) 

##<a name="subheading1"></a>Registrare un dominio personalizzato per un endpoint della rete CDN di Azure

1.	Accedere al [portale di Azure](http://portal.azure.com/).
2.	Fare clic su **Sfoglia**, quindi su **Profili della rete CDN**, poi sul profilo della rete CDN con l'endpoint su cui si desidera eseguire il mapping a un dominio personalizzato.  
3.	Nel pannello **profilo della rete CDN**, fare clic sull'endpoint della rete CDN a cui si desidera associare il sottodominio.
4.	Nella parte superiore del pannello dell’endpoint, fare clic sul pulsante **Aggiungi dominio personalizzato**. Nel pannello **Aggiungi un dominio personalizzato**verrà visualizzato il nome host, derivato dall'endpoint della rete CDN, da usare per la creazione di un nuovo record CNAME. Il formato dell'indirizzo del nome host apparirà come **&lt;EndpointName>.azureedge.net**. È possibile copiare questo nome host per usarlo per la creazione del record CNAME.  

5.	Passare al sito Web del registrar e individuare la sezione per la creazione di record DNS. Queste informazioni possono essere disponibili in una sezione come **Domain Name**, **DNS** o **Name Server Management**.
6.	Trovare la sezione per la gestione dei record CNAME. Potrebbe essere necessario passare a una pagina di impostazioni avanzate e cercare le parole CNAME, Alias o Subdomains.
7.	Creare un nuovo record CNAME per il mapping del sottodominio (ad esempio, **www** o **cdn**) al nome host fornito nel pannello **Aggiungi un dominio personalizzato**.
8.	Tornare al pannello **Aggiungi un dominio personalizzato** e immettere il dominio personalizzato, incluso il sottodominio, nella finestra di dialogo. Ad esempio, immettere il nome di dominio nel formato www.mydomain.com o cdn.mydomain.com.   

	Azure verificherà l'esistenza del record CNAME per il nome di dominio immesso. Se il record CNAME è corretto, il dominio personalizzato viene convalidato e può essere usato con il contenuto della rete CDN.

	Notare che in alcuni casi la propagazione del record CNAME nei server dei nomi in Internet può richiedere tempo. Se il dominio non viene convalidato immediatamente e si ritiene corretto il record CNAME, attendere alcuni minuti e quindi riprovare.

##<a name="subheading3"></a>Verificare che il sottodominio personalizzato faccia riferimento all'endpoint della rete CDN

-	Una volta completata la registrazione del dominio personalizzato, è possibile accedere al contenuto memorizzato nella cache nell'endpoint della rete CDN usando questo dominio personalizzato. Assicurarsi prima di tutto di disporre di contenuto pubblico memorizzato nella cache nell'endpoint. Ad esempio, se l'endpoint della rete CDN è associato a un account di archiviazione, la rete CDN memorizza nella cache il contenuto in contenitori BLOB pubblici. Per testare il dominio personalizzato, assicurarsi che il contenitore sia impostato in modo da consentire l'accesso pubblico e che contenga almeno un BLOB.
-	Nel browser passare all'indirizzo del BLOB usando il dominio personalizzato. Ad esempio, se il dominio personalizzato è **www.mydomain.com**, l'URL di un BLOB memorizzato nella cache sarà simile al seguente:  
	
		http://www.mydomain.com/mypubliccontainer/acachedblob.jpg
-	Se l'endpoint della rete CDN è associato a un servizio cloud, l'indirizzo del contenuto memorizzato nella cache sarà simile all'URL seguente:

		http://www.mydomain.com/mycloudservice

##Vedere anche


[Come abilitare la rete per la distribuzione di contenuti (rete CDN) per Azure](./cdn-create-new-endpoint.md)

 

<!---HONumber=AcomDC_1203_2015-->