---
title: Gestire l'accesso di utenti esterni con il controllo degli accessi in base al ruolo in Azure | Microsoft Docs
description: Informazioni su come gestire l'accesso di utenti esterni a un'organizzazione tramite il controllo degli accessi in base al ruolo in Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 03/20/2018
ms.author: rolyon
ms.reviewer: skwan
ms.custom: it-pro
ms.openlocfilehash: bd75ecde75d0f22dc66f047cd063dd85807f6f33
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2018
ms.locfileid: "44304442"
---
# <a name="manage-access-for-external-users-using-rbac"></a>Gestire l'accesso di utenti esterni tramite il controllo degli accessi in base al ruolo

Il controllo degli accessi in base al ruolo (RBAC) consente una migliore gestione della sicurezza nelle organizzazioni di grandi dimensioni e nelle piccole e medie imprese che si avvalgono di collaboratori esterni, fornitori o freelance che hanno necessità di accedere a risorse specifiche nell'ambiente, ma non necessariamente all'intera infrastruttura o agli ambiti correlati alla fatturazione. Il controllo degli accessi in base al ruolo consente la flessibilità di possedere una sottoscrizione di Azure gestita dall'account di amministratore (ruolo di amministratore del servizio a livello di sottoscrizione) e di invitare più utenti a usare la stessa sottoscrizione ma senza i diritti amministrativi.

> [!NOTE]
> Gli abbonamenti di Office 365 o le licenze di Azure Active Directory, ad esempio Accesso ad Azure Active Directory, di cui viene effettuato il provisioning dall'interfaccia di amministrazione di Office 365 non danno diritto all'uso del controllo degli accessi in base al ruolo.

## <a name="assign-rbac-roles-at-the-subscription-scope"></a>Assegnare i ruoli di controllo degli accessi in base al ruolo all'ambito della sottoscrizione

Ecco due esempi comuni di uso del controllo degli accessi in base al ruolo:

