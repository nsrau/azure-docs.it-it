---
title: Spiegazione in anteprima di Azure Active Directory | Microsoft Docs
description: Un argomento che illustra le differenze tra l'anteprima di Azure Active Directory nel portale classico e nel portale di Azure.
services: active-directory
documentationcenter: ''
author: curtand
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2016
ms.author: curtand

---
# Anteprima dell'esperienza di gestione di Azure Active Directory nel portale di Azure
L'esperienza di gestione di Azure Active Directory (Azure AD) è disponibile in anteprima nel portale di Azure. È possibile provarla effettuando l'accesso al [portale di Azure](https://portal.azure.com) come amministratore globale della directory. Dopodiché, selezionare Azure Active Directory se appare nell'elenco dei servizi, altrimenti selezionare **Altri servizi** per visualizzare l'elenco di tutti i servizi. Non occorre una sottoscrizione di Azure per utilizzare l'esperienza di gestione di Active Directory nel portale di Azure.

## Funzionalità dell'esperienza di anteprima
L'esperienza di anteprima consente di gestire molte risorse della directory, come utenti, gruppi e applicazioni, nonché le impostazioni della directory nel portale di Azure. È in corso un processo di miglioramento dell'esperienza per includere tutte le funzionalità esistenti nell'esperienza di gestione di Azure AD nel [portale di Azure classico](https://manage.windowsazure.com). Fino ad allora, esistono alcune operazioni di gestione della directory che devono essere effettuate nel portale classico.

## Gestire gli stessi tenant di Azure AD
L'esperienza di anteprima consente di leggere e scrivere sullo stesso tenant di Azure Active Directory come il portale classico e il centro di amministrazione di Office 365. Le modifiche apportate in uno di questi portali vengono applicate in tutti gli altri.

## Utilizzare la stessa logica di autorizzazione
L'esperienza di anteprima consente di utilizzare la stessa logica di autorizzazione come i client esistenti di Active Directory. Gli utenti sono autorizzati ad apportare modifiche alle risorse della directory in base al proprio ruolo nella directory, che può essere amministratore globale, utente amministratore e amministratore password. Il fatto di avere un ruolo sulle risorse di Azure o una sottoscrizione di Azure non autorizza l'utente a gestire le risorse della directory. Per altre informazioni sui ruoli di gestione di Azure AD, vedere [Assegnazione dei ruoli di amministratore in Azure Active Directory](active-directory-assign-admin-roles.md).

L'esperienza di anteprima è ottimizzata per gli amministratori globali. Se si utilizza l'esperienza di anteprima dopo aver eseguito l'accesso come utente che non ha il ruolo di amministratore globale, l'esperienza potrebbe non essere pienamente funzionale. Ad esempio, potrebbe essere possibile selezionare un pulsante che consente di iniziare un'operazione che non può essere completata nella directory. Questa esperienza verrà presto migliorata.

## Lasciare un feedback
È possibile lasciare un feedback sull'esperienza di anteprima nella sezione del portale di amministrazione del [forum dei commenti di Azure AD](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD&filter=alltypes&sort=lastpostdesc).

<!---HONumber=AcomDC_0914_2016-->