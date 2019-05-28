---
title: File di inclusione
description: File di inclusione
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 05/22/2019
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: 3b1019d45f4fee60e0e197f283ef38f4f3fca875
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66143072"
---
Ecco le limitazioni d'utilizzo e altri limiti di servizio per il servizio Azure Active Directory (Azure AD).

| Category | Limiti |
| --- | --- |
| Directory | Un utente singolo può appartenere a un massimo di 500 directory di Azure AD come membro o guest.<br/>Un utente singolo può creare un massimo di 20 directory. |
| Domini | È possibile aggiungere non più di 900 nomi di dominio gestito. Se si configurano tutti i domini per la federazione con l'istanza locale di Active Directory, è possibile aggiungere non più di 450 nomi di dominio in ogni directory. |
| Oggetti |<ul><li>Per impostazione predefinita, gli utenti dell'edizione gratuita di Azure Active Directory possono creare fino a 50.000 oggetti in una singola directory. Se è disponibile almeno un dominio verificato, la quota predefinita del servizio directory in Azure AD viene estesa a 300.000 oggetti. </li><li>Un utente non amministratore può creare al massimo 250 oggetti. I n questa quota vengono conteggiati sia gli oggetti attivi che gli oggetti eliminati disponibili per il ripristino. Solo gli oggetti eliminati da meno di 30 giorni sono disponibili per il ripristino. Gli oggetti eliminati che non sono più disponibili per il ripristino vengono conteggiati in questa quota con un valore di un quarto per 30 giorni. È possibile [assegnare un ruolo di amministratore](../articles/active-directory/users-groups-roles/directory-assign-admin-roles.md) a utenti senza privilegi di amministratore che possono superare ripetutamente questa quota nel corso delle normali attività.</li></ul> |
| Estensioni dello schema |<ul><li>Le estensioni di tipo stringa possono contenere un massimo di 256 caratteri. </li><li>Le estensioni di tipo binario sono limitate a 256 byte.</li><li>Solo 100 valori di estensione, tra *tutti* i tipi e *tutte* le applicazioni, possono essere scritti in un singolo oggetto.</li><li>Solo le entità User, Group, TenantDetail, Device, Application e ServicePrincipal possono essere estese con gli attributi a valore singolo di tipo stringa o di tipo binario.</li><li>Le estensioni dello schema sono disponibili solo nella versione di anteprima dell'API Graph 1.21. All'applicazione deve essere concesso l'accesso in scrittura per registrare un'estensione.</li></ul> |
| Applicazioni |Al massimo 100 utenti possono essere proprietari di una singola applicazione. |
| Gruppi |<ul><li>Al massimo 100 utenti possono essere proprietari di un singolo gruppo.</li><li>Qualsiasi numero di oggetti può essere membro di un singolo gruppo.</li><li>Un utente può essere un membro di un numero qualsiasi di gruppi.</li><li>Il numero di membri in un gruppo che è possibile sincronizzare tra l'istanza locale di Active Directory e Azure Active Directory usando Azure AD Connect è limitato a 50.000.</li></ul> |
| Proxy dell'applicazione | <ul><li>Un massimo di 500 transazioni al secondo per ogni App Proxy di applicazione</li><li>Un massimo di 750 transazioni al secondo per il tenant</li></ul><br/>Una transazione viene definita come una singola richiesta e risposta http per una risorsa univoca. Se limitate, i client riceveranno una risposta 429 (troppe richieste). |
| Pannello di accesso |<ul><li>Non sono previsti limiti al numero di applicazioni che possono essere visualizzate nel Pannello di accesso per ogni utente. Questo vale per gli utenti a cui sono state assegnate licenze per Azure AD Premium o Enterprise Mobility Suite.</li><li>Nel Pannello di accesso è possibile visualizzare un massimo di 10 riquadri di app per ogni utente. Questo limite si applica agli utenti a cui sono state assegnate licenze per le edizioni Free o Basic di Azure Active Directory. Esempi di riquadri di app includono Box, Salesforce o Dropbox. Questo limite non si applica agli account di amministratore.</li></ul> |
| Report | È possibile visualizzare o scaricare in qualsiasi report un massimo di 1000 righe. Eventuali dati aggiuntivi vengono troncati. |
| Unità amministrative | Un oggetto può appartenere al massimo a 30 unità amministrative. |
| Autorizzazioni e ruoli amministrativi | <li>Non è possibile aggiungere un gruppo come proprietario.<li>Non è possibile assegnare un gruppo a un ruolo.<li>Non è possibile modificare le autorizzazioni utente predefinite, ad eccezione delle opzioni del tenant che sono impostazioni utente in Azure AD. |
