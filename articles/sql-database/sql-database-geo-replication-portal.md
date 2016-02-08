<properties 
    pageTitle="Configurare la replica geografica per il Database SQL di Azure con il Portale di Azure | Microsoft Azure" 
    description="Configurare la replica geografica per il Database SQL di Azure tramite il Portale di Azure" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jeffreyg" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management" 
    ms.date="12/01/2015"
    ms.author="sstein"/>

# Configurare la replica geografica per il Database SQL di Azure con il Portale di Azure


> [AZURE.SELECTOR]
- [Azure portal](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [Transact-SQL](sql-database-geo-replication-transact-sql.md)


In questo articolo viene illustrato come configurare una replica geografica per il database SQL con il [portale di Azure](https://portal.azure.com).

La replica geografica consente di creare un massimo di 4 database di replica (secondari) in località, o aree geografiche, di data center diverse. I database secondari sono disponibili in caso di interruzione di un data center o dell'impossibilità di connettersi al database primario.

La replica geografica è disponibile solo per i database Standard e Premium.

I database Standard possono avere un solo database secondario non accessibile in lettura e devono usare l'area consigliata. I database Premium possono avere un massimo di quattro database secondari accessibili in lettura in una qualsiasi della aree disponibili.


Per configurare la replica geografica, sono necessari gli elementi seguenti:

- Una sottoscrizione di Azure. Se è necessaria una sottoscrizione ad Azure, fare semplicemente clic su **VERSIONE DI PROVA GRATUITA** nella parte superiore della pagina, quindi tornare e proseguire fino alla fine di questo articolo.
- Un database di Database SQL di Azure - Il database primario che si desidera replicare in un'area geografica diversa.



## Aggiunta del database secondario

La procedura seguente crea un nuovo database secondario in una relazione di replica geografica.

Per aggiungere un database secondario è necessario essere il proprietario o un comproprietario della sottoscrizione.

Il database secondario avrà lo stesso nome del database primario e avrà, per impostazione predefinita, lo stesso livello di servizio. Il database secondario può essere leggibile (solo livello Premium) o non leggibile e può essere un database singolo o un database elastico. Per altre informazioni, vedere [Livelli di servizio](sql-database-service-tiers.md). Dopo che il database secondario viene creato e viene effettuato il seeding, i dati verranno replicati dal database primario al nuovo database secondario.

> [AZURE.NOTE] Se il database partner esiste già (ad esempio, come risultato del termine di una relazione di replica geografica precedente), il comando avrà esito negativo.




### Aggiunta del secondario

1. Nel [Portale di Azure](https://portal.azure.com) selezionare il database per cui si desidera installare la replica geografica.
2. Nel pannello del Database SQL, selezionare **Tutte le impostazioni** > **Replica geografica**.
3. Selezionare l'area per creare il database secondario. I database Premium possono utilizzare qualsiasi area per un database secondario, quelli Standard devono utilizzare l'area consigliata:


    ![Aggiunta del secondario][1]


4. Configurare il **tipo secondario** (**Leggibile**, o **Non leggibile**), solo i database Premium possono avere database secondari leggibili, mentre i database secondari Standard possono essere impostati solo su **Non leggibile**.
5. Selezionare o configurare il server per il database secondario.

    ![Creare il database secondario][3]

5. Facoltativamente, è possibile aggiungere un database secondario a un pool di database elastici:

       - Fare clic su **Pool di database elastici** e selezionare un pool sul server di destinazione in cui creare il database secondario. Un pool deve già esistere nel server di destinazione poiché questo flusso di lavoro non crea un nuovo pool.

6. Fare clic su **Crea** per aggiungere il database secondario.
 
6. Il database secondario viene creato e viene avviato il processo di seeding.
 
    ![seeding][6]

7. Una volta completato il processo di seeding il database secondario visualizza il relativo stato (non leggibile.

    ![database secondario pronto][9]



## Rimuovere il database secondario

L'operazione interrompe in modo permanente la replica al database secondario e modifica il ruolo del database secondario in un database di lettura/scrittura normale. Se la connettività al database secondario viene interrotta il comando ha esito positivo ma il database secondario non diventerà un database di lettura-scrittura fino a quando la connettività non verrà ripristinata.

1. Nel [Portale di Azure](https://portal.azure.com) selezionare il database primario nella relazione di replica geografica.
2. Nel pannello del Database SQL, selezionare **Tutte le impostazioni** > **Replica geografica**.
3. Nell’elenco **DATABASE SECONDARI** selezionare il database che si desidera rimuovere della relazione di replica geografica.
4. Fare clic su **Arresta replica**.

    ![rimuovere database secondario][7]


5. Facendo clic su **Arresta replica** verrà visualizzata una finestra di conferma quindi fare clic su **Sì** per rimuovere il database dalla relazione di replica geografica (impostarlo su un database di lettura -scrittura che non fa parte di nessuna replica).


    ![confermare la rimozione][8]



## Avviare un failover

Il database secondario può diventare il database primario.

1. Nel [Portale di Azure](https://portal.azure.com) selezionare il database primario nella relazione di replica geografica.
2. Nel pannello del Database SQL, selezionare **Tutte le impostazioni** > **Replica geografica**.
3. Nell’elenco **DATABASE SECONDARI**, selezionare il database che si desidera utilizzare come nuovo database primario.
4. Fare clic su **Failover**.

    ![failover][10]

Il comando esegue il flusso di lavoro seguente:

1. Passa temporaneamente alla modalità di replica sincrona, In questo modo tutte le transazioni in attesa saranno scaricate nel database secondario. 

2. Invertire i ruoli primari e secondari dei due database nella relazione di replica geografica.

Per il failover pianificato, questa sequenza garantisce che non si verifichi alcuna perdita di dati. Esiste un breve periodo durante il quale entrambi i database saranno disponibili (nell'ordine da 0 a 25 secondi) durate l’inversione dei ruoli. Il completamento dell’intera operazione dovrebbe richiedere meno di un minuto in circostanze normali.

   

## Passaggi successivi

- [Progettazione di applicazioni cloud per la continuità aziendale mediante la replica geografica](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Esercitazioni di ripristino di emergenza](sql-database-disaster-recovery-drills.md)


## Risorse aggiuntive

- [Nuove funzionalità di replica geografica in evidenza](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
- [Progettazione di applicazioni cloud per la continuità aziendale mediante la replica geografica](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Panoramica sulla continuità aziendale](sql-database-business-continuity.md)
- [Documentazione relativa al database SQL](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-geo-replication-portal/configure-geo-replication.png
[2]: ./media/sql-database-geo-replication-portal/add-secondary.png
[3]: ./media/sql-database-geo-replication-portal/create-secondary.png
[4]: ./media/sql-database-geo-replication-portal/secondary-type.png
[5]: ./media/sql-database-geo-replication-portal/create.png
[6]: ./media/sql-database-geo-replication-portal/seeding0.png
[7]: ./media/sql-database-geo-replication-portal/remove-secondary.png
[8]: ./media/sql-database-geo-replication-portal/stop-confirm.png
[9]: ./media/sql-database-geo-replication-portal/seeding-complete.png
[10]: ./media/sql-database-geo-replication-portal/failover.png

<!---HONumber=AcomDC_0128_2016-->