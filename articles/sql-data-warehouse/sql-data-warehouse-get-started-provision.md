<properties
	pageTitle="Creare un database di SQL Data Warehouse nel portale di anteprima di Azure | Microsoft Azure"
	description="Informazioni su come creare un database di SQL Data Warehouse nel portale di anteprima di Azure"
	services="sql-data-warehouse"
	documentationCenter="NA"
	authors="lodipalm"
	manager="barbkess"
	editor=""
	tags="azure-sql-data-warehouse"/>
<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/29/2015"
   ms.author="lodipalm;barbkess"/>

# Creare un database di SQL Data Warehouse nel portale di anteprima di Azure#

In questa esercitazione viene illustrato come è facile creare un database di SQL Data Warehouse di Azure in pochi minuti nel portale di anteprima di Azure.

In questa procedura dettagliata si eseguiranno i passaggi seguenti:

- Creare un nuovo database di SQL Data Warehouse.
- Creare un server per il database.
- Caricare AdventureWorksDW nel nuovo database.

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]


## Accedere e trovare SQL Data Warehouse

1. Accedere al [portale di anteprima](https://portal.azure.com).

2. Nel menu Hub fare clic su **Nuovo** > **Dati + Archiviazione** > **SQL Data Warehouse**.

	![Creare un data warehouse](./media/sql-data-warehouse-get-started-provision/new-data-warehouse.png)

## Configurare le prestazioni e altre impostazioni di base

Nel riquadro **SQL Data Warehouse** compilare i campi seguenti. I campi **Server** e **Origine** verranno configurati nelle sezioni successive.

1. **Nome database**: immettere un nome per il database di SQL Data Warehouse.

2. **Prestazioni**: è possibile specificare le prestazioni iniziali per l'istanza mentre si esegue il provisioning. È consigliabile partire con 400 DWU, perché ciò consentirà di accedere a un numero maggiore dei vantaggi MPP offerti da SQL Data Warehouse.
     
    ![Nome e DWU](./media/sql-data-warehouse-get-started-provision/name-and-dwu.png)

    > [AZURE.NOTE]Le prestazioni vengono misurate in unità Data Warehouse (DWU, Data Warehouse Unit). Con l'aumento delle unità DWU, SQL Data Warehouse aumenta le risorse di elaborazione disponibili per le operazioni con il database di data warehouse.

	> [AZURE.NOTE]È possibile modificare rapidamente e facilmente il livello di prestazioni dopo la creazione del database. Ad esempio, se non si usa il database, spostare il dispositivo di scorrimento verso sinistra per ridurre i costi. Oppure, aumentare le prestazioni quando sono necessarie ulteriori risorse. Questo è il vantaggio a livello di scalabilità offerto da SQL Data Warehouse.
	

2. **Gruppo di risorse**. Mantenere i valori predefiniti. I gruppi di risorse sono contenitori progettati per facilitare la gestione di una raccolta di risorse di Azure. Per altre informazioni, vedere [gruppi di risorse](../azure-portal/resource-group-portal.md).
3. **Sottoscrizione**. Selezionare la sottoscrizione per la fatturazione del database.


## Configurare un server logico

3. Fare clic su **Server** > **Crea un nuovo server**. Verrà creato un server logico a cui verrà associato il database. Se si dispone già di un server V12 che si desidera usare, scegliere il server esistente e passare alla sezione successiva.

    ![Creare un nuovo server](./media/sql-data-warehouse-get-started-provision/create-new-server.png)

    >[AZURE.NOTE]In SQL Data Warehouse e database SQL, un server fornisce un sistema coerente per configurare i database basati su cloud. In Azure, anche se un server è associato a un singolo data center, non è hardware fisico, come nel caso di un'istanza locale di SQL Server, ma fa parte del software del servizio. Ecco perché viene chiamato server logico. Si noti che, a differenza del mondo reale, i carichi di lavoro che eseguono database e data warehouse nello stesso server non avranno un impatto sulle prestazioni reciproche.

1. Nella finestra **Nuovo server** compilare le informazioni richieste.

    Assicurarsi di registrare il nome del server, il nome dell'amministratore e la password per conservarli in un luogo sicuro. Queste informazioni saranno necessarie per accedere al server. - **Nome server**. Immettere un nome per il server logico. - **Nome amministratore server**. Immettere un nome utente per l'account amministratore del server. - **Password**. Immettere la password dell'amministratore per il server. - **Località**. Scegliere una posizione geografica vicina o in prossimità delle altre risorse di Azure. Ciò riduce la latenza di rete poiché tutti i database e tutte le risorse che appartengono al server logico verranno posizionati fisicamente nella stessa area geografica.

    ![Configurare il nuovo server](./media/sql-data-warehouse-get-started-provision/configure-new-server.png)

1. Fare clic su **OK** per salvare le impostazioni di configurazione del server.

## Caricare il database di esempio

1. Scegliere **Origine** > **Esempio** per inizializzare il nuovo database con il database di esempio AdventureWorksDW. 

    ![Creare un data warehouse](./media/sql-data-warehouse-get-started-provision/create-data-warehouse.png)

## Completare la creazione del database

1. Fare clic su **Crea** per creare il database di SQL Data Warehouse. 

1. A questo punto, è necessario solo attendere qualche minuto. Al termine, verrà visualizzato il database di esempio nella home page.

    ![Vista del portale SQL Data Warehouse](./media/sql-data-warehouse-get-started-provision/database-portal-view.png)

## Passaggi successivi

Dopo avere creato un database di esempio per SQL Data Warehouse, è possibile scoprire come usare SQL Data Warehouse in questa procedura dettagliata successiva.

- [Connettersi ed eseguire query](./sql-data-warehouse-get-started-connect-query.md).

	> [AZURE.NOTE]Si desidera migliorare questo articolo. Se si sceglie di rispondere "no" alla domanda "È stato utile questo articolo?", includere un suggerimento breve su cosa manca o su come migliorare l'articolo. Grazie in anticipo!!

<!---HONumber=Oct15_HO1-->