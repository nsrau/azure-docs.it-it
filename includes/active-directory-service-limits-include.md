---
title: File di inclusione
description: File di inclusione
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 07/30/2018
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: a2055c3f00306fbfdad3028a16bb49d919e1e251
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2018
ms.locfileid: "39361420"
---
Ecco le limitazioni d'utilizzo e altri limiti di servizio per il servizio Azure Active Directory (Azure AD).

| Categoria | Limiti |
| --- | --- |
| Directory | Un utente singolo può appartenere a un massimo di 500 directory di Azure AD come membro o guest.<br/>Un utente singolo può creare un massimo di 20 directory. |
| Domini | È possibile aggiungere non più di 900 nomi di dominio gestito. Se si intende configurare tutti i domini per la federazione con l'istanza locale di Active Directory, è possibile aggiungere non più di 450 nomi di dominio in ogni directory. |
| Oggetti |<ul><li>Gli utenti dell'edizione gratuita di Azure Active Directory possono creare fino a 500.000 oggetti in una singola directory.</li><li>Un utente non amministratore può creare al massimo 250 oggetti.</li></ul> |
| Estensioni dello schema |<ul><li>Le estensioni di tipo stringa possono contenere massimo 256 caratteri. </li><li>Le estensioni di tipo binario sono limitate a 256 byte.</li><li>100 valori di estensione (tra TUTTI i tipi e TUTTE le applicazioni) possono scritti in ogni singolo oggetto.</li><li>Solo le entità "User", "Group", "TenantDetail", "Device", "Application" e "ServicePrincipal" possono essere estese con gli attributi a valore singolo del tipo "String" o "Binary".</li><li>Le estensioni dello schema sono disponibili solo nella versione di anteprima dell'API Graph 1.21. All'applicazione deve essere concesso l'accesso in scrittura per registrare un'estensione.</li></ul> |
| APPLICAZIONI |Al massimo 100 utenti possono essere proprietari di una singola applicazione. |
| Gruppi |<ul><li>Al massimo 100 utenti possono essere proprietari di un singolo gruppo.</li><li>Qualsiasi numero di oggetti può essere membro di un singolo gruppo in Azure Active Directory.</li><li>Il numero di membri in un gruppo che è possibile sincronizzare tra l'istanza locale di Active Directory e Azure Active Directory usando Azure AD Connect è limitato a 50.000.</li></ul> |
| Pannello di accesso |<ul><li>Non esiste alcun limite al numero di applicazioni che è possibile visualizzare nel Pannello di accesso per ogni utente finale, per gli utenti a cui sono state assegnate licenze per Azure AD Premium o Enterprise Mobility Suite.</li><li>Al massimo 10 riquadri di app (ad esempio, Box, Salesforce o Dropbox) possono essere visualizzati nel Pannello di accesso per ogni utente finale per utenti con licenze assegnate per le edizioni Free o Basic di Azure Active Directory. Questo limite non si applica agli account di amministratore.</li></ul> |
| Report | È possibile visualizzare o scaricare in qualsiasi report un massimo di 1000 righe. Eventuali dati aggiuntivi vengono troncati. |
| Unità amministrative | Un oggetto può appartenere al massimo a 30 unità amministrative. |
