---
title: Proxy SMART on FHIR per Azure Active Directory
description: Questa esercitazione descrive come usare un proxy per abilitare le applicazioni SMART on FHIR con l'API di Azure per FHIR.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: dseven
ms.author: matjazl
author: matjazl
ms.date: 04/02/2019
ms.openlocfilehash: 2e13a9fc32964781dda07e5534e5cab79868ddf0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995540"
---
# <a name="tutorial-azure-active-directory-smart-on-fhir-proxy"></a>Esercitazione: Proxy SMART on FHIR per Azure Active Directory

[SMART on FHIR](https://docs.smarthealthit.org/) è un set di specifiche aperte per l'integrazione di applicazioni dei partner con i server FHIR e i sistemi di cartelle ciniche elettroniche dotati di interfacce FHIR. Uno dei principali scopi delle specifiche è descrivere in che modo le applicazioni individuano gli endpoint di autenticazione per un server FHIR e avviano una sequenza di autenticazione. 

L'autenticazione è basata su OAuth2. Tuttavia, poiché SMART on FHIR usa convenzioni di denominazione dei parametri che non sono immediatamente compatibili con Azure Active Directory (Azure AD), l'API di Azure per FHIR integra un proxy SMART on FHIR per Azure AD che abilita un sottoinsieme delle sequenze di avvio di SMART on FHIR. In particolare, il proxy abilita la [sequenza di avvio EHR](https://hl7.org/fhir/smart-app-launch/#ehr-launch-sequence).

Questa esercitazione descrive come usare il proxy per abilitare le applicazioni SMART on FHIR con l'API di Azure per FHIR.

## <a name="prerequisites"></a>Prerequisiti

- Un'istanza dell'API di Azure per FHIR
- [.NET Core 2.2](https://dotnet.microsoft.com/download/dotnet-core/2.2)

## <a name="configure-azure-ad-registrations"></a>Configurare le registrazioni di Azure AD

SMART on FHIR richiede che `Audience` abbia un URI identificatore uguale all'URI del servizio FHIR. La configurazione standard dell'API di Azure per FHIR usa un valore `https://azurehealthcareapis.com` per `Audience`. Tuttavia, è anche possibile impostare un valore corrispondente all'URL specifico del servizio FHIR, ad esempio `https://MYFHIRAPI.azurehealthcareapis.com`. Questa operazione è necessaria quando si usa il proxy SMART on FHIR.

Sarà necessaria anche la registrazione di un'applicazione client. La maggior parte delle applicazioni SMART on FHIR sono applicazioni JavaScript a pagina singola. Quindi, è necessario seguire le istruzioni per configurare un'[applicazione client Azure AD pubblica](register-public-azure-ad-client-app.md).

Dopo aver completato questi passaggi, si avranno a disposizione:

- Un server FHIR con destinatario impostato su `https://MYFHIRAPI.azurehealthcareapis.com`, dove `MYFHIRAPI` è il nome dell'istanza dell'API di Azure per FHIR.
- Una registrazione dell'applicazione client pubblica. Prendere nota dell'ID di questa applicazione client.

## <a name="enable-the-smart-on-fhir-proxy"></a>Abilitare il proxy SMART on FHIR

Abilitare il proxy SMART on FHIR nelle impostazioni di **Autenticazione** dell'istanza dell'API di Azure per FHIR selezionando la casella di controllo **SMART on FHIR proxy**:

![Selezioni per abilitare il proxy SMART on FHIR](media/tutorial-smart-on-fhir/enable-smart-on-fhir-proxy.png)

## <a name="enable-cors"></a>Abilitare CORS

Poiché la maggior parte delle applicazioni SMART on FHIR sono app JavaScript a pagina singola, è necessario [abilitare la condivisione di risorse tra le origini (CORS)](configure-cross-origin-resource-sharing.md) per l'API di Azure per FHIR:

![Selezioni per l'abilitazione di CORS](media/tutorial-smart-on-fhir/enable-cors.png)

## <a name="configure-the-reply-url"></a>Configurare l'URL di risposta

Il proxy SMART on FHIR funge da intermediario tra l'app SMART on FHIR e Azure AD. La risposta di autenticazione (codice di autenticazione) deve essere inviata al proxy SMART on FHIR invece che all'app stessa. Il proxy quindi la inoltra all'app. 

A causa di questo inoltro in due passaggi del codice di autenticazione, è necessario impostare l'URL di risposta (callback) per l'applicazione client Azure AD su un URL costituito da una combinazione dell'URL di risposta per il proxy SMART on FHIR e dell'URL di risposta per l'app SMART on FHIR. Il formato dell'URL di risposta combinato è il seguente:

```http
https://MYFHIRAPI.azurehealthcareapis.com/AadSmartOnFhirProxy/callback/aHR0cHM6Ly9sb2NhbGhvc3Q6NTAwMS9zYW1wbGVhcHAvaW5kZXguaHRtbA
```

In questa risposta `aHR0cHM6Ly9sb2NhbGhvc3Q6NTAwMS9zYW1wbGVhcHAvaW5kZXguaHRtbA` è una versione con codifica Base64 URL-safe dell'URL di risposta per l'app SMART on FHIR. Per l'utilità di avvio di app SMART on FHIR, quando l'app viene eseguita localmente, l'URL di risposta è `https://localhost:5001/sampleapp/index.html`. 

È possibile generare l'URL di risposta combinato usando uno script simile al seguente:

```PowerShell
$replyUrl = "https://localhost:5001/sampleapp/index.html"
$fhirServerUrl = "https://MYFHIRAPI.azurewebsites.net"
$bytes = [System.Text.Encoding]::UTF8.GetBytes($ReplyUrl)
$encodedText = [Convert]::ToBase64String($bytes)
$encodedText = $encodedText.TrimEnd('=');
$encodedText = $encodedText.Replace('/','_');
$encodedText = $encodedText.Replace('+','-');

$newReplyUrl = $FhirServerUrl.TrimEnd('/') + "/AadSmartOnFhirProxy/callback/" + $encodedText
```

Aggiungere l'URL di risposta all'applicazione client pubblica creata in precedenza per Azure AD:

![URL di risposta configurato per il client pubblico](media/tutorial-smart-on-fhir/configure-reply-url.png)

## <a name="get-a-test-patient"></a>Ottenere un paziente di test

Per testare l'API di Azure per FHIR e il proxy SMART on FHIR, è necessario avere almeno un paziente nel database. Se non si è ancora interagito con l'API e non sono presenti dati nel database, seguire l'[esercitazione sull'uso dell'API FHIR con Postman](access-fhir-postman-tutorial.md) per caricare un paziente. Prendere nota dell'ID di un paziente specifico.

## <a name="download-the-smart-on-fhir-app-launcher"></a>Scaricare l'utilità di avvio di app SMART on FHIR

Il [repository del server FHIR per Azure](https://github.com/Microsoft/fhir-server) open source include una semplice utilità di avvio di app SMART on FHIR e un'app SMART on FHIR di esempio. In questa esercitazione usare l'utilità di avvio di SMART on FHIR localmente per testare la configurazione.

È possibile clonare il repository GitHub e passare all'applicazione usando i comandi seguenti:

```PowerShell
git clone https://github.com/Microsoft/fhir-server
cd fhir-server/samples/apps/SmartLauncher
```

Per l'applicazione sono necessarie alcune impostazioni di configurazione, che è possibile specificare in `appsettings.json`:

```json
{
    "FhirServerUrl": "https://MYFHIRAPI.azurehealthcareapis.com",
    "ClientId": "APP-ID",
    "DefaultSmartAppUrl": "/sampleapp/launch.html"
}
```

È consigliabile usare la funzionalità `dotnet user-secrets`:

```PowerShell
dotnet user-secrets set FhirServerUrl https://MYFHIRAPI.azurehealthcareapis.com
dotnet user-secrets set ClientId <APP-ID>
```

Usare questo comando per eseguire l'applicazione:

```PowerShell
dotnet run
```

## <a name="test-the-smart-on-fhir-proxy"></a>Testare il proxy SMART on FHIR

Dopo aver avviato l'utilità di avvio di app SMART on FHIR, è possibile puntare il browser all'indirizzo `https://localhost:5001`, dove verrà visualizzata la schermata seguente:

![Utilità di avvio di app SMART on FHIR](media/tutorial-smart-on-fhir/smart-on-fhir-app-launcher.png)

Quando si immettono le informazioni per **Patient** (Paziente), **Encounter** (Primo contatto) o **Practitioner** (Medico), si noterà che il **contesto di avvio** è stato aggiornato. Quando si usa l'API di Azure per FHIR, il contesto di avvio è semplicemente un documento JSON che contiene informazioni su pazienti, medici e altro ancora. Questo contesto di avvio è codificato con Base64 e viene passato all'app SMART on FHIR come parametro di query `launch`. In base alla specifica SMART on FHIR, questa variabile è opaca per l'app SMART on FHIR e viene passata al provider di identità. 

Il proxy SMART on FHIR usa queste informazioni per popolare i campi nella risposta del token. L'app SMART on FHIR *può* usare questi campi per controllare per quale paziente richiede i dati e come visualizza l'applicazione sullo schermo. Il proxy SMART on FHIR supporta i campi seguenti:

* `patient`
* `encounter`
* `practitioner`
* `need_patient_banner`
* `smart_style_url`

Questi campi hanno lo scopo di fornire indicazioni all'app, ma non trasmettono informazioni di sicurezza. Un'applicazione SMART on FHIR può ignorarli.

Si noti che l'utilità di avvio di app SMART on FHIR aggiorna le informazioni sull'**URL di avvio** in fondo alla pagina. Selezionare **Launch** (Avvia) per avviare l'app di esempio. Verrà visualizzata una schermata simile a questo esempio:

![App SMART on FHIR](media/tutorial-smart-on-fhir/smart-on-fhir-app.png)

Esaminare la risposta del token per vedere come vengono passati i campi del contesto di avvio all'app.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato configurato il proxy SMART on FHIR per Azure Active Directory. Per esplorare l'uso di applicazioni SMART on FHIR con l'API di Azure per FHIR e il server FHIR open source per Azure, passare al repository di esempi del server FHIR in GitHub:

>[!div class="nextstepaction"]
>[Esempi del server FHIR](https://github.com/Microsoft/fhir-server-samples)
