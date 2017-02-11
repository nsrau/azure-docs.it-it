---
title: Creare un indice di Ricerca di Azure usando il Portale di Azure| Microsoft Docs
description: Creare un indice nel portale di Azure.
services: search
manager: jhubbard
author: ashmaka
documentationcenter: 
ms.assetid: c54d8787-6dae-4943-85ed-c8928d2a5caf
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 08/29/2016
ms.author: ashmaka
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f0d468d0cb2cf76bb90e73d3fef3f6a8c14d1850


---
# <a name="create-an-azure-search-index-using-the-azure-portal"></a>Creare un indice di Ricerca di Azure nel portale di Azure
> [!div class="op_single_selector"]
> * [Panoramica](search-what-is-an-index.md)
> * [Portale](search-create-index-portal.md)
> * [.NET](search-create-index-dotnet.md)
> * [REST](search-create-index-rest-api.md)
> 
> 

Questo articolo illustra il processo di creazione di un [indice](search-what-is-an-index.md) di Ricerca di Azure mediante il portale di Azure.

Prima di seguire le indicazioni di questa guida e creare un indice, è necessario [creare un servizio Ricerca di Azure](search-create-service-portal.md).

## <a name="i-go-to-your-azure-search-blade"></a>I. Passaggio al pannello del servizio Ricerca di Azure
1. Scegliere "Tutte le risorse" dal menu nel lato sinistro del [portale di Azure](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)
2. Selezionare il servizio Ricerca di Azure

## <a name="ii-add-and-name-your-index"></a>II. Aggiungere e denominare l'indice
1. Fare clic sul pulsante "Aggiungi indice"
2. Assegnare un nome all'indice di Ricerca di Azure. Poiché in questa guida verrà creato un indice per cercare gli hotel, l'indice è stato chiamato "hotel".
   * Il nome dell'indice deve iniziare con una lettera e contenere solo lettere minuscole, cifre o trattini ("-").
   * Il nome dell'indice scelto, analogamente al nome del servizio, farà parte dell'URL dell'endpoint in cui verranno inviate le richieste HTTP per le API di ricerca di Azure
3. Fare clic sulla voce "Campi" per aprire un nuovo pannello

![](./media/search-create-index-portal/add-index.png)

## <a name="iii-create-and-define-the-fields-of-your-index"></a>III. Creare e definire i campi dell'indice
1. Selezionando la voce "Campi" verrà visualizzato un nuovo pannello con un modulo per immettere la definizione dell'indice.
2. Aggiungere i campi all'indice tramite il modulo.
   
   * Per ogni indice di Ricerca di Azure è obbligatorio un campo *chiave* di tipo Edm.String. Questo campo chiave viene creato per impostazione predefinita con il nome di campo "id". Nell'indice di questa guida, il nome è stato modificato in "hotelId".
   * Alcune proprietà dello schema dell'indice possono essere impostate solo una volta e non possono essere aggiornate in futuro. Per questo motivo, per il momento dopo la configurazione iniziale non è possibile eseguire aggiornamenti dello schema che richiedono la reindicizzazione, ad esempio la modifica dei tipi di campo.
   * I valori delle proprietà di ogni campo sono stati scelti con attenzione in base al modo in cui si pensa che verranno usati nell'applicazione. Nel progettare l'indice, tenere presenti le esigenze d'uso e di business degli utenti, assegnando a ogni campo le [proprietà appropriate](https://msdn.microsoft.com/library/azure/dn798941.aspx). Queste proprietà controllano le funzionalità di ricerca (filtro, facet, ordinamento, ricerca full-text e così via) che vengono applicate ai campi. Ad esempio, è probabile che gli utenti che cercano hotel siano interessati alle corrispondenze delle parole chiave del campo "descrizione", viene quindi abilitata la ricerca full-text per il campo impostando la proprietà "Ricercabile".
     * È anche possibile impostare l'[analizzatore del linguaggio](https://msdn.microsoft.com/en-us/library/azure/dn879793.aspx) per ogni campo, facendo clic sulla scheda "Analizzatore" nella parte superiore del pannello. Di seguito viene mostrato un analizzatore francese selezionato per un campo nell'indice destinato al testo in francese.
3. Fare clic su **OK** nel pannello "Campi" per confermare le definizioni del campo
4. Fare clic su **OK** nel pannello "Aggiungi indice" per salvare e creare l'indice che è appena stato definito.

Nelle schermate riportate di seguito viene mostrato come sono stati denominati e definiti i campi dell'indice "hotel".

![](./media/search-create-index-portal/field-definitions.png)

![](./media/search-create-index-portal/set-analyzer.png)

## <a name="next"></a>Avanti
Dopo avere creato un indice di Ricerca di Azure, sarà possibile [caricare il contenuto nell'indice](search-what-is-data-import.md) , in modo che si possa iniziare a eseguire ricerche nei dati.




<!--HONumber=Nov16_HO3-->


