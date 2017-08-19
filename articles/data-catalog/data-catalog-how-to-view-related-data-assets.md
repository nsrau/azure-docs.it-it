---
title: Come visualizzare gli asset di dati correlati in Azure Data Catalog | Microsoft Docs
description: In questo articolo viene illustrato come visualizzare gli asset di dati correlati di un asset di dati selezionato in Azure Data Catalog.
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 08/17/2017
ms.author: maroche
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: ae028d3fafe0afac859055a61e2d5a154676f1b2
ms.contentlocale: it-it
ms.lasthandoff: 07/08/2017

---
# <a name="how-to-view-related-data-assets-in-azure-data-catalog"></a>Come visualizzare gli asset di dati correlati in Azure Data Catalog
Azure Data Catalog consente di visualizzare gli asset di dati correlati in un asset di dati selezionato e di visualizzare le relazioni esistenti tra di essi. 

## <a name="supported-data-sources"></a>Origini dati supportate 
Quando si registrano gli asset di dati dalle origini dati seguenti, Azure Data Catalog registra automaticamente i metadati relativi alle relazioni di join tra gli asset di dati selezionati. 

- SQL Server
- Database SQL di Azure
- MySQL
- Oracle

## <a name="view-related-data-assets"></a>Visualizzare gli asset di dati correlati
Per visualizzare gli asset di dati che sono correlati a un set di dati selezionato, usare la scheda **Relazioni** come illustrato nella figura seguente: 

![Azure Data Catalog - Visualizzare gli asset di dati correlati](media\data-catalog-how-to-view-related-data-assets\relationships-tab.png)

In questo esempio sono presenti due relazioni per l'asset di dati **ProductSubcategory** selezionato: 

- La colonna ProductSubcategoryID della tabella Prodotto dispone di una relazione di chiave esterna con la colonna ProductSubcategoryID della tabella ProductSubcategory selezionata. 
- La colonna ProductCategoryID della tabella ProductSubCategory dispone di una relazione di chiave esterna con la colonna ProductCategoryID della tabella ProductCategory selezionata.

> [!NOTE]
> Si noti la direzione della freccia nella visualizzazione struttura ad albero delle relazioni.  

Per visualizzare altri dettagli, ad esempio il nome completo della colonna, spostare il puntatore del mouse verso l'alto per mostrare un elemento popup simile a quello dell'immagine seguente: 

![Azure Data Catalog - Elemento popup della relazione](media\data-catalog-how-to-view-related-data-assets\relationship-popup.png)

Per includere le relazioni tra gli asset già registrati, è necessario registrare nuovamente gli asset.

## <a name="next-steps"></a>Passaggi successivi
- [Come gestire gli asset di dati](data-catalog-how-to-manage.md)
