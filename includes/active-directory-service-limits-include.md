---
title: File di inclusione
description: File di inclusione
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 02/21/2019
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: 38f2dd301ddc2a5f8d28322856b2011bd2034c30
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554602"
---
Ecco le limitazioni d'utilizzo e altri limiti di servizio per il servizio Azure Active Directory (Azure AD).

| Categoria | Limiti |
| --- | --- |
| Directory | Un utente singolo può appartenere a un massimo di 500 directory di Azure AD come membro o guest.<br/>Un utente singolo può creare un massimo di 20 directory. |
| Domini | È possibile aggiungere non più di 900 nomi di dominio gestito. Se si imposta tutti i domini per la federazione con Active Directory in locale, è possibile aggiungere non più di 450 nomi di dominio in ogni directory. |
| Oggetti |<ul><li>Gli utenti dell'edizione gratuita di Azure Active Directory possono creare fino a 500.000 oggetti in una singola directory.</li><li>Un utente non amministratore può creare al massimo 250 oggetti. Oggetti attivi e gli oggetti eliminati che sono possibile ripristinare conteggiati a questa quota. Solo gli oggetti eliminati che sono stati eliminati meno di 30 giorni fa sono disponibili per il ripristino. Gli oggetti eliminati che non sono più disponibili per il ripristino viene conteggiata per questa quota per un valore di un quarto per 30 giorni. Forse [assegnare un ruolo di amministratore](../articles/active-directory/users-groups-roles/directory-assign-admin-roles.md) agli utenti senza privilegi di amministratore che sono probabile che più volte superare questa quota durante le normali attività.</li></ul> |
| Estensioni dello schema |<ul><li>Le estensioni di tipo stringa possono avere un massimo di 256 caratteri. </li><li>Le estensioni di tipo binario sono limitate a 256 byte.</li><li>Solo 100 valori di estensione, attraverso *tutti i* tipi e *tutti* applicazioni, possono essere scritte in un oggetto.</li><li>Solo le entità utente, gruppo, TenantDetail, dispositivo, applicazione ed entità servizio possono essere estese con gli attributi a valore singolo di tipo binario o di tipo stringa.</li><li>Estensioni dello schema sono disponibili solo nella versione pari a 1,21 preview API Graph. All'applicazione deve essere concesso l'accesso in scrittura per registrare un'estensione.</li></ul> |
| APPLICAZIONI |Al massimo 100 utenti possono essere proprietari di una singola applicazione. |
| Gruppi |<ul><li>Al massimo 100 utenti possono essere proprietari di un singolo gruppo.</li><li>Qualsiasi numero di oggetti può essere membro di un singolo gruppo.</li><li>Un utente può essere un membro di un numero qualsiasi di gruppi.</li><li>Il numero di membri in un gruppo che è possibile sincronizzare dall'ambiente Active Directory locale ad Azure Active Directory con Azure AD Connect è limitato a 50.000 membri.</li></ul> |
| Pannello di accesso |<ul><li>Non sono previsti limiti al numero di applicazioni che possono essere visualizzati nel Pannello di accesso per ogni utente. Questo vale per utenti con licenze assegnate per Azure AD Premium o Enterprise Mobility Suite.</li><li>Massimo 10 riquadri di app può essere visualizzato nel Pannello di accesso per ogni utente. Questo limite si applica per gli utenti che sono assegnati licenze gratuite o Azure AD Basic Edition di Azure Active Directory. Sono esempi di app i riquadri casella, Salesforce o Dropbox. Questo limite non si applica agli account amministratore.</li></ul> |
| Report | È possibile visualizzare o scaricare in qualsiasi report un massimo di 1000 righe. Eventuali dati aggiuntivi vengono troncati. |
| Unità amministrative | Un oggetto può appartenere al massimo a 30 unità amministrative. |
| Autorizzazioni e ruoli amministrativi | <li>Un gruppo non può essere aggiunto come proprietario.<li>Un gruppo non può essere assegnato a un ruolo.<li>Impostazione predefinita utente non è possibile modificare le autorizzazioni eccetto commutatori di tenant, che sono le impostazioni utente in Azure AD. |
