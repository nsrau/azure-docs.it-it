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
ms.date: 12/19/2017
ms.author: genli
ms.openlocfilehash: bb82eeda21fe4913acbe930696aa110ea1554f28
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/22/2017
---
# <a name="add-or-change-azure-subscription-administrators"></a>Aggiungere o modificare gli amministratori delle sottoscrizioni di Azure

Gli amministratori delle sottoscrizioni classico di Azure e Azure [controllo di accesso basato sui ruoli (RBAC)](../active-directory/role-based-access-control-what-is.md) due sistemi per la gestione dell'accesso alle risorse di Azure:

* Ruoli di amministratore classico sottoscrizione offrono di gestione di accesso di base e includono Account amministratore, amministratore del servizio e coamministratori.
    * Quando effettua l'iscrizione per una nuova sottoscrizione di Azure, come l'amministratore dell'Account e l'amministratore del servizio per impostazione predefinita è impostato l'account.
    * Coamministratori possono essere aggiunte dopo la disconnessione.
* RBAC è un sistema più recente che offre la gestione di accesso con granularità fine con molti ruoli predefiniti, la flessibilità di ambito e ruoli personalizzati.
    * Tuttavia, gli utenti con solo ruoli RBAC e non i ruoli di amministratore classico sottoscrizione non è possibile gestire le distribuzioni di Azure classiche.

Per garantire un migliore controllo e per semplificare la gestione di accesso, è consigliabile usare RBAC per tutte le esigenze di gestione di accesso. Se possibile, è consigliabile riconfigurare i criteri di accesso usa tale controllo. 

<a name="add-an-admin-for-a-subscription"></a>

## <a name="add-an-rbac-owner-admin-for-a-subscription-in-azure-portal"></a>Aggiungere un amministratore proprietario di Controllo degli accessi in base al ruolo per una sottoscrizione nel portale di Azure 

Per aggiungere un utente come amministratore per l'amministrazione del servizio di sottoscrizione di Azure, assegnare loro un ruolo di proprietario RBAC alla sottoscrizione. Il ruolo di proprietario può gestire le risorse nella sottoscrizione assegnata e non ha privilegi di accesso per altre sottoscrizioni.

