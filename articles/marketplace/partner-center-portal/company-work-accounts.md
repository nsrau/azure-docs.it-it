---
title: Account di lavoro aziendali e Centro per i partner
description: Come verificare se la società dispone di un account di lavoro configurato con Microsoft, creare un nuovo account di lavoro o impostare più account di lavoro da utilizzare con il Centro per i partner.
author: dsindona
ms.author: parthp
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: c4e7427d87c5f88d8c686b867ef88ceb05f28286
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281426"
---
# <a name="company-work-accounts-and-partner-center"></a>Account di lavoro aziendali e Centro per i partner

Il Centro per i partner usa gli account di lavoro aziendali, noti anche come tenant di Azure Active Directory (AD), per gestire l'accesso agli account per più utenti, controllare le autorizzazioni, ospitare gruppi e applicazioni e gestire i dati del profilo. Collegando il dominio dell'account e-mail aziendale dell'azienda al tuo account del Centro per i partner, i dipendenti della tua azienda possono accedere al Centro per i partner per gestire le offerte del marketplace utilizzando i nomi utente e le password del proprio account di lavoro.

## <a name="check-whether-your-company-already-has-a-work-account"></a>Verificare se l'azienda dispone già di un account di lavoro

Se l'azienda ha sottoscritto un servizio cloud Microsoft, ad esempio Azure, Microsoft Intune o Office 365, si dispone già di un dominio dell'account di posta elettronica di lavoro (denominato anche tenant di Azure Active Directory) che può essere usato con il Centro per i partner.

Attenersi alla seguente procedura per verificare:
1. Accedere al portale di https://portal.azure.comamministrazione di Azure all'indirizzo .
2. Selezionare **Azure Active Directory** dal menu di spostamento a sinistra e quindi selezionare Nomi di dominio **personalizzati**.
3. Se hai già un account aziendale, il nome del tuo dominio verrà visualizzato nell'elenco.

Se la tua azienda non dispone già di un account di lavoro, ne verrà creato uno automaticamente durante il processo di registrazione al Centro per i partner.

## <a name="set-up-multiple-work-accounts"></a>Impostare più account di lavoro

Prima di decidere di utilizzare un account di lavoro esistente, considerare il numero di utenti nell'account di lavoro che dovranno accedere al Centro per i partner. Se nell'account di lavoro sono presenti utenti che non dovranno accedere al Centro per i partner, è consigliabile creare più account di lavoro, in modo che solo gli utenti che dovranno accedere al Centro per i partner siano rappresentati in un determinato account.

## <a name="create-a-new-work-account"></a>Creare un nuovo account di lavoro

Per creare un nuovo account di lavoro per la società, attenersi alla seguente procedura. Potrebbe essere necessario richiedere assistenza a chiunque disponga di autorizzazioni amministrative per l'account Microsoft Azure dell'azienda.

1. Accedere al [portale di Microsoft Azure](https://portal.azure.com).
2. Nel menu di spostamento sinistro selezionare**Utenti**di **Azure Active Directory** -> .
3. Selezionare **Nuovo utente** e creare un nuovo account di lavoro di Azure immettendo un nome e un indirizzo di posta elettronica. Assicurarsi che il **ruolo Directory** sia impostato su **Utente** e selezionare la casella di controllo **Mostra password** nella parte inferiore per visualizzare e prendere nota della password generata automaticamente.
4. Selezionare **Crea** per salvare il nuovo utente.

L'indirizzo e-mail dell'account utente deve essere un nome di dominio verificato nella directory. È possibile elencare tutti i domini verificati nella directory selezionando Nomi di**dominio personalizzati** di Azure **Active Directory** -> nel menu di spostamento a sinistra.

Per altre informazioni sull'aggiunta di domini personalizzati in Azure Active Directory, vedere [Aggiungere o associare un dominio in Azure AD.](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain)

## <a name="troubleshoot-work-email-sign-in"></a>Risolvere i problemi di accesso tramite posta elettronica di lavoroTroubleshoot work email sign-in

Se si verificano problemi di accesso all'account di lavoro (noto anche come tenant di Azure AD), trovare lo scenario nel diagramma seguente che meglio corrisponde alla situazione e seguire i passaggi consigliati.

![Diagramma per la risoluzione dei problemi di accesso all'account di lavoro](./media/onboarding-aad-flow.png)

## <a name="next-steps"></a>Passaggi successivi

- [Gestire l'account Del Mercato commerciale nel Centro per i partner](./manage-account.md) 
