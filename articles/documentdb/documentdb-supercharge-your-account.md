---
redirect_url: https://docs.microsoft.com/azure/documentdb/documentdb-performance-levels
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: 94ae24e605f99e23766702a3954db567bb64bb51
ms.openlocfilehash: 1c055187405b9c858676b2b80e4bc5c4157ef580


---
# <a name="migrate-your-documentdb-s1-s2-or-s3-account"></a>Eseguire la migrazione dell'account DocumentDB S1, S2 o S3
Seguire questa procedura per ottenere una maggiore velocità effettiva per l'account Azure DocumentDB passando al piano tariffario Standard. È possibile aumentare la velocità effettiva dell'account S1 esistente da 250 [UR/sec](documentdb-request-units.md) a oltre 400 UR/sec senza alcun costo aggiuntivo o con un costo aggiuntivo minimo. Tutti gli account Standard traggono vantaggi dalla possibilità di ridimensionare la velocità effettiva per soddisfare i requisiti delle applicazioni. Non è più necessario scegliere una delle opzioni di velocità effettiva predefinite: è possibile ridimensionarla quando necessario per raggiungere quella richiesta dall'applicazione. 

## <a name="change-to-user-defined-performance-in-the-azure-portal"></a>Passare alle prestazioni definite dall'utente nel portale di Azure
1. Nel browser passare al [**portale di Azure**](https://portal.azure.com). 
2. Nel menu a sinistra fare clic su **NoSQL (DocumentDB)** oppure fare clic su **Altri servizi**, scorrere fino a **Database**, quindi selezionare **NoSQL (DocumentDB)**.   
3. Nel pannello **NoSQL (DocumentDB)** selezionare l'account da modificare.
4. Nel nuovo pannello, nel menu in **Raccolte**, fare clic su **Ridimensiona**. 

      ![Screenshot dei pannelli Impostazioni e Scegliere il piano tariffario di DocumentDB](./media/documentdb-supercharge-your-account/documentdb-change-performance.png)
5. Eseguire le operazioni seguenti come illustrato nella schermata precedente: 

 - Nel nuovo pannello usare il menu a discesa per selezionare la raccolta con il piano tariffario S1, S2 o S3. 
 - Fare clic su **Piano tariffario S1**, **S2** o **S3**.
 - Nel pannello **Scegliere il piano tariffario** fare clic su **Standard** e quindi fare clic su **Seleziona** per salvare la modifica.
   
6. Nel pannello **Piano** il **piano tariffario** è impostato su **Standard** e nella casella **Velocità effettiva (UR/sec)** viene visualizzato un valore predefinito di 400.  È possibile modificare la velocità effettiva indicando il valore richiesto dall'applicazione. Si consiglia di usare [Capacity Planner di DocumentDB](https://www.documentdb.com/capacityplanner) per determinare di quante [unità richieste](documentdb-request-units.md) al secondo (UR) necessiti l'applicazione. Il contenuto di **Fattura mensile stimata** nella parte inferiore della pagina viene aggiornato automaticamente per fornire una stima del costo mensile. Fare clic su **Salva** per salvare le modifiche. 
      
    ![Screenshot del pannello Impostazioni che mostra dove modificare il valore della velocità effettiva](./media/documentdb-supercharge-your-account/documentdb-change-performance-set-thoughput.png)
7. La raccolta viene ridimensionata per soddisfare i nuovi requisiti e viene visualizzato un messaggio di conferma.  
   
    ![Screenshot del pannello Database con la raccolta modificata](./media/documentdb-supercharge-your-account/documentdb-change-performance-confirmation.png)

Per altre informazioni sulle modifiche alla velocità effettiva definita dall'utente e quella predefinita, vedere il post del blog [DocumentDB: Everything you need to know about using the new pricing options](https://azure.microsoft.com/blog/documentdb-use-the-new-pricing-options-on-your-existing-collections/)(DocumentDB: tutto quello che occorre sapere sull'uso delle nuove opzioni relative ai prezzi).

## <a name="next-steps"></a>Passaggi successivi

È possibile trovare ulteriori informazioni su come partizionare i dati e ottenere una scalabilità globale con DocumentDB in [Partizionamento e scalabilità in Azure DocumentDB](documentdb-partition-data.md).



<!--HONumber=Feb17_HO2-->


