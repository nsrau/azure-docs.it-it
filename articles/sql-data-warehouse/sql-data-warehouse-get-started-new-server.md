<properties
   pageTitle="Creare un database SQL Data Warehouse nel portale di Azure | Microsoft Azure"
   description="Informazioni su come creare un database SQL Data Warehouse nel portale di Azure"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor=""
   tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/03/2016"
   ms.author="lodipalm;"/>

# Creare un nuovo server logico

Nel database SQL e in SQL Data Warehouse ogni database viene assegnato a un server e ogni server viene assegnato a una posizione geografica. Il server è definito server SQL logico.

> [AZURE.NOTE] <a name="note"></a>Un server SQL logico:
  >
  > + Fornisce un modo coerente per configurare più database nella stessa località geografica.
  > + Non è hardware fisico come nel caso di un server locale. Fa parte del software del servizio. Ecco perché viene chiamato *server logico*.
  > + Può ospitare più database senza conseguenze per le prestazioni.
  > + Usa una lettera *s* minuscola nel nome. Il **s**erver SQL è un server logico di Azure, mentre SQL **S**erver è il prodotto per database locali di Microsoft.

1. Fare clic su **Server** > **Crea un nuovo server**. Non è previsto alcun addebito per il server. Se si ha già un server SQL logico versione 12 che si vuole usare, scegliere il server esistente e andare al passaggio successivo.

    ![Crea un nuovo server](./media/sql-data-warehouse-get-started-provision/create-server.png)

2. Specificare le informazioni in **Nuovo server**.

	- **Nome server**: immettere un nome per il server logico. Questo nome è univoco per ogni località geografica.
	- **Accesso amministratore server**: immettere un nome utente per l'account amministratore del server.
	- **Password**: immettere la password dell'amministratore del server.
	- **Località**: scegliere una località geografica per il server. Per ridurre i tempi di trasferimento dei dati, è consigliabile posizionare il server in una località geograficamente vicina alle altre risorse dati a cui accederà il database.
	- **Crea server V12**: SÌ è la sola opzione per SQL Data Warehouse.
	- **Consenti ai servizi di Azure di accedere al server**: questa opzione è sempre selezionata per SQL Data Warehouse.

    >[AZURE.NOTE] Assicurarsi di registrare il nome del server, il nome dell'amministratore e la password per conservarli in un luogo sicuro. Queste informazioni saranno necessarie per accedere al server.

3. Fare clic su **OK** per salvare le impostazioni di configurazione del server SQL logico e tornare al pannello di SQL Data Warehouse.

    ![Configurare il nuovo server](./media/sql-data-warehouse-get-started-provision/configure-server.png)

<!---HONumber=AcomDC_0504_2016-->