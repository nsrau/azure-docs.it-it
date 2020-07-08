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
ms.openlocfilehash: d25a3831897ff8ad2e7dfb0c69910fee246aec21
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85477686"
---
# <a name="mark-your-app-as-publisher-verified-preview"></a>Contrassegnare l'app come con autore verificato (anteprima)

Se un'applicazione è contrassegnata come con autore verificato, significa che l'autore ha verificato la propria identità usando il proprio account MPN (Microsoft Partner Network) e ha associato questo account MPN durante la registrazione dell'applicazione. Questo articolo descrive come eseguire il processo di [verifica dell'autore (anteprima)](publisher-verification-overview.md).

## <a name="quickstart"></a>Guida introduttiva
Se si è già iscritti a Microsoft Partner Network (MPN) e sono stati soddisfatti i [prerequisiti](publisher-verification-overview.md#requirements), è possibile iniziare subito: 

1. Accedere al [portale di registrazione delle app](https://aka.ms/PublisherVerificationPreview) di anteprima.

1. Scegliere un'app e fare clic su **Personalizzazione**. 

1. Fare clic su **Aggiungere l'ID MPN per verificare l'autore** ed esaminare i requisiti elencati.

1. Immettere l'ID MPN e fare clic su **Verifica e salva**.

Per informazioni dettagliate su vantaggi specifici, requisiti e domande frequenti, vedere la [panoramica](publisher-verification-overview.md).


## <a name="mark-your-app-as-publisher-verified"></a>Contrassegnare l'app come con autore verificato
Assicurarsi di soddisfare i [prerequisiti](publisher-verification-overview.md#requirements) e quindi seguire questa procedura per contrassegnare le app come con autore verificato.  

1. Assicurarsi di aver eseguito l'accesso con un account aziendale (Azure AD) autorizzato ad apportare modifiche alle app che si vogliono contrassegnare come con autore verificato e all'account MPN nel Partner Center. 

    - In Azure AD questo utente deve essere il proprietario dell'app o avere uno dei ruoli seguenti: Amministratore applicazione, Amministratore applicazione cloud, Amministratore globale. 

    - In Partner Center questo utente deve avere uno dei ruoli seguenti: Amministratore MPN, Amministratore account o Amministratore globale (si tratta di un ruolo condiviso gestito in Azure AD). 

1. Passare alla versione di anteprima del portale di registrazione delle app:  

1. Fare clic su un'app che si vuole contrassegnare come con autore verificato (Publisher Verified) e aprire il pannello Personalizzazione. 

1. Verificare che il dominio dell'autore dell'app sia impostato in modo appropriato. Il dominio deve essere: 

    - Aggiunto al tenant di Azure AD come dominio personalizzato verificato da DNS,  

    - Corrispondere al dominio dell'indirizzo di posta elettronica usato durante il processo di verifica per l'account MPN. 

1. Fare clic su **Aggiungere l'ID MPN per verificare l'autore** nella parte inferiore della pagina. 

1. Immettere l'**ID MPN**. Questo ID MPN deve corrispondere a: 

    - Un account Microsoft Partner Network valido che ha completato il processo di verifica.  

    - L'account globale del partner (PGA) per l'organizzazione. 

1. Fare clic su **Verifica e salva**. 

1. Attendere che la richiesta venga elaborata. L'operazione potrebbe richiedere alcuni minuti. 

1. Se la verifica ha esito positivo, la finestra di verifica dell'autore verrà chiusa e si tornerà al pannello Personalizzazione. Accanto al **nome visualizzato dell'autore** verificato verrà visualizzato un badge di verifica completata blu. 

1. Gli utenti che ricevono la richiesta di consenso per l'app inizieranno a visualizzare il badge subito dopo il completamento del processo di verifica, anche se per la replica in tutto il sistema potrebbe essere richiesto del tempo. 

1. Testare questa funzionalità effettuando l'accesso all'applicazione e assicurandosi che il badge di verifica completata venga visualizzato nella schermata di consenso. Se è stato eseguito l'accesso come utente che ha già concesso il consenso all'app, è possibile usare il parametro di query *prompt=consent* per forzare una richiesta di consenso. 

1. Ripetere questo processo in base alle esigenze per le app aggiuntive per le quali si vuole che venga visualizzato il badge. È possibile usare Microsoft Graph per eseguire questa operazione più rapidamente in blocco e i cmdlet di PowerShell saranno presto disponibili. Per altre informazioni, vedere [Esecuzione di chiamate all'API Microsoft Graph](troubleshoot-publisher-verification.md#making-microsoft-graph-api-calls). 

La procedura è terminata. Inviare commenti e suggerimenti sul processo, i risultati o la funzionalità in generale. 

## <a name="next-steps"></a>Passaggi successivi
Se si verificano problemi, leggere le [informazioni sulla risoluzione dei problemi](troubleshoot-publisher-verification.md).