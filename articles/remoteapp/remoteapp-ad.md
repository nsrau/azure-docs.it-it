---
title: Azure AD + requisiti di Active Directory per Azure RemoteApp | Documentazione Microsoft
description: Informazioni su come configurare Active Directory per lavorare con Azure RemoteApp.
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 66366b25-6012-45fa-a4f6-da0ddfe0b486
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 232901ab919c63ea70e52afb845240b41a517c51
ms.lasthandoff: 03/31/2017


---
# <a name="azure-ad--active-directory-requirements-for-azure-remoteapp"></a>Azure AD + requisiti di Active Directory per Azure RemoteApp
> [!IMPORTANT]
> Azure RemoteApp verrà sospeso a partire dal 31 agosto 2017. Per i dettagli, vedere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) .
> 
> 

Per la raccolta ibrida di RemoteApp di Azure o per un insieme di cloud che si desidera attuare utilizzando una connessione di Active Directory, è necessario eseguire le operazioni seguenti.

### <a name="connect-azure-ad-and-active-directory"></a>Connettersi AD Azure e ad Active Directory
Se si desidera connettere il tenant di Azure AD e gli ambienti di Active Directory in locale, utilizzare la connessione di Active Directory. Verranno visualizzati solo [4 clic](https://blogs.technet.microsoft.com/enterprisemobility/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect/) per connettere le due directory.

Nota - la sincronizzazione delle Directory è necessaria per le raccolte ibride.

### <a name="make-sure-your-domaincom-match"></a>Controllare la corrispondenza del valore "@domain.com"
Prima di iniziare, assicurarsi che il nome UPN per la foresta locale corrisponda al suffisso del dominio di Microsoft Azure. 

Dopo avere impostato il suffisso del dominio UPN in Azure AD, tutti gli utenti che accedono ad Azure RemoteApp accederanno come "user@<the suffix you set up>". Assicurarsi che gli utenti possano inoltre accedere con lo stesso user@suffix nel dominio locale. In alcuni casi è possibile impostare un nome di dominio in Azure AD quando si specifica un suffisso di dominio diverso per l'utente locale. In questo caso, gli utenti non potranno connettersi a nessun computer appartenente a un dominio o a risorse tramite Azure RemoteApp.

Ad esempio, se si imposta il suffisso di dominio UPN in AAD come contoso.com, ma alcuni utenti in locale o in Active Directory sono configurati per l'accesso con @contoso.uk, tali utenti non potranno accedere correttamente alla raccolta ARA. Gli utenti UPN in AAD e in Active Directory devono essere gli stessi affinché l'accesso sia possibile"

### <a name="create-objects-for-azure-remoteapp"></a>Creare gli oggetti per RemoteApp di Azure
Inoltre, è necessario creare i seguenti oggetti di Active Directory locali:

* Un account del servizio per fornire l'accesso alle risorse di dominio ai programmi RemoteApp unendo gli endpoint di Host sessione Desktop remoto al dominio locale.
* Un'unità organizzativa per contenere oggetti computer di RemoteApp. L'uso dell'unità organizzativa è consigliabile (ma non obbligatorio) per isolare gli account e i criteri che si useranno con RemoteApp.

Entrambi questi oggetti sono necessari quando si crea la raccolta di RemoteApp, sarà dunque necessario innanzitutto eseguire questi passaggi.


