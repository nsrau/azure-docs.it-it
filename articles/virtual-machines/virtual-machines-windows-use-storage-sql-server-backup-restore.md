<properties
	pageTitle="Come usare l'archiviazione di Azure per il backup e il ripristino di SQL Server | Microsoft Azure"
	description="Informazioni su come eseguire il backup di SQL Server in Archiviazione di Azure. Vengono illustrati i vantaggi del backup dei database SQL in Archiviazione di Azure."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="MikeRayMSFT"
	manager="jhubbard"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="07/22/2016"
	ms.author="mikeray"/>  

# Usare Archiviazione di Azure per il backup e il ripristino di SQL Server

## Overview

A partire da SQL Server 2012 SP1 CU2, è possibile scrivere i backup di SQL Server direttamente nel servizio di archiviazione BLOB di Azure. È possibile usare questa funzionalità per eseguire operazioni di backup e ripristino dal servizio BLOB di Azure, con un database di SQL Server locale o un database di SQL Server in una macchina virtuale di Azure. Il backup nel cloud offre vantaggi in termini di disponibilità, di archiviazione fuori sede con replica geografica illimitata e di semplicità di migrazione dei dati da e verso il cloud. È possibile eseguire istruzioni BACKUP o RESTORE usando T-SQL o SMO.

