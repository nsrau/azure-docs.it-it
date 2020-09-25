---
title: Contrassegnare un'app come con autore verificato - Microsoft Identity Platform | Azure
description: Viene descritto come contrassegnare un'app come con autore verificato. Se un'applicazione è contrassegnata come con autore verificato, significa che l'autore ha verificato la propria identità usando un account Microsoft Partner Network che ha completato il processo di verifica e ha associato l'account MPN durante la registrazione dell'applicazione.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 05/08/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: 80731421b6a0d3f5bdabf117a7239bafa056e652
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91258337"
---
# <a name="mark-your-app-as-publisher-verified"></a>Contrassegnare l'app come con autore verificato

Quando la registrazione di un'app ha un server di pubblicazione verificato, significa che l'autore dell'app ha [verificato](/partner-center/verification-responses) la propria identità usando il proprio account di Microsoft Partner Network (MPN) e ha associato l'account MPN alla registrazione dell'app. Questo articolo descrive come completare il processo di [Verifica dell'editore](publisher-verification-overview.md) .

## <a name="quickstart"></a>Guida introduttiva
Se si è già iscritti a Microsoft Partner Network (MPN) e sono stati soddisfatti i [prerequisiti](publisher-verification-overview.md#requirements), è possibile iniziare subito: 

1. Accedere al [portale di registrazione delle app](https://aka.ms/PublisherVerificationPreview) usando [l'autenticazione](../fundamentals/concept-fundamentals-mfa-get-started.md) a più fattori

1. Scegliere un'app e fare clic su **Personalizzazione**. 

1. Fare clic su **Aggiungere l'ID MPN per verificare l'autore** ed esaminare i requisiti elencati.

1. Immettere l'ID MPN e fare clic su **Verifica e salva**.

Per informazioni dettagliate su vantaggi specifici, requisiti e domande frequenti, vedere la [panoramica](publisher-verification-overview.md).


## <a name="mark-your-app-as-publisher-verified"></a>Contrassegnare l'app come con autore verificato
Assicurarsi di soddisfare i [prerequisiti](publisher-verification-overview.md#requirements) e quindi seguire questa procedura per contrassegnare le app come con autore verificato.  

1. Assicurarsi di aver eseguito l'accesso con l'autenticazione a più [fattori](../fundamentals/concept-fundamentals-mfa-get-started.md) a un account aziendale (Azure ad) autorizzato a apportare modifiche alle app che si desidera contrassegnare come server di pubblicazione verificato e nell'account MPN nel centro per i partner.

    - In Azure AD questo utente deve essere membro di uno dei [ruoli](../users-groups-roles/directory-assign-admin-roles.md)seguenti: Amministratore applicazione, amministratore applicazione cloud, amministratore globale. 

    - In Partner Center questo utente deve avere uno dei [ruoli](/partner-center/permissions-overview) seguenti: Amministratore MPN, Amministratore account o Amministratore globale (si tratta di un ruolo condiviso gestito in Azure AD). 

1. Passare al portale di registrazione delle app:  

1. Fare clic su un'app che si vuole contrassegnare come con autore verificato (Publisher Verified) e aprire il pannello Personalizzazione. 

1. Verificare che sia impostato il [dominio di pubblicazione](howto-configure-publisher-domain.md) dell'applicazione. 

1. Verificare che il dominio del server di pubblicazione o un [dominio personalizzato](../fundamentals/add-custom-domain.md) verificato dal DNS nel tenant corrisponda al dominio dell'indirizzo di posta elettronica usato durante il processo di verifica per l'account MPN.

1. Fare clic su **Aggiungere l'ID MPN per verificare l'autore** nella parte inferiore della pagina. 

1. Immettere l'**ID MPN**. Questo ID MPN deve corrispondere a: 

    - Un account Microsoft Partner Network valido che ha completato il processo di verifica.  

    - L'account globale del partner (PGA) per l'organizzazione. 

1. Fare clic su **Verifica e salva**. 

1. Attendere che la richiesta venga elaborata. L'operazione potrebbe richiedere alcuni minuti. 

1. Se la verifica ha esito positivo, la finestra di verifica dell'autore verrà chiusa e si tornerà al pannello Personalizzazione. Accanto al **nome visualizzato dell'autore** verificato verrà visualizzato un badge di verifica completata blu. 

1. Gli utenti che ricevono la richiesta di consenso per l'app inizieranno a visualizzare il badge subito dopo il completamento del processo di verifica, anche se per la replica in tutto il sistema potrebbe essere richiesto del tempo. 

1. Testare questa funzionalità effettuando l'accesso all'applicazione e assicurandosi che il badge di verifica completata venga visualizzato nella schermata di consenso. Se è stato eseguito l'accesso come utente che ha già concesso il consenso all'app, è possibile usare il parametro di query *prompt=consent* per forzare una richiesta di consenso. Questo parametro deve essere usato solo per i test e mai hardcoded nelle richieste dell'app.

1. Ripetere questo processo in base alle esigenze per le app aggiuntive per le quali si vuole che venga visualizzato il badge. È possibile usare Microsoft Graph per eseguire questa operazione più rapidamente in blocco e i cmdlet di PowerShell saranno presto disponibili. Per altre informazioni, vedere [Esecuzione di chiamate all'API Microsoft Graph](troubleshoot-publisher-verification.md#making-microsoft-graph-api-calls). 

La procedura è terminata. Inviare commenti e suggerimenti sul processo, i risultati o la funzionalità in generale. 

## <a name="next-steps"></a>Passaggi successivi
Se si verificano problemi, leggere le [informazioni sulla risoluzione dei problemi](troubleshoot-publisher-verification.md).
