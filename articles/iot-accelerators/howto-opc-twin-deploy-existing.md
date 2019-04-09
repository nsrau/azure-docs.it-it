---
title: Come distribuire modulo di gestione di dispositivi Azure IoT OPC UA in un progetto esistente | Microsoft Docs
description: Come distribuire gemello OPC a un progetto esistente.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: dcf6acca344fe2a34fdc48fe89c5a1ee62b10b23
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59255887"
---
# <a name="deploy-opc-twin-to-an-existing-project"></a>Distribuire OPC dei dispositivi gemelli in un progetto esistente

Il modulo gemello dispositivo OPC viene eseguito in dispositivi perimetrali IoT e offre diversi servizi di edge ai servizi del Registro di sistema e OPC dispositivo gemello. 

Il servizio di micro OPC dispositivo gemello facilita la comunicazione tra gli operatori di factory e i dispositivi server OPC UA nell'ambiente di produzione tramite un modulo IoT Edge gemello di OPC. Il servizio micro espone servizi OPC UA (Sfoglia, lettura, scrittura ed Execute) tramite l'API REST. 

Il servizio di registro del dispositivo OPC UA micro fornisce accesso alle applicazioni registrate OPC UA e i relativi endpoint. Gli operatori e gli amministratori possono registrare e annullare la registrazione di nuove applicazioni OPC UA e individuare quelle esistenti, inclusi i relativi endpoint. Oltre all'applicazione e la gestione di endpoint, il servizio Registro di sistema cataloga anche registrati i moduli di IoT Edge di OPC dispositivo gemello. L'API del servizio ti offre controllo della funzionalità modulo edge, ad esempio, avviare o arrestare l'individuazione server (servizi di analisi) o attivazione nuovi dispositivi gemelli di endpoint che è possibile accedere tramite il servizio di micro gemello OPC.

Il nucleo del modulo è l'identità del supervisore. Il Supervisore gestisce gemello endpoint, che corrisponde agli endpoint del server OPC UA che vengono attivati mediante l'API di registro OPC UA corrispondente. Dispositivi gemelli questo endpoint traducono OPC UA JSON ricevuto dal servizio micro gemello OPC in esecuzione nel cloud in messaggi binari OPC UA, che vengono inviati tramite un canale sicuro con stato per l'endpoint gestito. Il Supervisore fornisce anche servizi di individuazione che inviano eventi di individuazione di dispositivi al servizio di Onboarding di dispositivo OPC UA per l'elaborazione, in cui questi eventi che gli aggiornamenti al Registro di sistema OPC UA.  Questo articolo illustra come distribuire il modulo gemello OPC a un progetto esistente. 