In SQL Server 2016 sono disponibili nuove funzionalità. È possibile usare il [backup di snapshot di file ](http://msdn.microsoft.com/library/mt169363.aspx) per eseguire backup quasi istantanei e ripristini estremamente rapidi.

Questo argomento illustra i motivi per cui è possibile scegliere di usare Archiviazione di Azure per i backup di SQL e descrive i diversi componenti coinvolti. Le risorse indicate al termine dell'articolo consentono di accedere a procedure dettagliate e informazioni aggiuntive relative all'uso di questo servizio per i backup di SQL Server.

## Vantaggi dell'uso del servizio BLOB di Azure per i backup di SQL Server

Quando si eseguono backup di SQL Server, è necessario affrontare diverse problematiche, inclusi la gestione dell'archiviazione, i rischi correlati agli errori di archiviazione, l'accesso all'archiviazione fuori sede e la configurazione dell'hardware. Grazie all'uso del servizio di archiviazione BLOB di Azure per i backup di SQL Server, molti di questi problemi vengono risolti. Prendere in considerazione i vantaggi seguenti:

- **Semplicità d'uso**: l'archiviazione dei backup nel servizio BLOB di Azure è un'opzione di archiviazione fuori sede pratica, flessibile e di facile accesso. La creazione di un sistema di archiviazione fuori sede per i backup di SQL Server è estremamente semplice ed è realizzabile modificando gli script e i processi esistenti in modo da usare la sintassi **BACKUP TO URL**. L'archiviazione fuori sede dovrebbe in genere essere ubicata a una certa distanza dal luogo fisico in cui si trova il database di produzione, al fine di evitare che nell'impatto di una situazione di emergenza possano trovarsi coinvolti sia la sede remota che quella in cui si trova il database di produzione. Scegliendo di eseguire la [replica geografica dei BLOB di Azure](../storage/storage-redundancy.md), si otterrà un livello aggiuntivo di protezione nel caso in cui si verifichi un'emergenza che potrebbe interessare l'intera area.
- **Archivio di backup**: per l'archiviazione dei backup, il servizio di archiviazione BLOB di Azure offre un'alternativa ottimale alle opzioni più diffuse, ad esempio le soluzioni a nastro. L'archiviazione su nastro può richiedere il trasporto fisico alla struttura fuori sede e l'adozione di determinate misure per la protezione dei supporti. L'archiviazione dei backup nell'archiviazione BLOB di Azure rappresenta un'opzione di archiviazione istantanea, estremamente disponibile e durevole.
- **Hardware gestito**: con i servizi di Azure non viene addebitato alcun sovraccarico per la gestione dell'hardware. I servizi di Azure consentono di gestire l'hardware con l'aggiunta della replica geografica per la ridondanza e la protezione dagli errori hardware.
- **Archiviazione illimitata**: abilitando un backup diretto sui BLOB di Azure, si ottiene l'accesso a uno spazio di archiviazione teoricamente illimitato. L'esecuzione di backup su una macchina virtuale di Azure presenta invece dei limiti correlati alle dimensioni della macchina stessa. Il numero di dischi che è possibile collegare a una macchina virtuale di Azure per i backup è limitato. Tale limite è di 16 dischi per un'istanza molto grande e un numero di dischi inferiore per istanze più piccole.
- **Disponibilità dei backup**: i backup archiviati in BLOB di Azure sono disponibili ovunque e in qualsiasi momento. Sono facilmente accessibili per il ripristino in un'istanza di SQL Server locale oppure in un'altra istanza eseguita in una macchina virtuale di Azure, senza che sia necessario collegare e scollegare il database o scaricare e collegare il disco rigido virtuale.
- **Costo**: viene pagato solo il servizio effettivamente utilizzato. Può rivelarsi una soluzione economica per il backup e l'archiviazione fuori sede. Per altre informazioni, vedere il [Calcolatore dei costi di Azure](http://go.microsoft.com/fwlink/?LinkId=277060 "Calcolatore dei prezzi") e l'articolo [Panoramica dei prezzi di Azure](http://go.microsoft.com/fwlink/?LinkId=277059 "Prezzi articolo").
- **Usare gli snapshot di archiviazione**: quando i file di database sono archiviati in un BLOB di Azure e si usa SQL Server 2016, è possibile usare il [backup di snapshot di file](http://msdn.microsoft.com/library/mt169363.aspx) per eseguire backup quasi istantanei e ripristini estremamente rapidi.

Per altre informazioni, vedere [Backup e ripristino di SQL Server con il servizio di archiviazione BLOB di Azure](http://go.microsoft.com/fwlink/?LinkId=271617).

Le due sezioni seguenti forniscono un'introduzione al servizio di archiviazione BLOB di Azure, inclusi i componenti di SQL Server necessari. È importante comprendere la natura di tali componenti e le interazioni che avvengono tra di essi per assicurare la corretta esecuzione delle operazioni di backup e ripristino dal servizio di archiviazione BLOB di Azure.

## Componenti del servizio di archiviazione BLOB di Azure

Quando si eseguono backup nel servizio di archiviazione BLOB di Azure, vengono usati i componenti di Azure indicati di seguito.

| Componente | Descrizione |
|---------------------|-------------------------------|
| **Storage Account** | l'account di archiviazione è il punto di partenza per tutti i servizi di archiviazione. Per accedere a un servizio di archiviazione BLOB di Azure, è innanzitutto necessario creare un account di archiviazione di Azure. Per ulteriori informazioni sul servizio di archiviazione BLOB di Azure, vedere [Come utilizzare il servizio di archiviazione BLOB di Azure](https://azure.microsoft.com/develop/net/how-to-guides/blob-storage/) |
| **Contenitore** | Un contenitore fornisce il raggruppamento di un set di BLOB ed è in grado di archiviare un numero di BLOB illimitato. Per scrivere un backup di SQL Server in un servizio BLOB di Azure, deve prima essere stato creato almeno il contenitore radice. |
| **BLOB** | file di qualsiasi tipo o dimensione. È possibile fare riferimento ai BLOB usando il formato di URL seguente: **https://[storage account].blob.core.windows.net/[contenitore]/[BLOB]**. Per altre informazioni sui BLOB di pagine, vedere [Informazioni sui BLOB in blocchi, sui BLOB di aggiunta e sui BLOB di pagine](http://msdn.microsoft.com/library/azure/ee691964.aspx). |

## Componenti di SQL Server

Quando si eseguono backup nel servizio di archiviazione BLOB di Azure, vengono usati i componenti di SQL Server indicati di seguito.

| Componente | Descrizione |
|---------------------|-------------------------------|
| **URL** | un URL specifica un URI (Uniform Resource Identifier) per un file di backup specifico. L'URL viene usato per fornire il percorso e il nome del file di backup di SQL Server. L'URL deve puntare a un BLOB effettivo e non a un contenitore. Se il BLOB non esiste, verrà creato. Se viene specificato un BLOB esistente, il comando BACKUP non riuscirà a meno che non sia specificata l'opzione WITH FORMAT. Di seguito è riportato un esempio di URL specificato in un comando BACKUP: **http[s]://[accountarchiviazione].blob.core.windows.net/[contenitore]/[FILENAME.bak]**. HTTPS non è obbligatorio ma è consigliato. |
| **Credenziali** | le informazioni necessarie per la connessione e l'autenticazione a un servizio di archiviazione BLOB di Azure vengono archiviate in una credenziale. Per fare in modo che SQL Server sia in grado di scrivere backup in un BLOB di Azure o di eseguire un ripristino da quest'ultimo, è necessario creare una credenziale di SQL Server. Per altre informazioni, vedere [Credenziali di SQL Server](https://msdn.microsoft.com/library/ms189522.aspx). |

> [AZURE.NOTE] se si sceglie di copiare e caricare un file di backup nel servizio di archiviazione BLOB di Azure, e si prevede di usarlo per operazioni di ripristino, è necessario usare il tipo di BLOB di pagine. Il comando RESTORE da un tipo di BLOB in blocchi non riuscirà e restituirà un errore.

## Passaggi successivi

1. Se non se ne possiede già uno, creare un account di Azure. Se si sta valutando Azure, è consigliabile usare una [versione di prova gratuita](https://azure.microsoft.com/free/).

1. Eseguire quindi una delle esercitazioni seguenti, in cui vengono fornite informazioni dettagliate sulla creazione di un account di archiviazione e sull'esecuzione di un ripristino.

	- **SQL Server 2014** - [Esercitazione: Backup e ripristino di SQL Server 2014 nel servizio di archiviazione Blob di Microsoft Azure](https://msdn.microsoft.com/library/jj720558(v=sql.120).aspx).
	- **SQL Server 2016**: [Tutorial: Using the Microsoft Azure Blob storage service with SQL Server 2016 databases](https://msdn.microsoft.com/library/dn466438.aspx) (Esercitazione: Uso del servizio di archiviazione BLOB di Azure con database di SQL Server 2016)

1. Esaminare la documentazione aggiuntiva, a partire da [Backup e ripristino di SQL Server con il servizio di archiviazione BLOB di Microsoft Azure](https://msdn.microsoft.com/library/jj919148.aspx).

Se si verificano problemi, consultare l'argomento [Procedure consigliate e risoluzione dei problemi per il backup di SQL Server nell'URL](https://msdn.microsoft.com/library/jj919149.aspx).

Per una descrizione delle altre opzioni di backup e ripristino, vedere [Backup e ripristino per SQL Server in Macchine virtuali di Azure](../virtual-machines/virtual-machines-windows-sql-backup-recovery.md).

<!---HONumber=AcomDC_0907_2016-->