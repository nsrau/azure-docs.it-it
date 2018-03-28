---
title: RGPD in Azure Active Directory Application Proxy | Microsoft Docs
description: Altre informazioni sul regolamento RGPD in Azure Active Directory Application Proxy.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: c7186f98-dd80-4910-92a4-a7b8ff6272b9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2018
ms.author: markvi
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 5e99fba2f226e1e9b401bd8ef5b3b85a5e8c6a7c
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="gdpr-in-the-azure-active-directory-application-proxy"></a>RGDP in Azure Active Directory Application Proxy  

Azure Active Directory (Azure AD) Application Proxy è conforme al regolamento RGPD, come tutti gli altri servizi e funzionalità Microsoft. Per altre informazioni sul supporto del regolamento RGPD di Microsoft, vedere [Licensing Terms and Documentation](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) (Condizioni di licenza e documentazione).

Dato che questa funzionalità include connettori nei computer, esistono alcuni eventi che è necessario monitorare per mantenere la conformità al regolamento RGPD. Application Proxy crea i tipi di log seguenti, che possono contenere informazioni personali degli utenti finali:

- Registri eventi del connettore
- Registri eventi di Windows

Esistono due modi per essere conformi all'RGPD:

- Eliminare ed esportare le richieste quando si presentano

- Disattivare la registrazione

Per:

- Informazioni su come configurare la conservazione dei dati per i registri eventi di Windows, vedere [Settings for event logs](https://technet.microsoft.com/library/cc952132.aspx) (Impostazioni dei log eventi). 
- Informazioni generali sul registro eventi di Windows, vedere [Using Windows Event Log](https://msdn.microsoft.com/library/windows/desktop/aa385772.aspx) (Uso del registro eventi di Windows).


È possibile trovare i log degli eventi di connessione in `C:\ProgramData\Microsoft\Microsoft AAD Application Proxy Connector\Trace`. Le sezioni seguenti descrivono le procedure correlate per i log degli eventi del connettore. È necessario seguire queste procedure in tutti i computer del connettore.
 

## <a name="processing-requests"></a>Elaborazione delle richieste

Esistono tre tipi diversi di richieste di cui si è responsabili: 

- Delete
- Visualizza
- Esportazione
 
Per elaborare le richieste di visualizzazione ed esportazione, è necessario scorrere ogni file di log per cercare le voci correlate. 

Dato che i log sono file di testo, è possibile eseguire ricerche usando ad esempio il comando `findstr` per individuare le voci correlate a un utente specifico. Cercare i campi seguenti perché potrebbero essere presenti nei log: 

- UserId
- Tipo di nome utente configurato per qualsiasi applicazione che usa la delega vincolata Kerberos:
    - Nome dell'entità utente
    - Nome dell'entità utente locale
    - Parte nome utente del nome dell'entità utente
    - Parte nome utente del nome dell'entità utente locale
    - Nome dell'account SAM locale 

 
È quindi possibile raccogliere questi campi e condividerli con l'utente.
Per elaborare le richieste di eliminazione, è necessario eliminare i log rilevanti. È possibile riavviare il servizio connettore (Microsoft Azure Active Directory Application Proxy Connector) per generare un nuovo file di log. Il nuovo file di log consente di eliminare i vecchi file di log. È quindi possibile seguire la procedura per la visualizzazione/esportazione per trovare tutti i log rilevanti ed eliminare in modo selettivo tali campi o i file. È anche sempre possibile eliminare tutti i file di log precedenti se non sono più necessari.


## <a name="turn-off-connector-logs"></a>Disattivare i log del connettore

Per disattivare i log del connettore, è necessario modificare `C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config` rimuovendo la riga evidenziata: 


![Configurazione](./media/active-directory-application-proxy-gdpr/01.png)


## <a name="next-steps"></a>Passaggi successivi

Per una panoramica di Azure Active Directory Application Proxy, vedere [Come fornire l'accesso remoto sicuro alle applicazioni locali](active-directory-application-proxy-get-started.md).

