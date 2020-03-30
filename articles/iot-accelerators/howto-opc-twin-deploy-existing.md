---
title: Come distribuire un modulo OPC Twin in un progetto di Azure esistente Documenti Microsoft
description: In questo articolo viene descritto come distribuire OPC Twin in un progetto esistente. È inoltre possibile informazioni su come risolvere gli errori di distribuzione.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: b971ec13c71ccfd7d28ae6987593d09201b9b764
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73824127"
---
# <a name="deploy-opc-twin-to-an-existing-project"></a>Distribuire OPC Twin in un progetto esistenteDeploy OPC Twin to an existing project

Il modulo OPC Twin funziona su IoT Edge e fornisce diversi servizi edge per i servizi OPC Twin e Registry.

Il microservizio OPC Twin facilita la comunicazione tra gli operatori di fabbrica e i dispositivi server OPC UA in fabbrica tramite un modulo OPC Twin IoT Edge. Il microservizio espone i servizi OPC UA (Sfoglia, Lettura, Scrittura ed Esecuzione) tramite l'API REST. 

Il microservizio del registro dei dispositivi OPC UA fornisce l'accesso alle applicazioni OPC UA registrate e ai relativi endpoint. Gli operatori e gli amministratori possono registrare e annullare la registrazione di nuove applicazioni OPC UA ed esplorare quelle esistenti, inclusi i relativi endpoint. Oltre alla gestione delle applicazioni e degli endpoint, il servizio Registro di sistema cataloga anche i moduli OPC Twin IoT Edge registrati. L'API del servizio consente di controllare le funzionalità dei moduli perimetrali, ad esempio l'avvio o l'arresto dell'individuazione del server (servizi di scansione) o l'attivazione di nuovi endpoint gemelli a cui è possibile accedere tramite il microservizio OPC Twin.

Il nucleo del modulo è l'identità del supervisore. Il supervisore gestisce l'endpoint gemello, che corrisponde agli endpoint del server OPC UA attivati tramite l'API del Registro di sistema OPC UA corrispondente. Questo endpoint gemello traducono OPC UA JSON ricevuto dal microservizio OPC Twin in esecuzione nel cloud in messaggi binari OPC UA, che vengono inviati tramite un canale sicuro con stato all'endpoint gestito. Il supervisore fornisce anche servizi di individuazione che inviano eventi di individuazione dei dispositivi al servizio di onboarding dei dispositivi OPC UA per l'elaborazione, in cui questi eventi generano aggiornamenti al Registro di sistema OPC UA.  In questo articolo viene illustrato come distribuire il modulo OPC Twin a un progetto esistente.

