---
title: Nomi di server di Azure Analysis Services alias | Documenti Microsoft
description: Viene descritto come creare e utilizzare gli alias dei nomi di server.
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
ms.date: 11/01/2017
ms.author: owend
ms.openlocfilehash: a947dde1551c653faa54f088c1712c41c7657aa0
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2017
---
# <a name="alias-server-names"></a>Nomi di alias server

Utilizzando un alias del nome del server, gli utenti possono connettersi al server Azure Analysis Services con un più breve *alias* anziché il nome del server. Durante la connessione da un'applicazione client, l'alias viene specificato come un endpoint utilizzando il **collegamento: / /** al formato del protocollo. L'endpoint restituisce quindi il nome del server effettivo per la connessione.

I nomi di alias server sono ideali per:

- La migrazione di modelli tra i server senza influire su utenti. 
- I nomi descrittivi server sono più facili da ricordare. 
- Indirizzare gli utenti a server diversi in momenti diversi del giorno. 
- Indirizzare gli utenti in aree diverse per istanze di geograficamente più vicino, quando si utilizza Traffic Manager di Azure. 

Qualsiasi endpoint HTTP che restituisce un nome server valido di Azure Analysis Services può essere utilizzato come alias.

![Alias utilizzando il formato di collegamento](media/analysis-services-alias/aas-alias-browser.png)

Durante la connessione da un client, il nome del server alias viene immesso utilizzando **collegamento: / /** al formato del protocollo. Ad esempio, in Power BI Desktop:

![Connessione di Power BI Desktop](media/analysis-services-alias/aas-alias-connect-pbid.png)

## <a name="create-an-alias"></a>Crea un alias

Per creare un endpoint di alias, è possibile utilizzare qualsiasi metodo che restituisce un nome server valido di Azure Analysis Services. Ad esempio, un riferimento a un file nell'archiviazione Blob di Azure che contiene il server effettivo nome, o di creare e pubblicare un'applicazione Web Form ASP.NET.

In questo esempio, un'applicazione Web Form di ASP.NET viene creata in Visual Studio. Il controllo utente e di riferimento della pagina master vengono rimossi dalla pagina Default.aspx. Il contenuto di Default.aspx è semplicemente la seguente direttiva di pagina.

```
<%@ Page Title="Home Page" Language="C#" AutoEventWireup="true" CodeBehind="Default.aspx.cs" Inherits="FriendlyRedirect._Default" %>
```

Evento Page_Load Default.aspx.cs utilizza il metodo Response per restituire il nome del server Azure Analysis Services.

```
protected void Page_Load(object sender, EventArgs e)
{
    this.Response.Write("asazure://<region>.asazure.windows.net/<servername>");
}
```

## <a name="see-also"></a>Vedere anche 

[Librerie client](analysis-services-data-providers.md)   
[La connessione da Power BI Desktop](analysis-services-connect-pbi.md)
