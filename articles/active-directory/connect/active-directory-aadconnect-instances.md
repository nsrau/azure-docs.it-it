---
title: 'Azure AD Connect: Istanze del servizio di sincronizzazione | Documentazione Microsoft'
description: Questa pagina contiene considerazioni speciali per le istanze di Azure AD.
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: f340ea11-8ff5-4ae6-b09d-e939c76355a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: e8321c3d16253226a5931cacbce6fa5d50b697bd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-special-considerations-for-instances"></a>Azure AD Connect: Considerazioni speciali per le istanze
Azure AD Connect si usa soprattutto con l'istanza di Azure AD e Office 365 disponibili in tutto il mondo. Ma esistono anche altre istanze e hanno requisiti diversi per gli URL e altre considerazioni speciali.

## <a name="microsoft-cloud-germany"></a>Microsoft Cloud Germany
[Microsoft Cloud Germany](http://www.microsoft.de/cloud-deutschland) è un sovereign cloud gestito da un trustee dei dati tedesco.

| URL da aprire nel server proxy |
| --- |
| \*.microsoftonline.de |
| \*.windows.net |
| +Elenchi di revoche dei certificati |

Quando si accede al tenant di Azure AD, è necessario usare un account nel dominio onmicrosoft.de.

Funzionalità attualmente non presenti in Microsoft Cloud Germany:

* **Azure AD Connect Health** non è disponibile.
* **Aggiornamenti automatici** non è disponibile.
* **Writeback delle password** è disponibile in anteprima con Azure AD Connect versione 1.1.570.0 e successive.
* Altri servizi di Azure AD Premium non sono disponibili.

## <a name="microsoft-azure-government-cloud"></a>Cloud di Microsoft Azure per enti pubblici
Il [cloud di Microsoft Azure per enti pubblici](https://azure.microsoft.com/features/gov/) è un cloud riservato al governo degli Stati Uniti.

È stato supportato da versioni precedenti di DirSync. A partire dalla build 1.1.180 di Azure AD Connect è supportata la generazione successiva del cloud. Questa generazione usa endpoint situati esclusivamente negli Stati Uniti e un elenco diverso di URL da aprire nel server proxy.

| URL da aprire nel server proxy |
| --- |
| \*.microsoftonline.com |
| \*. microsoftonline.us |
| \*.gov.us.microsoftonline.com |
| +Elenchi di revoche dei certificati |

Azure AD Connect non è in grado di rilevare automaticamente che il tenant di Azure AD si trova nel cloud per enti pubblici. È necessario intraprendere le azioni seguenti quando si installa Azure AD Connect.

1. Avviare l'installazione di Azure AD Connect.
2. Quando viene visualizzata la prima pagina in cui si accetta il contratto di licenza, non continuare ma lasciare che venga eseguita l'installazione guidata.
3. Avviare regedit e modificare la chiave del Registro di sistema `HKLM\SOFTWARE\Microsoft\Azure AD Connect\AzureInstance` impostandola sul valore `2`.
4. Tornare all'installazione guidata di Azure AD Connect, accettare il contratto di licenza e continuare. Durante l'installazione, assicurarsi di usare il percorso di installazione della **configurazione personalizzata** e non l'installazione rapida. Continuare l'installazione come di consueto.

Funzionalità attualmente non presenti nel cloud di Microsoft Azure per enti pubblici:

* **Azure AD Connect Health** non è disponibile.
* **Aggiornamenti automatici** non è disponibile.
* **Writeback delle password** è disponibile in anteprima con Azure AD Connect versione 1.1.570.0 e successive.
* Altri servizi di Azure AD Premium non sono disponibili.

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).