> [!NOTE]
> Per ulteriori informazioni sui dettagli e le istruzioni di distribuzione, vedere il [repository](https://github.com/Azure/azure-iiot-opc-twin-module)GitHub .

## <a name="prerequisites"></a>Prerequisiti

Assicurarsi di avere installato le estensioni di PowerShell e [AzureRM PowerShell.Make](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) sure that you have PowerShell and AzureRM PowerShell extensions installed. Se non l'hai già fatto, clona questo repository GitHub. Eseguire i comandi seguenti in PowerShell:

```powershell
git clone --recursive https://github.com/Azure/azure-iiot-components.git
cd azure-iiot-components
```

## <a name="deploy-industrial-iot-services-to-azure"></a>Distribuire servizi IoT industriali in AzureDeploy industrial IoT services to Azure

1. Nella sessione di PowerShell eseguire:In your PowerShell session, run:

    ```powershell
    set-executionpolicy -ExecutionPolicy Unrestricted -Scope Process
    .\deploy.cmd
    ```

2. Seguire le istruzioni visualizzate per assegnare un nome al gruppo di risorse della distribuzione e un nome al sito Web.   Lo script distribuisce i microservizi e le relative dipendenze della piattaforma Azure nel gruppo di risorse nella sottoscrizione di Azure.The script deploys the microservices and their Azure platform dependencies into the resource group in your Azure subscription.  Lo script registra anche un'applicazione nel tenant di Azure Active Directory (AAD) per supportare l'autenticazione basata su OAUTH.  La distribuzione richiederà alcuni minuti.  Un esempio di ciò che si vedrà una volta che la soluzione è stata distribuita correttamente:

   ![Industriale IoT OPC Twin distribuire al progetto esistente](media/howto-opc-twin-deploy-existing/opc-twin-deploy-existing1.png)

   L'output include l'URL dell'endpoint pubblico. 

3. Una volta completato correttamente lo script, `.env` selezionare se si desidera salvare il file.  È necessario `.env` il file di ambiente se si desidera connettersi all'endpoint cloud utilizzando strumenti come la console o distribuire i moduli per lo sviluppo e il debug.

## <a name="troubleshooting-deployment-failures"></a>Risoluzione degli errori di distribuzioneTroubleshooting deployment failures

### <a name="resource-group-name"></a>Nome del gruppo di risorse

Assicurarsi di usare un nome di gruppo di risorse breve e semplice.  Il nome viene utilizzato anche per denominare le risorse in quanto tali deve essere conforme ai requisiti di denominazione delle risorse.  

### <a name="website-name-already-in-use"></a>Nome del sito Web già in uso

È possibile che il nome del sito sia già in uso.  Se si verifica questo errore, è necessario utilizzare un nome di applicazione diverso.

### <a name="azure-active-directory-aad-registration"></a>Registrazione di Azure Active Directory (AAD)Azure Active Directory (AAD) registration

Lo script di distribuzione tenta di registrare due applicazioni AAD in Azure Active Directory.The deployment script tries to register two AAD applications in Azure Active Directory.  A seconda dei diritti per il tenant AAD selezionato, la distribuzione potrebbe non riuscire. Sono disponibili due opzioni:

1. Se si sceglie un tenant AAD da un elenco di tenant, riavviare lo script e sceglierne uno diverso dall'elenco.
2. In alternativa, distribuire un tenant AAD privato in un'altra sottoscrizione, riavviare lo script e scegliere di usarlo.

> [!WARNING]
> Non continuare MAI senza Autenticazione.  Se si sceglie di farlo, chiunque può accedere agli endpoint OPC Twin da Internet non autenticati.   È sempre possibile scegliere [l'opzione di distribuzione "locale"](howto-opc-twin-deploy-dependencies.md) per calciare le gomme.

## <a name="deploy-an-all-in-one-industrial-iot-services-demo"></a>Distribuzione di una demo dei servizi IoT industriali all-in-oneDeploy an all-in-one industrial IoT services demo

Invece di soli servizi e dipendenze è anche possibile distribuire una demo all-in-one.  Il tutto in una demo contiene tre server OPC UA, il modulo OPC Twin, tutti i microservizi e un'applicazione Web di esempio.  È destinato a scopi dimostrativi.

1. Assicurarsi di disporre di un clone del repository (vedere sopra). Aprire un prompt di PowerShell nella radice del repository ed eseguire:

    ```powershell
    set-executionpolicy -ExecutionPolicy Unrestricted -Scope Process
    .\deploy -type demo
    ```

2. Seguire le istruzioni visualizzate per assegnare un nuovo nome al gruppo di risorse e un nome al sito Web.  Una volta distribuito correttamente, lo script visualizzerà l'URL dell'endpoint dell'applicazione Web.

## <a name="deployment-script-options"></a>Opzioni dello script di distribuzione

Lo script accetta i seguenti parametri:

```powershell
-type
```

Il tipo di distribuzione (vm, local, demo)

```powershell
-resourceGroupName
```

Può essere il nome di un gruppo di risorse esistente o nuovo.

```powershell
-subscriptionId
```

Facoltativo, l'ID sottoscrizione in cui verranno distribuite le risorse.

```powershell
-subscriptionName
```

O il nome della sottoscrizione.

```powershell
-resourceGroupLocation
```

Facoltativo, una posizione del gruppo di risorse. Se specificato, tenterà di creare un nuovo gruppo di risorse in questa posizione.

```powershell
-aadApplicationName
```

Nome dell'applicazione AAD con cui eseguire la registrazione.

```powershell
-tenantId
```

Tenant AAD da utilizzare.

```powershell
-credentials
```

## <a name="next-steps"></a>Passaggi successivi

Ora che hai imparato a distribuire OPC Twin in un progetto esistente, ecco il passaggio successivo suggerito:

> [!div class="nextstepaction"]
> [Comunicazione sicura del client OPC UA e OPC UA PLC](howto-opc-vault-secure.md)
