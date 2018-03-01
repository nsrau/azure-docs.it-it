---
title: Installare Azure AD Connect usando le autorizzazioni di amministratore con delega SQL | Microsoft Docs
description: Questo argomento illustra un aggiornamento ad Azure AD Connect che consente l'installazione con un account che ha solo autorizzazioni di proprietario del database SQL.
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.reviewer: jparsons
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2018
ms.author: billmath
ms.openlocfilehash: c2d77c37f2f65c9a7db1fd5c4010fc43bcbc7ebf
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="install-azure-ad-connect-using-sql-delegated-administrator-permissions"></a>Installare Azure AD Connect usando le autorizzazioni di amministratore con delega SQL
Nelle versioni precedenti rispetto alla build più recente di Azure AD Connect la delega amministrativa, in caso di distribuzione di configurazioni che necessitano di SQL, non è supportata.  Gli utenti che vogliono installare Azure AD Connect devono avere autorizzazioni di amministratore del server per SQL Server.

Con la versione più recente di Azure AD Connect, l'amministratore SQL può ora effettuare il provisioning del database fuori banda e quindi l'amministratore di Azure AD Connect può eseguire l'installazione con diritti di proprietario del database.

## <a name="before-you-begin"></a>Prima di iniziare
Per usare questa funzionalità, è necessario comprendere che interessa molti elementi e potrebbe coinvolgere un amministratore diverso dell'organizzazione.  La tabella seguente riepiloga i singoli ruoli e le rispettive responsabilità nella distribuzione di Azure AD Connect con questa funzionalità.

|Ruolo|DESCRIZIONE|
|-----|-----|
|Amministratore del dominio o della foresta di Active Directory|Crea l'account del servizio a livello di dominio usato da Azure AD Connect per eseguire il servizio di sincronizzazione.  Per altre informazioni sugli account del servizio, vedere [Account e autorizzazioni](active-directory-aadconnect-accounts-permissions.md).
|Amministratore SQL|Crea il database di ADSync e concede l'accesso di tipo accesso e proprietario del database all'account dell'amministratore e all'account del servizio creati dall'amministratore del dominio o della foresta.|
Amministratore di Azure AD Connect|Installa Azure AD Connect e specifica l'account del servizio durante l'installazione personalizzata.

## <a name="steps-for-installing-azure-ad-connect-using-sql-delegated-permissions"></a>Procedura per l'installazione di Azure AD Connect tramite le autorizzazioni con delega SQL
Per effettuare il provisioning del database fuori banda e installare Azure AD Connect con autorizzazioni di proprietario del database, seguire questa procedura.

>[!NOTE]
>Benché non sia necessario, è **decisamente consigliabile** selezionare le regole di confronto Latin1_General_CI_AS durante la creazione del database.


1.  Richiedere all'amministratore SQL di creare il database di ADSync con una sequenza di regole di confronto che non rispettano la distinzione tra maiuscole e minuscole **(Latin1_General_CI_AS)**.  Il database deve essere denominato **ADSync**.  Il modello di ripristino, il livello di compatibilità e il tipo di contenimento vengono aggiornati ai valori corretti durante l'installazione di Azure AD Connect.  La sequenza di regole di confronto deve essere configurata correttamente dall'amministratore SQL. In caso contrario, Azure AD Connect bloccherà l'installazione.  Per riprendere l'installazione, l'amministratore del server deve eliminare e creare di nuovo il database.</br>
![Regole di confronto](media/active-directory-aadconnect-sql-delegation/sql1.png)
2.  Concedere all'amministratore di Azure AD Connect e all'account del servizio di dominio le autorizzazioni seguenti:
    - Account di accesso SQL 
    - Diritti di **proprietario del database**.  </br>
![Autorizzazioni](media/active-directory-aadconnect-sql-delegation/sql3.png)
3.  Inviare un messaggio di posta elettronica all'amministratore di Azure AD Connect che include l'istanza di SQL Server e il nome di istanza da usare durante l'installazione di Azure AD Connect.

## <a name="additional-information"></a>Informazioni aggiuntive
Al termine del provisioning del database, l'amministratore di Azure AD Connect può installare e configurare la sincronizzazione locale in base alle necessità specifiche.  

Oltre a supportare nuove installazioni di Azure AD Connect, questa funzionalità consente anche la delega per qualsiasi scenario correlato al flag **/UseExistingDatabase**.  Per altre informazioni sull'installazione di Azure AD Connect con un database esistente, vedere [Installare Azure AD Connect usando un database ADSync esistente](active-directory-aadconnect-existing-database.md)


## <a name="next-steps"></a>Passaggi successivi
- [Introduzione alle impostazioni rapide per Azure AD Connect](active-directory-aadconnect-get-started-express.md)
- [Installazione personalizzata di Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
- [Installare Azure AD Connect usando un database ADSync esistente](active-directory-aadconnect-existing-database.md)  