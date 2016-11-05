---
title: Come gestire gli asset di dati | Microsoft Docs
description: Articolo sulle procedure che illustra come controllare la visibilità e la proprietà di asset di dati registrati in Azure Data Catalog.
services: data-catalog
documentationcenter: ''
author: steelanddata
manager: NA
editor: ''
tags: ''

ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 10/04/2016
ms.author: maroche

---
# <a name="how-to-manage-data-assets"></a>Come gestire gli asset di dati
## <a name="introduction"></a>Introduzione
**Azure Data Catalog** offre funzionalità di individuazione delle origini dati e permette agli utenti di comprendere e trovare facilmente le origini dati necessarie per eseguire analisi e prendere decisioni. Le funzionalità di individuazione fanno veramente la differenza quando tutti gli utenti hanno la possibilità di trovare e comprendere la più ampia gamma di origini dati disponibili. Con questa premessa, il comportamento predefinito di Azure Data Catalog prevede che tutte le origini dati registrate siano visibili e individuabili da tutti gli utenti del catalogo.

Azure Data Catalog non consente agli utenti di accedere ai dati stessi. L'accesso ai dati è controllato dal proprietario dell'origine dati. Azure Data Catalog consente agli utenti di trovare le origini dati e di visualizzare i metadati correlati alle origini registrate nel catalogo.

In alcune situazioni, tuttavia, le origini dati devono essere visibili solo a utenti specifici o ai membri di gruppi specifici. Per questi scenari, Azure Data Catalog consente agli utenti di acquisire la proprietà di asset di dati registrati all'interno del catalogo e quindi di controllare la visibilità degli asset di cui sono proprietari.

> [!NOTE]
> Le funzionalità descritte in questo articolo sono disponibili solo nell'edizione Standard di Azure Data Catalog. L'edizione gratuita non offre funzionalità relative alla proprietà e alla limitazione della visibilità di asset di dati.
> 
> 

## <a name="managing-ownership-of-data-assets"></a>Gestione della proprietà di asset di dati
Per impostazione predefinita, gli asset di dati registrati in Azure Data Catalog sono senza proprietario. Qualsiasi utente autorizzato ad accedere al catalogo può trovare e annotare tali risorse. Gli utenti possono acquisire la proprietà di asset di dati senza proprietario e quindi limitarne la visibilità.

Quando un asset di dati in Azure Data Catalog ha dei proprietari, solo gli utenti autorizzati dai proprietari possono trovare l'asset e visualizzarne i metadati e solo i proprietari possono eliminare l'asset dal catalogo.

> [!NOTE]
> In Azure Data Catalog la proprietà interessa unicamente i metadati archiviati nel catalogo, non conferisce autorizzazioni per l'origine dati sottostante.
> 
> 

### <a name="taking-ownership"></a>Acquisizione della proprietà
Per acquisire la proprietà di asset di dati gli utenti possono selezionare l'opzione "Diventa proprietario" nel portale di Azure Data Catalog. Non sono richieste autorizzazioni speciali per acquisire la proprietà di un asset di dati senza proprietario. Tutti gli utenti possono farlo.

### <a name="adding-owners-and-co-owners"></a>Aggiunta di proprietari e comproprietari
Se un asset di dati ha già dei proprietari, gli utenti non possono semplicemente acquisirne la proprietà ma devono essere aggiunti come comproprietari da un proprietario esistente. Qualsiasi proprietario può aggiungere altri utenti o gruppi di sicurezza come comproprietari.

> [!NOTE]
> È consigliabile che ogni asset di dati con proprietari abbia almeno due proprietari.
> 
> 

### <a name="removing-owners"></a>Rimozione di proprietari
Qualsiasi proprietario di asset può rimuovere i relativi comproprietari.

Se un proprietario di asset rimuove se stesso come proprietario, non potrà più gestire l'asset. Se un proprietario di asset rimuove se stesso come proprietario e non esistono altri comproprietari, l'asset tornerà allo stato senza proprietario.

## <a name="visibility"></a>Visibilità
La visibilità degli asset di dati è controllata dai relativi proprietari. Per limitare la visibilità rispetto all'impostazione predefinita, in cui tutti gli utenti di Azure Data Catalog possono trovare e visualizzare l'asset di dati, il proprietario dell'asset può alternare l'impostazione di visibilità tra "Tutti" e "Proprietario e questi utenti" nelle proprietà dell'asset. I proprietari possono quindi aggiungere utenti e gruppi di sicurezza specifici.

> [!NOTE]
> Quando è possibile, la proprietà dell'asset e le autorizzazioni di visibilità devono essere assegnate a gruppi di sicurezza e non a singoli utenti.
> 
> 

## <a name="catalog-administrators"></a>Amministratori del catalogo
Gli amministratori di Azure Data Catalog sono implicitamente comproprietari di tutti gli asset nel catalogo. I proprietari di asset non possono rimuovere la visibilità dagli amministratori del catalogo, che possono gestire la proprietà e la visibilità per tutti gli asset di dati nel catalogo.

## <a name="summary"></a>Riepilogo
Il modello di crowdsourcing di Azure Data Catalog per l'individuazione di asset di dati e metadati consente a tutti gli utenti del catalogo di contribuire all'individuazione. L'edizione Standard di Azure Data Catalog offre funzionalità di gestione e proprietà per limitare la visibilità e l'uso di asset di dati specifici.

<!--HONumber=Oct16_HO2-->


