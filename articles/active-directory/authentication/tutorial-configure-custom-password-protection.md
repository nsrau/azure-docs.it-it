---
title: Configurare elenchi di password di protezione personalizzate per Azure Active Directory
description: Questa esercitazione spiega come configurare elenchi di password personalizzate escluse per Azure Active Directory per limitare l'uso di parole comuni nell'ambiente corrente.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: iainfou
author: iainfoulds
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 642082b3fe23e0d007e21409062fe8e777728cc3
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86518540"
---
# <a name="tutorial-configure-custom-banned-passwords-for-azure-active-directory-password-protection"></a>Esercitazione: Configurare password personalizzate escluse per password di protezione di Azure Active Directory

Gli utenti creano spesso password che usano parole locali comuni, ad esempio il nome di una scuola, una squadra sportiva o un personaggio famoso. Queste password sono facili da indovinare e vulnerabili ad attacchi basati su dizionario. Per applicare password complesse all'interno dell'organizzazione, usare l'elenco di password personalizzate escluse di Azure Active Directory (Azure AD), che consente di aggiungere stringhe specifiche da valutare e bloccare. In questo modo una richiesta di modifica della password non verrà eseguita, se la password è presente nell'elenco di password personalizzate escluse.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Abilitare password personalizzate escluse
> * Configurare l'elenco personalizzato password escluse
> * Testare le modifiche della password con una password vietata

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, sono necessari i privilegi e le risorse seguenti:

