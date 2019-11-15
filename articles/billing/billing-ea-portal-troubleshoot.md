---
title: Risolvere i problemi di accesso ad Azure EA Portal
description: Questo articolo descrive alcuni problemi comuni che possono verificarsi con un contratto Azure Enterprise Agreement (EA) in Azure EA Portal.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/04/2019
ms.topic: troubleshooting
ms.service: billing
manager: boalcsva
ms.openlocfilehash: 1b0be54eea063399cc9ed2c3005808d5189e2663
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888350"
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
- Se si riceve il messaggio di errore _L'utente non è valido_ quando si usa un account Microsoft, è possibile che si disponga di più account Microsoft. Quello con cui si sta provando ad accedere non è l'indirizzo di posta elettronica principale.
Oppure, il messaggio di errore _L'utente non è valido_ può essere visualizzato perché quando l'utente è stato aggiunto alla registrazione è stato usato il tipo di account errato, ad esempio un account aziendale o dell'istituto di istruzione invece di un account Microsoft. In questo esempio chiedere a un altro amministratore EA di aggiungere l'account corretto oppure contattare il [supporto tecnico](https://support.microsoft.com/supportforbusiness/productselection?sapId=cf791efa-485b-95a3-6fad-3daf9cd4027c).
  - Se è necessario controllare l'alias principale, passare a [https://account.live.com](https://account.live.com). Fare clic su **Le tue info** e quindi su **Gestisci il modo in cui accedi a Microsoft**. Seguire le istruzioni per verificare un indirizzo di posta elettronica alternativo e ottenere un codice per accedere a informazioni riservate. Immettere il codice di sicurezza. Selezionare **Configura in seguito** se non si vuole configurare l'autenticazione a due fattori.
  - Verrà visualizzata la pagina **Gestisci la modalità di accesso a Microsoft** con gli alias dell'account disponibili. Verificare che l'alias principale sia quello che si usa per accedere ad Azure EA Portal. In caso contrario, è possibile impostarlo come alias principale. In alternativa, è possibile usare l'alias principale per Azure EA Portal.

## <a name="no-activation-email-received"></a>Nessun messaggio di posta elettronica di attivazione ricevuto

Azure EA Portal invia un messaggio di posta elettronica di attivazione dall'indirizzo *waep@microsoft.com* . Se questo messaggio non è stato ricevuto, controllare la cartella di posta indesiderata. Viene inviato con l'oggetto _Invitation to View/Manage the Microsoft Azure service_subject_ (Invito a visualizzare/gestire il servizio Microsoft Azure) a ogni amministratore EA appena aggiunto.

Se si è certi di avere il ruolo di amministratore EA, non è necessario aspettare di ricevere il messaggio di posta elettronica di attivazione per accedere ad Azure EA Portal. Al contrario, è possibile passare a https://ea.azure.com e accedere con il proprio indirizzo di posta elettronica (account aziendale o dell'istituto di istruzione oppure account Microsoft) e la password.

## <a name="azure-ea-activation-faq"></a>Domande frequenti sull'attivazione di Azure EA

Questa sezione dell'articolo illustra le soluzioni ai problemi comuni relativi all'attivazione di Azure EA.

### <a name="i-would-like-to-add-a-new-ea-administrator-to-my-enrollment"></a>Si vuole aggiungere un nuovo amministratore EA alla propria registrazione

Per aggiungere un nuovo amministratore dell'organizzazione è necessario essere già un amministratore. Se si ha il ruolo di amministratore EA, accedere a EA Portal, fare clic su **Gestisci** e quindi su **+ Aggiungi amministratore** nell'angolo in alto a destra per aggiungere un nuovo amministratore EA. Assicurarsi di conoscere l'indirizzo di posta elettronica e il metodo di accesso preferito dell'utente da aggiungere, ad esempio tramite autenticazione con account aziendale o dell'istituto di istruzione o Microsoft Live ID.

Se non si ha il ruolo di amministratore EA, rivolgersi agli amministratori EA della propria organizzazione per richiedere di essere aggiunti alla registrazione. Quando questa operazione sarà completata, si riceverà un messaggio di posta elettronica di attivazione.

Se tuttavia gli amministratori EA non sono in grado di eseguire questa operazione, Microsoft potrà aggiungere un amministratore per loro conto se si forniscono le informazioni seguenti:
- Numero di registrazione
- Indirizzo di posta elettronica da aggiungere e tipo di autenticazione (account aziendale o dell'istituto di istruzione o account Microsoft)
- Approvazione tramite posta elettronica dell'amministratore EA

Una volta raccolte tutte le informazioni necessarie, inviare una richiesta all'indirizzo [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport).

### <a name="i-would-like-to-update-the-first-ea-admin-on-the-enrollment"></a>Si vuole aggiornare il primo amministratore EA nella registrazione

Il primo amministratore EA può essere aggiornato in Volume Licensing Service Center (VLSC) modificando il contatto per le comunicazioni e l'amministratore online nel portale. Per l'aggiornamento in EA Portal saranno necessarie circa 24 ore. Al termine dell'aggiornamento, il nuovo amministratore EA riceverà un messaggio di posta elettronica di conferma dell'attivazione.

Se non si ha accesso al portale VLSC o se l'amministratore EA iniziale non è più in grado di gestire la registrazione e non ha accesso a EA Portal, inviare una richiesta all'indirizzo [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport) per richiedere un aggiornamento specificando le informazioni seguenti:
- Numero di registrazione
- Indirizzo di posta elettronica da aggiungere e tipo di autenticazione (account aziendale o dell'istituto di istruzione o account Microsoft)
- Motivo della modifica dell'amministratore EA iniziale
- Approvazione tramite posta elettronica dell'amministratore EA iniziale

### <a name="my-current-ea-admin-is-no-longer-with-the-company"></a>L'amministratore EA corrente non lavora più nell'organizzazione

Una registrazione EA può avere più amministratori EA. È possibile rivolgersi a un altro amministratore EA per richiedere di aggiungere un nuovo amministratore EA, proprietario di account o amministratore di reparto. Se tuttavia non si riesce a capire esattamente chi sia l'amministratore EA dell'organizzazione o non sono definiti altri amministratori EA per la registrazione, rivolgersi a Microsoft specificando le informazioni seguenti:
- Numero di registrazione
- Indirizzo di posta elettronica da aggiungere e tipo di autenticazione (account aziendale o dell'istituto di istruzione o account Microsoft)
- Informazioni da cui risulta che l'amministratore EA corrente non lavora più nell'organizzazione

Se sono definiti altri amministratori EA per la registrazione, Microsoft contatterà tali amministratori per richiedere l'approvazione delle modifiche ai dati amministrativi nella registrazione.

### <a name="my-enrollment-is-showing-in-pending-status-how-do-i-activate-my-enrollment"></a>Lo stato della registrazione risulta in sospeso. Come è possibile attivare la registrazione?

Lo stato di una registrazione risulta in sospeso se l'amministratore EA iniziale non ha mai eseguito l'accesso alla registrazione. Se si è l'amministratore EA della registrazione, accedere ad Azure EA Portal. Nella pagina visualizzata, con tutti i numeri di registrazione, la registrazione in sospeso potrebbe non essere visibile. Deselezionare la casella "attivo" nell'angolo in alto a destra della schermata di EA Portal. In questo modo, la registrazione risulterà in sospeso. Fare clic sulla registrazione per accedere alle informazioni e, una volta raggiunta la pagina di gestione della registrazione, lo stato verrà aggiornato da "In sospeso" ad "Attivo".

### <a name="why-is-my-account-stuck-in-pending-status"></a>Perché l'account rimane bloccato nello stato in sospeso?

Quando vengono aggiunti nuovi proprietari di account a una registrazione per la prima volta, il relativo stato risulta "In sospeso". Non appena riceve il messaggio di posta elettronica di benvenuto per l'attivazione, il proprietario può accedere per attivare il proprio account. Per effetto di questa operazione, lo stato dell'account passerà da "In sospeso" ad "Attivo".

### <a name="i-received-an-error-when-signing-in-to-azure-ea-portal"></a>Si è ricevuto un errore durante l'accesso ad Azure EA Portal

La visualizzazione di un messaggio di errore durante l'accesso ad Azure EA Portal può essere dovuta a diverse cause. Per risolvere il problema, seguire questa procedura:

 1. Assicurarsi di usare l'URL di EA Portal corretto, ovvero [https://ea.azure.com](https://ea.azure.com).
 1. Verificare se l'accesso ad Azure EA Portal è stato aggiunto come account aziendale o dell'istituto di istruzione o come Microsoft Live ID. Se si usa l'account aziendale, immettere l'indirizzo di posta elettronica e la password dell'azienda. Se si usa Microsoft Live ID, immettere il proprio indirizzo di posta elettronica e la propria password di Microsoft Live ID. Se si è dimenticata la password di Microsoft Live ID, è necessario reimpostarla all'indirizzo [https://account.live.com/password/reset](https://account.live.com/password/reset).
 1. Per eseguire l'accesso è consigliabile usare un browser personale in modo da evitare che vengano mantenuti cookie o dati della cache di sessioni precedenti o esistenti. Cancellare il contenuto della cache e usare la finestra di esplorazione in modalità privata o in incognito per aprire [https://ea.azure.com](https://ea.azure.com).
 1. Se viene restituito un errore di utente non valido quando si usa una account Microsoft, è possibile che si abbiano più account Microsoft e che quello usato per accedere non sia l'alias principale. Per controllare l'alias principale, andare alla pagina account.live.com:
    - Passare a "Le tue info" > "Gestisci il tuo indirizzo e-mail o il numero di telefono di accesso".
    - Seguire le istruzioni visualizzate per verificare un indirizzo e-mail alternativo e ottenere un codice per accedere a informazioni riservate.
    - Immettere il codice di sicurezza.
    - Se si preferisce configurare l'autenticazione in due passaggi in un secondo momento, selezionare "Configura in seguito".
    - Si passerà alla pagina "Gestisci gli alias dell'account" in cui sono visualizzati gli alias dell'account disponibili. Verificare che l'alias principale sia quello usato per accedere ad Azure EA Portal. In caso contrario, è possibile impostarlo come alias principale o usare in alternativa l'alias principale per EA Portal.

Se la procedura precedente per la risoluzione dei problemi non ha avuto esito positivo, inviare una richiesta all'indirizzo [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport) specificando le informazioni seguenti:
- Browser e versione usati.
- Screenshot del messaggio di errore.
- URL della pagina che mostra l'errore.  
- Data, ora e fuso orario in cui si verifica l'errore.
- Può inoltre utile fornire un file di log, se si riesce ad acquisirlo. Ecco i passaggi per acquisire una traccia di rete usando le informazioni seguenti:
  1. Aprire Internet Explorer.
  1. Premere F12. In Internet Explorer verrà aperto un riquadro.
  1. Selezionare la scheda **Rete**.
  1. Fare clic su **Avvia cattura**.
  1. Eseguire l'azione che causa l'errore.
  1. Non appena viene restituito l'errore, fare clic su **Interrompi cattura**.
  1. Salvare il file e includere le informazioni nella richiesta di supporto.
  1. Assicurarsi di specificare il numero di registrazione e l'indirizzo di posta elettronica all'interno della richiesta di supporto.

### <a name="what-is-the-difference-between-a-workschool-account-and-microsoft-account"></a>Qual è la differenza tra account aziendale o dell'istituto di istruzione e account Microsoft?

**Account Microsoft**: è l'account che è stato associato a Live ID su [https://signup.live.com](https://signup.live.com).

**Account aziendale o dell'istituto di istruzione:** è l'account disponibile solo per le organizzazioni che hanno configurato Active Directory con la federazione nel cloud e gestiscono tutti gli account in un singolo tenant. Se l'istanza interna di Active Directory dell'organizzazione è federata nel cloud, è possibile aggiungere utenti con tipo di autenticazione aziendale o dell'istituto di istruzione.

  Dal mese di settembre 2016, Microsoft non consente più la registrazione di indirizzi di posta elettronica aziendali o dell'istituto di istruzione come account Microsoft. Per altri dettagli, fare riferimento ai materiali seguenti: [https://blogs.technet.microsoft.com/enterprisemobility/2016/09/15/cleaning-up-the-azure-ad-and-microsoft-account-overlap/](https://blogs.technet.microsoft.com/enterprisemobility/2016/09/15/cleaning-up-the-azure-ad-and-microsoft-account-overlap/).

  Se l'organizzazione non è federata nel cloud, non sarà possibile usare l'indirizzo di posta elettronica aziendale o dell'istituto di istruzione. In alternativa, registrare o creare un nuovo indirizzo di posta elettronica e registrarlo come account Microsoft.

### <a name="i-forgot-my-password-to-azure-ea-portal"></a>Si è dimenticata la password per Azure EA Portal

Se si è dimenticata la password di Microsoft Live ID, è necessario reimpostarla all'indirizzo [https://account.live.com/password/reset](https://account.live.com/password/reset).

Se si è dimenticata la password aziendale, contattare l'amministratore IT della propria azienda.

### <a name="i-have-a-valid-work-or-school-account-but-i-cant-add-it-to-the-ea-portal"></a>Si dispone di un account aziendale o dell'istituto di istruzione valido ma non si riesce ad aggiungerlo in EA Portal

Se si dispone di un account aziendale o dell'istituto di istruzione in un tenant diverso, modificare il livello di autorizzazione nella pagina dei dettagli di registrazione impostando "Work or School Account Cross Tenant" (Account aziendale o dell'istituto di istruzione in più tenant). In questo modo sarà possibile aggiungere l'account.

## <a name="next-steps"></a>Passaggi successivi

- È consigliabile che gli amministratori di Azure EA Portal leggano [Amministrazione di Azure EA Portal](billing-ea-portal-administration.md) per informazioni sulle comuni attività amministrative.
