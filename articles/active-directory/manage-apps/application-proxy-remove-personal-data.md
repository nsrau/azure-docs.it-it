---
title: Rimuovere i dati personali in Azure Active Directory Application Proxy | Microsoft Docs
description: Rimuovere i dati personali dai connettori installati nei dispositivi nell'Azure Active Directory Application Proxy.
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: bccda196be82808e7dc369de3f517490f410e26e
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2018
ms.locfileid: "39366043"
---
# <a name="remove-personal-data-for-azure-active-directory-application-proxy"></a>Rimuovere i dati personali in Azure Active Directory Application Proxy  

Azure Active Directory Application Proxy richiede l'installazione dei connettori nei dispositivi e questo potrebbe comportare la presenza di dati personali. In questo articolo vengono illustrate le procedure per eliminare i dati personali e migliorare la privacy. 


## <a name="where-is-the-personal-data"></a>Dove si trovano i dati personali?
È possibile che l'Application Proxy scriva dati personali per i tipi di log seguenti:

- Registri eventi del connettore
- Registri eventi di Windows

## <a name="remove-personal-data-from-windows-event-logs"></a>Rimuovere i dati personali dai log eventi di Windows

Per informazioni su come configurare la conservazione dei dati per i registri eventi di Windows, consultare [Impostazioni dei log eventi](https://technet.microsoft.com/library/cc952132.aspx). Per informazioni sui log eventi di Windows, consultare [Utilizzare il log eventi di Windows](https://msdn.microsoft.com/library/windows/desktop/aa385772.aspx).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

## <a name="remove-personal-data-from-connector-event-logs"></a>Rimuovere i dati personali dai log eventi dei Connettori

Per assicurarsi che i log di Application Proxy non contengano dati personali, è possibile:

- Eliminare o visualizzare i dati se necessario, o
- Disattivare la registrazione

Utilizzare le sezioni seguenti per rimuovere i dati personali dai log eventi dei connettori. È necessario completare il processo di rimozione per tutti i dispositivi in cui è installato il connettore.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

### <a name="view-or-export-specific-data"></a>Visualizzare o esportare dati specifici

Per visualizzare o esportare dati specifici, cercare le voci relative in ognuno dei log eventi dei connettori. I log si trovano alla `C:\ProgramData\Microsoft\Microsoft AAD Application Proxy Connector\Trace`. 

Poiché i log sono file di testo, è possibile usare [findstr](https://docs.microsoft.com/windows-server/administration/windows-commands/findstr) per cercare le voci correlate a un utente.  

Per trovare i dati personali, cercare i file di log per l'ID utente. 

Per trovare i dati personali registrati da un'applicazione che utilizza la delega vincolata Kerberos, cercare questi componenti del tipo di nome utente:

- Nome dell'entità utente locale
- Parte nome utente del nome dell'entità utente
- Parte nome utente del nome dell'entità utente locale
- Nome dell'account locale di Security Accounts Manager (SAM) 


### <a name="delete-specific-data"></a>Eliminare i dati specifici

Per eliminare i dati specifici:

1. È possibile riavviare il connettore di Microsoft Azure Active Directory Application Proxy Connector per generare un nuovo file di log. Il nuovo file di log consente di eliminare o modificare i vecchi file di log. 
2. Seguire il processo di [visualizzazione o esportazione dati specifici](#view-or-export-specific-data) descritto in precedenza per trovare informazioni che devono essere eliminate. Cercare tutti i log dei connettori.
3. Eliminare i file di log rilevanti o eliminare in modo selettivo i campi che contengono dati personali. È anche possibile eliminare tutti i file di log precedenti se non sono più necessari.

### <a name="turn-off-connector-logs"></a>Disattivare i log del connettore

Un modo per verificare i log dei connettori non contengano dati personali consiste nel disattivare la generazione di log. Per interrompere la generazione dei log dei connettore, rimuovere la seguente riga evidenziata dal `C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config`. 

![Configurazione](./media/application-proxy-remove-personal-data/01.png)


## <a name="next-steps"></a>Passaggi successivi

Per una panoramica di Application Proxy, consultare [Come fornire l'accesso remoto sicuro alle applicazioni locali](application-proxy.md).