* Un tenant di Azure AD funzionante, con almeno una licenza di valutazione abilitata.
    * Se necessario, [crearne uno gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un account con privilegi di *amministratore globale*.
* Un utente non amministratore con una password conosciuta, ad esempio *testuser*. Questo account verrà usato per testare un evento di modifica della password in questa esercitazione.
    * Se è necessario creare un utente, vedere [Avvio rapido: Aggiungere nuovi utenti ad Azure Active Directory](../fundamentals/add-users-azure-active-directory.md).
    * Per testare la modifica della password con una password esclusa, è necessario che il tenant di Azure AD venga [configurato per la reimpostazione delle password self-service](tutorial-enable-sspr.md).

## <a name="what-are-banned-password-lists"></a>Che cosa sono gli elenchi di password escluse?

Azure AD include un elenco globale di password escluse. Il contenuto dell'elenco globale di password escluse non si basa su un'origine dati esterna, ma sui risultati restituiti continuamente dalla telemetria e dall'analisi della sicurezza di Azure AD. Quando un utente o un amministratore prova a modificare o reimpostare le proprie credenziali, la password specificata viene confrontata con l'elenco di password escluse. La richiesta di modifica della password non verrà eseguita se la password è presente nell'elenco globale di password escluse. Non è possibile modificare questo elenco globale di password escluse.

Per una maggiore flessibilità sui termini consentiti nelle password, è anche possibile definire un elenco di password personalizzate escluse. L'uso congiunto dell'elenco di password personalizzate escluse e dell'elenco globale di password escluse consente di applicare password complesse nell'organizzazione. I termini specifici dell'organizzazione possono essere aggiunti all'elenco di password personalizzate escluse, come negli esempi seguenti:

* Nomi di marchio
* Nomi di prodotto
* Località, ad esempio la sede centrale aziendale
* Termini interni specifici della società
* Abbreviazioni con un significato specifico per la società

Quando un utente prova a reimpostare una password specificando un termine incluso nell'elenco globale o personalizzato delle password escluse, viene visualizzato uno dei messaggi di errore seguenti:

* *La password contiene una parola, una frase o uno schema che la rende facile da indovinare. Riprovare con una password diversa.*
* *Non è possibile usare la password perché contiene parole o caratteri bloccati dall'amministratore. Riprovare con una password diversa.*

L'elenco di password personalizzate escluse è limitato a un massimo di 1000 termini. Non è quindi pensato per bloccare elenchi contenenti un numero elevato di password. Per sfruttare al massimo i vantaggi dell'elenco di password personalizzate escluse, vedere i [concetti relativi all'elenco di password personalizzate escluse](concept-password-ban-bad.md#custom-banned-password-list) e la [panoramica degli algoritmi di valutazione delle password](concept-password-ban-bad.md#how-are-passwords-evaluated).

## <a name="configure-custom-banned-passwords"></a>Configurare le password personalizzate escluse

Verrà abilitato l'elenco di password personalizzate escluse e verranno aggiunte alcune voci. È possibile aggiungere altre voci all'elenco di password personalizzate escluse in qualsiasi momento.

Per abilitare l'elenco di password personalizzate escluse e aggiungervi voci, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com) usando un account con autorizzazioni di *amministratore globale*.
1. Cercare e selezionare **Azure Active Directory**, quindi scegliere **Sicurezza** dal menu a sinistra.
1. Nell'intestazione di menu **Gestisci** selezionare **Metodi di autenticazione** e quindi **Protezione con password**.
1. Impostare l'opzione **Imponi elenco personalizzato** su *Sì*.
1. Aggiungere stringhe all'**Elenco password personalizzate escluse**, una stringa per riga. Per l'elenco di password personalizzate escluse valgono le considerazioni e le limitazioni seguenti:

    * L'elenco di password personalizzate escluse può contenere fino a 1000 termini.
    * L'elenco personalizzato di password escluse fa distinzione tra maiuscole e minuscole.
    * L'elenco di password personalizzate escluse tiene conto della sostituzione dei caratteri comuni, ad esempio "o" e "0" oppure "a" e "\@".
    * La lunghezza minima delle stringhe è quattro caratteri, la massima è 16 caratteri.

    Specificare le password personalizzate da escludere, come illustrato nell'esempio seguente.

    [![](media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords-cropped.png "Modify the custom banned password list under Authentication Methods in the Azure portal")](media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords.png#lightbox)

1. Lasciare l'opzione **Abilita la password di protezione in Windows Server Active Directory** impostata su *No*.
1. Per abilitare le password personalizzate escluse e le voci, selezionare **Salva**.

Potrebbero essere necessarie diverse ore prima che l'aggiornamento dell'elenco personalizzato di password escluse venga applicato.

Per un ambiente ibrido, è anche possibile [distribuire la password di protezione di Azure AD in un ambiente locale](howto-password-ban-bad-on-premises-deploy.md). Per le richieste di modifica delle password locali e cloud vengono usati Gli stessi elenchi globali e personalizzati di password escluse.

## <a name="test-custom-banned-password-list"></a>Testare l'elenco di password personalizzate escluse

Per vedere come funziona l'elenco di password personalizzate escluse, provare a cambiare la password specificando una variante di una aggiunta nella sezione precedente. Quando Azure AD prova a elaborare la modifica della password, la password viene confrontata con una voce nell'elenco di password personalizzate escluse. Viene quindi visualizzato un errore.

> [!NOTE]
> Prima che un utente possa reimpostare la propria password nel portale basato sul Web, è necessario che il tenant di Azure AD venga [configurato per la reimpostazione delle password self-service](tutorial-enable-sspr.md). Se necessario, l'utente può quindi [eseguire la registrazione per SSPR all'indirizzo https://aka.ms/ssprsetup](https://aka.ms/ssprsetup).

1. Passare alla pagina **App personali** all'indirizzo [https://myapps.microsoft.com](https://myapps.microsoft.com).
1. Nell'angolo in alto a destra selezionare il proprio nome, quindi scegliere **Profilo** dal menu a discesa.

    ![Selezionare un profilo](media/tutorial-configure-custom-password-protection/myapps-profile.png)

1. Nella pagina **Profilo** selezionare **Cambia password**.
1. Nella pagina **Cambia password** immettere la password esistente (precedente). Immettere e confermare una nuova password inclusa nell'elenco di password personalizzate escluse definito nella sezione precedente e quindi selezionare **Invia**.
1. Viene restituito un messaggio di errore che indica che la password è stata bloccata dall'amministratore, come illustrato nell'esempio seguente:

    ![Messaggio di errore visualizzato quando si prova a usare una password che fa parte dell'elenco di password personalizzate escluse](media/tutorial-configure-custom-password-protection/password-change-error.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si vuole più usare l'elenco di password personalizzate escluse configurato durante questa esercitazione, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Cercare e selezionare **Azure Active Directory**, quindi scegliere **Sicurezza** dal menu a sinistra.
1. Nell'intestazione di menu **Gestisci** selezionare **Metodi di autenticazione** e quindi **Protezione con password**.
1. Impostare l'opzione **Imponi elenco personalizzato** su *No*.
1. Per aggiornare la configurazione delle password personalizzate escluse, selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati abilitati e configurati elenchi di password di protezione personalizzate per Azure AD. Si è appreso come:

> [!div class="checklist"]
> * Abilitare password personalizzate escluse
> * Configurare l'elenco personalizzato password escluse
> * Testare le modifiche della password con una password vietata

> [!div class="nextstepaction"]
> [Abilitare il servizio Azure Multi-Factor Authentication basato sui rischi](tutorial-mfa-applications.md)
