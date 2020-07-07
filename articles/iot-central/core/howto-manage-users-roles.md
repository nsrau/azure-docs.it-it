---
title: Gestire utenti e ruoli nell'applicazione IoT Central di Azure | Microsoft Docs
description: Come amministratore, come gestire utenti e ruoli nell'applicazione IoT Central di Azure
author: lmasieri
ms.author: lmasieri
ms.date: 12/05/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: c00f9d8baa55ef0d0cf6322ee71f22e739e6acdc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80365509"
---
# <a name="manage-users-and-roles-in-your-iot-central-application"></a>Gestire utenti e ruoli nell'applicazione IoT Central

Questo articolo descrive come un amministratore può aggiungere, modificare ed eliminare utenti nell'applicazione IoT Central di Azure. Questo articolo descrive anche come gestire i ruoli nell'applicazione IoT Central di Azure.

Per accedere alla sezione **Amministrazione** e usarla, è necessario disporre del ruolo **Amministratore** per l'applicazione Azure IoT Central. Se si crea un'applicazione IoT Central di Azure, l'utente viene aggiunto automaticamente al ruolo di **amministratore** per l'applicazione.

## <a name="add-users"></a>Aggiungere utenti

Ogni utente deve avere un account utente prima di poter accedere a un'applicazione Azure IoT Central. Gli account Microsoft e Azure Active Directory sono supportati in Azure IoT Central. I gruppi di Azure Active Directory non sono attualmente supportati in Azure IoT Central.

