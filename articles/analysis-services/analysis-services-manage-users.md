---
title: Gestire gli utenti in Azure Analysis Services | Documentazione Microsoft
description: Informazioni su come gestire gli utenti in un server Analysis Services in Azure.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 01/18/2016
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 194910a3e4cb655b39a64d2540994d90d34a68e4
ms.openlocfilehash: 039ed6f4be9f3e0f6b92e5a9f11e12392912df9d
ms.lasthandoff: 02/16/2017


---
# <a name="manage-users-in-azure-analysis-services"></a>Gestire gli utenti in Azure Analysis Services
In Azure Analysis Services ci sono due tipi di utenti, gli amministratori del server e gli utenti del database. 

## <a name="server-administrators"></a>Amministratori del server
È possibile usare **Amministratori di Analysis Services** nel pannello di controllo del server nel portale di Azure o Proprietà server in SSMS per gestire gli amministratori del server. Gli amministratori di Analysis Services sono gli amministratori del server di database con diritti per attività di amministrazione di database comuni, ad esempio l'aggiunta e la rimozione di database e la gestione degli utenti. Per impostazione predefinita, l'utente che crea il server nel portale di Azure viene automaticamente aggiunto come amministratore di Analysis Services.

![Amministratori del server nel portale di Azure](./media/analysis-services-manage-users/aas-manage-users-admins.png)

È inoltre utile sapere che:

* Windows Live ID non è un tipo di identità supportato per Azure Analysis Services.  
* Gli amministratori di Analysis Services devono essere utenti validi di Azure Active Directory.
* Se si crea un server di Azure Analysis Services tramite modelli di Azure Resource Manager, gli amministratori di Analysis Services accettano una matrice JSON degli utenti che devono essere aggiunti come amministratori.

Gli amministratori di Analysis Services possono essere diversi dagli amministratori di risorse di Azure, che possono gestire le risorse per le sottoscrizioni di Azure. In questo modo, viene mantenuta la compatibilità con i comportamenti di gestione XMLA e TMSL esistenti in Analysis Services e si consente di separare i compiti tra la gestione delle risorse di Azure e la gestione del database di Azure. Per visualizzare tutti i ruoli e accedere ai tipi della risorsa di Azure Analysis Services, usare il Controllo di accesso (IAM) nel Pannello di controllo.

### <a name="to-add-administrators-using-azure-portal"></a>Per aggiungere amministratori usando il portale di Azure
1. Nel pannello di controllo del server fare clic su **Amministratori di Analysis Services**.
2. Nel pannello **\<nomeserver> - Amministratori di Analysis Services** fare clic su **Aggiungi**.
3. Nel pannello **Aggiungi amministratori del server** selezionare gli account degli utenti da aggiungere.

## <a name="database-users"></a>Utenti database
Gli utenti del database devono essere aggiunti ai ruoli del database. I ruoli definiscono gli utenti e i gruppi con le stesse autorizzazioni per un database. Per impostazione predefinita, i database modello tabulare hanno un ruolo Utenti predefinito con autorizzazioni di lettura. Per altre informazioni, vedere [Ruoli nei modelli tabulari](https://msdn.microsoft.com/library/hh213165.aspx).

Gli utenti del database modello di Azure Analysis Services *devono trovarsi in Azure Active Directory*. I nomi utente specificati devono basarsi sull'indirizzo di posta elettronica aziendale o sull'UPN. Questo scenario è diverso dai database modello tabulare locali che supportano gli utenti in base ai nomi utente di dominio di Windows. 

È possibile creare ruoli del database, aggiungere utenti e gruppi ai ruoli e configurare la sicurezza a livello di riga in SQL Server Data Tools (SSDT) o in SQL Server Management Studio (SSMS). È anche possibile aggiungere o rimuovere utenti nei ruoli usando i [cmdlet di Analysis Services per PowerShell](https://msdn.microsoft.com/library/hh758425.aspx) o usando [Tabular Model Scripting Language](https://msdn.microsoft.com/library/mt614797.aspx) (TMSL).

**Script di esempio del linguaggio di scripting del modello tabulare**

In questo esempio un utente e un gruppo vengono aggiunti al ruolo Utenti per il database SalesBI.

```
{
  "createOrReplace": {
    "object": {
      "database": "SalesBI",
      "role": "Users"
    },
    "role": {
      "name": "Users",
      "description": "All allowed users to query the model",
      "modelPermission": "read",
      "members": [
        {
          "memberName": "user1@contoso.com",
          "identityProvider": "AzureAD"
        },
        {
          "memberName": "group1@contoso.com",
          "identityProvider": "AzureAD"
        }
      ]
    }
  }
}
```

## <a name="role-based-access-control-rbac"></a>Controllo degli accessi in base al ruolo

Gli amministratori della sottoscrizione possono usare il **Controllo di accesso** nel pannello di controllo per configurare i ruoli. Non si tratta dello stesso controllo degli amministratori di server o degli utenti del database che, come descritto in precedenza, sono configurati a livello di database o server. 

![Controllo di accesso nel portale di Azure](./media/analysis-services-manage-users/aas-manage-users-rbac.png)

I ruoli si applicano agli utenti o agli account che devono eseguire attività completabili nel portale o tramite i modelli di Azure Resource Manager. Per altre informazioni, vedere l'articolo relativo al [controllo degli accessi in base al ruolo](../active-directory/role-based-access-control-what-is.md).

## <a name="next-steps"></a>Passaggi successivi
Se non è mai stato distribuito un modello tabulare nel server, questo è il momento migliore. Per altre informazioni, vedere [Distribuire in Azure Analysis Services](analysis-services-deploy.md).

Se un modello è stato distribuito nel server, si è pronti per connettersi a tale server tramite un client o un browser. Per altre informazioni, vedere [Get data from Azure Analysis Services server](analysis-services-connect.md) (Ottenere dati dal server Azure Analysis Services).


