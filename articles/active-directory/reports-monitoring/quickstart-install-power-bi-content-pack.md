---
title: Installare il pacchetto di contenuto Power BI per Azure AD | Microsoft Docs
description: Informazioni su come installare il pacchetto di contenuto Power BI per Azure AD
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: fd5604eb-1334-4bd8-bfb5-41280883e2b5
ms.service: active-directory
ms.workload: identity
ms.subservice: report-monitor
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 69a69732d8cb42c248fa954ef9047e5876f40837
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58437425"
---
# <a name="quickstart-install-azure-active-directory-power-bi-content-pack"></a>Avvio rapido: Installare il pacchetto di contenuto Power BI di Azure Active Directory

|  |
|--|
|Attualmente, il pacchetto di contenuto Power BI di Azure AD usa le API Graph di Azure AD per recuperare dati dal tenant di Azure AD. Di conseguenza, potrebbe notarsi una certa disparità tra i dati disponibili nel pacchetto di contenuto e i dati recuperati tramite le [API Microsoft Graph per la creazione di report](concept-reporting-api.md). |
|  |

Il pacchetto di contenuto di Power BI per Azure Active Directory (Azure AD) consente di visualizzare i dati dei report dall'ambiente specifico. È possibile scaricare il pacchetto di contenuto predefinito e usarlo per creare report su tutte le attività all'interno della directory tramite l'esperienza di visualizzazione avanzata offerta da Power BI. È inoltre possibile creare dashboard personalizzati e condividerli facilmente con altri utenti nell'organizzazione. 

Questa Guida introduttiva descrive come installare il pacchetto di contenuto.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione introduttiva, sono necessari gli elementi seguenti:

* Un account di Power BI. Si tratta dello stesso account di Office 365 o Azure AD. 
* ID del tenant di Azure AD. Si tratta dell'**ID directory** della directory, disponibile nella [pagina Proprietà](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties) del portale di Azure.
* Una licenza Azure AD Premium (P1 o P2). vedere [Procedura: Effettuare l'iscrizione alle edizioni Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) per aggiornare l'edizione di Azure Active Directory in uso.

## <a name="install-azure-ad-power-bi-content-pack"></a>Installare il pacchetto di contenuto Power BI per Azure AD 

1. Accedere a [Power BI](https://app.powerbi.com/groups/me/getdata/services) con il proprio account Power BI. Si tratta dello stesso account di Office 365 o Azure AD.

2. Cercare i **log attività di Azure Active Directory** nella pagina **App** e selezionare **Scarica adesso**. 

   ![Pacchetto di contenuto Power BI di Azure Active Directory](./media/quickstart-install-power-bi-content-pack/getitnow.png) 
    
3. Nella finestra popup digitare l'ID del tenant di Azure AD, immettere **7** per indicare il numero di giorni da cercare e quindi selezionare **Avanti**.
    
   ![Pacchetto di contenuto Power BI di Azure Active Directory](./media/quickstart-install-power-bi-content-pack/connect.png) 

4. Selezionare il dashboard dei log attività di Azure Active Directory, dopo che è stato creato.

   ![Pacchetto di contenuto Power BI di Azure Active Directory](./media/quickstart-install-power-bi-content-pack/dashboard.png) 
    
## <a name="next-steps"></a>Passaggi successivi

* [Come usare il pacchetto di contenuto Power BI](howto-power-bi-content-pack.md).
* [Risoluzione dei problemi relativi ai pacchetti di contenuto](troubleshoot-content-pack.md).
