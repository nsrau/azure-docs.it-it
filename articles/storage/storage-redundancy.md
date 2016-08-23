<properties
	pageTitle="Replica di Archiviazione di Azure | Microsoft Azure"
	description="I dati nell'account di archiviazione di Microsoft Azure vengono replicati per durabilità e disponibilità elevata. Le opzioni di replica includono archiviazione con ridondanza locale (LRS), archiviazione con ridondanza della zona (ZRS), archiviazione con ridondanza geografica (GRS) e archiviazione con ridondanza geografica e accesso in lettura (RA-GRS)."
	services="storage"
	documentationCenter=""
	authors="tamram"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/03/2016"
	ms.author="tamram"/>  

# Replica di Archiviazione di Azure

I dati dell'account di archiviazione di Microsoft Azure vengono sempre replicati per assicurarne durabilità e disponibilità elevata, rispettando il [contratto di servizio di Archiviazione di Azure](https://azure.microsoft.com/support/legal/sla/storage) anche in caso di errori hardware temporanei.

Quando si crea un account di archiviazione, è necessario selezionare una delle opzioni di replica seguenti:

- [Archiviazione con ridondanza locale (LRS)](#locally-redundant-storage)
- [Archiviazione con ridondanza della zona (ZRS).](#zone-redundant-storage)
- [Archiviazione con ridondanza geografica (GRS)](#geo-redundant-storage)
- [Archiviazione con ridondanza geografica e accesso in lettura (RA-GRS).](#read-access-geo-redundant-storage)

Nella tabella seguente è riportata una breve panoramica delle differenze tra i vari tipi di archiviazione, mentre nelle sezioni successive verranno forniti altri dettagli su ogni tipo di replica.


| Strategia di replica | Archiviazione con ridondanza locale | ZRS | Archiviazione con ridondanza geografica | RA-GRS |
|:----------------------------------------------------------------------------------|:---|:---|:---|:------|
| I dati vengono replicati in più strutture | No | Sì | Sì | Sì |
| I dati possono essere letti dalla posizione secondaria oltre che da quella primaria | No | No | No | Sì |
| Numero di copie di dati mantenute in nodi distinti | 3 | 3 | 6 | 6 |

Per informazioni sui prezzi delle varie opzioni di ridondanza, vedere [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/).

## Archiviazione con ridondanza locale

Con l'archiviazione con ridondanza locale (LRS), i dati vengono replicati all'interno dell'area in cui è stato creato l'account di archiviazione. Per massimizzare la durabilità, ogni richiesta effettuata per i dati nell'account di archiviazione viene replicata per tre volte. Queste tre repliche risiedono ognuna in domini di errore e domini di aggiornamento distinti. Un dominio di errore è un gruppo di nodi che rappresentano un'unità di errore fisica e possono essere considerati come nodi appartenenti allo stesso rack fisico. Un dominio di aggiornamento è un gruppo di nodi che vengono aggiornati contemporaneamente durante il processo di aggiornamento del servizio (implementazione). Le tre repliche vengono distribuite tra domini di aggiornamento e domini di errore per assicurare la disponibilità dei dati anche in caso di errori hardware che influiscono su un singolo rack e quando i nodi vengono aggiornati durante un'implementazione. Una richiesta viene restituita correttamente dopo che è stata scritta in tutte e tre le repliche.

Mentre per la maggior parte delle applicazioni è consigliabile adottare l'archiviazione con ridondanza geografica, l'archiviazione con ridondanza locale può essere la soluzione ideale in determinati scenari:

- Si tratta di una soluzione meno costosa dell'archiviazione con ridondanza geografica e offre inoltre una velocità effettiva più elevata. Se l'applicazione archivia dati che possono essere ricostruiti facilmente, è consigliabile scegliere l'archiviazione con ridondanza locale.

- Per alcune applicazioni è necessario replicare i dati solo all'interno di un'unica area a causa di requisiti di governance dei dati.

- Se l'applicazione prevede una specifica strategia di replica geografica, l'archiviazione con ridondanza geografica può non essere necessaria.


## Archiviazione con ridondanza della zona

Con l'archiviazione con ridondanza della zona (ZRS) i dati vengono replicati in due o tre strutture all'interno di una singola area o in due aree, fornendo una durabilità maggiore rispetto all'archiviazione con ridondanza locale. Se per l'account di archiviazione è stata abilitata l'archiviazione con ridondanza della zona, la durabilità dei dati è assicurata anche in caso di problemi in una delle strutture.


>[AZURE.NOTE]  ZONA è attualmente disponibile solo per i BLOB in blocchi ed è supportata solo nelle versioni 2014-02-14 e versioni successive. Tenere presente che dopo aver creato l'account di archiviazione e selezionato la replica con ridondanza della zona, non è possibile convertirlo per usarlo con un altro tipo di replica o viceversa.


## Archiviazione con ridondanza geografica

Con l'archiviazione con ridondanza geografica (GRS) i dati vengono replicati in un'area secondaria a centinaia di chilometri di distanza dall'area primaria. Se per l'account di archiviazione è stata abilitata l'archiviazione con ridondanza geografica, la durabilità dei dati è assicurata anche in caso di un'interruzione completa dell'alimentazione locale o in situazioni di emergenza in cui l'area primaria non è recuperabile.

Per un account di archiviazione con l'archiviazione con ridondanza geografica abilitata, il commit di un aggiornamento viene eseguito prima nell'area primaria, dove viene replicato per tre volte. Quindi l'aggiornamento viene replicato nell'area secondaria, anche in questo caso per tre volte, in domini di errore e domini di aggiornamento distinti.

> [AZURE.NOTE] Con l'archiviazione con ridondanza geografica, le richieste di scrittura dati vengono replicate in modo asincrono nell'area secondaria. È importante tenere presente che la scelta dell'archiviazione con ridondanza geografica non influisce sulla latenza delle richieste effettuate per l'area primaria. Tuttavia, poiché la replica asincrona implica un ritardo, in caso di un'emergenza locale è possibile che le modifiche non ancora replicate nell'area secondaria vadano perse se non è possibile recuperare i dati dall'area primaria.
 
L'area primaria viene selezionata durante la creazione di un account di archiviazione. L'area secondaria viene invece determinata in base a quella primaria e non è possibile modificarla. Nella tabella seguente vengono illustrate le associazioni di aree primarie e secondarie:

| Primario | Secondario |
|---------------------|---------------------|
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
| Governo degli Stati Uniti - Iowa | Governo degli Stati Uniti - Virginia |
| Governo degli Stati Uniti - Virginia | Governo degli Stati Uniti - Iowa |
| Canada centrale | Canada orientale |
| Canada orientale | Canada centrale |
| Regno Unito settentrionale | Regno Unito meridionale 2 |
| Regno Unito meridionale 2 | Regno Unito settentrionale |
| Germania centrale | Germania nord-orientale |
| Germania nord-orientale | Germania centrale |
| Stati Uniti occidentali 2 | Stati Uniti centro-occidentali |
| Stati Uniti centro-occidentali | Stati Uniti occidentali 2 |

Per informazioni aggiornate sulle aree supportate da Azure, vedere [Aree di Azure](https://azure.microsoft.com/regions/).
 
## Archiviazione con ridondanza geografica e accesso in lettura

L'archiviazione con ridondanza geografica e accesso in lettura(RA-GRS) massimizza la disponibilità dell'account di archiviazione fornendo l'accesso in sola lettura ai dati nella posizione secondaria, oltre alla replica tra due aree assicurata dall'archiviazione con ridondanza geografica. Qualora i dati diventino non disponibili nell'area primaria, l'applicazione potrà leggerli dall'area secondaria.

Se si abilita l'accesso in sola lettura ai dati nell'area secondaria, i dati saranno disponibili in un endpoint secondario, oltre che nell'endpoint primario dell'account di archiviazione. L'endpoint secondario è simile a quello primario, ma al nome dell'account viene aggiunto il suffisso `–secondary`. Se ad esempio l'endpoint primario per il servizio BLOB è `myaccount.blob.core.windows.net`, l'endpoint secondario sarà `myaccount-secondary.blob.core.windows.net`. Le chiavi di accesso per l'account di archiviazione sono identiche per gli endpoint primario e secondario.

## Passaggi successivi

- [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/)
- [Informazioni sugli account di archiviazione di Azure](storage-create-storage-account.md)
- [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](storage-scalability-targets.md)
- [Opzioni di ridondanza di Archiviazione di Microsoft Azure e Archiviazione con ridondanza geografica e accesso in lettura](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
- [Paper SOSP - Archiviazione di Microsoft Azure: Un servizio di archiviazione cloud a elevata disponibilità con coerenza assoluta](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

<!---HONumber=AcomDC_0810_2016-->