---
title: Nomi del server alias di Azure Analysis Services | Documentazione Microsoft
description: Descrive come creare e utilizzare gli alias dei nomi del server.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
ms.assetid: 
ms.service: analysis-services
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: owend
ms.openlocfilehash: e0119580c4128ffd237127a62e29dac34297da5d
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="alias-server-names"></a>Nomi del server alias

Utilizzando un alias del nome del server, gli utenti possono connettersi al server Azure Analysis Services con un *alias* più breve anziché con il nome del server. Durante la connessione da un'applicazione client, l'alias viene specificato come un endpoint utilizzando il formato di protocollo **link://**. L'endpoint restituisce quindi il nome del server effettivo per la connessione.

I nomi di server alias sono ottimali per:

- Migrare modelli tra server senza effetti sugli utenti. 
- Ricordare i nomi del server descrittivi è più facile. 
- Indirizzare gli utenti a server diversi nei diversi momenti della giornata. 
- Indirizzare gli utenti in aree diverse a istanze geograficamente più vicine, ad esempio quando si utilizza Gestione traffico di Microsoft Azure. 

Qualsiasi endpoint HTTP che restituisce un nome del server valido di Azure Analysis Services può essere utilizzato come alias.

![Alias che utilizza formato link](media/analysis-services-alias/aas-alias-browser.png)

Durante la connessione da un client, il nome del server alias viene immesso utilizzando il formato del protocollo **link://**. Ad esempio, in Power BI Desktop:

![Connessione di Power BI Desktop](media/analysis-services-alias/aas-alias-connect-pbid.png)

## <a name="create-an-alias"></a>Creare un alias

Per creare un endpoint alias, è possibile utilizzare qualsiasi metodo che restituisce un nome del server valido di Azure Analysis Services. Ad esempio, un riferimento a un file nell'archiviazione BLOB di Azure che contiene l'effettivo nome del server oppure la creazione e la pubblicazione di un'applicazione Web Form ASP.NET.

In questo esempio viene creata un'applicazione Web Form ASP.NET in Visual Studio. Il riferimento alla pagina master e il controllo utente vengono rimossi dalla pagina Default.aspx. Il contenuto di Default.aspx è semplicemente la direttiva di pagina seguente:

```
<%@ Page Title="Home Page" Language="C#" AutoEventWireup="true" CodeBehind="Default.aspx.cs" Inherits="FriendlyRedirect._Default" %>
```

L'evento Page_Load di Default.aspx.cs utilizza il metodo Response.Write() per restituire il nome del server di Azure Analysis Services.

```
protected void Page_Load(object sender, EventArgs e)
{
    this.Response.Write("asazure://<region>.asazure.windows.net/<servername>");
}
```

## <a name="see-also"></a>Vedere anche 

[Librerie client](analysis-services-data-providers.md)   
[Connettersi con Power BI Desktop](analysis-services-connect-pbi.md)
