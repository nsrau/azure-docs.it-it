---
title: Come aggiungere o modificare i ruoli di amministratore di Azure | Documentazione Microsoft
description: "Descrive come aggiungere o modificare il co-amministratore di Azure, l’amministratore del servizio e l’amministratore dell’account"
services: 
documentationcenter: 
author: genlin
manager: mbaldwin
editor: 
tags: billing
ms.assetid: 13a72d76-e043-4212-bcac-a35f4a27ee26
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2016
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: abc3d4119cfde69ed24f7390dc99e65614a7f489


---
# <a name="how-to-add-or-change-azure-administrator-roles"></a>Come aggiungere o modificare i ruoli di amministratore di Azure
Esistono tre tipi di ruoli di amministratore in Microsoft Azure:

| Ruolo amministrativo | Limite | Descrizione |
| --- | --- | --- |
| Amministratore dell'account |1 per ogni account di Azure |Indica la persona che si è iscritta o ha acquistato le sottoscrizioni di Azure ed è autorizzata ad accedere al [Centro account](https://account.windowsazure.com/Home/Index) ed eseguire diverse attività di gestione. Queste includono la possibilità di creare sottoscrizioni, annullare sottoscrizioni, modificare la fatturazione per una sottoscrizione e modificare l'amministratore del servizio. |
| Amministratore del servizio |1 per ogni sottoscrizione di Azure |Questo ruolo è autorizzato a gestire i servizi nel [portale di Azure](https://portal.azure.com). Per impostazione predefinita, per una nuova sottoscrizione l'amministratore account è anche amministratore del servizio. |
| Coamministratore (CA) del [portale di Azure classico](https://manage.windowsazure.com) |200 per ogni sottoscrizione |Questo ruolo ha gli stessi privilegi di accesso dell'amministratore del servizio, ma non può modificare l'associazione di sottoscrizioni alle directory di Azure. |

> [!NOTE]
> Il controllo degli accessi in base al ruolo di Azure Active Directory consente di aggiungere gli utenti a più ruoli. Per altre informazioni, vedere [Controllo degli accessi in base al ruolo di Azure Active Directory](active-directory/role-based-access-control-configure.md).
> 
> [!NOTE]
> Se in qualsiasi punto dell'articolo sono necessarie altre informazioni, è possibile [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.
> 
> 

## <a name="how-to-add-an-admin-for-a-subscription"></a>Procedura: aggiungere un amministratore per una sottoscrizione
**Portale di Azure**

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nel menu Hub selezionare **Sottoscrizione** > *la sottoscrizione per cui si vuole concedere l'accesso all'amministratore*.
   
    ![newselectsub](./media/billing-add-change-azure-subscription-administrator/newselectsub.png)
3. Nel pannello della sottoscrizione selezionare **Impostazioni**> **Utenti**.
   
    ![newsettings](./media/billing-add-change-azure-subscription-administrator/newsettings.png)
4. Nel pannello Utenti selezionare **Aggiungi**>**Selezionare un ruolo** > **Proprietario**.
   
    ![newselectrole](./media/billing-add-change-azure-subscription-administrator/newselectrole.png)
   
    **Nota**
   
   * Il ruolo di proprietario dispone degli stessi privilegi di accesso del coamministratore. Questo ruolo non ha i privilegi di accesso per il [Centro Account di Azure](https://account.windowsazure.com/subscriptions).
   * I proprietari aggiunti tramite il [portale di Azure](https://portal.azure.com) non possono gestire i servizi nel [portale di Azure classico](https://manage.windowsazure.com).  
5. Immettere l'indirizzo di posta elettronica dell'utente che si vuole aggiungere, fare clic sull'utente e quindi su **Seleziona**.
   
    ![newadduser](./media/billing-add-change-azure-subscription-administrator/newadduser.png)

**Portale di Azure classico**

1. Accedere al [portale di Azure classico](https://manage.windowsazure.com/).
2. Nel riquadro di spostamento selezionare **Impostazioni**> **Amministratori**> ** Aggiungi**. </br>
   
    ![addcodmin](./media/billing-add-change-azure-subscription-administrator/addcoadmin.png)
3. Digitare l'indirizzo di posta elettronica della persona che si vuole aggiungere come coamministratore e quindi selezionare la sottoscrizione a cui il coamministratore potrà accedere.</br>
   
    ![addcoadmin2](./media/billing-add-change-azure-subscription-administrator/addcoadmin2.png)</br>

L'indirizzo di posta elettronica seguente può essere aggiunto come co-amministratore:

* **Account Microsoft**, denominato in precedenza Windows Live ID </br>
   È possibile usare un account Microsoft per accedere a tutti i prodotti e servizi cloud Microsoft destinati all'utente, quali Outlook (Hotmail), Skype (MSN), OneDrive, Windows Phone e Xbox LIVE.
* **Organizational account**</br>
   Un account dell'organizzazione è un account che viene creato in Azure Active Directory. L'indirizzo dell'account aziendale è simile al seguente: user@&lt;dominio&gt;.onmicrosoft.com

### <a name="limitations-and-restrictions"></a>Limitazioni e restrizioni
* Ogni sottoscrizione è associata a una directory di Azure AD, ovvero la directory predefinita. Per trovare la directory predefinita associata alla sottoscrizione, accedere al [portale di Azure classico](https://manage.windowsazure.com/) e selezionare **Impostazioni** > **Sottoscrizioni**. Verificare l'ID sottoscrizione per trovare la directory predefinita.
* Se si è connessi con un account Microsoft, è possibile aggiungere come coamministratori solo altri account Microsoft o utenti nella directory predefinita.
* Se si è connessi con un account dell'organizzazione, è possibile aggiungere come coamministratori altri account dell'organizzazione. Ad esempio, abby@contoso.com può aggiungere bob@contoso.com come amministratore del servizio o coamministratore, ma non può aggiungere john@notcontoso.com a meno che john@noncontoso.com non sia l'utente nella directory predefinita. Gli utenti connessi con account dell'organizzazione possono continuare ad aggiungere utenti di account Microsoft come amministratori del servizio o coamministratori.
* Ora che è possibile accedere ad Azure con un account aziendale, ecco le modifiche ai requisiti di account di amministratore del servizio e di co-amministratore:
  
  | Metodo di accesso | Aggiungere un account Microsoft o utenti in una directory predefinita come coamministratori o amministratori del servizio? | Aggiungere un account aziendale nella stessa organizzazione come coamministratore o amministratore del servizio? | Aggiungere un account aziendale in un'organizzazione diversa come coamministratore o amministratore del servizio? |
  | --- | --- | --- | --- |
  |  Account Microsoft |Sì |No |No |
  |  Account dell'organizzazione |Sì |Sì |No |

## <a name="how-to-change-service-administrator-for-a-subscription"></a>Come modificare l'amministratore del servizio per una sottoscrizione
Solo l'amministratore account può modificare l'amministratore del servizio per una sottoscrizione.

1. Accedere al [Centro account di Azure](https://account.windowsazure.com/subscriptions) come amministratore account.
2. Selezionare la sottoscrizione da modificare.
3. Sul lato destro fare clic sui dettagli di **Modifica sottoscrizione** . </br>
   
    ![editsub](./media/billing-add-change-azure-subscription-administrator/editsub.png)
4. Nel casella **AMMINISTRATORE DEL SERVIZIO** immettere l'indirizzo di posta elettronica del nuovo amministratore del servizio. </br>
   
    ![changeSA](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

## <a name="how-to-change-the-account-administrator"></a>Come modificare l'amministratore dell'account
Per trasferire la proprietà dell'account Azure a un altro account, vedere [Trasferimento di proprietà di una sottoscrizione di Azure](billing-subscription-transfer.md).

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sul modo in cui l'accesso alle risorse viene controllato in Microsoft Azure, vedere [Informazioni sull'accesso alle risorse in Azure](active-directory/active-directory-understanding-resource-access.md)
* Per altre informazioni sul modo in cui Azure Active Directory è correlato alla sottoscrizione di Azure, vedere [Associare le sottoscrizioni di Azure ad Azure Active Directory](active-directory/active-directory-how-subscriptions-associated-directory.md)
* Per altre informazioni sul modo in cui Azure Active Directory è correlato alla sottoscrizione di Azure, vedere [Assegnazione dei ruoli di amministratore in Azure Active Directory](active-directory/active-directory-assign-admin-roles.md)

> [!NOTE]
> Per altre domande, è possibile [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.
> 
> 




<!--HONumber=Nov16_HO3-->


