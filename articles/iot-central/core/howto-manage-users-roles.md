---
title: Gestire utenti e ruoli nell'applicazione Azure IoT Central . Documenti Microsoft
description: In qualità di amministratore, come gestire utenti e ruoli nell'applicazione Azure IoT Central
author: lmasieri
ms.author: lmasieri
ms.date: 12/05/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: c00f9d8baa55ef0d0cf6322ee71f22e739e6acdc
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365509"
---
# <a name="manage-users-and-roles-in-your-iot-central-application"></a>Gestire utenti e ruoli nell'applicazione IoT Central

Questo articolo descrive come, come amministratore, è possibile aggiungere, modificare ed eliminare utenti nell'applicazione Azure IoT Central.This article describes how, as an administrator, you can add, edit, and delete users in your Azure IoT Central application. L'articolo descrive anche come gestire i ruoli nell'applicazione Azure IoT Central.The article also describes how to manage roles in your Azure IoT Central application.

Per accedere alla sezione **Amministrazione** e usarla, è necessario disporre del ruolo **Amministratore** per l'applicazione Azure IoT Central. Se si crea un'applicazione Azure IoT Central, si viene aggiunti automaticamente al ruolo **di amministratore** per tale applicazione.

## <a name="add-users"></a>Aggiungere utenti

Ogni utente deve avere un account utente prima di poter accedere a un'applicazione Azure IoT Central. Microsoft Accounts and Azure Active Directory accounts are supported in Azure IoT Central. I gruppi di Azure Active Directory non sono attualmente supportati in Azure IoT Central.

