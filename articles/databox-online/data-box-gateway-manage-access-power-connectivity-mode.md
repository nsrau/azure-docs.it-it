---
title: Accesso al dispositivo, alimentazione e connettività di Azure Data Box GatewayAzure Data Box Gateway device access, power, and connectivity mode
description: Descrive come gestire l'accesso, l'avvio/arresto e la modalità di connessione per il dispositivo Azure Data Box Gateway che permette di trasferire dati in Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: e4d85bd460c39964c9f42ac946e3522f5f129c1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474442"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-gateway"></a>Gestire la modalità di accesso, alimentazione e connettività per il gateway casella dati di AzureManage access, power, and connectivity mode for your Azure Data Box Gateway

Questo articolo descrive come gestire l'accesso, l'avvio/arresto e la modalità di connessione per Azure Data Box Gateway. Queste operazioni vengono eseguite tramite l'interfaccia utente Web locale o il portale di Azure.

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Gestire l'accesso al dispositivo
> * Gestire la modalità di connessione
> * Gestire l'avvio/arresto

## <a name="manage-device-access"></a>Gestire l'accesso al dispositivo

L'accesso al dispositivo Data Box Gateway è controllato dall'uso di una password del dispositivo. È possibile modificare la password tramite l'interfaccia utente Web locale. È anche possibile reimpostare la password del dispositivo nel portale di Azure.You can also reset the device password in the Azure portal.

### <a name="change-device-password"></a>Modificare la password del dispositivo

Seguire questi passaggi nell'interfaccia utente locale per modificare la password del dispositivo.

1. Nell'interfaccia utente Web locale passare a **Manutenzione > Modifica password**.
2. Immettere la password corrente e quindi quella nuova. La password specificata deve essere costituita da 8-16 caratteri. La password deve contenere almeno tre di questi caratteri: lettera maiuscola, lettera minuscola, numero e caratteri speciali. Confermare la nuova password.

    ![Cambia password](media/data-box-gateway-manage-access-power-connectivity-mode/change-password-1.png)

3. Fare clic su **Cambia password**.
 
### <a name="reset-device-password"></a>Reimpostare la password del dispositivo

Il flusso di lavoro di reimpostazione non richiede che l'utente ricordi la vecchia password ed è utile in caso di smarrimento della password. Questo flusso di lavoro viene eseguito nel portale di Azure.

1. Nel portale di Azure passare a **Panoramica > Reimposta password amministratore**.

    ![Reimposta password](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-1.png)

 
