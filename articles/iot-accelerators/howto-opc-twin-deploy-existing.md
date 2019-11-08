---
title: Come distribuire un modulo OPC gemelle in un progetto Azure esistente | Microsoft Docs
description: Questo articolo descrive come distribuire un dispositivo OPC gemello a un progetto esistente. È anche possibile scoprire come risolvere i problemi di distribuzione.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: b971ec13c71ccfd7d28ae6987593d09201b9b764
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824127"
---
# <a name="deploy-opc-twin-to-an-existing-project"></a>Distribuire un dispositivo OPC gemello a un progetto esistente

Il modulo OPC gemello viene eseguito su IoT Edge e fornisce diversi servizi perimetrali ai servizi OPC gemello e registro di sistema.

Il microservizio OPC gemello semplifica la comunicazione tra gli operatori Factory e i dispositivi server OPC UA nel piano Factory tramite un modulo OPC gemello IoT Edge. Il microservizio espone i servizi OPC UA (Browse, Read, Write ed Execute) tramite l'API REST. 

Il microservizio registro di sistema del dispositivo OPC UA fornisce l'accesso alle applicazioni OPC UA registrate e ai relativi endpoint. Gli operatori e gli amministratori possono registrare e annullare la registrazione di nuove applicazioni OPC UA ed esplorare quelle esistenti, inclusi i relativi endpoint. Oltre alla gestione di applicazioni ed endpoint, il servizio Registro di sistema cataloga anche i moduli IoT Edge OPC gemelli registrati. L'API del servizio consente di controllare la funzionalità del modulo perimetrale, ad esempio l'avvio o l'arresto dell'individuazione del server (servizi di analisi) o l'attivazione di nuovi endpoint gemelli a cui è possibile accedere tramite il microservizio OPC gemello.

Il nucleo del modulo è l'identità del supervisore. Il supervisore gestisce l'endpoint gemello, che corrisponde agli endpoint server OPC UA attivati mediante l'API del registro di sistema OPC UA corrispondente. Questo endpoint gemelli traduce il JSON OPC UA ricevuto dal microservizio OPC gemello in esecuzione nel cloud in messaggi binari OPC UA, che vengono inviati tramite un canale sicuro con stato all'endpoint gestito. Il supervisore fornisce anche servizi di individuazione che inviano eventi di individuazione dei dispositivi al servizio di caricamento dei dispositivi OPC UA per l'elaborazione, in cui questi eventi generano aggiornamenti al registro OPC UA.  Questo articolo illustra come distribuire il modulo OPC gemelle in un progetto esistente.