> [!NOTE]
> Per altre informazioni sui dettagli di distribuzione e istruzioni, vedere GitHub [repository](https://github.com/Azure/azure-iiot-opc-twin-module).

## <a name="prerequisites"></a>Prerequisiti

Assicurarsi di disporre di PowerShell e [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) estensioni installate.   Se non si fatto ancora, clonare questo repository GitHub.  Aprire un prompt dei comandi o terminale ed eseguire:

```bash
git clone --recursive https://github.com/Azure/azure-iiot-components 
cd azure-iiot-components
```

## <a name="deploy-industrial-iot-services-to-azure"></a>Distribuire i servizi IoT industriali in Azure

1. Nel prompt dei comandi aperta o terminale eseguire:

   ```bash
   deploy
   ```

2. Seguire le istruzioni per assegnare un nome al gruppo di risorse della distribuzione e un nome per il sito Web.   Lo script distribuisce i microservizi e le relative dipendenze di piattaforma di Azure nel gruppo di risorse nella sottoscrizione di Azure.  Lo script registra inoltre un'applicazione nel tenant di Azure Active Directory (AAD) per supportare l'autenticazione basata su OAUTH.  Distribuzione richiederà alcuni minuti.  Un esempio di ciò che viene viene visualizzato dopo aver distribuita la soluzione:

   ![Dispositivo gemello di OPC IoT industriale distribuire al progetto esistente](media/howto-opc-twin-deploy-existing/opc-twin-deploy-existing1.png)

   L'output include l'URL dell'endpoint pubblico. 

3. Una volta lo script viene completato correttamente, selezionare se si desidera salvare il file con estensione env.  Se si desidera connettersi all'endpoint cloud usando strumenti come la Console o distribuire i moduli per lo sviluppo e il debug, è necessario il file con estensione env dell'ambiente.

## <a name="troubleshooting-deployment-failures"></a>Risoluzione degli errori di distribuzione

### <a name="resource-group-name"></a>Nome del gruppo di risorse

Assicurarsi di che usare il nome di un gruppo di risorse a breve e semplice.  Il nome viene usato anche assegnare nomi alle risorse di conseguenza che deve rispondere con i requisiti di denominazione.  

### <a name="website-name-already-in-use"></a>Nome del sito Web già in uso

È possibile che il nome del sito Web è già in uso.  Se si verifica questo errore, è necessario usare un nome di applicazione diversi.

### <a name="azure-active-directory-aad-registration"></a>Registrazione con Azure Active Directory (AAD)

Lo script di distribuzione tenta di registrare due applicazioni AAD in Azure Active Directory.  A seconda di diritti per il tenant di AAD selezionato, la distribuzione potrebbe non riuscire. Sono disponibili due opzioni:

1. Se si sceglie un tenant di AAD da un elenco di tenant, riavviare lo script e scegliere una diversa dall'elenco.
2. In alternativa, distribuire un tenant AAD privato in un'altra sottoscrizione, riavviare lo script e selezionare questa opzione per usarlo.

> [!WARNING]
> Non continuano mai senza autenticazione.  Se si sceglie di eseguire questa operazione, chiunque può accedere gli endpoint di gestione dei dispositivi OPC da Internet non autenticati.   È sempre possibile scegliere il [opzione di distribuzione "local"](howto-opc-twin-deploy-dependencies.md) per provarla prima del.

## <a name="deploy-an-all-in-one-industrial-iot-services-demo"></a>Distribuire una demo di servizi IoT industriale all-in-one

È anche possibile distribuire una demo di all-in-one anziché solo i servizi e le dipendenze.  Il tutto in una demo contiene tre server OPC UA, il modulo gemello OPC, tutti i microservizi e un'applicazione Web di esempio.  Destinato a scopo dimostrativo.

1. Assicurarsi di avere un clone del repository (vedere sopra). Aprire un prompt dei comandi o terminale nella radice del repository ed eseguire:

   ```bash
   deploy -type demo
   ```

2. Seguire le istruzioni per assegnare un nuovo nome per il gruppo di risorse e un nome per il sito Web.  Dopo aver distribuito correttamente, lo script verrà visualizzato l'URL dell'endpoint dell'applicazione web.

## <a name="deployment-script-options"></a>Opzioni di script di distribuzione

Lo script accetta i parametri seguenti:

```bash
-type
```

Il tipo di distribuzione (macchina virtuale locale, demo)

```bash
-resourceGroupName
```

Può essere il nome di un gruppo di risorse nuovo o esistente.

```bash
-subscriptionId
```

Facoltativo, l'ID sottoscrizione in cui verranno distribuite risorse.

```bash
-subscriptionName
```

O il nome della sottoscrizione.

```bash
-resourceGroupLocation
```

Facoltativo, il percorso di un gruppo di risorse. Se specificato, tenterà di creare un nuovo gruppo di risorse in questa posizione.

```bash
-aadApplicationName
```

Un nome per l'applicazione di AAD registrare in. 

```bash
-tenantId
```

Tenant di AAD da usare.

```bash
-credentials
```

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come distribuire OPC gemello di un progetto esistente, ecco il passaggio successivo consigliato:

> [!div class="nextstepaction"]
> [Proteggere le comunicazioni di OPC Client e OPC PLC](howto-opc-vault-deploy-existing-client-plc-communication.md)