Per altre informazioni, vedere [Guida di Account Microsoft](https://support.microsoft.com/products/microsoft-account?category=manage-account) e [Guida introduttiva: Aggiungere nuovi utenti ad Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

1. Per aggiungere un utente a un'applicazione IoT Central, passare alla pagina **Utenti** nella sezione **Amministrazione**.
    
    > [!div class="mx-imgBorder"]
    >![Gestire gli utenti](media/howto-manage-users-roles/manage-users-pnp.png)

1. Per aggiungere un utente, nella pagina **Utenti** scegliere **+ Aggiungi utente**.

1. Scegliere un ruolo per l'utente dal menu a discesa **Ruolo**. Per altre informazioni sui ruoli, vedere la sezione [Gestire i ruoli](#manage-roles) in questo articolo.

    > [!div class="mx-imgBorder"]
    >![Aggiungere un utente e selezionare un ruoloAdd user and select a role](media/howto-manage-users-roles/add-user-pnp.png)

    > [!NOTE]
    > Un utente che risieda un ruolo personalizzato che concede loro l'autorizzazione per aggiungere altri utenti può aggiungere solo utenti a un ruolo con autorizzazioni uguali o inferiori rispetto al proprio ruolo.

Se un ID utente di IoT Central viene eliminato da Azure Active Directory e quindi aggiunto nuovamente, l'utente non sarà in grado di accedere all'applicazione IoT Central.If an IoT Central user ID is deleted from Azure Active Directory and then readded, the user's not be able to sign in the IoT Central application. Per riattivare l'accesso, l'amministratore di IoT Central deve eliminare e aggiungere nuovamente l'utente nell'applicazione.

### <a name="edit-the-roles-that-are-assigned-to-users"></a>Modificare i ruoli assegnati agli utenti

I ruoli non possono essere modificati dopo che sono stati assegnati. Per modificare il ruolo assegnato a un utente, eliminare l'utente e aggiungerlo nuovamente con un ruolo diverso.

> [!NOTE]
> The roles assigned are specific to IoT Central application and cannot be managed from the Azure Portal.

## <a name="delete-users"></a>Eliminare gli utenti

Per eliminare gli utenti, selezionare una o più caselle di controllo nella pagina **Utenti**. Quindi selezionare **Elimina**.

## <a name="manage-roles"></a>Gestire i ruoli

I ruoli consentono di controllare chi all'interno dell'organizzazione è autorizzato a eseguire varie attività in IoT Central. Esistono tre ruoli predefiniti che è possibile assegnare agli utenti dell'applicazione. È inoltre possibile [creare ruoli personalizzati](#create-a-custom-role) se è necessario un controllo con granularità più fine.

> [!div class="mx-imgBorder"]
> ![Gestire la selezione dei ruoli](media/howto-manage-users-roles/manage-roles-pnp.png)

### <a name="administrator"></a>Amministratore

Gli utenti con il ruolo **Amministratore** possono gestire e controllare ogni parte dell'applicazione, inclusa la fatturazione.

All'utente che ha creato un'applicazione viene assegnato automaticamente il ruolo **Amministratore**. Ci deve sempre essere almeno un utente con il ruolo **Administrator** (Amministratore).

### <a name="builder"></a>Generatore

Gli utenti nel ruolo **Generatore** possono gestire ogni parte dell'app, ma non possono apportare modifiche nelle schede Amministrazione o Esportazione dati continua.

### <a name="operator"></a>Operatore

Gli utenti con il ruolo **Operatore** possono monitorare l'integrità e lo stato del dispositivo. Non è consentito apportare modifiche ai modelli di dispositivo o amministrare l'applicazione. Gli operatori possono aggiungere ed eliminare dispositivi, gestire set di dispositivi ed eseguire analisi e processi. 

## <a name="create-a-custom-role"></a>Creare un ruolo personalizzato

Se la soluzione richiede controlli di accesso con granularità più fine, è possibile creare ruoli personalizzati con set personalizzati di autorizzazioni. Per creare un ruolo personalizzato, passare alla pagina **Ruoli** nella sezione **Amministrazione** dell'applicazione. Quindi selezionare **Nuovo ruolo**e aggiungere un nome e una descrizione per il ruolo. Selezionare le autorizzazioni necessarie per il ruolo, quindi selezionare **Salva**.

È possibile aggiungere utenti al ruolo personalizzato nello stesso modo in cui si aggiungono utenti a un ruolo predefinito.

> [!div class="mx-imgBorder"]
> ![Creare un ruolo personalizzato](media/howto-manage-users-roles/create-custom-role-pnp.png)

### <a name="custom-role-options"></a>Opzioni di ruolo personalizzate

Quando si definisce un ruolo personalizzato, si sceglie il set di autorizzazioni che un utente viene concesso se è un membro del ruolo. Alcune autorizzazioni dipendono da altre. Ad esempio, se si aggiunge l'autorizzazione **Aggiorna dashboard dell'applicazione** a un ruolo, l'autorizzazione **Visualizza dashboard dell'applicazione** viene aggiunta automaticamente. Nelle tabelle seguenti sono riepilogate le autorizzazioni disponibili e le relative dipendenze che è possibile utilizzare durante la creazione di ruoli personalizzati.

#### <a name="managing-devices"></a>Gestione dei dispositivi

**Autorizzazioni del modello di dispositivo**

| Nome | Dependencies |
| ---- | -------- |
| Visualizza | nessuno     |
| Gestione | Visualizza <br/> Altre dipendenze: visualizzare le istanze del dispositivoOther dependencies: View device instances  |
| Controllo completo | Visualizza, Gestisci <br/> Altre dipendenze: visualizzare le istanze del dispositivoOther dependencies: View device instances |

**Autorizzazioni dell'istanza del dispositivoDevice instance permissions**

| Nome | Dependencies |
| ---- | -------- |
| Visualizza | nessuno <br/> Altre dipendenze: visualizzare i modelli di dispositivo e i gruppi di dispositiviOther dependencies: View device templates and device groups |
| Aggiornamento | Visualizza <br/> Altre dipendenze: visualizzare i modelli di dispositivo e i gruppi di dispositiviOther dependencies: View device templates and device groups  |
| Create | Visualizza <br/> Altre dipendenze: visualizzare i modelli di dispositivo e i gruppi di dispositiviOther dependencies: View device templates and device groups  |
| Delete | Visualizza <br/> Altre dipendenze: visualizzare i modelli di dispositivo e i gruppi di dispositiviOther dependencies: View device templates and device groups  |
| Comandi di esecuzione | Aggiorna, Visualizza <br/> Altre dipendenze: visualizzare i modelli di dispositivo e i gruppi di dispositiviOther dependencies: View device templates and device groups  |
| Controllo completo | Comandi di visualizzazione, aggiornamento, creazione, eliminazione, esecuzione <br/> Altre dipendenze: visualizzare i modelli di dispositivo e i gruppi di dispositiviOther dependencies: View device templates and device groups  |

**Autorizzazioni per i gruppi di dispositivi**

| Nome | Dependencies |
| ---- | -------- |
| Visualizza | nessuno <br/> Altre dipendenze: visualizzare i modelli di dispositivo e le istanze dei dispositiviOther dependencies: View device templates and device instances |
| Aggiornamento | Visualizza <br/> Altre dipendenze: visualizzare i modelli di dispositivo e le istanze dei dispositiviOther dependencies: View device templates and device instances   |
| Create | Visualizza, Aggiorna <br/> Altre dipendenze: visualizzare i modelli di dispositivo e le istanze dei dispositiviOther dependencies: View device templates and device instances   |
| Delete | Visualizza <br/> Altre dipendenze: visualizzare i modelli di dispositivo e le istanze dei dispositiviOther dependencies: View device templates and device instances   |
| Controllo completo | Visualizza, Aggiorna, Crea, Elimina <br/> Altre dipendenze: visualizzare i modelli di dispositivo e le istanze dei dispositiviOther dependencies: View device templates and device instances |

**Autorizzazioni di gestione della connettività dei dispositivi**

| Nome | Dependencies |
| ---- | -------- |
| Esempio di lettura dell'istanza | nessuno <br/> Altre dipendenze: visualizzare modelli di dispositivo, gruppi di dispositivi, istanze di dispositivoOther dependencies: View device templates, device groups, device instances |
| Gestire l'istanza | nessuno |
| Leggi globale | nessuno   |
| Gestire globale | Leggi Globale |
| Controllo completo | Leggere istanza, Gestisci istanza, Leggi globale, Gestisci globale. <br/> Altre dipendenze: visualizzare modelli di dispositivo, gruppi di dispositivi, istanze di dispositivoOther dependencies: View device templates, device groups, device instances |

**Autorizzazioni per i processi**

| Nome | Dependencies |
| ---- | -------- |
| Visualizza | nessuno <br/> Altre dipendenze: visualizzare i modelli di dispositivo, le istanze dei dispositivi e i gruppi di dispositiviOther dependencies: View device templates, device instances, and device groups |
| Aggiornamento | Visualizza <br/> Altre dipendenze: visualizzare i modelli di dispositivo, le istanze dei dispositivi e i gruppi di dispositiviOther dependencies: View device templates, device instances, and device groups |
| Create | Visualizza, Aggiorna <br/> Altre dipendenze: visualizzare i modelli di dispositivo, le istanze dei dispositivi e i gruppi di dispositiviOther dependencies: View device templates, device instances, and device groups |
| Delete | Visualizza <br/> Altre dipendenze: visualizzare i modelli di dispositivo, le istanze dei dispositivi e i gruppi di dispositiviOther dependencies: View device templates, device instances, and device groups |
| Execute | Visualizza <br/> Altre dipendenze: visualizzare i modelli di dispositivo, le istanze dei dispositivi e i gruppi di dispositivi; Aggiornare le istanze del dispositivo; Eseguire comandi sulle istanze del dispositivoExecute commands on device instances |
| Controllo completo | Visualizza, Aggiorna, Crea, Elimina, Esegui <br/> Altre dipendenze: visualizzare i modelli di dispositivo, le istanze dei dispositivi e i gruppi di dispositivi; Aggiornare le istanze del dispositivo; Eseguire comandi sulle istanze del dispositivoExecute commands on device instances |

**Autorizzazioni per le regole**

| Nome | Dependencies |
| ---- | -------- |
| Visualizza | nessuno <br/> Altre dipendenze: visualizzare i modelli di dispositivoOther dependencies: View device templates |
| Aggiornamento | Visualizza <br/> Altre dipendenze: visualizzare i modelli di dispositivoOther dependencies: View device templates |
| Create | Visualizza, Aggiorna <br/> Altre dipendenze: visualizzare i modelli di dispositivoOther dependencies: View device templates |
| Delete | Visualizza <br/> Altre dipendenze: visualizzare i modelli di dispositivoOther dependencies: View device templates |
| Controllo completo | Visualizza, Aggiorna, Crea, Elimina <br/> Altre dipendenze: visualizzare i modelli di dispositivoOther dependencies: View device templates |

#### <a name="managing-the-app"></a>Gestione dell'app

**Autorizzazioni per le impostazioni dell'applicazione**

| Nome | Dependencies |
| ---- | -------- |
| Visualizza | nessuno     |
| Aggiornamento | Visualizza   |
| Copiare | Visualizza <br/> Altre dipendenze: visualizzare modelli di dispositivo, istanze di dispositivi, gruppi di dispositivi, dashboard, esportazione dati, personalizzazione, collegamenti della Guida, ruoli personalizzati, regole |
| Delete | Visualizza   |
| Controllo completo | Visualizza, Aggiorna, Copia, Elimina <br/> Altre dipendenze: visualizzare modelli di dispositivo, gruppi di dispositivi, dashboard dell'applicazione, esportazione dati, personalizzazione, collegamenti della Guida, ruoli personalizzati, regole |

**Autorizzazioni di esportazione del modello di applicazioneApplication template export permissions**

| Nome | Dependencies |
| ---- | -------- |
| Visualizza | nessuno     |
| Esportazione | Visualizza <br/> Altre dipendenze: visualizzare modelli di dispositivo, istanze di dispositivi, gruppi di dispositivi, dashboard, esportazione dati, personalizzazione, collegamenti della Guida, ruoli personalizzati, regole |
| Controllo completo | Visualizza, Esporta <br/> Altre dipendenze: visualizzare modelli di dispositivo, gruppi di dispositivi, dashboard dell'applicazione, esportazione dati, personalizzazione, collegamenti della Guida, ruoli personalizzati, regole |

**Autorizzazioni di fatturazione**

| Nome | Dependencies |
| ---- | -------- |
| Gestione | nessuno     |
| Controllo completo | Gestione |

#### <a name="managing-users-and-roles"></a>Gestione di utenti e ruoli

**Autorizzazioni per i ruoli personalizzati**

| Nome | Dependencies |
| ---- | -------- |
| Visualizza | nessuno |
| Aggiornamento | Visualizza |
| Create | Visualizza, Aggiorna |
| Delete | Visualizza |
| Controllo completo | Visualizza, Aggiorna, Crea, Elimina |

**Autorizzazioni di gestione utenti**

| Nome | Dependencies |
| ---- | -------- |
| Visualizza | nessuno <br/> Altre dipendenze: visualizzare ruoli personalizzati |
| Add | Visualizza <br/> Altre dipendenze: visualizzare ruoli personalizzati |
| Delete | Visualizza <br/> Altre dipendenze: visualizzare ruoli personalizzati |
| Controllo completo | Visualizza, Aggiungi, Elimina <br/> Altre dipendenze: visualizzare ruoli personalizzati |

> [!NOTE]
> Un utente che risieda un ruolo personalizzato che concede loro l'autorizzazione per aggiungere altri utenti può aggiungere solo utenti a un ruolo con autorizzazioni uguali o inferiori rispetto al proprio ruolo.

#### <a name="customizing-the-app"></a>Personalizzazione dell'app

**Autorizzazioni del dashboard dell'applicazione**

| Nome | Dependencies |
| ---- | -------- |
| Visualizza | nessuno     |
| Aggiornamento | Visualizza   |
| Create | Visualizza, Aggiorna |
| Delete | Visualizza   |
| Controllo completo | Visualizza, Aggiorna, Crea, Elimina |

**Autorizzazioni dashboard personali**

| Nome | Dependencies |
| ---- | -------- |
| Visualizza | nessuno     |
| Aggiornamento | Visualizza   |
| Create | Visualizza, Aggiorna   |
| Delete | Visualizza   |
| Controllo completo | Visualizza, Aggiorna, Crea, Elimina |

**Autorizzazioni per personalizzazione, favicon e colori**

| Nome | Dependencies |
| ---- | -------- |
| Visualizza | nessuno     |
| Aggiornamento | Visualizza   |
| Controllo completo | Visualizza, Aggiorna |

**Autorizzazioni per i collegamenti della Guida**

| Nome | Dependencies |
| ---- | -------- |
| Visualizza | nessuno     |
| Aggiornamento | Visualizza   |
| Controllo completo | Visualizza, Aggiorna |

#### <a name="extending-the-app"></a>Estensione dell'app

**Autorizzazioni di esportazione dei dati**

| Nome | Dependencies |
| ---- | -------- |
| Visualizza | nessuno     |
| Aggiornamento | Visualizza   |
| Create | Visualizza, Aggiorna  |
| Delete | Visualizza   |
| Controllo completo | Visualizza, Aggiorna, Crea, Elimina |

**Autorizzazioni token API**

| Nome | Dependencies |
| ---- | -------- |
| Visualizza | nessuno     |
| Create | Visualizza   |
| Delete | Visualizza   |
| Controllo completo | Visualizza, Crea, Elimina |

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso come gestire gli utenti e i ruoli nell'applicazione Azure IoT Central, il passaggio successivo consigliato consiste nell'apprendere come [gestire la fattura.](howto-view-bill.md)