> [!NOTE]
> Per ulteriori informazioni sui dettagli e le istruzioni per la distribuzione, vedere il [repository](https://github.com/Azure/azure-iiot-opc-twin-module)github.

## <a name="prerequisites"></a>Prerequisiti

Verificare che siano installate le estensioni [PowerShell e AzureRM PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) . Se non è già stato fatto, clonare questo repository GitHub. Eseguire i comandi seguenti in PowerShell:

```powershell
git clone --recursive https://github.com/Azure/azure-iiot-components.git
cd azure-iiot-components
```

## <a name="deploy-industrial-iot-services-to-azure"></a>Distribuisci i servizi di Industrial Internet in Azure

1. Nella sessione di PowerShell eseguire:

    ```powershell
    set-executionpolicy -ExecutionPolicy Unrestricted -Scope Process
    .\deploy.cmd
    ```

2. Seguire le istruzioni per assegnare un nome al gruppo di risorse della distribuzione e un nome al sito Web.   Lo script distribuisce i microservizi e le relative dipendenze della piattaforma Azure nel gruppo di risorse nella sottoscrizione di Azure.  Lo script registra anche un'applicazione nel tenant di Azure Active Directory (AAD) per supportare l'autenticazione basata su OAUTH.  La distribuzione può richiedere diversi minuti.  Un esempio di ciò che si vedrebbe dopo che la soluzione è stata distribuita correttamente:

   ![Distribuzione di Internet delle cose OPC gemelli nel progetto esistente](media/howto-opc-twin-deploy-existing/opc-twin-deploy-existing1.png)

   L'output include l'URL dell'endpoint pubblico. 

3. Una volta completato lo script, selezionare se si desidera salvare il file di `.env`.  È necessario il file dell'ambiente `.env` se si desidera connettersi all'endpoint cloud utilizzando strumenti come la console di o distribuire moduli per lo sviluppo e il debug.

## <a name="troubleshooting-deployment-failures"></a>Risoluzione degli errori di distribuzione

### <a name="resource-group-name"></a>Nome del gruppo di risorse

Assicurarsi di usare un nome di gruppo di risorse breve e semplice.  Il nome viene usato anche per assegnare un nome alle risorse, in quanto deve essere conforme ai requisiti di denominazione delle risorse.  

### <a name="website-name-already-in-use"></a>Il nome del sito Web è già in uso

È possibile che il nome del sito Web sia già in uso.  Se si esegue questo errore, è necessario usare un nome di applicazione diverso.

### <a name="azure-active-directory-aad-registration"></a>Registrazione di Azure Active Directory (AAD)

Lo script di distribuzione tenta di registrare due applicazioni AAD in Azure Active Directory.  A seconda dei diritti per il tenant AAD selezionato, la distribuzione potrebbe non riuscire. Sono disponibili due opzioni:

1. Se si sceglie un tenant AAD da un elenco di tenant, riavviare lo script e sceglierne uno diverso dall'elenco.
2. In alternativa, distribuire un tenant AAD privato in un'altra sottoscrizione, riavviare lo script e selezionare per usarlo.

> [!WARNING]
> NON continuare senza autenticazione.  Se si sceglie di eseguire questa operazione, chiunque può accedere agli endpoint di OPC gemelli da Internet non autenticato.   È sempre possibile scegliere l' [opzione di distribuzione "locale"](howto-opc-twin-deploy-dependencies.md) per avviare le gomme.

## <a name="deploy-an-all-in-one-industrial-iot-services-demo"></a>Distribuzione di una demo dei servizi Internet delle cose industriali all-in-One

Anziché solo i servizi e le dipendenze, è anche possibile distribuire una demo All-in-One.  Il tutto in un'unica demo contiene tre server OPC UA, il modulo OPC gemello, tutti i microservizi e un'applicazione Web di esempio.  È destinato a scopi dimostrativi.

1. Assicurarsi di avere un clone del repository (vedere sopra). Aprire un prompt di PowerShell nella radice del repository ed eseguire:

    ```powershell
    set-executionpolicy -ExecutionPolicy Unrestricted -Scope Process
    .\deploy -type demo
    ```

2. Seguire le istruzioni per assegnare un nuovo nome al gruppo di risorse e un nome al sito Web.  Una volta distribuita correttamente, lo script visualizzerà l'URL dell'endpoint dell'applicazione Web.

## <a name="deployment-script-options"></a>Opzioni dello script di distribuzione

Lo script accetta i parametri seguenti:

```powershell
-type
```

Tipo di distribuzione (macchina virtuale, locale, demo)

```powershell
-resourceGroupName
```

Può essere il nome di un gruppo di risorse esistente o nuovo.

```powershell
-subscriptionId
```

Facoltativo, ID sottoscrizione in cui verranno distribuite le risorse.

```powershell
-subscriptionName
```

O il nome della sottoscrizione.

```powershell
-resourceGroupLocation
```

Facoltativo, percorso del gruppo di risorse. Se specificato, tenterà di creare un nuovo gruppo di risorse in questa posizione.

```powershell
-aadApplicationName
```

Nome dell'applicazione AAD con cui eseguire la registrazione.

```powershell
-tenantId
```

Tenant di AAD da usare.

```powershell
-credentials
```

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come distribuire un dispositivo OPC gemello a un progetto esistente, ecco il passaggio successivo suggerito:

> [!div class="nextstepaction"]
> [Comunicazione sicura di client OPC UA e OPC UA PLC](howto-opc-vault-secure.md)
