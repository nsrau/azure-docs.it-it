---
title: File di inclusione
description: File di inclusione
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 02/19/2019
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: 619dd7f3b01e2e7ce71e945fce77aa73cb87f264
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2019
ms.locfileid: "56443313"
---
Ecco le limitazioni d'utilizzo e altri limiti di servizio per il servizio Azure Active Directory (Azure AD).

| Categoria | Limiti |
| --- | --- |
| Directory | Un utente singolo può appartenere a un massimo di 500 directory di Azure AD come membro o guest.<br/>Un utente singolo può creare un massimo di 20 directory. |
| Domini | È possibile aggiungere non più di 900 nomi di dominio gestito. Se si intende configurare tutti i domini per la federazione con l'istanza locale di Active Directory, è possibile aggiungere non più di 450 nomi di dominio in ogni directory. |
| Oggetti |<ul><li>Gli utenti dell'edizione gratuita di Azure Active Directory possono creare fino a 500.000 oggetti in una singola directory.</li><li>Un utente non amministratore può creare al massimo 250 oggetti.</li></ul> |
| Estensioni dello schema |<ul><li>Le estensioni di tipo stringa possono contenere massimo 256 caratteri. </li><li>Le estensioni di tipo binario sono limitate a 256 byte.</li><li>100 valori di estensione (tra TUTTI i tipi e TUTTE le applicazioni) possono scritti in ogni singolo oggetto.</li><li>Solo le entità "User", "Group", "TenantDetail", "Device", "Application" e "ServicePrincipal" possono essere estese con gli attributi a valore singolo del tipo "String" o "Binary".</li><li>Le estensioni dello schema sono disponibili solo nella versione di anteprima dell'API Graph 1.21. All'applicazione deve essere concesso l'accesso in scrittura per registrare un'estensione.</li></ul> |
| APPLICAZIONI |Al massimo 100 utenti possono essere proprietari di una singola applicazione. |
| Gruppi |<ul><li>Al massimo 100 utenti possono essere proprietari di un singolo gruppo.</li><li>Qualsiasi numero di oggetti può essere membro di un singolo gruppo.</li><li>Un utente può essere un membro di un numero qualsiasi di gruppi.</li><li>Il numero di membri in un gruppo che è possibile sincronizzare tra l'istanza locale di Active Directory e Azure Active Directory usando Azure AD Connect è limitato a 50.000.</li></ul> |
| Pannello di accesso |<ul><li>Non esiste alcun limite al numero di applicazioni che è possibile visualizzare nel Pannello di accesso per ogni utente finale, per gli utenti a cui sono state assegnate licenze per Azure AD Premium o Enterprise Mobility Suite.</li><li>Fino a un massimo di 10 riquadri di app (ad esempio: Box, Salesforce o Dropbox) possono essere visualizzati nel Pannello di accesso per ogni utente finale per utenti con licenze assegnate per le edizioni Free o Basic di Azure Active Directory. Questo limite non si applica agli account di amministratore.</li></ul> |
| Report | È possibile visualizzare o scaricare in qualsiasi report un massimo di 1000 righe. Eventuali dati aggiuntivi vengono troncati. |
| Unità amministrative | Un oggetto può appartenere al massimo a 30 unità amministrative. |
| Autorizzazioni e ruoli amministrativi | <li>Un gruppo non può essere aggiunto come proprietario<li>Un gruppo non può essere assegnato a un ruolo<li>Non è possibile modificare le autorizzazioni utente predefinite oltre ai parametri del tenant (impostazioni utente in Azure AD) |
