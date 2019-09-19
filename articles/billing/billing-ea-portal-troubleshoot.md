---
title: Risolvere i problemi di accesso ad Azure EA Portal
description: Questo articolo descrive alcuni problemi comuni che possono verificarsi con un contratto Azure Enterprise Agreement (EA) in Azure EA Portal.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/09/2019
ms.topic: troubleshooting
ms.service: billing
manager: boalcsva
ms.openlocfilehash: a2214ef0dd3b7c4f706ebbb529b6c44e956f9242
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70900880"
---
# <a name="troubleshoot-azure-ea-portal-access"></a>Risolvere i problemi di accesso ad Azure EA Portal

Questo articolo descrive alcuni problemi comuni che possono verificarsi con un contratto Azure Enterprise Agreement (EA). Azure EA Portal consente di gestire gli utenti e i costi del contratto Enterprise Agreement. Questi problemi potrebbero verificarsi durante la configurazione o l'aggiornamento dell'accesso ad Azure EA Portal.

## <a name="issues-adding-an-admin-to-an-enrollment"></a>Problemi durante l'aggiunta di un amministratore a una registrazione

Esistono diversi tipi di livelli di autenticazione per le registrazioni Enterprise. Se i livelli di autenticazione vengono applicati in modo non corretto, è possibile che si verifichino problemi quando si prova ad accedere ad Azure EA Portal.

Azure EA Portal si usa per concedere l'accesso agli utenti con diversi livelli di autenticazione. Un amministratore dell'organizzazione può aggiornare il livello di autenticazione per soddisfare i requisiti di sicurezza aziendali.

### <a name="authentication-level-types"></a>Tipi di livelli di autenticazione

- Solo account Microsoft: per le organizzazioni che vogliono usare, creare e gestire gli utenti tramite account Microsoft.
- Account aziendale o dell'istituto di istruzione: per le organizzazioni che hanno configurato Active Directory con la federazione nel cloud e hanno gli account in un singolo tenant.
- Account aziendale o dell'istituto di istruzione tra più tenant: per le organizzazioni che hanno configurato Active Directory con la federazione nel cloud e hanno account in più tenant.
- Account misto: consente di aggiungere utenti con account Microsoft e/o con un account aziendale o dell'istituto di istruzione.

Il primo account aziendale o dell'istituto di istruzione aggiunto alla registrazione determina il dominio _predefinito_ o _master_. Per aggiungere un account aziendale o dell'istituto di istruzione con un altro tenant, è necessario impostare il livello di autenticazione sull'autenticazione tra tenant nella registrazione.

Per aggiornare il livello di autenticazione:

1. Accedere ad Azure EA Portal come amministratore dell'organizzazione.
2. Fare clic su **Gestisci** nel riquadro di spostamento sinistro.
3. Fare clic sulla scheda **Registrazione**.
4. In **Dettagli della registrazione** selezionare **Livello di autenticazione**.
5. Fare clic sul simbolo della matita.
6. Fare clic su **Save**.

![Esempio che illustra i livelli di autenticazione ](./media/billing-ea-portal-troubleshoot/create-ea-authentication-level-types.png)

