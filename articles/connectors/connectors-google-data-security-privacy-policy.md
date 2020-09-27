---
title: Sicurezza dei dati e criteri di privacy per i connettori Google
description: Informazioni sull'effetto che i criteri di sicurezza e privacy di Google hanno sui connettori Google, ad esempio Gmail, nelle app per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: conceptual
ms.date: 06/05/2020
ms.openlocfilehash: 2a5204be638f108b40e431b148c9cb97788c4a52
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2020
ms.locfileid: "91400758"
---
# <a name="data-security-and-privacy-policies-for-google-connectors-in-azure-logic-apps"></a>Sicurezza dei dati e criteri di privacy per i connettori Google in app per la logica di Azure

A partire dal **1 ° maggio 2020**, le modifiche dovute alla [sicurezza dei dati e ai criteri di privacy](https://www.blog.google/technology/safety-security/project-strobe/) di Google possono influenzare i flussi di lavoro delle app per la logica che usano il [connettore Gmail](/connectors/gmail/). Se le app per la logica usano il connettore Gmail con un account utente Gmail, ovvero un indirizzo di posta elettronica che termina con @gmail.com o @googlemail.com , le app per la logica possono usare solo specifici [trigger, azioni e connettori approvati da Google](#approved-connectors).

> [!NOTE]
> Se le app per la logica usano il connettore Gmail con un account aziendale G-Suite (indirizzo di posta elettronica con un dominio personalizzato), le app per la logica non sono interessate e non hanno restrizioni sull'uso del connettore gmail.

## <a name="affected-logic-apps"></a>App per la logica interessate

Se sono presenti app per la logica che usano il connettore Gmail, si riceverà un messaggio di posta elettronica sulle app per la logica potenzialmente interessate. Tuttavia, a partire dal **15 giugno 2020**, eventuali flussi di lavoro non conformi verranno disabilitati. È possibile eseguire una delle operazioni seguenti:

* Aggiornare le app per la logica interessate [attenendosi alla procedura descritta in questo argomento](#update-affected-workflows). È necessario creare un'app client Google, che fornisce un ID client e un segreto client da usare per l'autenticazione nel trigger o nell'azione gmail.

* Aggiornare le app per la logica interessate in modo che usino solo i [connettori approvati da Google](#approved-connectors) prima di abilitare di nuovo le app per la logica disabilitate.

<a name="approved-connectors"></a>

## <a name="google-approved-connectors"></a>Connettori approvati da Google

Con questi criteri, quando si usa un account del consumer Gmail, è possibile usare il connettore Gmail solo con specifici servizi approvati da Google, soggetti a modifiche. I team di progettazione continuano a collaborare con Google per aggiungere altri servizi a questo elenco. Per il momento, di seguito sono riportati i trigger, le azioni e i connettori approvati da Google che è possibile usare nello stesso flusso di lavoro dell'app per la logica con il connettore Gmail quando si usa un account utente Gmail:

* Trigger e azioni predefiniti per le app per la logica: batch, controllo, operazioni dati, data e ora, file flat, liquido, richiesta, pianificazione, variabili e XML

  Trigger e azioni predefiniti che non sono approvati da Google, ad esempio HTTP, funzioni di Azure, app per la logica di Azure e altri, rendono un'app per la logica non conforme a Gmail Connector perché l'app può inviare o ricevere dati da qualsiasi posizione.

* Google Services: Gmail, Google Calendar, Google Contacts, Google Drive, Google Sheets e Google Task

* Servizi Microsoft approvati: Dynamics 365, Excel online, Microsoft teams, Microsoft 365, OneDrive e SharePoint Online

* Connettori per le origini dati gestite dal cliente: FTP, RSS, SFTP, SMTP e SQL Server

## <a name="non-compliant-examples"></a>Esempi non conformi

Di seguito sono riportati alcuni esempi che usano il connettore Gmail con trigger e azioni predefiniti o connettori gestiti che non sono approvati da Google:

* Questa app per la logica usa il connettore Gmail con il trigger predefinito HTTP:

  ![App per la logica non conforme-esempio 1](./media/connectors-google-data-security-privacy-policy/not-compliant-logic-app-1.png)
  
  L'app per la logica usa anche Google Calendar Connector, che è approvato.

* Questa app per la logica usa il connettore Gmail con il connettore di archiviazione BLOB di Azure:

  ![App per la logica non conforme-esempio 2](./media/connectors-google-data-security-privacy-policy/not-compliant-logic-app-2.png)

* Questa app per la logica usa il connettore Gmail con il connettore Twitter:

  ![App per la logica non conforme-esempio 3](./media/connectors-google-data-security-privacy-policy/not-compliant-logic-app-3.png)

Per informazioni aggiornate, vedere la [documentazione di riferimento tecnico del connettore Gmail](/connectors/gmail/).

<a name="update-affected-workflows"></a>

## <a name="steps-for-affected-logic-apps"></a>Passaggi per le app per la logica interessate

Se è necessario usare il connettore Gmail con un account utente Gmail e i connettori Google non approvati in un'app per la logica, è possibile creare un'app Google personalizzata per uso personale o interno nell'azienda. Per questo scenario, di seguito sono riportati i passaggi di alto livello che è necessario eseguire:

1. Creare un'app client Google usando la [console API Google](https://console.developers.google.com).

1. Nel connettore Gmail usare i valori relativi all'ID client e al segreto client dell'app client Google.

Per ulteriori informazioni, vedere la [documentazione di riferimento tecnico per il connettore Gmail](/connectors/gmail/#authentication-and-bring-your-own-application).

### <a name="create-google-client-app"></a>Crea app client Google

Per configurare un progetto per l'app client, usare la [procedura guidata della console dell'API Google](https://console.developers.google.com/start/api?id=gmail&credential=client_key) e seguire le istruzioni. In alternativa, per i passaggi dettagliati, consultare le istruzioni nella [documentazione di riferimento tecnico del connettore Gmail](/connectors/gmail/#authentication-and-bring-your-own-application).

Al termine, la schermata avrà un aspetto simile a questo esempio, ad eccezione del fatto che i valori di **ID client** e **segreto client** verranno usati in un secondo momento nell'app per la logica.

![ID client e segreto client per l'app client Google](./media/connectors-google-data-security-privacy-policy/google-api-console.png)

### <a name="use-client-app-settings-in-logic-app"></a>Usare le impostazioni dell'app client nell'app per la logica

Per usare l'ID client e il segreto client dall'app client Google nel trigger o nell'azione di Gmail, seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com) aprire l'app per la logica in Progettazione app per la logica.

1. Se si aggiunge un nuovo trigger o un'azione di Gmail e si crea una connessione completamente nuova, continuare con il passaggio successivo. In caso contrario, nel trigger o nell'azione Gmail selezionare **Modifica connessione**  >  **Aggiungi nuovo**, ad esempio:

   ![Selezionare "Cambia connessione" > "Aggiungi nuovo"](./media/connectors-google-data-security-privacy-policy/change-gmail-connection.png)

1. Fornire le informazioni di connessione, ad esempio:

   ![Specificare le informazioni di connessione](./media/connectors-google-data-security-privacy-policy/authentication-type-bring-your-own.png)

   | Proprietà | Valore | Descrizione |
   |----------|-------|-------------|
   | **Tipo di autenticazione** | **Porta la tua applicazione** | Specifica che verrà usata l'app client personalizzata per l'autenticazione. |
   | **ID client** | <*client-ID*> | ID client dall'app client Google |
   | **Segreto client** | <*client-secret*> | Il segreto client dall'app client Google |
   ||||

1. Al termine, selezionare **Sign in (accedi**).

   Viene visualizzata una pagina che mostra l'app client creata. Se si usa un account utente Gmail, è possibile che venga visualizzata una pagina che indica che l'app client non è stata verificata da Google e chiede di consentire l'accesso all'account Google.

   ![Richiedi l'accesso al tuo account Google](./media/connectors-google-data-security-privacy-policy/allow-access-authorized-domain.png)

1. Se necessario, selezionare **Consenti**.

   È ora possibile usare il connettore Gmail senza restrizioni nell'app per la logica.

## <a name="next-steps"></a>Passaggi successivi

Scopri di più sul [connettore Gmail](/connectors/gmail/)

