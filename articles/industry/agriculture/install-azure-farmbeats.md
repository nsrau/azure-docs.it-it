---
title: Installare Azure FarmBeats
description: Questo articolo descrive come installare Azure FarmBeats nella sottoscrizione di AzureThis article describes how to install Azure FarmBeats in your Azure subscription
author: usha-rathnavel
ms.topic: article
ms.date: 1/17/2020
ms.author: atinb
ms.openlocfilehash: 0761db6b73c6fcfeb1ef6fda729a68c9644bbc72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479560"
---
# <a name="install-azure-farmbeats"></a>Installare Azure FarmBeats

Questo articolo descrive come installare Azure FarmBeats nella sottoscrizione di Azure.This article describes how to install Azure FarmBeats in your Azure subscription.

Azure FarmBeats è un'offerta business-to-business disponibile in Azure Marketplace. Consente l'aggregazione di set di dati agricoli tra i provider e la generazione di informazioni utili. Azure FarmBeats esegue questa operazione consentendo di creare modelli di intelligenza artificiale (AI) o Machine Learning (ML) basati su set di dati fusi. I due componenti principali di Azure FarmBeats sono:

- **Datahub**: un livello API che consente l'aggregazione, la normalizzazione e la contestualizzazione di vari set di dati agricoli tra provider diversi.

- **Acceleratore:** applicazione Web basata su Datahub. Avvia lo sviluppo e la visualizzazione del modello. L'acceleratore usa le API FarmBeats di Azure per illustrare la visualizzazione dei dati dei sensori ingeriti come grafici e la visualizzazione dell'output del modello come mappe.

## <a name="general-information"></a>Informazioni generali

### <a name="components-installed"></a>Componenti installati

Quando si installa Azure FarmBeats, viene eseguito il provisioning delle risorse seguenti nella sottoscrizione di Azure:When you install Azure FarmBeats, the following resources are provisioned in your Azure subscription:

| Azure Resources Installed  | Componente FarmBeats di AzureAzure FarmBeats component  |
|---------|---------|
| Application Insights   |      Acceleratore di & hub dati      |
| Servizio app     |     Acceleratore di & hub dati     |
| Piano di servizio app   | Acceleratore di & hub dati  |
| Connessione API    |  Datahub       |
| Cache Redis di Azure       | Datahub      |
| Azure Cosmos DB   |  Datahub       |
| Azure Data Factory V2       |     Acceleratore di & hub dati      |
| Account Azure Batch    | Datahub   |
| Insieme di credenziali chiave di Azure |  Acceleratore di & hub dati        |
| Account di Mappe di Azure       |     Acceleratore    |
| Spazio dei nomi dell'hub eventiEvent Hub Namespace    |     Datahub      |
| App per la logica      |  Datahub       |
| Account di archiviazione      |     Acceleratore di & hub dati      |
| Time Series Insights     |    Datahub    |

### <a name="costs-incurred"></a>Costi sostenuti

Il costo di Azure FarmBeats è un'aggregazione del costo dei servizi di Azure sottostanti. Le informazioni sui prezzi per i servizi di Azure possono essere calcolate usando il [Calcolatore dei prezzi.](https://azure.microsoft.com/pricing/calculator) Il costo effettivo dell'installazione totale varia in base all'utilizzo. Il costo dello stato costante per i due componenti è:

- Datahub - meno di 10 dollari al giorno
- Acceleratore - meno di 2 dollari al giorno

### <a name="regions-supported"></a>Aree supportate

Attualmente, Azure FarmBeats è supportato in ambienti cloud pubblici nelle aree seguenti:Currently, Azure FarmBeats is supported in public cloud environments in the following regions:

- Australia orientale
- Stati Uniti centrali
- Stati Uniti orientali
- Stati Uniti orientali 2
- Stati Uniti occidentali
- Stati Uniti occidentali 2
- Europa settentrionale
- Europa occidentale
- Asia orientale
- Asia sudorientale

### <a name="time-taken"></a>Tempo impiegato

L'intera configurazione di Azure FarmBeats, inclusa la preparazione e l'installazione richiederà meno di un'ora.

## <a name="prerequisites"></a>Prerequisiti

È necessario completare i passaggi seguenti prima di avviare l'installazione effettiva di Azure FarmBeats:

### <a name="verify-permissions"></a>Verificare le autorizzazioni

Per installare Azure FarmBeats sono necessarie le autorizzazioni seguenti nel tenant di Azure:You'll need the following permissions in the Azure tenant to install Azure FarmBeats:

- Tenant - Creatore di app AAD
- Abbonamento - Proprietario
- Gruppo di risorse in cui viene installato FarmBeats - ProprietarioResource Group in which FarmBeats is being installed - Owner