Gli account Microsoft devono essere associati a un ID creato nella pagina [https://signup.live.com](https://signup.live.com/).

Gli account aziendali o dell'istituto di istruzione sono disponibili per le organizzazioni che hanno configurato Active Directory con la federazione e in cui tutti gli account si trovano in un singolo tenant. Se l'istanza interna di Active Directory della società è federata, è possibile aggiungere utenti con autenticazione federata aziendale o dell'istituto di istruzione.

Se l'organizzazione non usa la federazione di Active Directory, non è possibile usare l'indirizzo di posta elettronica aziendale o dell'istituto di istruzione. In alternativa, registrare o creare un nuovo indirizzo di posta elettronica e registrarlo come account Microsoft.

## <a name="unable-to-access-the-azure-ea-portal"></a>Non è possibile accedere ad Azure EA Portal

Se si riceve un messaggio di errore quando si prova ad accedere ad Azure EA Portal, seguire questa procedura per la risoluzione dei problemi:

- Assicurarsi di usare l'URL corretto di Azure EA Portal, ovvero https://ea.azure.com.
- Determinare se l'accesso ad Azure EA Portal è stato aggiunto come account aziendale o dell'istituto di istruzione o come account Microsoft.
  - Se si usa l'account aziendale, immettere l'indirizzo di posta elettronica aziendale e la password. La password viene fornita dall'organizzazione. È possibile rivolgersi al reparto IT per informazioni su come reimpostare la password in caso di problemi.
  - Se si usa un account Microsoft, immettere l'indirizzo di posta elettronica e la password corrispondenti. Se si dimentica la password dell'account Microsoft, è possibile reimpostarla all'indirizzo [https://account.live.com/password/reset](https://account.live.com/password/reset).
- Per accedere, usare una sessione privata o in incognito del browser, in modo che non vengano conservati cookie o informazioni memorizzate nella cache da sessioni precedenti o esistenti. Cancellare la cache del browser e usare una finestra privata o in incognito per aprire https://ea.azure.com.
- Se si riceve il messaggio di errore _L'utente non è valido_ quando si usa un account Microsoft, è possibile che si abbiano più account Microsoft. Quello con cui si sta provando ad accedere non è l'indirizzo di posta elettronica principale.
Oppure, il messaggio di errore _L'utente non è valido_ può essere visualizzato perché quando l'utente è stato aggiunto alla registrazione è stato usato il tipo di account errato, ad esempio un account aziendale o dell'istituto di istruzione invece di un account Microsoft. In questo esempio chiedere a un altro amministratore EA di aggiungere l'account corretto oppure contattare il [supporto tecnico](https://support.microsoft.com/supportforbusiness/productselection?sapId=cf791efa-485b-95a3-6fad-3daf9cd4027c).

## <a name="no-activation-email-received"></a>Nessun messaggio di posta elettronica di attivazione ricevuto

Azure EA Portal invia un messaggio di posta elettronica di attivazione dall'indirizzo *waep@microsoft.com* . Se questo messaggio non è stato ricevuto, controllare la cartella di posta indesiderata. Viene inviato con l'oggetto _Invitation to View/Manage the Microsoft Azure service_subject_ (Invito a visualizzare/gestire il servizio Microsoft Azure) a ogni amministratore EA appena aggiunto.

Se si è certi di avere il ruolo di amministratore EA, non è necessario aspettare di ricevere il messaggio di posta elettronica di attivazione per accedere ad Azure EA Portal. Al contrario, è possibile passare a https://ea.azure.com e accedere con il proprio indirizzo di posta elettronica (account aziendale o dell'istituto di istruzione oppure account Microsoft) e la password.

Se è necessario controllare l'alias principale, passare a [https://account.live.com](https://account.live.com). Quindi, fare clic su **Le tue info** e quindi su **Gestisci il modo in cui accedi a Microsoft**. Seguire le istruzioni per verificare un indirizzo di posta elettronica alternativo e ottenere un codice per accedere a informazioni riservate. Immettere il codice di sicurezza. Selezionare **Configura in seguito** se non si vuole configurare l'autenticazione a due fattori.

Verrà visualizzata la pagina **Gestisci il modo in cui accedi a Microsoft** con gli alias dell'account disponibili. Verificare che l'alias principale sia quello che si usa per accedere ad Azure EA Portal. In caso negativo, è possibile impostarlo come alias principale. In alternativa, è possibile usare l'alias principale per Azure EA Portal.

## <a name="next-steps"></a>Passaggi successivi

- È consigliabile che gli amministratori di Azure EA Portal leggano [Amministrazione di Azure EA Portal](billing-ea-portal-administration.md) per informazioni sulle comuni attività amministrative.
