---
title: Azure AD Connect in Microsoft Cloud per la Germania
description: "Azure AD Connect integra le directory locali con Azure Active Directory. Consente di fornire un&quot;identità comune per le applicazioni di Office 365, Azure e SaaS integrate con Azure AD."
keywords: "introduzione ad Azure AD Connect, panoramica di Azure AD Connect, che cos&quot;è Azure AD Connect, installare Active Directory, Germania, Foresta Nera"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 2bcb0caf-5d97-46cb-8c32-bda66cc22dad
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/08/2016
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 7db56a4c0efb208591bb15aa03a4c0dbf833d22e
ms.openlocfilehash: a6bb1c4b3a4972cdab9b99c548ef918a4d1070a0


---
# <a name="azure-ad-connect-in-microsoft-cloud-germany---public-preview"></a>Azure AD Connect in Microsoft Cloud per la Germania: anteprima pubblica
## <a name="introduction"></a>Introduzione
Azure AD Connect consente la sincronizzazione tra Active Directory locale e Azure Active Directory.
Attualmente, molti scenari in [Microsoft Cloud per la Germania](https://www.microsoft.com/de-de/cloud/deutschland/default.aspx) devono essere gestiti dall'operatore. Quando si usa Microsoft Cloud per la Germania, è necessario tenere presente quanto segue:

* Gli URL seguenti devono essere aperti su un server proxy per eseguire correttamente la sincronizzazione:
  
  * *.microsoftonline.de
  * *.windows.net
  * * Elenchi di revoche dei certificati
* Quando si accede alla directory di Azure AD, è necessario usare un account nel dominio onmicrosoft.de.
* Non sono disponibili le funzionalità seguenti:
  * Azure AD Connect Health
  * Aggiornamenti automatici
  * Writeback delle password

## <a name="download"></a>Scaricare
È possibile scaricare Azure AD Connect dal pannello Azure AD Connect nel portale.  Usare le istruzioni seguenti per trovare il pannello Azure AD Connect.

### <a name="the-azure-ad-connect-blade"></a>Pannello Azure AD Connect
Dopo l'accesso al portale di Azure, seguire questa procedura:

1. Andare a Esplora
2. Selezionare Azure Active Directory
3. Selezionare Azure AD Connect

Dovrebbe essere visualizzata la seguente schermata:

![Pannello Azure AD Connect](media/active-directory-aadconnect-germany/germany1.png)

La tabella seguente illustra le funzionalità visualizzate nel pannello.

| Titolo | Descrizione |
| --- | --- |
| STATO SINCRONIZZAZIONE |Indica se la sincronizzazione è abilitata o disabilitata. |
| ULTIMA SINCRONIZZAZIONE |Ultima volta in cui è stata completata correttamente una sincronizzazione. |
| DOMINI FEDERATI |Indica il numero di domini federati attualmente configurati. |

## <a name="installation"></a>Installazione
Per installare Azure AD Connect, è possibile usare la documentazione disponibile [qui](active-directory-aadconnect.md#install-azure-ad-connect).

## <a name="advanced-features-and-additional-information"></a>Funzionalità avanzate e informazioni aggiuntive
Per altre informazioni e indicazioni sulle impostazioni personalizzate o sulle configurazioni avanzate, vedere [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).  Questa pagina contiene informazioni e collegamenti ad altre indicazioni.




<!--HONumber=Jan17_HO1-->


