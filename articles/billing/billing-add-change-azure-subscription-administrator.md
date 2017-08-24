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
ms.topic: article
ms.date: 07/20/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: da5995535d42ed52772cb09e0f4da51bbf878748
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="add-or-change-azure-administrator-roles-that-manage-the-subscription-or-services"></a>Aggiungere o modificare i ruoli di amministratore di Azure che gestiscono la sottoscrizione o i servizi
È possibile modificare l'amministratore di Azure che gestisce la sottoscrizione di Azure o i servizi Azure usati nella sottoscrizione. Per visualizzare le informazioni di fatturazione di Azure e gestire le sottoscrizioni, è necessario accedere al [Centro account](https://account.windowsazure.com/Home/Index) come amministratore account. 

## <a name="add-an-admin-for-a-subscription"></a>Aggiungere un amministratore per una sottoscrizione
È possibile aggiungere un amministratore di Azure nel portale di Azure o nel portale di Azure classico.

**Portale di Azure**

Per aggiungere un utente come amministratore per una sottoscrizione nel portale di Azure, è necessario assegnare il ruolo di proprietario. Il ruolo di proprietario può gestire solo le risorse nella sottoscrizione che è stata assegnata. Questo ruolo non dispone dei privilegi di accesso per altre sottoscrizioni. I proprietari aggiunti tramite il [portale di Azure](https://portal.azure.com) non possono gestire le risorse nel [portale di Azure classico](https://manage.windowsazure.com).

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nel menu Hub selezionare **Sottoscrizione** > *la sottoscrizione per cui si vuole concedere l'accesso all'amministratore*.

    ![Schermata che mostra una sottoscrizione selezionata](./media/billing-add-change-azure-subscription-administrator/newselectsub.png)

3. Nel pannello della sottoscrizione selezionare **Controllo di accesso (IAM)**.
4. Selezionare **Aggiungi** > **Ruolo** > **Proprietario**. Immettere l'indirizzo di posta elettronica dell'utente che si vuole aggiungere, selezionare l'utente e quindi selezionare **Salva**.

    ![Schermata che mostra il pulsante Proprietario selezionato](./media/billing-add-change-azure-subscription-administrator/add-role.png)

5. Se si desidera aggiungere l'account del proprietario come coamministratore, nella pagina **Controllo di accesso (IAM)** fare clic con il pulsante destro del mouse sull'utente e quindi selezionare **Aggiungi come coamministratore**. Questa funzionalità è ora disponibile nel [portale di anteprima di Azure](https://preview.portal.azure.com/). 

     ![Schermata per l'aggiunta di un coamministratore](./media/billing-add-change-azure-subscription-administrator/add-coadmin.png)

    >[!TIP]
    >È necessario aggiungere l'utente "Proprietario" come coamministratore se l'utente deve gestire i servizi di Azure nel [portale di Azure classico](https://manage.windowsazure.com/).

    Per rimuovere l'autorizzazione di coamministratore, fare clic sull'utente "coamministratore" e quindi selezionare **Rimuovi coamministratore**.

    ![Schermata per la rimozione di un coamministratore](./media/billing-add-change-azure-subscription-administrator/remove-coadmin.png)


**portale di Azure classico**

1. Accedere al [portale di Azure classico](https://manage.windowsazure.com/).
2. Nel riquadro di spostamento selezionare **Impostazioni**> **Amministratori**> **Aggiungi**. </br>

    ![Schermata che mostra come ottenere il pulsante Aggiungi](./media/billing-add-change-azure-subscription-administrator/addcoadmin.png)
3. Digitare l'indirizzo di posta elettronica della persona che si vuole aggiungere come coamministratore e quindi selezionare la sottoscrizione a cui il coamministratore potrà accedere.</br>

    ![Schermata che mostra una sottoscrizione selezionata ](./media/billing-add-change-azure-subscription-administrator/addcoadmin2.png)</br>

L'indirizzo di posta elettronica seguente può essere aggiunto come co-amministratore:

* **Account Microsoft**, denominato in precedenza Windows Live ID </br>
  È possibile usare un account Microsoft per accedere a tutti i prodotti e servizi cloud Microsoft destinati all'utente, quali Outlook (Hotmail), Skype (MSN), OneDrive, Windows Phone e Xbox LIVE.
* **Organizational account**</br>
  Un account dell'organizzazione è un account che viene creato in Azure Active Directory. L'indirizzo dell'account dell'organizzazione ha il formato seguente:

    utente@&lt;dominio&gt;.onmicrosoft.com

## <a name="change-service-administrator-for-a-subscription"></a>Modificare l'amministratore del servizio per una sottoscrizione
Solo l'amministratore account può modificare l'amministratore del servizio per una sottoscrizione.

1. Accedere al [Centro account di Azure](https://account.windowsazure.com/subscriptions) come amministratore account.
2. Selezionare la sottoscrizione da modificare.
3. Sul lato destro selezionare i dettagli di **Modifica sottoscrizione**. </br>

    ![editsub](./media/billing-add-change-azure-subscription-administrator/editsub.png)
4. Nel casella **AMMINISTRATORE DEL SERVIZIO** immettere l'indirizzo di posta elettronica del nuovo amministratore del servizio. </br>

    ![changeSA](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

## <a name="change-the-account-administrator"></a>Modificare l'amministratore dell'account
Per trasferire la proprietà dell'account Azure a un altro account, vedere [Trasferimento di proprietà di una sottoscrizione di Azure](billing-subscription-transfer.md).

È consigliabile non eliminare o rinominare l'indirizzo di posta elettronica dell'amministratore dell'Account. È possibile riscontrare un comportamento imprevisto e indesiderato dell'account di Azure. L'utente potrebbe non essere in grado di accedere ad Azure con tale account, apportare modifiche all'account o gestire le risorse. 

## <a name="check-the-account-administrator-of-the-subscription"></a>Verificare l'amministratore account della sottoscrizione
Se non si conosce l'amministratore account per la sottoscrizione, seguire questa procedura per individuarlo.

  1. Accedere al [portale di Azure](https://portal.azure.com).
  2. Nel menu Hub, selezionare **Sottoscrizione**.
  3. Scegliere la sottoscrizione da controllare e quindi controllare la sezione **Impostazioni**.
  4. Selezionare **Proprietà**. L'amministratore account della sottoscrizione viene visualizzato nella casella **Amministratore account** .  

## <a name="types-of-azure-admin-accounts"></a>Tipi di account amministratore di Azure
 L'Amministratore account, l'Amministratore del servizio e il Coamministratore costituiscono i tre tipi di ruoli di amministratore in Microsoft Azure. La tabella seguente descrive la differenza tra questi tre ruoli amministrativi.

| Ruolo amministrativo | Limite | Descrizione |
| --- | --- | --- |
| Amministratore dell'account |1 per ogni account di Azure |Indica la persona che si è iscritta o ha acquistato le sottoscrizioni di Azure ed è autorizzata ad accedere al [Centro account](https://account.windowsazure.com/Home/Index) ed eseguire diverse attività di gestione. Queste includono la possibilità di creare sottoscrizioni, annullare sottoscrizioni, modificare la fatturazione per una sottoscrizione e modificare l'amministratore del servizio. |
| Amministratore del servizio |1 per ogni sottoscrizione di Azure |Questo ruolo è autorizzato a gestire i servizi nel [portale di Azure](https://portal.azure.com). Per impostazione predefinita, per una nuova sottoscrizione l'amministratore account è anche amministratore del servizio. |
| Coamministratore (CA) del [portale di Azure classico](https://manage.windowsazure.com) |200 per ogni sottoscrizione |Questo ruolo ha gli stessi privilegi di accesso dell'amministratore del servizio, ma non può modificare l'associazione di sottoscrizioni alle directory di Azure. |

Il controllo degli accessi in base al ruolo di Azure Active Directory consente di aggiungere gli utenti a più ruoli. Per altre informazioni, vedere [Controllo degli accessi in base al ruolo di Azure Active Directory](../active-directory/role-based-access-control-configure.md).

## <a name="limitations-and-restrictions-for-admin-accounts"></a>Limitazioni e restrizioni per gli account amministratore
* Ogni sottoscrizione è associata a una directory di Azure AD, ovvero la directory predefinita. Per trovare la directory predefinita associata alla sottoscrizione, accedere al [portale di Azure classico](https://manage.windowsazure.com/) e selezionare **Impostazioni** > **Sottoscrizioni**. Verificare l'ID sottoscrizione per trovare la directory predefinita.
* Se l'accesso è stato eseguito con un account Microsoft, è possibile aggiungere come coamministratori solo altri account Microsoft o utenti nella directory predefinita.
* Se l'accesso è stato eseguito con un account dell'organizzazione, è possibile aggiungere come coamministratori altri account dell'organizzazione. Ad esempio, abby@contoso.com può aggiungere bob@contoso.com come amministratore del servizio o coamministratore, ma non può aggiungere john@notcontoso.com a meno che john@notcontoso.com non si trovi nella directory predefinita. Gli utenti che hanno eseguito l'accesso con un account dell'organizzazione possono continuare ad aggiungere utenti di account Microsoft come amministratori del servizio o coamministratori.
* Ora che è possibile accedere ad Azure con un account dell'organizzazione, ecco le modifiche ai requisiti degli account di amministratore del servizio e di coamministratore:

  | Metodo di accesso | Aggiungere un account Microsoft o utenti in una directory predefinita come coamministratori o amministratori del servizio? | Aggiungere un account aziendale nella stessa organizzazione come coamministratore o amministratore del servizio? | Aggiungere un account aziendale in un'organizzazione diversa come coamministratore o amministratore del servizio? |
  | --- | --- | --- | --- |
  |  Account Microsoft |Sì |No |No |
  |  Account dell'organizzazione |Sì |Sì |No |

## <a name="learn-more-about-resource-access-control-and-active-directory"></a>Altre informazioni sul controllo dell'accesso alle risorse e Active Directory
* Per altre informazioni sul controllo dell'accesso alle risorse in Microsoft Azure, vedere [Informazioni sull'accesso alle risorse in Azure](../active-directory/active-directory-understanding-resource-access.md).
* Per altre informazioni sul modo in cui Azure Active Directory, vedere [Associare le sottoscrizioni di Azure ad Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md) e [Assegnazione dei ruoli di amministratore in Azure Active Directory](../active-directory/active-directory-assign-admin-roles.md).

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico.
Se si necessita ancora di assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.

