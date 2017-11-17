---
title: Aggiungere o modificare i ruoli di amministratore della sottoscrizione di Azure | Documentazione Microsoft
description: "Descrive come aggiungere o modificare il co-amministratore di Azure, l’amministratore del servizio e l’amministratore dell’account"
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.assetid: 13a72d76-e043-4212-bcac-a35f4a27ee26
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/19/2017
ms.author: genli
ms.openlocfilehash: 091fe208783a2fe5d5c91abe4ec498bf760a3eb3
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
---
# <a name="add-or-change-azure-administrator-roles-that-manage-the-subscription-or-services"></a>Aggiungere o modificare i ruoli di amministratore di Azure che gestiscono la sottoscrizione o i servizi

È possibile modificare l'amministratore di Azure che gestisce la sottoscrizione di Azure o i servizi Azure usati nella sottoscrizione. Per visualizzare le informazioni di fatturazione di Azure e gestire le sottoscrizioni, è necessario accedere al Centro account come amministratore account. 

<a name="add-an-admin-for-a-subscription"></a>

## <a name="add-an-rbac-owner-admin-for-a-subscription-in-azure-portal"></a>Aggiungere un amministratore proprietario di Controllo degli accessi in base al ruolo per una sottoscrizione nel portale di Azure 

Per aggiungere un utente come amministratore per una sottoscrizione nel portale di Azure, è necessario assegnare il ruolo di proprietario di [Controllo degli accessi in base al ruolo](../active-directory/role-based-access-control-configure.md). Il ruolo di proprietario può gestire le risorse nella sottoscrizione assegnata e non ha privilegi di accesso per altre sottoscrizioni. I proprietari aggiunti tramite il [portale di Azure](https://portal.azure.com) non possono gestire le risorse nel [portale di Azure classico](https://manage.windowsazure.com).

1. Accedere alla [visualizzazione Sottoscrizioni nel portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Selezionare la sottoscrizione a cui si vuole che l'amministratore acceda.
1. Selezionare **Controllo di accesso (IAM)** nel menu.
1. Selezionare **Aggiungi** > **Ruolo** > **Proprietario**. Immettere l'indirizzo e-mail dell'utente che si vuole aggiungere come proprietario, selezionare l'utente e quindi fare clic su **Salva**.

    ![Schermata che mostra il pulsante Proprietario selezionato](./media/billing-add-change-azure-subscription-administrator/add-role.png)

## <a name="add-or-change-co-administrator"></a>Aggiungere o modificare un coamministratore

Solo un proprietario può essere aggiunto come coamministratore. Gli altri utenti con ruoli come collaboratore e lettore non possono essere aggiunti come coamministratori.

1. Se non è già stato fatto, aggiungere un utente come proprietario seguendo le istruzioni illustrate in precedenza.
2. **Fare clic con il pulsante destro del mouse** sull'utente proprietario appena aggiunto e quindi scegliere **Aggiungi come coamministratore**. Se l'opzione **Aggiungi come coamministratore** non è visibile, aggiornare la pagina o provare a usare un altro browser Internet. 

     ![Schermata per l'aggiunta di un coamministratore](./media/billing-add-change-azure-subscription-administrator/add-coadmin.png)

    >[!TIP]
    >È necessario aggiungere l'account "Proprietario" come coamministratore se l'utente deve gestire i servizi di Azure nel [portale di Azure classico](https://manage.windowsazure.com/).

    Per rimuovere l'autorizzazione di coamministratore, **fare clic con il pulsante destro del mouse** sull'utente "Coamministratore" e quindi scegliere **Rimuovi coamministratore**.

    ![Schermata per la rimozione di un coamministratore](./media/billing-add-change-azure-subscription-administrator/remove-coadmin.png)

<a name="change-service-administrator-for-a-subscription"></a>

## <a name="change-the-service-administrator-for-an-azure-subscription"></a>Modificare l'amministratore del servizio per una sottoscrizione di Azure

Solo l'amministratore account può modificare l'amministratore del servizio per una sottoscrizione. Per impostazione predefinita, al momento dell'iscrizione, l'amministratore del servizio corrisponde all'amministratore account.

1. Assicurarsi che lo scenario sia supportato verificando le [limitazioni per la modifica degli amministratori del servizio](#limits).
1. Accedere al [Centro account](https://account.windowsazure.com/subscriptions) come amministratore account.
1. Selezionare una sottoscrizione.
1. Sul lato destro selezionare **Modifica i dettagli della sottoscrizione**.

    ![Screenshot che mostra il pulsante di modifica della sottoscrizione nel Centro account](./media/billing-add-change-azure-subscription-administrator/editsub.png)
1. Nel casella **AMMINISTRATORE DEL SERVIZIO** immettere l'indirizzo di posta elettronica del nuovo amministratore del servizio.

    ![Screenshot che mostra la casella per modificare l'indirizzo e-mail dell'amministratore del servizio](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

<a name="limits"></a>

### <a name="limitations-for-changing-service-administrators"></a>Limitazioni per la modifica degli amministratori del servizio

* Ogni sottoscrizione è associata a una directory di Azure AD. Per trovare la directory a cui è associata la sottoscrizione, accedere al [portale di Azure classico](https://manage.windowsazure.com/) e selezionare **Impostazioni** > **Sottoscrizioni**. Verificare l'ID sottoscrizione per trovare la directory.
* Se l'accesso è stato eseguito con un account aziendale o dell'istituto di istruzione, è possibile aggiungere altri account dell'organizzazione come amministratori del servizio. Ad esempio, abby@contoso.com può aggiungere bob@contoso.com come amministratore del servizio, ma non può aggiungere john@notcontoso.com a meno che john@notcontoso.com non si trovi nella directory contoso.com. Gli utenti che hanno eseguito l'accesso con un account aziendale o dell'istituto di istruzione possono continuare ad aggiungere utenti con account Microsoft come amministratori del servizio.

  | Metodo di accesso | Aggiunta di un utente con account Microsoft come amministratore del servizio? | Aggiunta di un account aziendale o dell'istituto di istruzione nella stessa organizzazione come amministratore del servizio? | Aggiunta di un account aziendale o dell'istituto di istruzione in un'organizzazione diversa come amministratore del servizio? |
  | --- | --- | --- | --- |
  |  Account Microsoft |Sì |No |No |
  |  Account aziendale o dell'istituto di istruzione |Sì |Sì |No |

## <a name="change-the-account-administrator-for-an-azure-subscription"></a>Modificare l'amministratore account per una sottoscrizione di Azure

Per modificare l'amministratore account di una sottoscrizione, vedere [Trasferimento della proprietà di una sottoscrizione di Azure a un altro account](billing-subscription-transfer.md).

<a name="check-the-account-administrator-of-the-subscription"></a>

**Non si è sicuri di chi sia l'amministratore account?** A tale scopo, seguire questa procedura:

1. Accedere alla [visualizzazione Sottoscrizioni nel portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Scegliere la sottoscrizione da controllare e quindi controllare la sezione **Impostazioni**.
1. Selezionare **Proprietà**. L'amministratore account della sottoscrizione viene visualizzato nella casella **Amministratore account** .  

## <a name="types-of-azure-admin-accounts"></a>Tipi di account amministratore di Azure

 L'Amministratore account, l'Amministratore del servizio e il Coamministratore costituiscono i tre tipi di ruoli di amministratore in Microsoft Azure. La tabella seguente descrive la differenza tra questi tre ruoli amministrativi.

| Ruolo amministrativo | Limite | Descrizione |
| --- | --- | --- |
| Amministratore dell'account |1 per ogni account di Azure |Indica la persona che si è iscritta o ha acquistato le sottoscrizioni di Azure ed è autorizzata ad accedere al [Centro account](https://account.azure.com/Subscriptions) ed eseguire diverse attività di gestione. Queste includono la possibilità di creare sottoscrizioni, annullare sottoscrizioni, modificare la fatturazione per una sottoscrizione e modificare l'amministratore del servizio. |
| Amministratore del servizio |1 per ogni sottoscrizione di Azure |Questo ruolo è autorizzato a gestire i servizi nel [portale di Azure](https://portal.azure.com). Per impostazione predefinita, per una nuova sottoscrizione l'amministratore account è anche amministratore del servizio. |
| Coamministratore (CA) del [portale di Azure classico](https://manage.windowsazure.com) |200 per ogni sottoscrizione |Questo ruolo ha gli stessi privilegi di accesso dell'amministratore del servizio, ma non può modificare l'associazione di sottoscrizioni alle directory di Azure. |

Il controllo degli accessi in base al ruolo di Azure Active Directory consente di aggiungere gli utenti a più ruoli. Per altre informazioni, vedere [Controllo degli accessi in base al ruolo di Azure Active Directory](../active-directory/role-based-access-control-configure.md).


## <a name="learn-more-about-resource-access-control-and-active-directory"></a>Altre informazioni sul controllo dell'accesso alle risorse e Active Directory

* Per altre informazioni sul controllo dell'accesso alle risorse in Microsoft Azure, vedere [Informazioni sull'accesso alle risorse in Azure](../active-directory/active-directory-understanding-resource-access.md).
* Per altre informazioni sul modo in cui Azure Active Directory, vedere [Associare le sottoscrizioni di Azure ad Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md) e [Assegnazione dei ruoli di amministratore in Azure Active Directory](../active-directory/active-directory-assign-admin-roles-azure-portal.md).

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico.

Se si necessita ancora di assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.
