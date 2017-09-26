Con l'archiviazione con ridondanza geografica (GRS) i dati vengono replicati in un'area secondaria a centinaia di chilometri di distanza dall'area primaria. Se per l'account di archiviazione è stata abilitata l'archiviazione con ridondanza geografica, la durabilità dei dati è assicurata anche in caso di un'interruzione completa dell'alimentazione locale o in situazioni di emergenza in cui l'area primaria non è recuperabile.

Per un account di archiviazione con l'archiviazione con ridondanza geografica abilitata, il commit di un aggiornamento viene eseguito prima nell'area primaria, dove viene replicato per tre volte. Quindi l'aggiornamento viene replicato in modo asincrono nell'area secondaria, dove viene inoltre replicato tre volte.

Con l'archiviazione con ridondanza geografica, entrambe le aree primaria e secondaria gestiscono repliche tra domini di errore e domini di aggiornamento separati all'interno di un'unità di scala di archiviazione, come descritto per l'archiviazione con ridondanza locale.

Considerazioni:

* Poiché la replica asincrona implica un ritardo, in caso di un'emergenza a livello di area è possibile che le modifiche non ancora replicate nell'area secondaria vadano perse se non è possibile recuperare i dati dall'area primaria.
* La replica non è disponibile a meno che Microsoft non avvii il failover all'area secondaria. Se Microsoft avvia un failover nell'area secondaria, al termine del failover si avrà accesso in lettura e scrittura a tali dati. Per altre informazioni, vedere le [indicazioni sul ripristino di emergenza](../articles/storage/common/storage-disaster-recovery-guidance.md). 
* Per consentire a un'applicazione la lettura dall'area secondaria, l'utente deve abilitare l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS).

L'area primaria viene selezionata durante la creazione di un account di archiviazione. L'area secondaria viene invece determinata in base a quella primaria e non è possibile modificarla. Nella tabella seguente vengono illustrate le associazioni di aree primarie e secondarie:

| Primario | Secondario |
| --- | --- |
| Stati Uniti centro-settentrionali | Stati Uniti centro-meridionali |
| Stati Uniti centro-meridionali | Stati Uniti centro-settentrionali |
| Stati Uniti orientali | Stati Uniti occidentali |
| Stati Uniti occidentali | Stati Uniti orientali |
| Stati Uniti orientali 2 | Stati Uniti centrali |
| Stati Uniti centrali | Stati Uniti orientali 2 |
| Europa settentrionale | Europa occidentale |
| Europa occidentale | Europa settentrionale |
| Asia sudorientale | Asia orientale |
| Asia orientale | Asia sudorientale |
| Cina orientale | Cina settentrionale |
| Cina settentrionale | Cina orientale |
| Giappone orientale | Giappone occidentale |
| Giappone occidentale | Giappone orientale |
| Brasile meridionale | Stati Uniti centro-meridionali |
| Australia orientale | Australia sudorientale |
| Australia sudorientale | Australia orientale |
| India meridionale | India centrale |
| India centrale | India meridionale |
| India occidentale | India meridionale |
| Governo degli Stati Uniti - Iowa | Governo degli Stati Uniti - Virginia |
| Governo degli Stati Uniti - Virginia | Governo degli Stati Uniti - Texas |
| Governo degli Stati Uniti - Texas | Governo degli Stati Uniti - Arizona |
| Governo degli Stati Uniti - Arizona | Governo degli Stati Uniti - Texas |
| Canada centrale | Canada orientale |
| Canada orientale | Canada centrale |
| Regno Unito occidentale | Regno Unito meridionale |
| Regno Unito meridionale | Regno Unito occidentale |
| Germania centrale | Germania nord-orientale |
| Germania nord-orientale | Germania centrale |
| Stati Uniti occidentali 2 | Stati Uniti centro-occidentali |
| Stati Uniti centro-occidentali | Stati Uniti occidentali 2 |

Per informazioni aggiornate sulle aree supportate da Azure, vedere [Aree di Azure](https://azure.microsoft.com/regions/).

>[!NOTE]  
> L'area secondaria di US Gov Virginia è US Gov Texas. In precedenza, US Gov Virginia usava come area secondaria US Gov Iowa. È in corso la migrazione degli account di archiviazione che usano ancora US Gov Iowa all'area secondaria US Gov Texas. 
> 
> 