1. Visitare [ **sottoscrizioni** nel portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Selezionare la sottoscrizione che si desidera consentire l'accesso.
1. Selezionare **Controllo di accesso (IAM)** nel menu.
1. Selezionare **Aggiungi** > **Ruolo** > **Proprietario**. Immettere l'indirizzo e-mail dell'utente che si vuole aggiungere come proprietario, selezionare l'utente e quindi fare clic su **Salva**.

    ![Schermata che mostra il pulsante Proprietario selezionato](./media/billing-add-change-azure-subscription-administrator/add-role.png)

In questo modo all'utente l'accesso completo a tutte le risorse, compreso il diritto di delegare l'accesso ad altri utenti. Per concedere l'accesso a un ambito diverso, ad esempio un gruppo di risorse, visitare il menu IAM di tale ambito. 

## <a name="add-or-change-co-administrator"></a>Aggiungere o modificare un coamministratore

Solo un proprietario può essere aggiunto come coamministratore. Gli altri utenti con ruoli come collaboratore e lettore non possono essere aggiunti come coamministratori.

> [!TIP]
> È sufficiente aggiungere l'account di "Proprietario" come co-amministratore se l'utente deve gestire le distribuzioni di Azure classiche. È consigliabile usare RBAC per tutti gli altri scopi.

1. Se non è già stato fatto, aggiungere un utente come proprietario seguendo le istruzioni illustrate in precedenza.
2. **Fare clic con il pulsante destro del mouse** sull'utente proprietario appena aggiunto e quindi scegliere **Aggiungi come coamministratore**. Se non viene visualizzato il **Aggiungi come coamministratore** opzione, aggiornare la pagina o provare un altro browser Internet. 

    ![Schermata per l'aggiunta di un coamministratore](./media/billing-add-change-azure-subscription-administrator/add-coadmin.png)

    Per rimuovere l'autorizzazione di coamministratore, **fare clic con il pulsante destro del mouse** sull'utente "Coamministratore" e quindi scegliere **Rimuovi coamministratore**.

    ![Schermata per la rimozione di un coamministratore](./media/billing-add-change-azure-subscription-administrator/remove-coadmin.png)

<a name="change-service-administrator-for-a-subscription"></a>

## <a name="change-the-service-administrator-for-an-azure-subscription"></a>Modificare l'amministratore del servizio per una sottoscrizione di Azure

Solo l'amministratore account può modificare l'amministratore del servizio per una sottoscrizione. Per impostazione predefinita, al momento dell'iscrizione, l'amministratore del servizio corrisponde all'amministratore account. Se l'amministratore del servizio viene modificato in un altro utente, quindi l'amministratore dell'Account perde l'accesso al portale di Azure. Tuttavia, l'amministratore dell'Account possono utilizzare sempre il centro Account per modificare l'amministratore del servizio torna a se stessi.

1. Assicurarsi che lo scenario sia supportato verificando le [limitazioni per la modifica degli amministratori del servizio](#limits).
1. Accedere al [Centro account](https://account.windowsazure.com/subscriptions) come amministratore account.
1. Selezionare una sottoscrizione.
1. Sul lato destro selezionare **Modifica i dettagli della sottoscrizione**.

    ![Screenshot che mostra il pulsante di modifica della sottoscrizione nel Centro account](./media/billing-add-change-azure-subscription-administrator/editsub.png)
1. Nel casella **AMMINISTRATORE DEL SERVIZIO** immettere l'indirizzo di posta elettronica del nuovo amministratore del servizio.

    ![Screenshot che mostra la casella per modificare l'indirizzo e-mail dell'amministratore del servizio](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

<a name="limits"></a>

### <a name="limitations-for-changing-service-administrators"></a>Limitazioni per la modifica degli amministratori del servizio

* Ogni sottoscrizione è associata a una directory di Azure AD. Per trovare la directory è associata la sottoscrizione, andare al [ **sottoscrizioni**](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), quindi selezionare una sottoscrizione per visualizzare la directory.
* Se l'accesso è stato eseguito con un account aziendale o dell'istituto di istruzione, è possibile aggiungere altri account dell'organizzazione come amministratori del servizio. Ad esempio, abby@contoso.com può aggiungere bob@contoso.com come amministratore del servizio, ma non può aggiungere john@notcontoso.com a meno che john@notcontoso.com non si trovi nella directory contoso.com. Gli utenti che hanno eseguito l'accesso con un account aziendale o dell'istituto di istruzione possono continuare ad aggiungere utenti con account Microsoft come amministratori del servizio.

  | Metodo di accesso | Aggiunta di un utente con account Microsoft come amministratore del servizio? | Aggiunta di un account aziendale o dell'istituto di istruzione nella stessa organizzazione come amministratore del servizio? | Aggiunta di un account aziendale o dell'istituto di istruzione in un'organizzazione diversa come amministratore del servizio? |
  | --- | --- | --- | --- |
  |  Account Microsoft |Sì |No  |No  |
  |  Account aziendale o dell'istituto di istruzione |Sì |Sì |No  |

## <a name="change-the-account-administrator-for-an-azure-subscription"></a>Modificare l'amministratore account per una sottoscrizione di Azure

L'amministratore dell'Account è l'utente che ha inizialmente effettuata l'iscrizione per la sottoscrizione di Azure e responsabile come proprietario della sottoscrizione di fatturazione. Per modificare l'amministratore account di una sottoscrizione, vedere [Trasferimento della proprietà di una sottoscrizione di Azure a un altro account](billing-subscription-transfer.md).

<a name="check-the-account-administrator-of-the-subscription"></a>

**Non si è sicuri di chi sia l'amministratore account?** A tale scopo, seguire questa procedura:

1. Visitare [ **sottoscrizioni** nel portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Scegliere la sottoscrizione da controllare e quindi controllare la sezione **Impostazioni**.
1. Selezionare **Proprietà**. L'amministratore dell'Account della sottoscrizione viene visualizzato nel **amministratore dell'Account** casella.  

## <a name="types-of-classic-subscription-admins"></a>Tipi di amministratori della sottoscrizione classico

 Account amministratore, amministratore del servizio e coamministratore sono tre tipi di ruoli di amministratore classico sottoscrizione in Azure. L'account utilizzato per iscriversi a Azure viene impostato automaticamente come l'amministratore dell'Account e l'amministratore del servizio. Quindi, è possibile aggiungere altri coamministratori. Nella tabella seguente descrive esatte differenze tra questi tre ruoli amministrativi. 

> [!TIP]
> Per un migliore controllo e gestione di accesso granulare, è consigliabile utilizzare Azure Role-based Access controllo (RBAC), che consente agli utenti di essere aggiunti a più ruoli. Per ulteriori informazioni, vedere [controllo di accesso basato sui ruoli di Azure Active Directory](../active-directory/role-based-access-control-what-is.md).

| Amministratore sottoscrizione classico | Limite | DESCRIZIONE |
| --- | --- | --- |
| Amministratore dell'account |1 per ogni account di Azure |Questo è l'utente che ha effettuato l'iscrizione per la sottoscrizione di Azure ed è autorizzato ad accedere il [centro Account](https://account.azure.com/Subscriptions) ed eseguire varie attività di gestione. Queste includono la possibilità di creare nuove sottoscrizioni, annullare sottoscrizioni, modificare la fatturazione per una sottoscrizione e modificare l'amministratore del servizio. Concettualmente, l'amministratore dell'Account è il proprietario di fatturazione della sottoscrizione. In RBAC, l'amministratore dell'Account non è assegnato un ruolo.|
| Amministratore del servizio |1 per ogni sottoscrizione di Azure |Questo ruolo è autorizzato a gestire i servizi nel [portale di Azure](https://portal.azure.com). Per impostazione predefinita, per una nuova sottoscrizione l'amministratore account è anche amministratore del servizio. In RBAC, il ruolo proprietario viene assegnato per l'amministratore del servizio nell'ambito della sottoscrizione.|
| CO-amministratore (CA) |200 per ogni sottoscrizione |Questo ruolo ha gli stessi privilegi di accesso dell'amministratore del servizio, ma non può modificare l'associazione di sottoscrizioni alle directory di Azure. In RBAC, viene assegnato il ruolo di proprietario per il coamministratore nell'ambito della sottoscrizione.|

## <a name="learn-more-about-resource-access-control-and-active-directory"></a>Altre informazioni sul controllo dell'accesso alle risorse e Active Directory

* Per altre informazioni sul controllo dell'accesso alle risorse in Microsoft Azure, vedere [Informazioni sull'accesso alle risorse in Azure](../active-directory/active-directory-understanding-resource-access.md).
* Per altre informazioni sul modo in cui Azure Active Directory, vedere [Associare le sottoscrizioni di Azure ad Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md) e [Assegnazione dei ruoli di amministratore in Azure Active Directory](../active-directory/active-directory-assign-admin-roles-azure-portal.md).

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico.

Se si necessita ancora di assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.
