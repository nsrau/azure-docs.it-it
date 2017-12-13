>[!NOTE]
>Per le risorse non fisse è possibile richiedere l'aumento delle quote aprendo un ticket di supporto. **Non** creare altri account di Servizi multimediali di Azure per provare a ottenere limiti superiori.

| Risorsa | Limite predefinito | 
| --- | --- | 
| Account di Servizi multimediali di Azure in una singola sottoscrizione | 25 (fisso) |
| Unità riservate multimediali per account AMS |25 (S1, S2)<br/>10 (S3) <sup>(1)</sup> | 
| Processi per ogni account di Servizi multimediali di Azure | 50.000<sup>(2)</sup> |
| Attività concatenate per processo | 30 (fisso) |
| Asset per ogni account di Servizi multimediali di Azure | 1.000.000|
| Asset per attività | 50 |
| Asset per processo | 100 |
| Localizzatori univoci associati a un asset contemporaneamente | 5<sup>(4)</sup> |
| Canali live per ogni account AMS  |5|
| Programmi con stato arrestato per canale  |50|
| Programmi con stato in esecuzione per canale  |3|
| Endpoint di streaming con stato in esecuzione per ogni account AMS |2|
| Unità di streaming per endpoint di streaming  |10 |
| Account di archiviazione | 1.000<sup>(5)</sup> (fisso) |
| Criteri | 1.000.000<sup>(6)</sup> |
| Dimensioni complete| In alcuni scenari è previsto un limite per le dimensioni massime dei file supportate per l'elaborazione in Servizi multimediali. <sup>7</sup> |
  
<sup>1</sup> Le unità riservate S3 non sono disponibili in India occidentale. Se si modifica il tipo, ad esempio da S2 a S1, i limiti per le UR vengono reimpostati.

<sup>2</sup> Questo numero include i processi in coda, terminati, attivi e annullati. Non include invece i processi eliminati. È possibile eliminare i processi obsoleti usando **IJob.Delete** o la richiesta HTTP **DELETE**.

A partire dal 1° aprile 2017, tutti i record di processo presenti nell'account e più vecchi di 90 giorni verranno eliminati automaticamente, insieme ai record attività associati, anche se il numero totale di record è inferiore alla quota massima. Se è necessario archiviare le informazioni sul processo o sull'attività, è possibile usare il codice descritto [qui](../articles/media-services/media-services-dotnet-manage-entities.md).

<sup>3</sup> L'elenco di entità Job restituite può includere al massimo 1.000 voci per richiesta. Per tenere traccia di tutte le entità Job inviate, si possono usare le opzioni top/skip descritte nell'argomento [Opzioni query di sistema OData](http://msdn.microsoft.com/library/gg309461.aspx).

<sup>4</sup> I localizzatori non sono progettati per gestire il controllo di accesso per utente. Per assegnare a singoli utenti diritti di accesso diversi, è possibile usare soluzioni DRM (Digital Rights Management). Per altre informazioni, vedere [questa](../articles/media-services/media-services-content-protection-overview.md) sezione.

<sup>5</sup> Gli account di archiviazione devono appartenere alla stessa sottoscrizione di Azure.

<sup>6</sup> È previsto un limite di 1.000.000 criteri per i diversi criteri di servizi multimediali di Azure (ad esempio, per i criteri Locator o ContentKeyAuthorizationPolicy). 

>[!NOTE]
> Se si utilizzano sempre gli stessi giorni, autorizzazioni di accesso e così via, è necessario utilizzare lo stesso ID criterio. Per informazioni e un esempio, vedere [questa](../articles/media-services/media-services-dotnet-manage-entities.md#limit-access-policies) sezione.

<sup>7</sup>Se si caricano contenuti in un asset in Servizi multimediali di Azure per elaborarli con uno dei processori di contenuti multimediali del servizio, ad esempio codificatori come Media Encoder Standard e il flusso di lavoro Premium del codificatore multimediale oppure motori di analisi come Face Detector, è necessario essere consapevoli dei vincoli relativi alle dimensioni massime dei file supportate. 

Le dimensioni massime supportate per un singolo BLOB sono 5 TB in Archiviazione BLOB di Azure. Vengono tuttavia applicati altri limiti in Servizi multimediali di Azure in base alle dimensioni delle macchine virtuali usate dal servizio. La tabella seguente mostra i limiti per ciascuna Media Reserved Unit (S1, S2, S3). Se le dimensioni del file di origine superano i limiti definiti nella tabella, il processo di codifica avrà esito negativo. Se si esegue la codifica di origini risoluzione 4K di lunga durata, è necessario usare Media Reserved Unit S3 per ottenere le prestazioni necessarie. Se le dimensioni dei contenuti 4K superano il limite di 260 GB su Media Reserved Unit S3, scrivere a amshelp@microsoft.com per delle possibili soluzioni che supportino questo scenario.

| Unità riservata multimediale | Dimensioni massime input (GB)| 
| --- | --- | 
|S1 | 325|
|S2 | 640|
|S3 | 260|
