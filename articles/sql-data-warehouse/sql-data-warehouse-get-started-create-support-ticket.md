<properties
   pageTitle="Come creare un ticket di supporto per SQL Data Warehouse | Microsoft Azure"
   description="Come creare un ticket di supporto per SQL Data Warehouse di Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/01/2016"
   ms.author="sonyama;barbkess"/>

# Come creare un ticket di supporto per SQL Data Warehouse
 
Nel caso di problemi con SQL Data Warehouse, creare un ticket di supporto per ottenere assistenza dal team di progettazione.

## Creare un ticket di supporto

1. Aprire il [portale di Azure][].

2. Nella schermata iniziale fare clic sul riquadro **Guida e supporto tecnico**.

    ![Guida e supporto tecnico](./media/sql-data-warehouse-get-started-create-support-ticket/help-support.png)

3. Nel pannello Guida e supporto tecnico fare clic su **Crea un ticket di supporto**.

    ![Nuova richiesta di supporto](./media/sql-data-warehouse-get-started-create-support-ticket/create-support-request.png)
    
    <a name="request-quota-change"></a>

4. Selezionare il **tipo di richiesta**.

    ![Tipo di richiesta](./media/sql-data-warehouse-get-started-create-support-ticket/request-type.png)
    
    >[AZURE.NOTE]  Per impostazione predefinita, ogni server SQL, ad esempio myserver.database.windows.net, ha una **Quota DTU** pari a 45.000. Questa quota è semplicemente un limite di sicurezza. È possibile aumentare la quota creando un ticket di supporto e selezionando *Quota* come tipo di richiesta. Per calcolare le esigenze in termini di DTU, moltiplicare 7,5 per le [DWU][] totali necessarie. Se, ad esempio, si vogliono ospitare due DW6000 in una istanza di SQL Server, è necessario richiedere una quota di DTU pari a 90.000. È possibile visualizzare l'utilizzo di DTU attuale nel pannello SQL Server del portale. I database in pausa e non in pausa vengono conteggiati nella quota di DTU.

5. Selezionare la **sottoscrizione** che ospita il database con il problema segnalato.

    ![Subscription](./media/sql-data-warehouse-get-started-create-support-ticket/subscription.png)

6. Selezionare **SQL Data Warehouse** come risorsa.

    ![Risorsa](./media/sql-data-warehouse-get-started-create-support-ticket/resource.png)

7. Selezionare il proprio [piano di supporto di Azure][].

    - Il supporto per problemi correlati a **fatturazione, quota e gestione delle sottoscrizioni** è disponibile per tutti i livelli.
    - Il supporto **in garanzia** viene fornito tramite il supporto tecnico [Developer][], [Standard][], [Professional Direct][] o [Premier][]. I problemi in garanzia si verificano quando i clienti usano Azure ed è ragionevolmente probabile che il problema sia provocato da Microsoft.
    - Servizi di **mentoring per sviluppatori** e **consulenza** sono disponibili solo per i livelli di supporto tecnico [Professional Direct][] e [Premier][].
    
    Se si dispone di un piano di supporto Premier, è anche possibile segnalare problemi relativi a SQL Data Warehouse sul [portale online Microsoft Premier][]. Per altre informazioni sui vari piani di supporto, inclusi l'ambito, i tempi di risposta, i prezzi e così via, vedere [Supporto di Azure per i clienti][Azure support plan]. Per domande frequenti sul supporto di Azure, vedere [Domande frequenti sul supporto di Azure][].

    ![Piano di supporto](./media/sql-data-warehouse-get-started-create-support-ticket/support-plan.png)

8. Selezionare il **tipo di problema** e la **categoria**.

    ![Categoria del tipo di problema](./media/sql-data-warehouse-get-started-create-support-ticket/problem-type-category.png)

9. Descrivere il problema e scegliere il livello di impatto aziendale.

    ![Descrizione del problema](./media/sql-data-warehouse-get-started-create-support-ticket/problem-description.png)

10. Le **informazioni di contatto** per il ticket di supporto saranno precompilate. Aggiornare le informazioni, se necessario.

    ![Informazioni di contatto](./media/sql-data-warehouse-get-started-create-support-ticket/contact-info.png)

11. Fare clic su **Crea** per inviare la richiesta di supporto.


## Monitorare un ticket di supporto

Dopo avere inviato la richiesta di supporto, si verrà contattati dal team di supporto di Azure. Per controllare i dettagli e lo stato della richiesta, fare clic su **Gestisci richieste di supporto** nel dashboard.

![Controlla stato](./media/sql-data-warehouse-get-started-create-support-ticket/check-status.png)

## Altre risorse

È anche possibile connettersi alla community di SQL Data Warehouse in [Stack Overflow][] o nel [forum MSDN su Azure SQL Data Warehouse][].

<!--Image references--> 

<!--Article references--> 
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units

<!--MSDN references--> 

<!--Other web references--> 
[portale di Azure]: https://portal.azure.com/
[Azure support plan]: https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/
[piano di supporto di Azure]: https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/
[Developer]: https://azure.microsoft.com/support/plans/developer/
[Standard]: https://azure.microsoft.com/support/plans/standard/
[Professional Direct]: https://azure.microsoft.com/support/plans/prodirect/
[Premier]: https://azure.microsoft.com/support/plans/premier/
[Domande frequenti sul supporto di Azure]: https://azure.microsoft.com/support/faq/
[portale online Microsoft Premier]: https://premier.microsoft.com/
[Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw/
[forum MSDN su Azure SQL Data Warehouse]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/

<!---HONumber=AcomDC_0907_2016-->