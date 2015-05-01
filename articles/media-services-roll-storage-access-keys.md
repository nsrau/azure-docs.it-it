<properties 
	pageTitle="Aggiornamento di Servizi multimediali dopo il rollover delle chiavi di accesso alle risorse di archiviazione" 
	description="Questo articolo fornisce informazioni sulle modalità per aggiornare Servizi multimediali dopo aver eseguito il rollover delle chiavi di accesso alle risorse di archiviazione." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/01/2015" 
	ms.author="juliako"/>

# Procedura: Aggiornare Servizi multimediali dopo il rollover delle chiavi di accesso alle risorse di archiviazione

Quando si crea un nuovo account di Servizi multimediali di Azure, viene chiesto di selezionare anche un account di archiviazione di Azure da usare per l'archiviazione dei contenuti multimediali. È possibile aggiungere più di un account di archiviazione all'account di Servizi multimediali.

Quando viene creato un nuovo account di archiviazione, Azure genera due chiavi di accesso a 512 bit alle risorse di archiviazione, che consentono di autenticare l'accesso all'account di archiviazione. Per mantenere le connessioni di archiviazione più sicure, si consiglia di rigenerare e far ruotare periodicamente la chiave di accesso alle risorse di archiviazione. Per non perdere mai la connessione all'account di archiviazione, vengono fornite due chiavi di accesso (primaria e secondaria), in modo da poter usare la prima mentre si rigenera la seconda. Questa procedura viene anche denominata "rollover delle chiavi di accesso".

Servizi multimediali presenta una dipendenza da una delle chiavi di archiviazione (primaria o secondaria). In particolare, a dipendere dalla chiave di accesso sono i localizzatori usati per trasmettere in streaming o scaricare gli asset. Quando si esegue il rollover delle chiavi di accesso alle risorse di archiviazione, quindi, è necessario aggiornare anche i localizzatori, in modo da evitare qualsiasi interruzione del servizio di streaming.

>[AZURE.NOTE]Dopo aver rigenerato una chiave di archiviazione, è necessario sincronizzare l'aggiornamento con Servizi multimediali. 

Questo argomento descrive le procedure da eseguire per effettuare il rollover delle chiavi di archiviazione e aggiornare Servizi multimediali per l'uso della chiave di archiviazione appropriata. Se si dispone di più account di archiviazione, è necessario eseguire questa procedura per ogni account di archiviazione.

>[AZURE.NOTE]Prima di eseguire la procedura descritta in questo argomento su un account di produzione, effettuarne il test in un account di pre-produzione.


## Passaggio 1: Rigenerare la chiave di accesso alle risorse di archiviazione secondaria

