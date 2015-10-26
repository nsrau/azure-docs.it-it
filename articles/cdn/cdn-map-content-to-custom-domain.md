<properties 
	 pageTitle="Come eseguire il mapping del contenuto della rete per la distribuzione di contenuti (CDN) a un dominio personalizzato" 
	 description="Questo argomento descrive come eseguire il mapping del contenuto della rete CDN a un dominio personalizzato." 
	 services="cdn" 
	 documentationCenter="" 
	 authors="zhangmanling" 
	 manager="dwrede" 
	 editor=""/>
<tags 
	 ms.service="cdn" 
	 ms.workload="media" 
	 ms.tgt_pltfrm="na" 
	 ms.devlang="na" 
	 ms.topic="article" 
	 ms.date="09/01/2015" 
	 ms.author="mazha"/>

#Come eseguire il mapping di un dominio personalizzato all'endpoint della rete per la distribuzione di contenuti (rete CDN)

È possibile eseguire il mapping di un dominio personalizzato a un endpoint della rete CDN per usare il proprio nome di dominio negli URL del contenuto memorizzato nella cache anziché l'endpoint della rete CDN fornito automaticamente.

Per eseguire il mappping del dominio personalizzato a un endpoint della rete CDN, sono disponibili due modi.

1. **Creare un record CNAME con il registrar ed eseguire il mapping del dominio personalizzato e del sottodominio all'endpoint della rete CDN** 
	
	Un record CNAME è una funzionalità DNS tramite cui viene eseguito il mapping di un dominio di origine a uno di destinazione. In questo caso, il dominio di origine corrisponde al dominio personalizzato e al relativo sottodominio (il sottodominio è sempre obbligatorio). Il dominio di destinazione è l'endpoint della rete CDN.

	Il processo di mapping del dominio personalizzato all'endpoint della rete CDN può tuttavia comportare un breve periodo di inattività del dominio durante la sua registrazione nel portale di gestione di Azure. 
2. **Aggiungere un passaggio di registrazione intermedio con cdnverify di Azure**

	Se il dominio personalizzato supporta attualmente un'applicazione con un contratto di servizio in base al quale non sono previsti tempi di inattività, è possibile usare il sottodominio **cdnverify** di Azure per fornire un passaggio di registrazione intermedio, in modo che gli utenti possano accedere al dominio durante il mapping DNS.

> AZURE.NOTE
> 
-	Per eseguire il mapping del dominio all'endpoint della rete CDN, è necessario creare un record CNAME con il registrar. I record CNAME eseguono il mapping di sottodomini specifici come www.mydomain.com o myblog.mydomain.com. Non è possibile eseguire il mapping di un record CNAME a un dominio radice come mydomain.com.
-	Un sottodominio può essere associato a un solo endpoint della rete CDN. Il record CNAME creato instrada all'endpoint specificato tutto il traffico indirizzato al sottodominio. Ad esempio, se si associa www.mydomain.com all'endpoint della rete CDN, non è possibile associarlo ad altri endpoint di Azure, come un endpoint dell'account di archiviazione o del servizio cloud. Tuttavia, è possibile usare più sottodomini dello stesso dominio per endpoint di servizio diversi. È anche possibile eseguire il mapping di sottodomini diversi allo stesso endpoint della rete CDN.

Le procedure disponibili in questo argomento descrivono come:

