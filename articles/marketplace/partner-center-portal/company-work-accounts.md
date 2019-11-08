---
title: Account di lavoro aziendali e centro per i partner
description: Come verificare se la società ha un account di lavoro configurato con Microsoft, creare un nuovo account aziendale o configurare più account di lavoro da usare con il centro per i partner.
author: ChJenk
manager: evansma
ms.author: parthp
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 4783d3ac8ef78929621179d87f9c8948f6dd079c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73811993"
---
# <a name="company-work-accounts-and-partner-center"></a>Account di lavoro aziendali e centro per i partner

Il centro per i partner USA account aziendali aziendali, noti anche come tenant Azure Active Directory (AD), per gestire l'accesso all'account per più utenti, controllare le autorizzazioni, i gruppi e le applicazioni host e gestire i dati di profilo. Collegando il dominio dell'account di posta elettronica aziendale dell'azienda all'account del centro per i partner, i dipendenti della società possono accedere al centro per i partner per gestire le offerte del Marketplace usando i nomi utente e le password dell'account di lavoro.

## <a name="check-whether-your-company-already-has-a-work-account"></a>Controllare se la società dispone già di un account di lavoro

Se la società ha effettuato la sottoscrizione a un servizio cloud Microsoft, ad esempio Azure, Microsoft Intune o Office 365, si dispone già di un dominio di account di posta elettronica di lavoro (noto anche come tenant di Azure Active Directory) che può essere usato con il centro per i partner.

Seguire questa procedura per verificare:
1. Accedere al portale di amministrazione di Azure all'https://portal.azure.com.
2. Selezionare **Azure Active Directory** dal menu di spostamento a sinistra e quindi selezionare **nomi di dominio personalizzati**.
3. Se si dispone già di un account aziendale, verrà elencato il nome di dominio.

Se la società non dispone già di un account di lavoro, ne verrà creato uno durante il processo di registrazione del centro per i partner.

## <a name="set-up-multiple-work-accounts"></a>Configurare più account di lavoro

Prima di decidere di usare un account aziendale esistente, prendere in considerazione il numero di utenti nell'account di lavoro che dovranno accedere al centro per i partner. Se gli utenti dell'account aziendale non devono accedere al centro per i partner, è consigliabile creare più account di lavoro, in modo che solo gli utenti che dovranno accedere a partner Center siano rappresentati in un determinato account.

## <a name="create-a-new-work-account"></a>Creare un nuovo account aziendale

Per creare un nuovo account aziendale per la società, attenersi alla procedura riportata di seguito. Potrebbe essere necessario richiedere assistenza da chiunque disponga di autorizzazioni amministrative per l'account Microsoft Azure della società.

1. Accedere al [portale di Microsoft Azure](https://portal.azure.com).
2. Nel menu di spostamento a sinistra selezionare il **Azure Active Directory** -> **utenti**.
3. Selezionare **nuovo utente** e creare un nuovo account di lavoro di Azure immettendo un nome e un indirizzo di posta elettronica. Verificare che il **ruolo della directory** sia impostato su **utente** , quindi selezionare la casella di controllo **Mostra password** nella parte inferiore per visualizzare e prendere nota della password generata automaticamente.
4. Selezionare **Crea** per salvare il nuovo utente.

L'indirizzo di posta elettronica per l'account utente deve essere un nome di dominio verificato nella directory. È possibile elencare tutti i domini verificati nella directory selezionando **Azure Active Directory** -> **nomi di dominio personalizzati** nel menu di spostamento a sinistra.

Per ulteriori informazioni sull'aggiunta di domini personalizzati in Azure Active Directory, vedere [aggiungere o associare un dominio nel Azure ad](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain).

## <a name="troubleshoot-work-email-sign-in"></a>Risolvere i problemi di accesso alla posta elettronica di lavoro

Se si verificano problemi durante l'accesso all'account aziendale (noto anche come tenant Azure AD), trovare lo scenario nel diagramma seguente che meglio corrisponde alla situazione e seguire le procedure consigliate.

![Diagramma per la risoluzione dei problemi di accesso dell'account di lavoro](./media/onboarding-aad-flow.png)

## <a name="next-steps"></a>Passaggi successivi

- [Gestisci l'account del Marketplace commerciale nel centro per i partner](./manage-account.md) 
