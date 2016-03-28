<properties
	pageTitle="Limitazioni della superficie di attacco e problemi di blocco per Database Estensione | Microsoft Azure"
	description="Informazioni sui problemi di blocco che è necessario risolvere prima di abilitare Database Estensione."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/26/2016"
	ms.author="douglasl"/>

# Limitazioni della superficie di attacco e problemi di blocco per Database Estensione

Informazioni sui problemi di blocco che è necessario risolvere prima di abilitare Database Estensione.

## <a name="Limitations"></a>Problemi di blocco
Nella versione di anteprima corrente di SQL Server 2016, gli elementi seguenti rendono una tabella non idonea per l'estensione.

**Proprietà tabella**
-   Più di 1.023 colonne

-   Più di 998 indici

-   Tabelle contenenti dati FILESTREAM

-   FileTable

-   Tabelle replicate

-   Tabelle che usano Change Tracking o Change Data Capture

-   Tabelle con ottimizzazione per la memoria

**Tipi di dati e proprietà delle colonne**
-   timestamp

-   sql\_variant

-   XML

-   geometry

-   geography

-   hierarchyid

-   Tipi CLR definiti dall'utente

**Tipi di colonna**
-   COLUMN\_SET

-   Colonne calcolate

**Vincoli**
-   Vincoli CHECK

-   Vincoli predefiniti

-   Vincoli di chiave esterna che fanno riferimento alla tabella

**Indici**
-   Indici full-text

-   Indici XML

-   Indici spaziali

-   Viste indicizzate che fanno riferimento alla tabella

## <a name="Caveats"></a>Limitazioni e avvertenze per le tabelle con l'estensione abilitata
Nella versione di anteprima corrente di SQL Server 2016, le tabelle con l'estensione abilitata hanno le limitazioni seguenti.

-   L'univocità non viene applicata per i vincoli UNIQUE e i vincoli PRIMARY KEY in una tabella abilitata per l'estensione.

-   Non è possibile eseguire le operazioni UPDATE o DELETE in una tabella abilitata per l'estensione.

-   Non è possibile eseguire l'operazione INSERT in remoto in una tabella abilitata per l'estensione su un server collegato.

-   Non è possibile usare la replica con una tabella abilitata per l'estensione.

-   Non è possibile creare un indice per una vista che include tabelle abilitate per l'estensione.

-   Non è possibile eseguire aggiornamenti o eliminazioni da una vista che include tabelle abilitate per l'estensione. È possibile tuttavia eseguire inserimenti in una vista che include tabelle abilitate per l'estensione.

-   I filtri sugli indici non vengono propagati alla tabella remota.

## Vedere anche

[Identificare database e tabelle per Database Estensione eseguendo l'ottimizzazione guidata Database Estensione](sql-server-stretch-database-identify-databases.md)

[Abilitare Database Estensione per un database](sql-server-stretch-database-enable-database.md)

[Abilitare Database Estensione per una tabella](sql-server-stretch-database-enable-table.md)

<!---HONumber=AcomDC_0316_2016-->