-	[Registrare un dominio personalizzato per un endpoint della rete CDN di Azure](#subheading1)
-	[Registrare un dominio personalizzato per un endpoint della rete CDN di Azure usando il sottodominio cdnverify intermedio](#subheading2)
-	[Verificare che il sottodominio personalizzato faccia riferimento all'endpoint della rete CDN](#subheading3) 

##<a name="subheading1"></a>Registrare un dominio personalizzato per un endpoint della rete CDN di Azure

1.	Accedere al [portale di gestione di Azure](http://manage.windowsazure.com/).
2.	Nel pannello di navigazione fare clic su **Rete CDN**.
3.	Nella visualizzazione elenco fare clic sul nome dell'endpoint della rete CDN a cui si vuole associare il sottodominio per passare alla pagina dei dettagli per l'endpoint.
4.	Sulla barra multifunzione, fare clic su **Gestisci domini** per visualizzare la finestra di dialogo **Gestisci domini personalizzati**. Nel testo della finestra di dialogo verrà visualizzato il nome host, derivato dall'endpoint della rete CDN, da usare per la creazione di un nuovo record CNAME. Il formato dell'indirizzo del nome host apparirà come **az#####.vo.msecnd.net** (dove **az#####** è l’identificatore dell’endpoint della rete CDN). È possibile copiare questo nome host per usarlo per la creazione del record CNAME. Per questa procedura, ignorare il testo che fa riferimento al sottodominio **cdnverify**.
5.	Passare al sito Web del registrar e individuare la sezione per la creazione di record DNS. Queste informazioni possono essere disponibili in una sezione come **Domain Name**, **DNS** o **Name Server Management**.
6.	Trovare la sezione per la gestione dei record CNAME. Potrebbe essere necessario passare a una pagina di impostazioni avanzate e cercare le parole CNAME, Alias o Subdomains.
7.	Creare un nuovo record CNAME per il mapping del sottodominio (ad esempio, **www** o **cdn**) al nome host fornito nella finestra di dialogo **Gestisci domini personalizzati**.
8.	Tornare alla finestra di dialogo **Gestisci domini personalizzati** e immettere il dominio personalizzato, incluso il sottodominio. Ad esempio, immettere il nome di dominio nel formato www.mydomain.com o cdn.mydomain.com.   

	Azure verificherà l'esistenza del record CNAME per il nome di dominio immesso. Se il record CNAME è corretto, il dominio personalizzato viene convalidato e può essere usato con il contenuto della rete CDN.

	Notare che in alcuni casi la propagazione del record CNAME nei server dei nomi in Internet può richiedere tempo. Se il dominio non viene convalidato immediatamente e si ritiene corretto il record CNAME, attendere alcuni minuti e quindi riprovare.

##<a name="subheading2"></a>Registrare un dominio personalizzato per un endpoint della rete CDN di Azure usando il sottodominio cdnverify intermedio  


1.	Accedere al [portale di gestione di Azure](http://manage.windowsazure.com/).
2.	Nel pannello di navigazione fare clic su **Rete CDN**.
3.	Nella visualizzazione elenco fare clic sul nome dell'endpoint della rete CDN a cui si vuole associare il sottodominio per passare alla pagina dei dettagli per l'endpoint.
4.	Sulla barra multifunzione, fare clic su **Gestisci domini** per visualizzare la finestra di dialogo **Gestisci domini personalizzati**. Nel testo della finestra di dialogo verrà visualizzato il nome host, derivato dall'endpoint della rete CDN, da usare per la creazione di un nuovo record CNAME mediante il sottodominio **cdnverify** intermedio. Il formato dell'indirizzo del nome host apparirà come **cdnverify.az#####.vo.msecnd.net**. È possibile copiare questo nome host per usarlo per la creazione del record CNAME.
5.	Passare al sito Web del registrar e individuare la sezione per la creazione di record DNS. Queste informazioni possono essere disponibili in una sezione come **Domain Name**, **DNS** o **Name Server Management**.
6.	Individuare la sezione per la gestione dei record CNAME. Può essere necessario passare in una pagina di impostazioni avanzate e cercare il termine **CNAME**, **Alias** o **Subdomains**.
7.	Creare un nuovo record CNAME e specificare un alias di sottodominio che includa il sottodominio **cdnverify**. Ad esempio, il formato del sottodominio specificato sarà **cdnverify.www** o **cdnverify.cdn**. Specificare quindi il nome host, che è l’endpoint della rete CDN, nel fomato **cdnverify.az#####.vo.msecnd.net** (dove **az#####** è l’identificatore dell’endpoint della rete CDN). Il formato del nome host viene definito automaticamente nella finestra di dialogo **Gestisci domini personalizzati**.
8.	Tornare alla finestra di dialogo **Gestisci domini personalizzati** e immettere il dominio personalizzato, incluso il sottodominio. Ad esempio, immettere il nome di dominio nel formato **www.mydomain.com** o **cdn.mydomain.com**. Notare che in questo passaggio non è necessario anteporre **cdnverify** al sottodominio.  

	Azure verificherà l'esistenza del record CNAME per il nome di dominio cdnverify immesso.
9.	A questo punto, il dominio personalizzato è stato verificato da Azure, ma il traffico verso il dominio non viene ancora instradato all'endpoint della rete CDN. Per completare il processo, tornare al sito Web del registrar DNS e creare un altro record CNAME per il mapping del sottodominio all'endpoint della rete CDN. Ad esempio, specificare il sottodominio come **www** o **cdn**, e il nome host come **az#####.vo.msecnd.net**. Con questo passaggio viene completata la registrazione del dominio personalizzato. 
10.	Infine, è possibile eliminare il record CNAME creato usando **cdnverify**, in quanto è stato necessario solo come passaggio intermedio.  


##<a name="subheading3"></a>Verificare che il sottodominio personalizzato faccia riferimento all'endpoint della rete CDN

-	Una volta completata la registrazione del dominio personalizzato, è possibile accedere al contenuto memorizzato nella cache nell'endpoint della rete CDN usando questo dominio personalizzato. Assicurarsi prima di tutto di disporre di contenuto pubblico memorizzato nella cache nell'endpoint. Ad esempio, se l'endpoint della rete CDN è associato a un account di archiviazione, la rete CDN memorizza nella cache il contenuto in contenitori BLOB pubblici. Per testare il dominio personalizzato, assicurarsi che il contenitore sia impostato in modo da consentire l'accesso pubblico e che contenga almeno un BLOB.
-	Nel browser passare all'indirizzo del BLOB usando il dominio personalizzato. Ad esempio, se il dominio personalizzato è **www.mydomain.com**, l'URL di un BLOB memorizzato nella cache sarà simile al seguente:  
	
		http://www.mydomain.com/mypubliccontainer/acachedblob.jpg
-	Se l'endpoint della rete CDN è associato a un servizio cloud, l'indirizzo del contenuto memorizzato nella cache sarà simile all'URL seguente:

		http://www.mydomain.com/cdn/mycloudservice

##Vedere anche


[Come abilitare la rete per la distribuzione di contenuti (rete CDN) per Azure](./cdn-create-new-endpoint.md)

 

<!---HONumber=Oct15_HO3-->