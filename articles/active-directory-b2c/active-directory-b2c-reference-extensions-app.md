---
title: App Estensioni - Azure AD B2C | Microsoft Docs
description: Ripristino di b2c-extensions-app
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: f0392e32-0771-473c-a799-81438ca2bcff
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 9/06/2017
ms.author: parja
ms.translationtype: HT
ms.sourcegitcommit: eeed445631885093a8e1799a8a5e1bcc69214fe6
ms.openlocfilehash: a2b883ad010b93ec83c273988493412c06597641
ms.contentlocale: it-it
ms.lasthandoff: 09/07/2017

---
# <a name="azure-ad-b2c-extensions-app"></a>Azure AD B2C: app Estensioni

Quando viene creata una directory di Azure AD B2C, un'app denominata `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` viene creata automaticamente nella nuova directory. Questa app, definita **b2c-extensions-app**, è visibile in *Registrazioni per l'app*. Viene usata dal servizio Azure AD B2C per archiviare informazioni sugli utenti e sugli attributi personalizzati. Se l'app viene eliminata, Azure AD B2C non funzionerà correttamente e questo problema influirà sull'ambiente di produzione.

> [!IMPORTANT]
> Eliminare l'app b2c-extensions-app solo se si prevede l'eliminazione immediata del tenant. Se l'app rimane eliminata per più di 30 giorni, le informazioni utente andranno perse definitivamente.

## <a name="verifying-that-the-extensions-app-is-present"></a>Verifica della presenza dell'app Estensioni

Per verificare che l'app b2c-extensions-app sia presente:

1. Nel tenant di Azure AD B2C fare clic su **Altri servizi** nel menu di spostamento a sinistra.
1. Cercare e aprire **Registrazioni per l'app**.
1. Cercare un'app che inizia con **b2c-extensions-app**

## <a name="recover-the-extensions-app"></a>Recuperare l'app Estensioni

Se l'app b2c-extensions-app è stata eliminata accidentalmente, sono disponibili 30 giorni per il recupero. È possibile ripristinare l'app usando l'API Graph:

1. Passare a [https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/).
1. Accedere al sito come amministratore globale per la directory di Azure AD B2C per cui si vuole ripristinare l'app eliminata. L'amministratore globale deve avere un indirizzo di posta elettronica simile al seguente: `username@{yourTenant}.onmicrosoft.com`.
1. Emettere una richiesta HTTP GET per l'URL `https://graph.windows.net/myorganization/deletedApplications` con api-version=1.6. Questa operazione consente di elencare tutte le applicazioni eliminate negli ultimi 30 giorni.
1. Trovare nell'elenco l'applicazione il cui nome inizia con 'b2c-extension-app' e copiare il valore della rispettiva proprietà `objectid`.
1. Emettere una richiesta HTTP POST per l'URL `https://graph.windows.net/myorganization/deletedApplications/{OBJECTID}/restore`. Sostituire la parte `{OBJECTID}` dell'URL con il valore `objectid` del passaggio precedente. 

Dovrebbe essere possibile [visualizzare l'app ripristinata](#verifying-that-the-extensions-app-is-present) nel portale di Azure.

> [!NOTE]
> Un'applicazione può essere ripristinata solo se è stata eliminata negli ultimi 30 giorni. Se sono trascorsi più di 30 giorni, i dati andranno persi definitivamente. Per ottenere assistenza, inviare un ticket al supporto tecnico.
