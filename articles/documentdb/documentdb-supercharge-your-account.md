<properties 
	pageTitle="Potenziare l'account DocumentDB S1 | Microsoft Azure" 
	description="Ottenere una maggiore velocità effettiva nell'account DocumentDB S1 con alcune semplici modifiche nel portale di Azure." 
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/27/2016" 
	ms.author="mimig"/>

# Potenziare l'account DocumentDB

Seguire questa procedura per ottenere una maggiore velocità effettiva per l'account Azure DocumentDB S1. È possibile aumentare la velocità effettiva dell'account S1 esistente da 250 [UR/sec](documentdb-request-units.md) a oltre 400 UR/sec senza alcun costo aggiuntivo o con un costo aggiuntivo minimo.

> [AZURE.VIDEO changedocumentdbcollectionperformance]

## Passare alle prestazioni definite dall'utente nel portale di Azure

1. Nel browser passare al [**portale di Azure**](https://portal.azure.com).
2. Fare clic su **Esplora** -> **Account DocumentDB** e quindi selezionare l'account DocumentDB da modificare.
3. Selezionare il database da modificare nella sezione **Database** e quindi selezionare la raccolta con il piano tariffario S1 nel pannello **Database**.

      ![Screenshot del nuovo pannello Database con una raccolta S1](./media/documentdb-supercharge-your-account/documentdb-change-performance-S1.png)

4. Nel pannello **Raccolte** fare clic su **Impostazioni** nella barra superiore.
5. Nel pannello **Impostazioni** fare clic su **Piano tariffario**. Verrà visualizzata la stima dei costi mensili per ogni piano. Nel pannello **Scegliere il piano tariffario** fare clic su **Standard** e quindi fare clic su **Seleziona** per salvare la modifica.

      ![Screenshot dei pannelli Impostazioni e Scegliere il piano tariffario di DocumentDB](./media/documentdb-supercharge-your-account/documentdb-change-performance.png)

6. Nel pannello **Impostazioni** il **Piano tariffario** passa a **Standard** e la casella **Velocità effettiva (UR/sec)** riporta un valore predefinito di 400. Fare clic su **OK** per salvare le modifiche.

    > [AZURE.NOTE] È possibile impostare la velocità effettiva tra 400 e 10.000 [unità richiesta](../articles/documentdb/documentdb-request-units.md) al secondo (UR/sec). Il **Riepilogo dei prezzi** nella parte inferiore della pagina si aggiorna automaticamente per fornire una stima del costo mensile.
    
	![Screenshot del pannello Impostazioni che mostra dove modificare il valore della velocità effettiva](./media/documentdb-supercharge-your-account/documentdb-change-performance-set-thoughput.png)

8. Nel pannello **Database** è possibile verificare la velocità effettiva potenziata della raccolta.

	![Screenshot del pannello Database con la raccolta modificata](./media/documentdb-supercharge-your-account/documentdb-change-performance-confirmation.png)

Per altre informazioni sulle modifiche alla velocità effettiva definita dall'utente e predefinita, vedere il post del blog [DocumentDB: Everything you need to know about using the new pricing options](https://azure.microsoft.com/blog/documentdb-use-the-new-pricing-options-on-your-existing-collections/) (DocumentDB: tutto quello che occorre sapere sull'uso delle nuove opzioni relative ai prezzi).

## Passaggi successivi

Se è necessaria una velocità effettiva maggiore di 10.000 UR/sec o uno spazio di archiviazione maggiore di 10 GB è possibile creare una raccolta partizionata. Per creare una raccolta partizionata, vedere [Come creare una raccolta DocumentDB usando il portale di Azure](documentdb-create-collection.md).

<!---HONumber=AcomDC_0720_2016-->