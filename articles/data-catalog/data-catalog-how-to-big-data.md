<properties
   pageTitle="Come usare le origini dati di tipo ";Big Data"; | Microsoft Azure"
   description="Articolo di procedure che descrive gli schemi per usare il Catalogo dati di Azure con origini dati di tipo ";Big Data";, incluso l'archivio BLOB di Azure, Azure Data Lake e HDFS di Hadoop."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="03/31/2016"
   ms.author="maroche"/>


# Come utilizzare origini dati di tipo Big Data nel Catalogo dati di Azure

## Introduzione
**Catalogo dati di Microsoft Azure** è un servizio cloud completamente gestito che funge da sistema di registrazione e di individuazione per origini dati aziendali. In altre parole, il **Catalogo dati di Azure** consente agli utenti di individuare, comprendere e usare origini dati e aiuta le organizzazioni a ottenere maggior valore dalle origini dati esistenti, inclusi i Big Data.

Il **Catalogo dati di Azure** supporta la registrazione di BLOB e directory di Archiviazione BLOB di Azure, nonché file e directory HDFS di Hadoop. La natura semistrutturata di queste origini di dati offre una notevole flessibilità, ma significa anche che gli utenti devono considerare come sono organizzate le origini dati per ottenere il massimo valore dalla registrazione con il **Catalogo dati di Azure**.

## Directory come set di dati logici

Un modello comune per l'organizzazione delle origini dati di tipo Big Data consiste nel considerare le directory come set di dati logici. Le directory di primo livello vengono usate per definire un set di dati, mentre le sottocartelle definiscono le partizioni e i file che contengono archiviano i dati stessi.

Ecco un esempio di questo schema:

    \vehicle_maintenance_events
        \2013
        \2014
        \2015
            \01
                \2015-01-trailer01.csv
                \2015-01-trailer92.csv
                \2015-01-canister9635.csv
                ...
    \location_tracking_events
        \2013
        ...

In questo esempio vehicle\_maintenance\_events e location\_tracking\_events rappresentano i set di dati logici. Ognuna di queste cartelle contiene file di dati organizzati in sottocartelle per anno e mese. Ogni cartella potrebbe contenere centinaia o migliaia di file.

In questo schema la registrazione di singoli file con **Catalogo dati di Azure** probabilmente non ha senso. Registrare invece le directory che rappresentano i set di dati che saranno significativi per gli utenti che utilizzano i dati.

## File di dati di riferimento

Uno schema complementare consiste nell'archiviare i set di dati di riferimento come singoli file. Questi set di dati possono essere considerati come il lato "piccolo" dei Big Data e spesso sono simili alle dimensioni in un modello di dati analitici. I file di dati di riferimento contengono record usati per fornire il contesto per la maggior parte dei file di dati archiviati altrove nell'archivio di Big Data.

Ecco un esempio di questo schema:

    \vehicles.csv
    \maintenance_facilities.csv
    \maintenance_types.csv

Quando un analista o un data scientist utilizza i dati contenuti nelle strutture di directory più grandi, i dati in questi file di riferimento possono essere usati per fornire informazioni più dettagliate per le entità a cui viene fatto riferimento solo per nome o ID nel set di dati più grande.

In questo schema può essere utile registrare i singoli file di dati di riferimento con il **Catalogo dati di Azure**. Ogni file rappresenta un set di dati e ognuno può essere annotato ed individuato singolarmente.

## Schemi alternativi

Gli schemi descritti sopra sono solo due possibili modalità di organizzazione di un archivio di Big Data, ma ogni implementazione sarà diversa. Indipendentemente da come sono strutturate le origini dati, quando si registrano origini dati di tipo Big Data con il **Catalogo dati di Azure**, concentrarsi sulla registrazione di file e directory che rappresentano i set di dati importanti per altri utenti all'interno dell'organizzazione. La registrazione di tutti i file e tutte le directory può creare confusione nel catalogo, rendendo più difficile per gli utenti trovare le informazioni necessarie.

## Riepilogo
La registrazione di origini dati con il **Catalogo dati di Azure** ne rende più semplice l'individuazione e la comprensione. La registrazione e l'annotazione dei file e delle directory di Big Data che rappresentano set di dati logici permettono agli utenti di trovare e usare le origini dati di tipo Big Data necessarie.

<!---HONumber=AcomDC_0511_2016-->