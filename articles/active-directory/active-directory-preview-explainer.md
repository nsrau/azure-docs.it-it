---
title: Informazioni sull&quot;anteprima di Azure Active Directory | Documentazione Microsoft
description: Un argomento che illustra le differenze tra l&quot;anteprima di Azure Active Directory nel portale classico e nel portale di Azure.
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: e717c11c-e7c9-4565-8e47-1950905e5b3c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2016
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 664abe5887b3d96eb48e3b47df9a32648ee623dd


---
# <a name="preview-of-the-azure-active-directory-management-experience-in-the-azure-portal"></a>Anteprima dell'esperienza di gestione di Azure Active Directory nel portale di Azure
L'esperienza di gestione di Azure Active Directory (Azure AD) è disponibile in anteprima nel portale di Azure. È possibile provarla effettuando l'accesso al [portale di Azure](https://portal.azure.com) come amministratore globale della directory. Dopodiché, selezionare Azure Active Directory se appare nell'elenco dei servizi, altrimenti selezionare **Altri servizi** per visualizzare l'elenco di tutti i servizi. Non occorre una sottoscrizione di Azure per utilizzare l'esperienza di gestione di Active Directory nel portale di Azure.

## <a name="learn-about-what-you-can-do-in-the-preview-experience"></a>Informazioni sulle operazioni disponibili nella versione di anteprima
La versione di anteprima permette di gestire molte risorse della directory, come utenti, gruppi e applicazioni, nonché le impostazioni della directory nel portale di Azure. È in corso un processo di miglioramento dell'esperienza per includere tutte le funzionalità esistenti nell'esperienza di gestione di Azure AD nel [portale di Azure classico](https://manage.windowsazure.com). Fino ad allora, esistono alcune operazioni di gestione della directory che devono essere effettuate nel portale classico.

## <a name="manage-the-same-azure-ad-tenants"></a>Gestire gli stessi tenant di Azure AD
La versione di anteprima consente di leggere e scrivere nello stesso tenant di Azure Active Directory del portale classico e dell'interfaccia di amministrazione di Office 365. Le modifiche apportate in uno di questi portali vengono applicate in tutti gli altri.

## <a name="use-the-same-authorization-logic"></a>Utilizzare la stessa logica di autorizzazione
L'esperienza di anteprima consente di utilizzare la stessa logica di autorizzazione come i client esistenti di Active Directory. Gli utenti sono autorizzati ad apportare modifiche alle risorse della directory in base al proprio ruolo nella directory, che può essere di amministratore globale, amministratore utenti e amministratore password. Avere un ruolo per le risorse di Azure o una sottoscrizione di Azure non autorizza l'utente a gestire le risorse della directory. Per altre informazioni sui ruoli di gestione di Azure AD, vedere [Assegnazione dei ruoli di amministratore in Azure Active Directory](active-directory-assign-admin-roles.md). 

L'esperienza di anteprima è ottimizzata per gli amministratori globali. Se si usa la versione di anteprima dopo aver eseguito l'accesso come utente senza il ruolo di amministratore globale, la versione potrebbe avere una funzionalità ridotta. Ad esempio, potrebbe essere possibile selezionare un pulsante che consente di iniziare un'operazione che non può essere completata nella directory. La versione verrà presto migliorata.

## <a name="tell-us-what-you-think"></a>Lasciare un feedback
È possibile lasciare un feedback sull'esperienza di anteprima nella sezione del portale di amministrazione del [forum dei commenti di Azure AD](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD&filter=alltypes&sort=lastpostdesc).




<!--HONumber=Nov16_HO3-->


