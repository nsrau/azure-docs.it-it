---
title: Azure AD Connect in Microsoft Cloud per la Germania
description: Azure AD Connect integra le directory locali con Azure Active Directory. Consente di fornire un'identità comune per le applicazioni di Office 365, Azure e SaaS integrate con Azure AD.
keywords: introduzione ad Azure AD Connect, panoramica di Azure AD Connect, che cos'è Azure AD Connect, installare Active Directory, Germania, Foresta Nera
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 2bcb0caf-5d97-46cb-8c32-bda66cc22dad
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/12/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 9ea28e1215c989e7cb64c54dde7eac5363699089
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54464239"
---
# <a name="azure-ad-connect-in-microsoft-cloud-germany---public-preview"></a>Azure AD Connect in Microsoft Cloud per la Germania: anteprima pubblica
## <a name="introduction"></a>Introduzione
Azure AD Connect consente la sincronizzazione tra Active Directory locale e Azure Active Directory.
Attualmente, molti scenari in [Microsoft Cloud per la Germania](https://azure.microsoft.com/global-infrastructure/germany/
) devono essere gestiti dall'operatore. Quando si usa Microsoft Cloud per la Germania, è necessario tenere presente le informazioni seguenti:

* Gli URL seguenti devono essere aperti su un server proxy per eseguire correttamente la sincronizzazione:
  
  * *.microsoftonline.de
  * *.windows.net
  * * Elenchi di revoche dei certificati
* Quando si accede alla directory di Azure AD, è necessario usare un account nel dominio onmicrosoft.de.

 
## <a name="download"></a>Download
È possibile scaricare Azure AD Connect dal pannello Azure AD Connect nel portale.  Usare le istruzioni seguenti per trovare il pannello Azure AD Connect.

### <a name="the-azure-ad-connect-blade"></a>Pannello Azure AD Connect
Dopo avere eseguito l'accesso al portale di Azure:

1. Andare a Esplora
2. Selezionare Azure Active Directory
3. Selezionare Azure AD Connect

Verranno visualizzati i dettagli seguenti:

![Pannello Azure AD Connect](./media/reference-connect-germany/germany1.png)

La tabella seguente illustra le funzionalità visualizzate nel pannello.

| Title | DESCRIZIONE |
| --- | --- |
| STATO SINCRONIZZAZIONE |Indica se la sincronizzazione è abilitata o disabilitata. |
| ULTIMA SINCRONIZZAZIONE |Ultima volta in cui è stata completata correttamente una sincronizzazione. |
| DOMINI FEDERATI |Indica il numero di domini federati attualmente configurati. |

## <a name="installation"></a>Installazione
Per installare Azure AD Connect, è possibile usare la documentazione disponibile [qui](how-to-connect-install-roadmap.md).

## <a name="advanced-features-and-additional-information"></a>Funzionalità avanzate e informazioni aggiuntive
Per altre informazioni sulle impostazioni personalizzate o sulle configurazioni avanzate, passare a [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md). Questa pagina contiene informazioni e collegamenti ad altre indicazioni.

