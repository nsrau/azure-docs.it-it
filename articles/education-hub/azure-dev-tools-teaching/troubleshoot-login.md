---
title: Risolvere l'errore di accesso per gli strumenti di sviluppo di Azure per l'insegnamento
description: Questo articolo descrive le azioni che uno studente deve eseguire se riceve un messaggio di errore durante l'accesso ad Azure Dev Tools per l'insegnamento.
author: rymend
ms.author: rymend
ms.topic: troubleshooting
ms.service: azure-education
ms.subservice: education-hub
ms.date: 06/30/2020
ms.openlocfilehash: 276ab2cc1d79235c00ce1bb50ff9b648bb1de6ea
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87097678"
---
# <a name="troubleshooting-student-login-issues"></a>Risoluzione dei problemi di accesso degli studenti
Per accedere agli strumenti di sviluppo di Azure per l'insegnamento è necessario che un utente disponga di un account Microsoft (MSA). Gli studenti verranno automaticamente indirizzati per creare un MSA se il proprio account non è già un MSA o è collegato a un MSA. Se il dominio è associato a Active Directory, tutti gli account in tale dominio sono già considerati MSA.

Se uno studente tenta di accedere e riceve il messaggio di errore seguente, usare una delle soluzioni descritte di seguito.

:::image type="content" source="media/troubleshoot-login/error-login-message.png" alt-text="Messaggio di errore di accesso." border="false":::

Esistono due soluzioni: creare una nuova account Microsoft o usare una account Microsoft esistente.

## <a name="create-a-new-microsoft-account"></a>Crea una nuova account Microsoft
### <a name="use-a-university-email-address"></a>Usa un indirizzo di posta elettronica dell'Università
Se si esegue l'accesso con un indirizzo di posta elettronica dell'Università, ad esempio, `John.Smith@university.edu` è necessario creare un account Microsoft con tale indirizzo di posta elettronica. La creazione di un account è gratuita e richiede pochi minuti. La presenza di un account Microsoft consentirà di accedere automaticamente a tutti i prodotti Microsoft con un singolo nome utente e una password.

### <a name="use-a-personal-email-address"></a>Usa un indirizzo di posta elettronica personale
Se si esegue l'accesso con un indirizzo di posta elettronica personale, ad esempio, `John.Smith@email.com` ma si ha anche un indirizzo di posta elettronica dell'Università, provare a usare l'indirizzo di posta elettronica dell'Università. Se in precedenza è stato effettuato l'accesso al negozio online dell'Istituto con un indirizzo di posta elettronica personale o se non si dispone di un indirizzo di posta elettronica dell'Università, è necessario creare o collegare un account Microsoft con l'indirizzo di posta elettronica personale.

## <a name="use-an-existing-microsoft-account"></a>Usa un account Microsoft esistente
Se uno studente ha un account Microsoft esistente (ad esempio, Xbox), potrà connettere tale account a un account di Azure Dev Tools.

1. Passare a https://account.microsoft.com.
1. Accedere con le credenziali di account Microsoft.
1. Selezionare le **informazioni** dal menu superiore della barra multifunzione.

1. Fare clic su **Gestisci come si accede a Microsoft**. Verrà richiesto di verificare la propria identità. Verrà inviato un messaggio di posta elettronica con un codice di sicurezza.

    :::image type="content" source="media/troubleshoot-accounts/manage-sign-in.png" alt-text="Gestire l'accesso." border="false":::

1. Immettere il codice di sicurezza inviato tramite posta elettronica.

    :::image type="content" source="media/troubleshoot-accounts/sign-in-enter-code.png" alt-text="Immettere il codice di accesso." border="false":::

1. Fare clic su **Aggiungi messaggio di posta elettronica** all'account e immettere l'indirizzo di posta elettronica dell'Università.
Al successivo accesso, è possibile usare l'indirizzo di posta elettronica dell'Università per accedere agli strumenti di sviluppo di Azure per l'insegnamento.

    :::image type="content" source="media/troubleshoot-accounts/manage-sign-in-add-email.png" alt-text="Consente di gestire la modalità di accesso a Microsoft." border="false":::

## <a name="next-steps"></a>Passaggi successivi
- [Domande frequenti](program-faq.md)

- [Opzioni di supporto](program-support.md)