Le prime due autorizzazioni sono necessarie per la creazione del passaggio [dell'applicazione AAD.](#create-an-aad-application) Se necessario, è possibile ottenere un utente con le autorizzazioni appropriate per creare l'applicazione AAD.

La persona che esegue l'installazione di FarmBeats dal marketplace deve essere proprietaria del gruppo di risorse in cui viene installato FarmBeats. Per i proprietari della sottoscrizione, ciò si verifica automaticamente quando viene creato il gruppo di risorse. Per altri utenti, pre-creare il gruppo di risorse e chiedere al proprietario della sottoscrizione di rendere l'utente proprietario del gruppo di risorse.

È possibile verificare le autorizzazioni di accesso nel portale di Azure seguendo le istruzioni sul controllo degli accessi [basato sui](https://docs.microsoft.com/azure/role-based-access-control/check-access)ruoli.

### <a name="decide-subscription-and-region"></a>Decidere abbonamento e regioneDecide Subscription and Region

Avrai bisogno dell'ID sottoscrizione di Azure e dell'area in cui vuoi installare Azure FarmBeats. Scegliere una delle aree elencate nella sezione [Aree supportate.](#regions-supported)

Prendere nota **dell'ID sottoscrizione** di Azure e dell'area di **Azure.**

### <a name="create-an-aad-application"></a>Creare un'applicazione AADCreate an AAD application

Azure FarmBeats richiede la creazione e la registrazione delle applicazioni di Azure Active Directory.Azure FarmBeats require Azure Active Directory application creation and registration. Per eseguire correttamente lo script di creazione AAD, sono necessarie le autorizzazioni seguenti:

- Tenant - Creatore di app AAD
- Abbonamento - Proprietario

Eseguire i passaggi seguenti in un'istanza di Cloud Shell usando l'ambiente PowerShell.Run the following steps in a Cloud Shell instance using the PowerShell environment. Verrà richiesto agli utenti per la prima volta di selezionare una sottoscrizione e creare un account di archiviazione. Completare la configurazione come indicato.

1. Scaricare lo [script del generatore di app AADDownload](https://aka.ms/FarmBeatsAADScript) the AAD app generator script

    ```azurepowershell-interactive
        wget -q https://aka.ms/FarmBeatsAADScript -O ./create_aad_script.ps1
    ```

2. Per impostazione predefinita, il file viene scaricato nella home directory. Passare alla directory.

    ```azurepowershell-interactive
        cd
    ```

3. Eseguire lo script AAD

    ```azurepowershell-interactive
        ./create_aad_script.ps1
    ```

4. Lo script richiede i seguenti tre input:

    - **Nome sito Web FarmBeats**: Questo è il prefisso URL univoco per l'applicazione web FarmBeats. Nel caso in cui il prefisso è già preso, lo script sarà errore. Una volta installata, la distribuzione di\<FarmBeats sarà accessibile da https:// FarmBeats-nome-sito Web>.azurewebsites.net e le API swagger saranno in https://\<FarmBeats-nome-sito web>-api.azurewebsites.net

    - **ID di accesso**di Azure: specificare l'ID di accesso di Azure per l'utente che si vuole aggiungere come amministratore di FarmBeats.Azure login ID : Provide Azure login ID for the user that you want to be added as admin of FarmBeats. Questo utente può quindi concedere l'accesso per accedere all'applicazione Web FarmBeats ad altri utenti. L'ID di accesso john.doe@domain.comè generalmente nel formato . È supportato anche l'UPN di Azure.Azure UPN is also supported.

    - **ID sottoscrizione:** ID della sottoscrizione in cui si vuole installare Azure FarmBeats

5. L'esecuzione dello script AAD richiede circa 2 minuti e restituisce i valori sullo schermo e in un file json nella stessa directory. Se qualcun altro ha eseguito lo script, chiedigli di condividere questo output con te.

### <a name="create-sentinel-account"></a>Crea account Sentinel

L'installazione di Azure FarmBeats consente di ottenere immagini satellitari dalla missione satellitare [Sentinel-2](https://scihub.copernicus.eu/) dell'Agenzia spaziale europea per la farm. Per configurare questa configurazione, è necessario un account Sentinel.

Segui i passaggi per creare un account gratuito con Sentinel:

1. Vai alla pagina di [iscrizione](https://aka.ms/SentinelRegistration) ufficiale.
2. Fornire i dettagli richiesti (nome, cognome, nome utente, password e ID e-mail) e completare il modulo.
3. Un link di verifica viene inviato all'ID e-mail registrato. Seleziona il link fornito nell'e-mail e completa la verifica.

Il processo di registrazione è completo. Prendere nota del **nome utente Sentinel** e della **password Sentinel**, una volta completata anche la verifica.

## <a name="install"></a>Installazione di

È ora possibile installare FarmBeats. Attenersi alla seguente procedura per avviare l'installazione:

1. Accedere al portale di Azure. Selezionare l'account nell'angolo superiore destro e passare al tenant di Azure AD in cui si vuole installare Azure FarmBeats.Select your account in the top-right corner and switch to the Azure AD tenant where you want to install Azure FarmBeats.

2. Passare ad Azure Marketplace all'interno del portale e cercare **Azure FarmBeats** nel Marketplace.

3. Viene visualizzata una nuova finestra con una panoramica di Azure FarmBeats.New window with an overview of Azure FarmBeats appears. Selezionare **Crea**.

4. Viene visualizzata una nuova finestra. Completare il processo di iscrizione scegliendo la sottoscrizione, il gruppo di risorse e il percorso corretti in cui si vuole installare Azure FarmBeats.Complete the sign-up process by choosing the correct subscription, resource group, and location to which you want to install Azure FarmBeats.

5. Specificare l'indirizzo di posta elettronica che deve ricevere tutti gli avvisi di servizio correlati ad Azure FarmBeats nella sezione **FarmBeats Service Alerts.** Selezionare **Avanti** nella parte inferiore della pagina per passare alla scheda **Dipendenze.**

    ![Scheda Nozioni di base](./media/install-azure-farmbeats/create-azure-farmbeats-basics.png)

6. Copiare le singole voci dall'output dello [script AAD](#create-an-aad-application) agli input nella sezione Applicazione AAD.

7. Immettere il nome utente e la password [dell'account Sentinel](#create-sentinel-account) nella sezione Account Sentinel. Selezionare **Avanti** per passare alla scheda **Revisione e creazione.**

    ![scheda Dipendenze](./media/install-azure-farmbeats/create-azure-farmbeats-dependencies.png)

8. Una volta convalidati i dettagli immessi, selezionare **OK**. Viene visualizzata la pagina Condizioni per l'utilizzo. Rivedere i termini e selezionare **Crea** per avviare l'installazione. Si verrà reindirizzati alla pagina in cui è possibile seguire l'avanzamento dell'installazione.

Una volta completata l'installazione, è possibile verificare l'installazione e iniziare a utilizzare\<il portale FarmBeats passando al nome del sito Web fornito durante l'installazione: https:// FarmBeats-website-name>.azurewebsites.net. Verrà visualizzata l'interfaccia utente di FarmBeats con un'opzione per creare farm.

**Datahub** è disponibile\<all'indirizzo https:// FarmBeats-website-name>-api.azurewebsites.net/swagger. Qui vedrai i diversi oggetti API FarmBeats ed eseguirai operazioni REST sulle API.

## <a name="upgrade"></a>Aggiornamento

Per aggiornare FarmBeats alla versione più recente, eseguire la procedura seguente in un'istanza di Cloud Shell usando l'ambiente PowerShell.To upgrade FarmBeats to the latest version, run the following steps in a Cloud Shell instance using the PowerShell environment. L'utente dovrà essere il proprietario della sottoscrizione in cui è installato FarmBeats.

Verrà richiesto agli utenti per la prima volta di selezionare una sottoscrizione e creare un account di archiviazione. Completare la configurazione come indicato.

1. Scaricare lo [script di aggiornamento](https://aka.ms/FarmBeatsUpgradeScript)

    ```azurepowershell-interactive
        wget –q https://aka.ms/FarmBeatsUpgradeScript -O ./upgrade-farmbeats.ps1
    ```

2. Per impostazione predefinita, il file viene scaricato nella home directory. Passare alla directory.

    ```azurepowershell-interactive
        cd
    ```

3. Eseguire lo script di aggiornamento

    ```azurepowershell-interactive
        ./upgrade-farmbeats.ps1 -InputFilePath [Path to input.json file]
    ```

Il percorso del file input.json è facoltativo. Se non specificato, lo script richiederà tutti gli input richiesti. L'aggiornamento dovrebbe terminare in circa 30 minuti.

## <a name="uninstall"></a>Disinstallare

Per disinstallare Azure FarmBeats Datahub o Accelerator, completare i passaggi seguenti:To uninstall Azure FarmBeats Datahub or Accelerator, complete the following steps:

1. Accedere al portale di Azure ed **eliminare i gruppi** di risorse in cui sono installati questi componenti.

2. Passare ad Azure Active Directory & **eliminare l'applicazione Azure AD** collegata all'installazione di Azure FarmBeats.Go to Azure Active Directory & delete the Azure AD application linked to the Azure FarmBeats installation.

## <a name="next-steps"></a>Passaggi successivi

Si è appreso come installare Azure FarmBeats nella sottoscrizione di Azure.You have learned how to install Azure FarmBeats in your Azure subscription. A questo punto, informazioni su come aggiungere utenti all'istanza di Azure FarmBeats.Now, learn how to [add users](manage-users-in-azure-farmbeats.md#manage-users) to your Azure FarmBeats instance.