2. Immettere la nuova password e quindi confermarla. La password specificata deve essere costituita da 8-16 caratteri. La password deve contenere almeno tre di questi caratteri: lettera maiuscola, lettera minuscola, numero e caratteri speciali. Fare clic su **Reimposta**.

    ![Reimposta password](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>Gestire l'accesso alle risorse

Per creare il gateway Edge/Data Box, l'hub IoT e la risorsa di Archiviazione di Azure, sono necessarie autorizzazioni come collaboratore o versione successiva a livello di gruppo di risorse. È inoltre necessario registrare i provider di risorse corrispondenti. Per tutte le operazioni che prevedono la chiave di attivazione e le credenziali, sono necessarie anche le autorizzazioni per l'API Graph di Azure Active Directory.For any operations that involve activation key and credentials, permissions to Azure Active Directory Graph API are also required. Questi sono descritti nelle sezioni seguenti.

### <a name="manage-microsoft-graph-api-permissions"></a>Gestire le autorizzazioni API di Microsoft Graph

Quando si genera la chiave di attivazione per il dispositivo Data Box Edge o si eseguono operazioni che richiedono credenziali, sono necessarie le autorizzazioni per l'API Microsoft Graph. Le operazioni che richiedono credenziali potrebbero essere:

-  Creazione di una condivisione con un account di archiviazione associato.
-  Creazione di un utente che può accedere alle condivisioni nel dispositivo.

È necessario `User` disporre di un accesso nel tenant `Read all directory objects`di Active Directory in quanto è necessario essere in grado di . Non è possibile essere un utente Guest perché `Read all directory objects`non dispone delle autorizzazioni per . Se si è utenti guest, tutte le operazioni come la generazione di una chiave di attivazione, la creazione di una condivisione nel dispositivo Data Box Edge o la creazione di un utente hanno esito negativo.

Per ulteriori informazioni su come fornire l'accesso agli utenti all'API Microsoft Graph, vedere Informazioni di riferimento sulle autorizzazioni di [Microsoft Graph](https://docs.microsoft.com/graph/permissions-reference).

### <a name="register-resource-providers"></a>Registrare i provider di risorseRegister resource providers

Per eseguire il provisioning di una risorsa in Azure (nel modello di Azure Resource Manager), è necessario un provider di risorse che supporti la creazione di tale risorsa. Ad esempio, per eseguire il provisioning di una macchina virtuale, è necessario disporre di un provider di risorse 'Microsoft.Compute' disponibile nella sottoscrizione.
 
I provider di risorse sono registrati a livello di sottoscrizione. Per impostazione predefinita, qualsiasi nuova sottoscrizione di Azure è già registrata con un elenco di provider di risorse usate comunemente. Il provider di risorse per 'Microsoft.DataBoxEdge' non è incluso in questo elenco.

Non è necessario concedere autorizzazioni di accesso al livello di sottoscrizione per consentire agli utenti di creare risorse come 'Microsoft.DataBoxEdge' all'interno dei gruppi di risorse per cui dispongono dei diritti di proprietario, purché i provider di risorse per queste risorse siano già disponibili Registrato.

Prima di tentare di creare qualsiasi risorsa, assicurarsi che il provider di risorse sia registrato nella sottoscrizione. Se il provider di risorse non è registrato, è necessario assicurarsi che l'utente che crea la nuova risorsa disponga di diritti sufficienti per registrare il provider di risorse richiesto a livello di sottoscrizione. Se non l'hai fatto anche tu, vedrai il seguente errore:

*Il \<nome della sottoscrizione> non dispone delle autorizzazioni per registrare i provider di risorse: Microsoft.DataBoxEdge.The subscription Subscription name> doesn't have permissions to register the resource provider(s): Microsoft.DataBoxEdge.*


Per ottenere un elenco dei provider di risorse registrate nella sottoscrizione corrente, eseguire il comando seguente:

```PowerShell
Get-AzResourceProvider -ListAvailable |where {$_.Registrationstate -eq "Registered"}
```

Per il dispositivo `Microsoft.DataBoxEdge` Data Box Edge, deve essere registrato. Per `Microsoft.DataBoxEdge`registrare , admin della sottoscrizione deve eseguire il comando seguente:

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataBoxEdge
```

Per ulteriori informazioni su come registrare un provider di risorse, vedere Risolvere gli errori per la registrazione del [provider di risorse.](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors)

## <a name="manage-connectivity-mode"></a>Gestire la modalità di connessione

Oltre alla modalità normale predefinita, il dispositivo può anche essere eseguito in modalità parzialmente disconnessa o disconnessa. Ognuna di queste modalità viene descritta di seguito:

- **Parzialmente disconnesso**: in questa modalità il dispositivo non può caricare dati nelle condivisioni, ma può essere gestito tramite il portale di Azure.

    Questa modalità viene in genere usata in caso di rete satellitare a consumo e l'obiettivo consiste nel ridurre al minimo il consumo di larghezza di banda di rete. Può comunque avvenire un uso di rete minimo per le operazioni di monitoraggio del dispositivo.

- **Disconnesso**: in questa modalità il dispositivo è completamente disconnesso dal cloud e le operazioni di caricamento e download nel cloud sono entrambe disabilitate. Il dispositivo può essere gestito solo tramite l'interfaccia utente Web locale.

    Questa modalità viene usata in genere quando si vuole portare offline il dispositivo.

Per modificare la modalità del dispositivo, completare questi passaggi:

1. Nell'interfaccia utente Web locale del dispositivo passare a **Configurazione > Impostazioni del cloud**.
2. Disabilitare **Cloud upload and download** (Caricamento e download nel cloud).
3. Per eseguire il dispositivo in modalità parzialmente disconnessa, attivare **Azure portal management** (Gestione portale di Azure).

    ![Modalità di connessione](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-1.png)
 
4. Per eseguire il dispositivo in modalità parzialmente disconnessa, disabilitare **Azure portal management** (Gestione portale di Azure). A questo punto, il dispositivo può essere gestito solo tramite l'interfaccia utente Web locale.

    ![Modalità di connessione](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-2.png)

## <a name="manage-power"></a>Gestire l'avvio/arresto

È possibile arrestare o riavviare il dispositivo virtuale tramite l'interfaccia utente Web locale. Prima di riavviare, si consiglia di portare offline le condivisioni sull'host e quindi il dispositivo. Questa operazione riduce al minimo il rischio di danneggiamento dei dati.

1. Nell'interfaccia utente Web locale passare a **Manutenzione > Power settings** (Impostazioni di alimentazione).
2. Fare clic su **Arresta** o **Riavvia** in base all'operazione desiderata.

    ![Impostazioni di alimentazione](media/data-box-gateway-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Quando viene richiesta una conferma, fare clic su **Sì** per continuare.

> [!NOTE]
> Se si arresta il dispositivo virtuale, è necessario avviare il dispositivo tramite la gestione dell'hypervisor.