* Utenti esterni alle organizzazioni (non inclusi nel tenant di Azure Active Directory dell'utente amministratore) invitati a gestire risorse specifiche o l'intera sottoscrizione
* Collaborazione con utenti all'interno dell'organizzazione (inclusi nel tenant di Azure Active Directory dell'utente), ma appartenenti a team o gruppi diversi che necessitano di un accesso granulare all'intera sottoscrizione o a determinati ambiti o gruppi di risorse nell'ambiente

## <a name="grant-access-at-a-subscription-level-for-a-user-outside-of-azure-active-directory"></a>Concedere l'accesso a livello di sottoscrizione per un utente all'esterno di Azure Active Directory

I ruoli RBAC possono essere concessi solo dai **proprietari** della sottoscrizione. Pertanto, l'amministratore deve eseguire l'accesso con un account utente a cui è preassegnato questo ruolo o che ha creato la sottoscrizione di Azure.

Dopo avere eseguito l'accesso come amministratore dal portale di Azure, selezionare "Sottoscrizioni" e scegliere quella desiderata.
![Pannello delle sottoscrizioni nel portale di Azure](./media/role-assignments-external-users/0.png) Per impostazione predefinita, se l'utente amministratore ha acquistato la sottoscrizione di Azure, l'utente verrà visualizzato come **amministratore dell'account**, vale a dire con il ruolo di sottoscrizione. Per altre informazioni sui ruoli di sottoscrizione di Azure, vedere [Aggiungere o modificare gli amministratori delle sottoscrizioni di Azure](../billing/billing-add-change-azure-subscription-administrator.md).

In questo esempio l'utente "alflanigan@outlook.com" è **Proprietario** della sottoscrizione nella "versione di valutazione gratuita" nel tenant AAD "Default tenant Azure". Poiché questo utente ha creato la sottoscrizione di Azure con l'account Microsoft iniziale "Outlook" (account Microsoft = Outlook, Live e così via), il nome di dominio predefinito per tutti gli altri utenti aggiunti in questo tenant sarà **"\@alflaniganuoutlook.onmicrosoft.com"**. Per impostazione predefinita, la sintassi del nuovo dominio è costituita dalla combinazione di nome utente e nome dominio dell'utente che ha creato il tenant con l'aggiunta dell'estensione **".onmicrosoft.com"**.
Gli utenti possono anche accedere con un nome di dominio personalizzato nel tenant dopo averlo aggiunto e verificato per il nuovo tenant. Per altre informazioni su come verificare un nome di dominio personalizzato in un tenant di Azure Active Directory, vedere [Aggiungere un nome di dominio personalizzato alla directory](/active-directory/active-directory-add-domain).

In questo esempio la directory "Default tenant Azure" contiene solo gli utenti con il nome di dominio "\@alflanigan.onmicrosoft.com".

Dopo avere selezionato la sottoscrizione, l'utente amministratore deve fare clic su **Controllo di accesso (IAM)** e quindi su **Aggiungi nuovo ruolo**.

![funzione IAM di controllo di accesso nel portale di Azure](./media/role-assignments-external-users/1.png)

![aggiungere un nuovo utente nella funzione IAM di controllo di accesso nel portale di Azure](./media/role-assignments-external-users/2.png)

Il passaggio successivo consiste nel selezionare il ruolo da assegnare e l'utente a cui verrà assegnato il ruolo di controllo degli accessi in base al ruolo. Nel menu a discesa **Ruolo** l'utente amministratore vede solo i ruoli predefiniti di controllo degli accessi in base al ruolo disponibili in Azure. Per una descrizione più dettagliata di ogni ruolo e dei relativi ambiti assegnabili, vedere [Ruoli predefiniti](built-in-roles.md).

L'utente amministratore deve quindi aggiungere l'indirizzo e-mail dell'utente esterno. Per l'utente esterno il comportamento previsto è quello di non essere visibile nel tenant esistente. Dopo che è stato invitato, l'utente esterno sarà visibile in **Sottoscrizioni > Controllo di accesso (IAM)** con tutti gli utenti correnti assegnati attualmente a un ruolo di controllo degli accessi in base al ruolo nell'ambito della sottoscrizione.

![aggiungere autorizzazioni al nuovo ruolo di controllo degli accessi in base al ruolo](./media/role-assignments-external-users/3.png)

![elenco di ruoli di controllo degli accessi in base al ruolo a livello di sottoscrizione](./media/role-assignments-external-users/4.png)

L'utente "chessercarlton@gmail.com" è stato inviato ad essere un **proprietario** per la sottoscrizione nella "versione di valutazione gratuita". Dopo avere inviato l'invito, l'utente esterno riceverà una conferma tramite posta elettronica con un collegamento di attivazione.
![Invito tramite posta elettronica per il ruolo di controllo degli accessi in base al ruolo](./media/role-assignments-external-users/5.png)

Essendo esterno all'organizzazione, il nuovo utente non dispone degli attributi esistenti nella directory "Default tenant Azure". Questi verranno creati previo consenso dell'utente esterno alla registrazione nella directory associata alla sottoscrizione per cui gli è stato assegnato un ruolo.

![messaggio di invito tramite posta elettronica per il ruolo di controllo degli accessi in base al ruolo](./media/role-assignments-external-users/6.png)

L'utente esterno diventa visibile nel tenant di Azure Active Directory da questo momento in poi come utente esterno e può essere visualizzato nel portale di Azure.

![pannello utenti azure active directory portale di Azure](./media/role-assignments-external-users/7.png)

Nella visualizzazione **Utenti**, gli utenti esterni sono riconoscibili dal tipo di icona diverso nel portale di Azure.

Tuttavia, la concessione dell'accesso come **Proprietario** o **Collaboratore** a un utente esterno nell'ambito della **sottoscrizione**, non consente l'accesso alla directory dell'utente amministratore, a meno che ciò non sia consentito dall'opzione di **amministrazione globale**. Nelle proprietà dell'utente è possibile identificare il **tipo di utente**, che dispone di due parametri comuni, **Membro** e **Guest**. Un membro è un utente registrato nella directory, mentre un utente guest è un utente invitato nella directory da un'origine esterna. Per altre informazioni, vedere [Procedura per aggiungere utenti di Collaborazione B2B ad Azure Active Directory da parte degli amministratori](../active-directory/active-directory-b2b-admin-add-users.md).

> [!NOTE]
> Assicurarsi che, dopo avere immesso le credenziali nel portale, l'utente esterno selezioni la directory corretta a cui accedere. Lo stesso utente può avere accesso a più directory e selezionarne una facendo clic sul nome utente nella parte superiore destra nel portale di Azure e quindi scegliere la directory appropriata nell'elenco a discesa.

Pur essendo un utente guest nella directory, l'utente esterno può gestire tutte le risorse per la sottoscrizione di Azure, ma non è in grado di accedere alla directory.

![accesso limitato ad Azure Active Directory nel portale di Azure](./media/role-assignments-external-users/9.png)

Azure Active Directory e una sottoscrizione di Azure non hanno una relazione padre-figlio come quella che hanno altre risorse di Azure, ad esempio le macchine virtuali, le reti virtuali, le app Web, le risorse di archiviazione e così via, con una sottoscrizione di Azure. Tutte queste ultime vengono create, gestite e fatturate in una sottoscrizione di Azure, mentre una sottoscrizione di Azure viene usata per gestire l'accesso a una directory di Azure. Per altre informazioni, vedere [Come associare una sottoscrizione di Azure ad Azure AD](/active-directory/active-directory-how-subscriptions-associated-directory).

Da tutti i ruoli predefiniti di controllo degli accessi in base al ruolo, **Proprietario** e **Collaboratore** offrono l'accesso completo a livello di gestione a tutte le risorse nell'ambiente, ma il ruolo Collaboratore non può creare ed eliminare nuovi ruoli di controllo degli accessi in base al ruolo. Altri ruoli predefiniti, come **Collaboratore Macchina Virtuale**, offrono l'accesso completo a livello di gestione solo alle risorse indicate dal nome, indipendentemente dal **gruppo di risorse** in cui sono state create.

L'assegnazione del ruolo predefinito di controllo degli accessi in base al ruolo **Collaboratore Macchina Virtuale** a livello di sottoscrizione implica che all'utente a cui è stato assegnato il ruolo seguente:

* Può visualizzare tutte le macchine virtuali indipendentemente dalla data di distribuzione e dai gruppi di risorse di appartenenza
* Ha accesso completo a livello di gestione alle macchine virtuali nella sottoscrizione
* Non può visualizzare gli altri tipi di risorse nella sottoscrizione
* Non può applicare modifiche dalla prospettiva della fatturazione

## <a name="assign-a-built-in-rbac-role-to-an-external-user"></a>Assegnare un ruolo predefinito di controllo degli accessi in base al ruolo a un utente esterno

Per uno scenario diverso in questo test, l'utente esterno "alflanigan@gmail.com" viene aggiunto come **Collaboratore Macchina virtuale**.

![ruolo predefinito collaboratore macchina virtuale](./media/role-assignments-external-users/11.png)

Il comportamento normale per questo utente esterno con questo ruolo predefinito è visualizzare e gestire solo le macchine virtuali e solo le risorse di Resource Manager adiacenti necessarie durante la distribuzione. Per impostazione predefinita, questi ruoli limitati consentono l'accesso solo alle risorse corrispondenti create nel portale di Azure.

![Panoramica del ruolo Collaboratore Macchina virtuale nel portale di Azure](./media/role-assignments-external-users/12.png)

## <a name="grant-access-at-a-subscription-level-for-a-user-in-the-same-directory"></a>Concedere l'accesso a livello di sottoscrizione per un utente nella stessa directory

Il flusso del processo è identico all'aggiunta di un utente esterno sia dal punto di vista dell'amministratore che concede il ruolo di controllo degli accessi in base al ruolo sia dal punto di vista dell'utente a cui viene concesso l'accesso al ruolo. La differenza è che l'utente invitato non riceverà gli inviti tramite posta elettronica e tutti gli ambiti di risorse nella sottoscrizione saranno disponibili nel dashboard dopo l'accesso.

## <a name="assign-rbac-roles-at-the-resource-group-scope"></a>Assegnare ruoli di controllo degli accessi in base al ruolo nell'ambito del gruppo di risorse

L'assegnazione di un ruolo di controllo degli accessi in base al ruolo nell'ambito del **gruppo di risorse** prevede un processo identico per l'assegnazione del ruolo a livello di sottoscrizione per entrambi i tipi di utenti: esterni o interni (appartenenti alla stessa directory). Gli utenti a cui viene assegnato il ruolo Controllo degli accessi in base al ruolo vedono nel proprio ambiente solo il gruppo di risorse per cui è stato loro assegnato l'accesso dall'icona **Gruppi di risorse** nel portale di Azure.

## <a name="assign-rbac-roles-at-the-resource-scope"></a>Assegnare ruoli di controllo degli accessi in base al ruolo nell'ambito delle risorse

Il processo di assegnazione di un ruolo di controllo degli accessi in base al ruolo nell'ambito delle risorse in Azure è identico per l'assegnazione del ruolo a livello di sottoscrizione o di gruppo di risorse, seguendo lo stesso flusso di lavoro per entrambi gli scenari. Anche in questo caso, gli utenti assegnati al ruolo Controllo degli accessi in base al ruolo possono vedere solo gli elementi per cui è stato loro assegnato l'accesso nella scheda **Tutte le risorse** o direttamente nel dashboard.

Un aspetto importante per il controllo degli accessi in base al ruolo nell'ambito del gruppo di risorse o delle risorse è che gli utenti devono eseguire l'accesso alla directory corretta.

![accesso alla directory nel portale di Azure](./media/role-assignments-external-users/13.png)

## <a name="assign-rbac-roles-for-an-azure-active-directory-group"></a>Assegnare ruoli di controllo degli accessi in base al ruolo per un gruppo di Azure Active Directory

Tutti gli scenari che usano il controllo degli accessi in base al ruolo nei tre ambiti diversi in Azure offrono il privilegio di gestione, distribuzione e amministrazione di varie risorse come utente assegnato, senza la necessità di gestire una sottoscrizione personale. Indipendentemente dal fatto che venga assegnato il ruolo di controllo degli accessi in base al ruolo nell'ambito di una sottoscrizione, di un gruppo di risorse o delle risorse, tutte le risorse create successivamente dagli utenti assegnati vengono fatturate per la sottoscrizione di Azure a cui gli utenti hanno accesso. In questo modo gli utenti con autorizzazioni di amministratore per la fatturazione per l'intera sottoscrizione di Azure hanno una panoramica completa sul consumo, indipendentemente da chi gestisce le risorse.

Per le organizzazioni di dimensioni maggiori, i ruoli di controllo degli accessi in base al ruolo possono essere applicati allo stesso modo per i gruppi di Azure Active Directory considerando la prospettiva che l'utente amministratore intenda concedere l'accesso granulare a team o a interi reparti, non singolarmente per ogni utente; si tratta quindi di un'opzione molto efficiente in termini di gestione e tempo. Per illustrare questo esempio, il ruolo **Collaboratore** è stato aggiunto a uno dei gruppi nel tenant a livello di sottoscrizione.

![aggiungere il ruolo di controllo degli accessi in base al ruolo per i gruppi AAD](./media/role-assignments-external-users/14.png)

Questi gruppi sono gruppi di sicurezza di cui viene eseguito il provisioning e la gestione solo all'interno di Azure Active Directory.

