---
title: Sincronizzazione con ambito per Azure AD Domain Services | Microsoft Docs
description: Informazioni su come usare la portale di Azure per configurare la sincronizzazione con ambito da Azure AD a un dominio gestito Azure Active Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/24/2020
ms.author: iainfou
ms.openlocfilehash: 97c561b2ae919799343abe844b57239d04c26d90
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87283130"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services-using-the-azure-portal"></a>Configurare la sincronizzazione con ambito da Azure AD a Azure Active Directory Domain Services utilizzando il portale di Azure

Per fornire servizi di autenticazione, Azure Active Directory Domain Services (Azure AD DS) sincronizza gli utenti e i gruppi da Azure AD. In un ambiente ibrido, è possibile sincronizzare gli utenti e i gruppi di un ambiente di Active Directory Domain Services locale (AD DS) con Azure AD tramite Azure AD Connect e quindi sincronizzarli con un dominio gestito Azure AD DS.

Per impostazione predefinita, tutti gli utenti e i gruppi di una directory Azure AD sono sincronizzati con un dominio gestito. Se si hanno esigenze specifiche, è invece possibile scegliere di sincronizzare solo un set di utenti definito.

Questo articolo illustra come configurare la sincronizzazione con ambito e quindi modificare o disabilitare il set di utenti con ambito usando il portale di Azure. È anche possibile [completare questi passaggi usando PowerShell][scoped-sync-powershell].

## <a name="before-you-begin"></a>Prima di iniziare

Per completare le procedure descritte in questo articolo, sono necessari i privilegi e le risorse seguenti:

* Una sottoscrizione di Azure attiva.
    * Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un tenant di Azure Active Directory associato alla sottoscrizione, sincronizzato con una directory locale o con una directory solo cloud.
    * Se necessario, [creare un tenant di Azure Active Directory][create-azure-ad-tenant] o [associare una sottoscrizione di Azure al proprio account][associate-azure-ad-tenant].
* Un dominio gestito di Azure Active Directory Domain Services abilitato e configurato nel tenant di Azure AD.
    * Se necessario, completare l'esercitazione per [creare e configurare un Azure Active Directory Domain Services dominio gestito][tutorial-create-instance].
* Per modificare l'ambito di sincronizzazione di Azure AD DS, è necessario disporre dei privilegi di *amministratore globale* nel tenant di Azure ad.

## <a name="scoped-synchronization-overview"></a>Panoramica della sincronizzazione con ambito

Per impostazione predefinita, tutti gli utenti e i gruppi di una directory Azure AD sono sincronizzati con un dominio gestito. Se solo alcuni utenti devono accedere al dominio gestito, è possibile sincronizzare solo gli account utente. Questa sincronizzazione con ambito è basata sul gruppo. Quando si configura la sincronizzazione con ambito basata su gruppo, solo gli account utente che appartengono ai gruppi specificati vengono sincronizzati con il dominio gestito. I gruppi annidati non sono sincronizzati, ma solo i gruppi specifici selezionati.

È possibile modificare l'ambito di sincronizzazione quando si crea il dominio gestito o una volta distribuito. È anche possibile modificare l'ambito di sincronizzazione in un dominio gestito esistente senza che sia necessario ricrearlo.

Per ulteriori informazioni sul processo di sincronizzazione, vedere informazioni sulla [sincronizzazione in Azure ad Domain Services][concepts-sync].

> [!WARNING]
> La modifica dell'ambito di sincronizzazione determina la risincronizzazione di tutti i dati da parte del dominio gestito. Si applicano le considerazioni seguenti:
>
>  * Quando si modifica l'ambito di sincronizzazione per un dominio gestito, si verifica una risincronizzazione completa.
>  * Gli oggetti che non sono più necessari nel dominio gestito vengono eliminati. Vengono creati nuovi oggetti nel dominio gestito.

## <a name="enable-scoped-synchronization"></a>Abilita sincronizzazione con ambito

Per abilitare la sincronizzazione con ambito nella portale di Azure, attenersi alla procedura seguente:

1. Nella portale di Azure cercare e selezionare **Azure ad Domain Services**. Scegliere il dominio gestito, ad esempio *aaddscontoso.com*.
1. Selezionare **sincronizzazione** dal menu sul lato sinistro.
1. Per *tipo di sincronizzazione*selezionare con **ambito**.
1. Scegliere **Seleziona gruppi**, quindi cercare e scegliere i gruppi da aggiungere.
1. Quando vengono apportate tutte le modifiche, selezionare **Salva ambito di sincronizzazione**.

La modifica dell'ambito di sincronizzazione determina la risincronizzazione di tutti i dati da parte del dominio gestito. Gli oggetti che non sono più necessari nel dominio gestito vengono eliminati e il completamento della risincronizzazione può richiedere del tempo.

## <a name="modify-scoped-synchronization"></a>Modificare la sincronizzazione con ambito

Per modificare l'elenco dei gruppi i cui utenti devono essere sincronizzati con il dominio gestito, attenersi alla procedura seguente:

1. Nella portale di Azure cercare e selezionare **Azure ad Domain Services**. Scegliere il dominio gestito, ad esempio *aaddscontoso.com*.
1. Selezionare **sincronizzazione** dal menu sul lato sinistro.
1. Per aggiungere un gruppo, scegliere **+ Seleziona gruppi** nella parte superiore, quindi scegliere i gruppi da aggiungere.
1. Per rimuovere un gruppo dall'ambito di sincronizzazione, selezionarlo dall'elenco dei gruppi attualmente sincronizzati e scegliere **Rimuovi gruppi**.
1. Quando vengono apportate tutte le modifiche, selezionare **Salva ambito di sincronizzazione**.

La modifica dell'ambito di sincronizzazione determina la risincronizzazione di tutti i dati da parte del dominio gestito. Gli oggetti che non sono più necessari nel dominio gestito vengono eliminati e il completamento della risincronizzazione può richiedere del tempo.

## <a name="disable-scoped-synchronization"></a>Disabilitare la sincronizzazione con ambito

Per disabilitare la sincronizzazione con ambito gruppo per un dominio gestito, completare i passaggi seguenti:

1. Nella portale di Azure cercare e selezionare **Azure ad Domain Services**. Scegliere il dominio gestito, ad esempio *aaddscontoso.com*.
1. Selezionare **sincronizzazione** dal menu sul lato sinistro.
1. Modificare il *tipo di sincronizzazione* dall' **ambito** a **tutti**, quindi selezionare **Salva ambito di sincronizzazione**.

La modifica dell'ambito di sincronizzazione determina la risincronizzazione di tutti i dati da parte del dominio gestito. Gli oggetti che non sono più necessari nel dominio gestito vengono eliminati e il completamento della risincronizzazione può richiedere del tempo.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sul processo di sincronizzazione, vedere informazioni sulla [sincronizzazione in Azure ad Domain Services][concepts-sync].

<!-- INTERNAL LINKS -->
[scoped-sync-powershell]: powershell-scoped-synchronization.md
[concepts-sync]: synchronization.md
[tutorial-create-instance]: tutorial-create-instance.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md