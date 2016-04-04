<properties
	pageTitle="Spostamento dei dati da e verso Archiviazione di Azure | Microsoft Azure"
	description="In questo articolo sono raccolte informazioni generali sulle diverse soluzioni per spostare i dati da e verso Archiviazione di Azure."
	services="storage"
	documentationCenter=""
	authors="micurd"
	manager="jahogg"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/18/2016"
	ms.author="micurd"/>

# Spostamento dei dati da e verso Archiviazione di Azure

Esistono modi diversi per spostare i dati locali in Archiviazione di Azure (o viceversa). Quale sia il metodo adatto dipende dallo scenario. Questo articolo offre informazioni generali su diversi scenari e la soluzione appropriata per ciascuno.

## Compilazione di applicazioni

Se si usa l’API REST o una delle numerose librerie client per la compilazione e lo sviluppo di un’applicazione, è consigliabile spostare i dati da e verso Archiviazione di Azure.

In Archiviazione di Azure sono disponibili librerie client complete per .NET, iOS, Java, Android, piattaforma UWP, Xamarin, C++, Node.JS, PHP, Ruby e Python. Le librerie client offrono funzionalità avanzate, ad esempio la logica di ripetizione dei tentativi, la registrazione e i caricamenti paralleli. È possibile sviluppare usando direttamente l'API REST, che può essere chiamata da qualsiasi linguaggio in grado di eseguire richieste HTTP/HTTPS.

Vedere [Introduzione all'archiviazione BLOB di Azure](storage-dotnet-how-to-use-blobs.md) per altre informazioni.

## Visualizzazione e interazione rapida dei dati

Per visualizzare semplicemente i dati in Archiviazione di Azure e al tempo stesso poterli caricare e scaricare, è consigliabile usare un Esplora archivi Azure.

Consultare l'elenco di [Esplora archivi Azure](storage-explorers.md) per altre informazioni.

## System Administration

Se è richiesta un’utilità da riga di comando o si preferisce usare questa soluzione, ad esempio Amministratori di sistema, considerare le possibilità seguenti:

### AzCopy

AzCopy è un'utilità da riga di comando Windows progettata per offrire prestazioni elevate di copia dei dati da e verso Archiviazione di Azure. È inoltre possibile copiare i dati all'interno di un account di archiviazione o tra account di archiviazione diversi.

Vedere [Trasferire dati con l'utilità da riga di comando AzCopy](storage-use-azcopy.md) per altre informazioni.

### Azure PowerShell

Azure PowerShell è un modulo che offre cmdlet per la gestione dei servizi in Azure. Si tratta di una shell da riga di comando basata su attività e di un linguaggio di scripting progettato appositamente per gli amministratori di sistema.

Vedere [Uso di Azure PowerShell con Archiviazione di Azure](storage-powershell-guide-full.md)

### Interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure offre un insieme di comandi open source e multipiattaforma per usare i servizi di Azure. L’interfaccia della riga di comando di Azure è disponibile in Windows, OSX e Linux.

Vedere [Uso dell'interfaccia della riga di comando di Azure con Archiviazione di Azure](storage-azure-cli.md).

## Spostamento di molti dati tramite una rete lenta

Il tempo di trasferimento è una delle difficoltà principali che riguardano lo spostamento di molti dati. Se è necessario spostare da e verso Archiviazione di Azure i dati senza doversi preoccupare dei costi di rete o della scrittura di codice, Importazione/Esportazione di Azure è la soluzione adatta.

Vedere [Importazione/Esportazione di Azure](storage-import-export-service.md) per altre informazioni.

## Backup dei dati

Se è necessario semplicemente eseguire il backup dei dati in Archiviazione di Azure, Backup di Azure è la scelta giusta. Si tratta di una soluzione potente per il backup di dati locali e macchine virtuali di Azure.

Vedere [Backup di Azure](../backup/backup-introduction-to-azure-backup.md) per altre informazioni.

## Accesso ai dati locali e dal cloud

Per accedere ai dati locali e dal cloud, considerare l’uso di StorSimple, una soluzione di archiviazione cloud ibrida di Azure. Questa soluzione è costituita da un dispositivo StorSimple fisico che, in modo intelligente, archivia i dati utilizzati di frequente in unità SSD, i dati utilizzati occasionalmente in unità HDD e i dati inattivi, di backup e di archiviazione in Archiviazione di Azure.

Vedere [StorSimple](../storsimple/storsimple-overview.md) per altre informazioni.

## Ripristino dei dati

In presenza di carichi di lavoro e applicazioni locali, è consigliabile l’uso di una soluzione che sia in grado di garantire continuità aziendale in caso di emergenza. Azure Site Recovery coordina la replica, il failover e il ripristino di macchine virtuali e server fisici. I dati replicati vengono archiviati in Archiviazione di Azure eliminando così la necessità di un centro dati on-site secondario.

Vedere [Azure Site Recovery](../site-recovery/site-recovery-overview.md) per altre informazioni.

<!---HONumber=AcomDC_0323_2016-->