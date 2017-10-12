---
title: Gestire gli asset di dati in Azure Data Catalog | Microsoft Docs
description: "L'articolo illustra come controllare la visibilità e la proprietà degli asset di dati registrati in Azure Data Catalog."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 623f5ed4-8da7-48f5-943a-448d0b7cba69
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 08/15/2017
ms.author: maroche
ms.openlocfilehash: 8b9159b7bc4f7b2dac12d9012c6c903e75a6ac16
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-data-assets-in-azure-data-catalog"></a>Gestire gli asset di dati in Azure Data Catalog
## <a name="introduction"></a>Introduzione
Azure Data Catalog è progettato per l'individuazione delle origini dati, per poter comprendere e trovare facilmente le origini dati necessarie per eseguire analisi e prendere decisioni. Le funzionalità di individuazione fanno veramente la differenza quando tutti gli utenti hanno la possibilità di trovare e comprendere la più ampia gamma di origini dati disponibili. Con questi elementi, il comportamento predefinito di Data Catalog prevede che tutte le origini dati registrate siano visibili e individuabili da tutti gli utenti del catalogo.

Data Catalog non consente di accedere ai dati stessi. L'accesso ai dati è controllato dal proprietario dell'origine dati. Data Catalog consente di trovare le origini dati e di visualizzare i metadati correlati alle origini registrate nel catalogo.

In alcune situazioni tuttavia le origini dati devono essere visibili solo a utenti specifici o ai membri di gruppi specifici. In tali scenari gli utenti possono acquisire la proprietà di asset di dati registrati nel catalogo e quindi di controllare la visibilità degli asset di cui sono proprietari.

> [!NOTE]
> Le funzionalità descritte in questo articolo sono disponibili solo nell'edizione Standard di Azure Data Catalog. L'edizione gratuita non offre funzionalità relative alla proprietà e alla limitazione della visibilità di asset di dati.
>
>

## <a name="manage-ownership-of-data-assets"></a>Gestire la proprietà degli asset di dati
Per impostazione predefinita, gli asset di dati registrati in Data Catalog sono senza proprietario. Qualsiasi utente con l'autorizzazione necessaria per accedere al catalogo può trovare e annotare questi asset. Gli utenti possono acquisire la proprietà di asset di dati senza proprietario e quindi limitarne la visibilità.

Quando un asset di dati in Data Catalog ha dei proprietari, solo gli utenti autorizzati dai proprietari possono trovare l'asset e visualizzarne i metadati e solo i proprietari possono eliminare l'asset dal catalogo.

> [!NOTE]
> In Data Catalog la proprietà interessa unicamente i metadati archiviati nel catalogo. La proprietà non conferisce autorizzazioni per l'origine dati sottostante.
>
>

### <a name="take-ownership"></a>Diventare proprietario
Per acquisire la proprietà di asset di dati, gli utenti possono selezionare l'opzione **Diventa proprietario** nel portale di Data Catalog. Non sono necessarie speciali autorizzazioni per diventare proprietario di un asset di dati senza proprietario. Qualsiasi utente può diventare proprietario di un asset di dati senza proprietario.

### <a name="add-owners-and-co-owners"></a>Aggiungere proprietari e comproprietari
Se un asset di dati ha già un proprietario, gli altri utenti non possono semplicemente diventare proprietari. Devono essere aggiunti come comproprietari da un proprietario esistente. Qualsiasi proprietario può aggiungere altri utenti o gruppi di sicurezza come comproprietari.

> [!NOTE]
> È consigliabile che ogni asset di dati con proprietari abbia almeno due proprietari.
>
>

### <a name="remove-owners"></a>Rimuovere i proprietari
Qualsiasi proprietario di asset può rimuovere i relativi comproprietari.

Un proprietario di un asset che rimuove se stesso come proprietario, non potrà più gestire l'asset. Se il proprietario di un asset rimuove se stesso come proprietario e non esistono altri comproprietari, l'asset torna allo stato senza proprietario.

## <a name="control-visibility"></a>Controllare la visibilità
La visibilità degli asset di dati è controllata dai relativi proprietari. Per limitare la visibilità come impostazione predefinita, in cui tutti gli utenti di Data Catalog possono trovare e visualizzare l'asset di dati, il proprietario dell'asset può alternare l'impostazione di visibilità tra **Tutti** e **Proprietario e questi utenti** nelle proprietà dell'asset. I proprietari possono quindi aggiungere utenti e gruppi di sicurezza specifici.

> [!NOTE]
> Quando è possibile, la proprietà dell'asset e le autorizzazioni di visibilità devono essere assegnate a gruppi di sicurezza e non a singoli utenti.
>
>

## <a name="catalog-administrators"></a>Amministratori del catalogo
Gli amministratori di Data Catalog sono implicitamente comproprietari di tutti gli asset nel catalogo. I proprietari di asset non possono rimuovere la visibilità dagli amministratori, che possono gestire la proprietà e la visibilità per tutti gli asset di dati nel catalogo.

## <a name="summary"></a>Riepilogo
Il modello di crowdsourcing di Data Catalog per l'individuazione di asset di dati e metadati consente a tutti gli utenti del catalogo di contribuire all'individuazione. L'edizione Standard di Data Catalog è progettata per la proprietà e la gestione per poter limitare la visibilità e l'uso di asset di dati specifici.