Per altre informazioni, vedere [Guida di Account Microsoft](https://support.microsoft.com/products/microsoft-account?category=manage-account) e [Guida introduttiva: Aggiungere nuovi utenti ad Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

1. Per aggiungere un utente a un'applicazione IoT Central, passare alla pagina **Utenti** nella sezione **Amministrazione**.
    
    > [!div class="mx-imgBorder"]
    >![Gestire gli utenti](media/howto-manage-users-roles/manage-users-pnp.png)

1. Per aggiungere un utente, nella pagina **Utenti** scegliere **+ Aggiungi utente**.

1. Scegliere un ruolo per l'utente dal menu a discesa **Ruolo**. Per altre informazioni sui ruoli, vedere la sezione [Gestire i ruoli](#manage-roles) in questo articolo.

    > [!div class="mx-imgBorder"]
    >![Aggiungere un utente e selezionare un ruolo](media/howto-manage-users-roles/add-user-pnp.png)

    > [!NOTE]
    > Un utente che si trova in un ruolo personalizzato che concede loro l'autorizzazione ad aggiungere altri utenti può solo aggiungere utenti a un ruolo con le stesse autorizzazioni o un minor numero di autorizzazioni rispetto al proprio ruolo.

Se un IoT Central ID utente viene eliminato da Azure Active Directory e quindi aggiunto nuovamente, l'utente non sarà in grado di accedere all'applicazione IoT Central. Per abilitare nuovamente l'accesso, l'amministratore di IoT Central deve eliminare e leggere l'utente nell'applicazione.

### <a name="edit-the-roles-that-are-assigned-to-users"></a>Modificare i ruoli assegnati agli utenti

I ruoli non possono essere modificati dopo essere stati assegnati. Per modificare il ruolo assegnato a un utente, eliminare l'utente e aggiungerlo nuovamente con un ruolo diverso.

> [!NOTE]
> I ruoli assegnati sono specifici dell'applicazione IoT Central e non possono essere gestiti dal portale di Azure.

## <a name="delete-users"></a>Eliminare gli utenti

Per eliminare gli utenti, selezionare una o più caselle di controllo nella pagina **Utenti**. Selezionare **Elimina**.

## <a name="manage-roles"></a>Gestire i ruoli

I ruoli consentono di controllare chi all'interno dell'organizzazione è autorizzato a eseguire varie attività in IoT Central. Sono disponibili tre ruoli predefiniti che è possibile assegnare agli utenti dell'applicazione. È anche possibile [creare ruoli personalizzati](#create-a-custom-role) se è necessario un controllo con granularità fine.

> [!div class="mx-imgBorder"]
> ![Gestione Selezione ruoli](media/howto-manage-users-roles/manage-roles-pnp.png)

### <a name="administrator"></a>Amministratore

Gli utenti con il ruolo di **amministratore** possono gestire e controllare ogni parte dell'applicazione, inclusa la fatturazione.

All'utente che ha creato un'applicazione viene assegnato automaticamente il ruolo **Amministratore**. Ci deve sempre essere almeno un utente con il ruolo **Administrator** (Amministratore).

### <a name="builder"></a>Generatore

Gli utenti nel ruolo di **Generatore** possono gestire ogni parte dell'app, ma non possono apportare modifiche nelle schede amministrazione o esportazione dati continua.

### <a name="operator"></a>Operatore

Gli utenti del ruolo **operatore** possono monitorare l'integrità e lo stato dei dispositivi. Non è consentito apportare modifiche ai modelli di dispositivo o amministrare l'applicazione. Gli operatori possono aggiungere ed eliminare dispositivi, gestire set di dispositivi ed eseguire analisi e processi. 

## <a name="create-a-custom-role"></a>Creare un ruolo personalizzato

Se la soluzione richiede controlli di accesso con granularità fine, è possibile creare ruoli personalizzati con set personalizzati di autorizzazioni. Per creare un ruolo personalizzato, passare alla pagina **ruoli** nella sezione **Amministrazione** dell'applicazione. Quindi selezionare **+ nuovo ruolo**e aggiungere un nome e una descrizione per il ruolo. Selezionare le autorizzazioni necessarie per il ruolo e quindi fare clic su **Salva**.

È possibile aggiungere utenti al ruolo personalizzato nello stesso modo in cui si aggiungono gli utenti a un ruolo predefinito.

> [!div class="mx-imgBorder"]
> ![Creazione di un ruolo personalizzato](media/howto-manage-users-roles/create-custom-role-pnp.png)

### <a name="custom-role-options"></a>Opzioni del ruolo personalizzato

Quando si definisce un ruolo personalizzato, si sceglie il set di autorizzazioni che un utente viene concesso se è membro del ruolo. Alcune autorizzazioni dipendono da altri. Ad esempio, se si aggiunge l'autorizzazione **Aggiorna dashboard applicazione** a un ruolo, l'autorizzazione **Visualizza dashboard applicazione** viene aggiunta automaticamente. Nelle tabelle seguenti vengono riepilogate le autorizzazioni disponibili e le relative dipendenze, che è possibile utilizzare per la creazione di ruoli personalizzati.

#### <a name="managing-devices"></a>Gestione dei dispositivi

**Autorizzazioni per i modelli di dispositivo**

| Nome | Dependencies |
| ---- | -------- |
| Visualizzazione | nessuno     |
| Gestione | Visualizzazione <br/> Altre dipendenze: visualizzare le istanze del dispositivo  |
| Controllo completo | Visualizza, Gestisci <br/> Altre dipendenze: visualizzare le istanze del dispositivo |

**Autorizzazioni per l'istanza del dispositivo**

| Nome | Dependencies |
| ---- | -------- |
| Visualizzazione | nessuno <br/> Altre dipendenze: visualizzare i modelli di dispositivo e i gruppi di dispositivi |
| Aggiornamento | Visualizzazione <br/> Altre dipendenze: visualizzare i modelli di dispositivo e i gruppi di dispositivi  |
| Create | Visualizzazione <br/> Altre dipendenze: visualizzare i modelli di dispositivo e i gruppi di dispositivi  |
| Elimina | Visualizzazione <br/> Altre dipendenze: visualizzare i modelli di dispositivo e i gruppi di dispositivi  |
| Comandi Execute | Aggiorna, Visualizza <br/> Altre dipendenze: visualizzare i modelli di dispositivo e i gruppi di dispositivi  |
| Controllo completo | Visualizzare, aggiornare, creare, eliminare ed eseguire comandi <br/> Altre dipendenze: visualizzare i modelli di dispositivo e i gruppi di dispositivi  |

**Autorizzazioni per gruppi di dispositivi**

| Nome | Dependencies |
| ---- | -------- |
| Visualizzazione | nessuno <br/> Altre dipendenze: visualizzare i modelli di dispositivo e le istanze del dispositivo |
| Aggiornamento | Visualizzazione <br/> Altre dipendenze: visualizzare i modelli di dispositivo e le istanze del dispositivo   |
| Crea | Visualizza, aggiorna <br/> Altre dipendenze: visualizzare i modelli di dispositivo e le istanze del dispositivo   |
| Elimina | Visualizzazione <br/> Altre dipendenze: visualizzare i modelli di dispositivo e le istanze del dispositivo   |
| Controllo completo | Visualizzare, aggiornare, creare, eliminare <br/> Altre dipendenze: visualizzare i modelli di dispositivo e le istanze del dispositivo |

**Autorizzazioni di gestione della connettività del dispositivo**

| Nome | Dependencies |
| ---- | -------- |
| Lettura istanza | nessuno <br/> Altre dipendenze: visualizzare i modelli di dispositivo, i gruppi di dispositivi, le istanze del dispositivo |
| Gestisci istanza | nessuno |
| Lettura globale | nessuno   |
| Gestione globale | Lettura globale |
| Controllo completo | Lettura istanza, Gestisci istanza, lettura globale, Gestisci globale. <br/> Altre dipendenze: visualizzare i modelli di dispositivo, i gruppi di dispositivi, le istanze del dispositivo |

**Autorizzazioni processi**

| Nome | Dependencies |
| ---- | -------- |
| Visualizzazione | nessuno <br/> Altre dipendenze: visualizzare i modelli di dispositivo, le istanze del dispositivo e i gruppi di dispositivi |
| Aggiornamento | Visualizzazione <br/> Altre dipendenze: visualizzare i modelli di dispositivo, le istanze del dispositivo e i gruppi di dispositivi |
| Crea | Visualizza, aggiorna <br/> Altre dipendenze: visualizzare i modelli di dispositivo, le istanze del dispositivo e i gruppi di dispositivi |
| Elimina | Visualizzazione <br/> Altre dipendenze: visualizzare i modelli di dispositivo, le istanze del dispositivo e i gruppi di dispositivi |
| Execute | Visualizzazione <br/> Altre dipendenze: visualizzare i modelli di dispositivo, le istanze del dispositivo e i gruppi di dispositivi; Aggiornare le istanze del dispositivo; Eseguire comandi sulle istanze del dispositivo |
| Controllo completo | Visualizzare, aggiornare, creare, eliminare ed eseguire <br/> Altre dipendenze: visualizzare i modelli di dispositivo, le istanze del dispositivo e i gruppi di dispositivi; Aggiornare le istanze del dispositivo; Eseguire comandi sulle istanze del dispositivo |

**Autorizzazioni per le regole**

| Nome | Dependencies |
| ---- | -------- |
| Visualizzazione | nessuno <br/> Altre dipendenze: visualizzare i modelli di dispositivo |
| Aggiornamento | Visualizzazione <br/> Altre dipendenze: visualizzare i modelli di dispositivo |
| Crea | Visualizza, aggiorna <br/> Altre dipendenze: visualizzare i modelli di dispositivo |
| Elimina | Visualizzazione <br/> Altre dipendenze: visualizzare i modelli di dispositivo |
| Controllo completo | Visualizzare, aggiornare, creare, eliminare <br/> Altre dipendenze: visualizzare i modelli di dispositivo |

#### <a name="managing-the-app"></a>Gestione dell'app

**Autorizzazioni per le impostazioni dell'applicazione**

| Nome | Dependencies |
| ---- | -------- |
| Visualizzazione | nessuno     |
| Aggiornamento | Visualizzazione   |
| Copia | Visualizzazione <br/> Altre dipendenze: visualizzare i modelli di dispositivo, le istanze di dispositivo, i gruppi di dispositivi, i dashboard, l'esportazione dei dati, la personalizzazione, i collegamenti guida, i ruoli personalizzati, le regole |
| Elimina | Visualizzazione   |
| Controllo completo | Visualizzazione, aggiornamento, copia, eliminazione <br/> Altre dipendenze: visualizzare modelli di dispositivi, gruppi di dispositivi, dashboard dell'applicazione, esportazione dei dati, personalizzazione, collegamenti alla guida, ruoli personalizzati, regole |

**Autorizzazioni esportazione modello applicazione**

| Nome | Dependencies |
| ---- | -------- |
| Visualizzazione | nessuno     |
| Esportazione | Visualizzazione <br/> Altre dipendenze: visualizzare i modelli di dispositivo, le istanze di dispositivo, i gruppi di dispositivi, i dashboard, l'esportazione dei dati, la personalizzazione, i collegamenti guida, i ruoli personalizzati, le regole |
| Controllo completo | Visualizza, Esporta <br/> Altre dipendenze: visualizzare modelli di dispositivi, gruppi di dispositivi, dashboard dell'applicazione, esportazione dei dati, personalizzazione, collegamenti alla guida, ruoli personalizzati, regole |

**Autorizzazioni di fatturazione**

| Nome | Dependencies |
| ---- | -------- |
| Gestione | nessuno     |
| Controllo completo | Gestione |

#### <a name="managing-users-and-roles"></a>Gestione di utenti e ruoli

**Autorizzazioni per ruoli personalizzati**

| Nome | Dependencies |
| ---- | -------- |
| Visualizzazione | nessuno |
| Aggiornamento | Visualizzazione |
| Crea | Visualizza, aggiorna |
| Elimina | Visualizzazione |
| Controllo completo | Visualizzare, aggiornare, creare, eliminare |

**Autorizzazioni di gestione dell'utente**

| Nome | Dependencies |
| ---- | -------- |
| Visualizzazione | nessuno <br/> Altre dipendenze: visualizzare i ruoli personalizzati |
| Aggiunta | Visualizzazione <br/> Altre dipendenze: visualizzare i ruoli personalizzati |
| Elimina | Visualizzazione <br/> Altre dipendenze: visualizzare i ruoli personalizzati |
| Controllo completo | Visualizza, Aggiungi, Elimina <br/> Altre dipendenze: visualizzare i ruoli personalizzati |

> [!NOTE]
> Un utente che si trova in un ruolo personalizzato che concede loro l'autorizzazione ad aggiungere altri utenti può solo aggiungere utenti a un ruolo con le stesse autorizzazioni o un minor numero di autorizzazioni rispetto al proprio ruolo.

#### <a name="customizing-the-app"></a>Personalizzazione dell'app

**Autorizzazioni del dashboard dell'applicazione**

| Nome | Dependencies |
| ---- | -------- |
| Visualizzazione | nessuno     |
| Aggiornamento | Visualizzazione   |
| Crea | Visualizza, aggiorna |
| Elimina | Visualizzazione   |
| Controllo completo | Visualizzare, aggiornare, creare, eliminare |

**Autorizzazioni per Dashboard personali**

| Nome | Dependencies |
| ---- | -------- |
| Visualizzazione | nessuno     |
| Aggiornamento | Visualizzazione   |
| Crea | Visualizza, aggiorna   |
| Elimina | Visualizzazione   |
| Controllo completo | Visualizzare, aggiornare, creare, eliminare |

**Autorizzazioni per personalizzazione, favicon e colori**

| Nome | Dependencies |
| ---- | -------- |
| Visualizzazione | nessuno     |
| Aggiornamento | Visualizzazione   |
| Controllo completo | Visualizza, aggiorna |

**Autorizzazioni collegamenti Guida**

| Nome | Dependencies |
| ---- | -------- |
| Visualizzazione | nessuno     |
| Aggiornamento | Visualizzazione   |
| Controllo completo | Visualizza, aggiorna |

#### <a name="extending-the-app"></a>Estensione dell'app

**Autorizzazioni esportazione dati**

| Nome | Dependencies |
| ---- | -------- |
| Visualizzazione | nessuno     |
| Aggiornamento | Visualizzazione   |
| Crea | Visualizza, aggiorna  |
| Elimina | Visualizzazione   |
| Controllo completo | Visualizzare, aggiornare, creare, eliminare |

**Autorizzazioni token API**

| Nome | Dependencies |
| ---- | -------- |
| Visualizzazione | nessuno     |
| Create | Visualizzazione   |
| Elimina | Visualizzazione   |
| Controllo completo | Visualizza, crea, Elimina |

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come gestire utenti e ruoli nell'applicazione IoT Central di Azure, il passaggio successivo suggerito consiste nell'apprendere come [gestire la fattura](howto-view-bill.md).
