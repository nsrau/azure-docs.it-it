Se si dispone di un URL di firma di accesso condiviso che concede l'accesso alle risorse in un account di archiviazione, è possibile usare la firma di accesso condiviso in una stringa di connessione. Poiché la firma di accesso condiviso include nell'URI le informazioni necessarie per autenticare la richiesta, l'URI di firma di accesso condiviso fornisce il protocollo, l'endpoint di servizio e le credenziali necessarie per accedere alla risorsa.

Per creare una stringa di connessione che include una firma di accesso condiviso, specificare la stringa nel seguente formato:

```
BlobEndpoint=myBlobEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
FileEndpoint=myFileEndpoint;
SharedAccessSignature=sasToken
```

Ogni endpoint di servizio è facoltativo anche se la stringa di connessione deve contenerne almeno uno.

> [!NOTE]
> La procedura consigliata prevede l'uso di HTTPS con la firma di accesso condiviso.
>
> Se si specifica una firma di accesso condiviso in una stringa di connessione all'interno di un file di configurazione, potrebbe essere necessario codificare caratteri speciali nell'URL.
>
>

### <a name="service-sas-example"></a>Esempio di firma di accesso condiviso del servizio
Ecco un esempio di stringa di connessione che include una firma di accesso condiviso del servizio per l'archiviazione BLOB:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;SharedAccessSignature=sv=2015-04-05&sr=b&si=tutorial-policy-635959936145100803&sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D
```

Ecco invece un esempio della stessa stringa di connessione con la codifica dei caratteri speciali:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;SharedAccessSignature=sv=2015-04-05&amp;sr=b&amp;si=tutorial-policy-635959936145100803&amp;sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D
```

### <a name="account-sas-example"></a>Esempio di firma di accesso condiviso dell'account
Ecco un esempio di stringa di connessione che include una firma di accesso condiviso dell'account per l'archivio BLOB e file. Si noti che sono specificati gli endpoint per entrambi i servizi:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
FileEndpoint=https://storagesample.file.core.windows.net;
SharedAccessSignature=sv=2015-07-08&sig=iCvQmdZngZNW%2F4vw43j6%2BVz6fndHF5LI639QJba4r8o%3D&spr=https&st=2016-04-12T03%3A24%3A31Z&se=2016-04-13T03%3A29%3A31Z&srt=s&ss=bf&sp=rwl
```

Ecco invece un esempio della stessa stringa di connessione con la codifica dell'URL:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
FileEndpoint=https://storagesample.file.core.windows.net;
SharedAccessSignature=sv=2015-07-08&amp;sig=iCvQmdZngZNW%2F4vw43j6%2BVz6fndHF5LI639QJba4r8o%3D&amp;spr=https&amp;st=2016-04-12T03%3A24%3A31Z&amp;se=2016-04-13T03%3A29%3A31Z&amp;srt=s&amp;ss=bf&amp;sp=rwl
```



<!--HONumber=Nov16_HO3-->