Iniziare con la rigenerazione della chiave di archiviazione secondaria. Per impostazione predefinita, infatti, la chiave secondaria non viene usata da Servizi multimediali.  Per informazioni sul rollover delle chiavi di archiviazione, vedere [Procedura: Visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
  
## <a id="step2"></a>Passaggio 2:  Aggiornare Servizi multimediali per l'uso della nuova chiave di archiviazione secondaria

Aggiornare Servizi multimediali per l'uso della chiave di accesso alle risorse di archiviazione secondaria. Per sincronizzare la chiave di archiviazione rigenerata con Servizi multimediali è possibile usare uno dei due seguenti metodi.

- Usare il portale di Azure: selezionare l'account di Servizi multimediali e fare clic sull'icona "GESTISCI CHIAVI" nella parte inferiore della finestra del portale. A seconda della chiave di archiviazione che si desidera sincronizzare con Servizi multimediali, selezionare il pulsante relativo alla sincronizzazione con la chiave primaria o secondaria. In questo caso, usare la chiave secondaria.

- Usare l'API REST di gestione di Servizi multimediali. 

	Il seguente esempio di codice mostra come creare la richiesta https://endpoint/<subscriptionId>/services/mediaservices/Accounts/<accountName>/StorageAccounts/<storageAccountName>/Key per sincronizzare la chiave di archiviazione specificata con Servizi multimediali. In questo caso, viene usato il valore relativo alla chiave di archiviazione secondaria. Per altre informazioni, vedere [Procedura: Utilizzare l'API REST di gestione dei servizi multimediali](http://msdn.microsoft.com/library/azure/dn167656.aspx).
 
		public void UpdateMediaServicesWithStorageAccountKey(string mediaServicesAccount, string storageAccountName, string storageAccountKey)
		{
		    var clientCert = GetCertificate(CertThumbprint);
		
		    HttpWebRequest request = (HttpWebRequest)WebRequest.Create(string.Format("{0}/{1}/services/mediaservices/Accounts/{2}/StorageAccounts/{3}/Key",
		        Endpoint, SubscriptionId, mediaServicesAccount, storageAccountName));
		    request.Method = "PUT";
		    request.ContentType = "application/json; charset=utf-8";
		    request.Headers.Add("x-ms-version", "2011-10-01");
		    request.Headers.Add("Accept-Encoding: gzip, deflate");
		    request.ClientCertificates.Add(clientCert);
		
		
		    using (var streamWriter = new StreamWriter(request.GetRequestStream()))
		    {
		        streamWriter.Write("\"");
		        streamWriter.Write(storageAccountKey);
		        streamWriter.Write("\"");
		        streamWriter.Flush();
		    }
		
		    using (var response = (HttpWebResponse)request.GetResponse())
		    {
		        string jsonResponse;
		        Stream receiveStream = response.GetResponseStream();
		        Encoding encode = Encoding.GetEncoding("utf-8");
		        if (receiveStream != null)
		        {
		            var readStream = new StreamReader(receiveStream, encode);
		            jsonResponse = readStream.ReadToEnd();
		        }
		    }
		}

Aggiornare quindi i localizzatori esistenti (che presentano una dipendenza dalla chiave di archiviazione precedente).

>[AZURE.NOTE]Attendere 30 minuti prima di eseguire qualsiasi operazione con Servizi multimediali (ad esempio, creare nuovi localizzatori), in modo da evitare qualsiasi interferenza con i processi in corso.

## Passaggio 3: Aggiornare i localizzatori 

Dopo 30 minuti è possibile aggiornare i localizzatori esistenti in modo che acquisiscano la dipendenza dalla nuova chiave di archiviazione secondaria.  

Per aggiornare la data di scadenza di un localizzatore, è possibile usare API [REST](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator ) o [.NET](http://go.microsoft.com/fwlink/?LinkID=533259). Si noti che quando si aggiorna la data di scadenza di un localizzatore di firma di accesso condiviso, l'URL viene modificato. 

## Passaggio 5: Rigenerare la chiave di accesso alle risorse di archiviazione primaria

Rigenerare la chiave di accesso alle risorse di archiviazione primaria. Per informazioni sul rollover delle chiavi di archiviazione, vedere [Procedura: Visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

## Passaggio 6: Aggiornare Servizi multimediali per l'uso della nuova chiave di archiviazione primaria
	
Usare la stessa procedura descritta nel [passaggio 2](media-services-roll-storage-access-keys.md#step2), questa volta sincronizzando con l'account di Servizi multimediali la nuova chiave di accesso alle risorse di archiviazione primaria.

>[AZURE.NOTE]Attendere 30 minuti prima di eseguire qualsiasi operazione con Servizi multimediali (ad esempio, creare nuovi localizzatori), in modo da evitare qualsiasi interferenza con i processi in corso.

## Passaggio 7: Aggiornare i localizzatori  

Dopo 30 minuti è possibile aggiornare i localizzatori esistenti in modo che acquisiscano la dipendenza dalla nuova chiave di archiviazione primaria.  

Per aggiornare la data di scadenza di un localizzatore, è possibile usare API [REST](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator ) o [.NET](http://go.microsoft.com/fwlink/?LinkID=533259). Si noti che quando si aggiorna la data di scadenza di un localizzatore di firma di accesso condiviso, l'URL viene modificato. 

 


<!--HONumber=52-->