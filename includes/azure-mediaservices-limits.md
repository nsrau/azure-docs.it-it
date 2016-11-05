| Risorsa | Limite predefinito | Limite massimo |
| --- | --- | --- |
| Account di Servizi multimediali di Azure in una singola sottoscrizione | |25 |
| Asset per ogni account di Servizi multimediali di Azure | |1\.000.000<sup>1</sup> |
| Attività concatenate per processo | |30 |
| Asset per attività | |50 |
| Asset per processo | |100 |
| Processi per ogni account di Servizi multimediali di Azure | |50\.000<sup>2</sup> |
| Localizzatori univoci associati a un asset contemporaneamente | |5<sup>4</sup> |
| Canali live per ogni account AMS </p></td> |5</p></td> |N/D<sup>1</sup> |
| Programmi con stato arrestato per canale </p></td> |50</p></td> |N/D<sup>1</sup> |
| Programmi con stato in esecuzione per canale </p></td> |3</p></td> |3 |
| Endpoint di streaming con stato in esecuzione per ogni account AMS </p></td> |2</p></td> |N/D<sup>1</sup> |
| Unità di streaming per endpoint di streaming </p></td> |10 </p></td> |N/D<sup>1</sup> |
| Unità di codifica per ogni account AMS </p></td> |25</p></td> |N/D<sup>1</sup> |
| Account di archiviazione | |1\.000<sup>5</sup> |
| Criteri | |1\.000.000<sup>6</sup> |

<sup>1</sup> È possibile richiedere l'aggiornamento dei limiti per questa quota aprendo un ticket di supporto. Non creare altri account di Servizi multimediali di Azure per aumentare i limiti, ma inviare invece un ticket di supporto.

<sup>2</sup> Questo numero include i processi in coda, terminati, attivi e annullati. Non include invece i processi eliminati. È possibile eliminare i processi obsoleti usando **IJob.Delete** o la richiesta HTTP **DELETE**.

<sup>3</sup> L'elenco di entità Job restituite può includere al massimo 1.000 voci per richiesta. Per tenere traccia di tutte le entità Job inviate, si possono usare le opzioni top/skip descritte nell'argomento [Opzioni query di sistema OData](http://msdn.microsoft.com/library/gg309461.aspx).

<sup>4</sup> I localizzatori non sono progettati per gestire il controllo di accesso per utente. Per assegnare a singoli utenti diritti di accesso diversi, è possibile usare soluzioni DRM (Digital Rights Management).

<sup>5</sup> Gli account di archiviazione devono appartenere alla stessa sottoscrizione di Azure.

<sup>6</sup> è previsto un limite di 1.000.000 criteri per i diversi criteri di servizi multimediali di Azure (ad esempio, per i criteri Locator o ContentKeyAuthorizationPolicy). Se si utilizzano sempre gli stessi giorni, autorizzazioni di accesso e così via, è necessario utilizzare lo stesso ID criterio.

<!---HONumber=AcomDC_0720_2016-->