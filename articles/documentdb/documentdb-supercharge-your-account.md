---
title: Potenziare l&quot;account DocumentDB S1 | Documentazione Microsoft
description: "Ottenere una maggiore velocità effettiva nell&quot;account DocumentDB S1 con alcune semplici modifiche nel portale di Azure."
services: documentdb
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 6f373fb6-b0d9-4745-b17c-88e8bc5f906a
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2016
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 166e0bc7838510b52324fd9a040acd21d55118c4


---
# <a name="supercharge-your-documentdb-account"></a>Potenziare l'account DocumentDB
Seguire questa procedura per ottenere una maggiore velocità effettiva per l'account Azure DocumentDB S1. È possibile aumentare la velocità effettiva dell'account S1 esistente da 250 [UR/sec](documentdb-request-units.md) a oltre 400 UR/sec senza alcun costo aggiuntivo o con un costo aggiuntivo minimo.  

> [!VIDEO https://channel9.msdn.com/Blogs/AzureDocumentDB/ChangeDocumentDBCollectionPerformance/player]
> 
> 

## <a name="change-to-user-defined-performance-in-the-azure-portal"></a>Passare alle prestazioni definite dall'utente nel portale di Azure
1. Nel browser passare al [**portale di Azure**](https://portal.azure.com). 
2. Fare clic su **Esplora** -> **DocumentDB (NoSQL)** e quindi selezionare l'account DocumentDB da modificare.   
3. Selezionare il database da modificare nella sezione **Database** e quindi selezionare la raccolta con il piano tariffario S1 nel pannello **Database**.
   
      ![Screenshot del nuovo pannello Database con una raccolta S1](./media/documentdb-supercharge-your-account/documentdb-change-performance-S1.png)
4. Nel pannello **Raccolta** fare clic su **Altro** e quindi su **Impostazioni**.   
5. Nel pannello **Impostazioni** fare clic su **Piano tariffario**. Verrà visualizzata la stima dei costi mensili per ogni piano. Nel pannello **Scegliere il piano tariffario** fare clic su **Standard** e quindi fare clic su **Seleziona** per salvare la modifica.
   
      ![Screenshot dei pannelli Impostazioni e Scegliere il piano tariffario di DocumentDB](./media/documentdb-supercharge-your-account/documentdb-change-performance.png)
6. Nel pannello **Impostazioni** il **Piano tariffario** passa a **Standard** e la casella **Velocità effettiva (UR/sec)** riporta un valore predefinito di 400. Fare clic su **OK** per salvare le modifiche. 
   
   > [!NOTE]
   > È possibile impostare la velocità effettiva tra 400 e 10.000 [unità richiesta](documentdb-request-units.md) al secondo (UR/sec). **Riepilogo dei prezzi** nella parte inferiore della pagina si aggiorna automaticamente per fornire una stima del costo mensile.
   > 
   > 
   
    ![Screenshot del pannello Impostazioni che mostra dove modificare il valore della velocità effettiva](./media/documentdb-supercharge-your-account/documentdb-change-performance-set-thoughput.png)
7. Nel pannello **Database** è possibile verificare la velocità effettiva potenziata della raccolta. 
   
    ![Screenshot del pannello Database con la raccolta modificata](./media/documentdb-supercharge-your-account/documentdb-change-performance-confirmation.png)

Per altre informazioni sulle modifiche alla velocità effettiva definita dall'utente e quella predefinita, vedere il post del blog [DocumentDB: Everything you need to know about using the new pricing options](https://azure.microsoft.com/blog/documentdb-use-the-new-pricing-options-on-your-existing-collections/)(DocumentDB: tutto quello che occorre sapere sull'uso delle nuove opzioni relative ai prezzi).

## <a name="next-steps"></a>Passaggi successivi
Se è necessaria una velocità effettiva maggiore di 10.000 UR/sec o uno spazio di archiviazione maggiore di 10 GB è possibile creare una raccolta partizionata. Per creare una raccolta partizionata, vedere [Come creare una raccolta DocumentDB usando il portale di Azure](documentdb-create-collection.md).




<!--HONumber=Nov16_HO3-->


