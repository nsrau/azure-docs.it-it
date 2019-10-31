---
title: Gestire utenti e ruoli nell'applicazione IoT Central di Azure | Microsoft Docs
description: Come amministratore, come gestire utenti e ruoli nell'applicazione IoT Central di Azure
author: lmasieri
ms.author: lmasieri
ms.date: 10/22/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 17dd4862a02a736237257c50802135fb62883d77
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2019
ms.locfileid: "73200607"
---
# <a name="manage-users-and-roles-in-your-iot-central-application-preview-features"></a>Gestire utenti e ruoli nell'applicazione IoT Central (funzionalità di anteprima)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Questo articolo descrive come un amministratore può aggiungere, modificare ed eliminare utenti nell'applicazione IoT Central di Azure. Questo articolo descrive anche come gestire i ruoli nell'applicazione IoT Central di Azure.

Per accedere alla sezione **Amministrazione** e usarla, è necessario disporre del ruolo **Amministratore** per l'applicazione Azure IoT Central. Se si crea un'applicazione IoT Central di Azure, l'utente viene aggiunto automaticamente al ruolo di **amministratore** per l'applicazione.

## <a name="add-users"></a>Aggiungere utenti

Ogni utente deve avere un account utente prima di poter accedere a un'applicazione Azure IoT Central. Gli account Microsoft (account del servizio gestito) e gli account di Azure Active Directory (Azure AD) sono supportati in Azure IoT Central. I gruppi di Azure Active Directory non sono attualmente supportati in Azure IoT Central.

Per altre informazioni, vedere [Guida di Account Microsoft](https://support.microsoft.com/products/microsoft-account?category=manage-account) e [Guida introduttiva: Aggiungere nuovi utenti ad Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

1. Per aggiungere un utente a un'applicazione IoT Central, passare alla pagina **Utenti** nella sezione **Amministrazione**.
    
    > [!div class="mx-imgBorder"]
    >![Gestire gli utenti](media/howto-manage-users-roles-pnp/manage-users-pnp.png)

1. Per aggiungere un utente, nella pagina **Utenti** scegliere **+ Aggiungi utente**.

1. Scegliere un ruolo per l'utente dal menu a discesa **Ruolo**. Per altre informazioni sui ruoli, vedere la sezione [Gestire i ruoli](#manage-roles) in questo articolo.

    > [!div class="mx-imgBorder"]
    >![aggiungere un utente e selezionare un ruolo](media/howto-manage-users-roles-pnp/add-user-pnp.png)

    > [!NOTE]
    > Un utente che si trova in un ruolo personalizzato che concede loro l'autorizzazione ad aggiungere altri utenti può solo aggiungere utenti a un ruolo con le stesse autorizzazioni o un minor numero di autorizzazioni rispetto al proprio ruolo.

### <a name="edit-the-roles-that-are-assigned-to-users"></a>Modificare i ruoli assegnati agli utenti

I ruoli non possono essere modificati dopo essere stati assegnati. Per modificare il ruolo assegnato a un utente, eliminare l'utente e aggiungerlo nuovamente con un ruolo diverso.

> [!NOTE]
> I ruoli assegnati sono specifici dell'applicazione IoT Central e non possono essere gestiti dal portale di Azure.

## <a name="delete-users"></a>Eliminare gli utenti

Per eliminare gli utenti, selezionare una o più caselle di controllo nella pagina **Utenti**. Selezionare **Elimina**.

## <a name="manage-roles"></a>Gestire i ruoli

I ruoli consentono di controllare chi all'interno dell'organizzazione è autorizzato a eseguire varie attività in IoT Central. Sono disponibili tre ruoli predefiniti che è possibile assegnare agli utenti dell'applicazione. È anche possibile [creare ruoli personalizzati](#create-a-custom-role) se è necessario un controllo con granularità fine.

> [!div class="mx-imgBorder"]
> ![gestire la selezione dei ruoli](media/howto-manage-users-roles-pnp/manage-roles-pnp.png)

### <a name="administrator"></a>Amministratore

Gli utenti con il ruolo di **amministratore** possono gestire e controllare ogni parte dell'applicazione, inclusa la fatturazione.

All'utente che ha creato un'applicazione viene assegnato automaticamente il ruolo **Amministratore**. Ci deve sempre essere almeno un utente con il ruolo **Administrator** (Amministratore).

### <a name="builder"></a>Generatore

Gli utenti nel ruolo di **Generatore** possono gestire ogni parte dell'app, ma non possono apportare modifiche nelle schede amministrazione o esportazione dati continua.

### <a name="operator"></a>Operator

Gli utenti del ruolo **operatore** possono monitorare l'integrità e lo stato dei dispositivi. Non è consentito apportare modifiche ai modelli di dispositivo o amministrare l'applicazione. Gli operatori possono aggiungere ed eliminare dispositivi, gestire set di dispositivi ed eseguire analisi e processi. 

## <a name="create-a-custom-role"></a>Creare un ruolo personalizzato

Se la soluzione richiede controlli di accesso con granularità fine, è possibile creare ruoli personalizzati con set personalizzati di autorizzazioni. Per creare un ruolo personalizzato, passare alla pagina **ruoli** nella sezione **Amministrazione** dell'applicazione. Quindi selezionare **+ nuovo ruolo**e aggiungere un nome e una descrizione per il ruolo. Selezionare le autorizzazioni necessarie per il ruolo e quindi fare clic su **Salva**.

È possibile aggiungere utenti al ruolo personalizzato nello stesso modo in cui si aggiungono gli utenti a un ruolo predefinito.

> [!div class="mx-imgBorder"]
> ![compilare un ruolo personalizzato](media/howto-manage-users-roles-pnp/create-custom-role-pnp.png)

### <a name="custom-role-options"></a>Opzioni del ruolo personalizzato

Quando si definisce un ruolo personalizzato, si sceglie il set di autorizzazioni che un utente viene concesso se è membro del ruolo. Alcune autorizzazioni dipendono da altri. Ad esempio, se si aggiunge l'autorizzazione **Aggiorna dashboard applicazione** a un ruolo, l'autorizzazione **Visualizza dashboard applicazione** viene aggiunta automaticamente. Nelle tabelle seguenti vengono riepilogate le autorizzazioni disponibili e le relative dipendenze, che è possibile utilizzare per la creazione di ruoli personalizzati.

#### <a name="managing-devices"></a>Gestione dei dispositivi

**Autorizzazioni per i modelli di dispositivo**

| name | Dipendenze |
| ---- | -------- |
| Visualizza | Nessuno     |
| Gestione | Visualizza <br/> Altre dipendenze: visualizzare le istanze del dispositivo  |
| Controllo completo | Visualizza, Gestisci <br/> Altre dipendenze: visualizzare le istanze del dispositivo |

**Autorizzazioni per l'istanza del dispositivo**

| name | Dipendenze |
| ---- | -------- |
| Visualizza | Nessuno <br/> Altre dipendenze: visualizzare i modelli di dispositivo e i gruppi di dispositivi |
| Aggiornare | Visualizza <br/> Altre dipendenze: visualizzare i modelli di dispositivo e i gruppi di dispositivi  |
| Create | Visualizza <br/> Altre dipendenze: visualizzare i modelli di dispositivo e i gruppi di dispositivi  |
| Eliminazione | Visualizza <br/> Altre dipendenze: visualizzare i modelli di dispositivo e i gruppi di dispositivi  |
| Comandi Execute | Aggiorna, Visualizza <br/> Altre dipendenze: visualizzare i modelli di dispositivo e i gruppi di dispositivi  |
| Controllo completo | Visualizzare, aggiornare, creare, eliminare ed eseguire comandi <br/> Altre dipendenze: visualizzare i modelli di dispositivo e i gruppi di dispositivi  |

**Autorizzazioni per gruppi di dispositivi**

| name | Dipendenze |
| ---- | -------- |
| Visualizza | Nessuno <br/> Altre dipendenze: visualizzare i modelli di dispositivo e le istanze del dispositivo |
| Aggiornare | Visualizza <br/> Altre dipendenze: visualizzare i modelli di dispositivo e le istanze del dispositivo   |
| Create | Visualizza, aggiorna <br/> Altre dipendenze: visualizzare i modelli di dispositivo e le istanze del dispositivo   |
| Eliminazione | Visualizza <br/> Altre dipendenze: visualizzare i modelli di dispositivo e le istanze del dispositivo   |
| Controllo completo | Visualizzare, aggiornare, creare, eliminare <br/> Altre dipendenze: visualizzare i modelli di dispositivo e le istanze del dispositivo |

**Autorizzazioni di gestione della connettività del dispositivo**

| name | Dipendenze |
| ---- | -------- |
| Lettura istanza | Nessuno <br/> Altre dipendenze: visualizzare i modelli di dispositivo, i gruppi di dispositivi, le istanze del dispositivo |
| Gestisci istanza | Nessuno |
| Lettura globale | Nessuno   |
| Gestione globale | Lettura globale |
| Controllo completo | Lettura istanza, Gestisci istanza, lettura globale, Gestisci globale. <br/> Altre dipendenze: visualizzare i modelli di dispositivo, i gruppi di dispositivi, le istanze del dispositivo |

**Autorizzazioni processi**

| name | Dipendenze |
| ---- | -------- |
| Visualizza | Nessuno <br/> Altre dipendenze: visualizzare i modelli di dispositivo, le istanze del dispositivo e i gruppi di dispositivi |
| Aggiornare | Visualizza <br/> Altre dipendenze: visualizzare i modelli di dispositivo, le istanze del dispositivo e i gruppi di dispositivi |
| Create | Visualizza, aggiorna <br/> Altre dipendenze: visualizzare i modelli di dispositivo, le istanze del dispositivo e i gruppi di dispositivi |
| Eliminazione | Visualizza <br/> Altre dipendenze: visualizzare i modelli di dispositivo, le istanze del dispositivo e i gruppi di dispositivi |
| Esegui | Visualizza <br/> Altre dipendenze: visualizzare i modelli di dispositivo, le istanze del dispositivo e i gruppi di dispositivi; Aggiornare le istanze del dispositivo; Eseguire comandi sulle istanze del dispositivo |
| Controllo completo | Visualizzare, aggiornare, creare, eliminare ed eseguire <br/> Altre dipendenze: visualizzare i modelli di dispositivo, le istanze del dispositivo e i gruppi di dispositivi; Aggiornare le istanze del dispositivo; Eseguire comandi sulle istanze del dispositivo |

**Autorizzazioni per le regole**

| name | Dipendenze |
| ---- | -------- |
| Visualizza | Nessuno <br/> Altre dipendenze: visualizzare i modelli di dispositivo |
| Aggiornare | Visualizza <br/> Altre dipendenze: visualizzare i modelli di dispositivo |
| Create | Visualizza, aggiorna <br/> Altre dipendenze: visualizzare i modelli di dispositivo |
| Eliminazione | Visualizza <br/> Altre dipendenze: visualizzare i modelli di dispositivo |
| Controllo completo | Visualizzare, aggiornare, creare, eliminare <br/> Altre dipendenze: visualizzare i modelli di dispositivo |

#### <a name="managing-the-app"></a>Gestione dell'app

**Autorizzazioni per le impostazioni dell'applicazione**

| name | Dipendenze |
| ---- | -------- |
| Visualizza | Nessuno     |
| Aggiornare | Visualizza   |
| Copia | Visualizza <br/> Altre dipendenze: visualizzare i modelli di dispositivo, le istanze di dispositivo, i gruppi di dispositivi, i dashboard, l'esportazione dei dati, la personalizzazione, i collegamenti guida, i ruoli personalizzati, le regole |
| Eliminazione | Visualizza   |
| Controllo completo | Visualizzazione, aggiornamento, copia, eliminazione <br/> Altre dipendenze: visualizzare modelli di dispositivi, gruppi di dispositivi, dashboard dell'applicazione, esportazione dei dati, personalizzazione, collegamenti alla guida, ruoli personalizzati, regole |

**Autorizzazioni esportazione modello applicazione**

| name | Dipendenze |
| ---- | -------- |
| Visualizza | Nessuno     |
| Esportazione | Visualizza <br/> Altre dipendenze: visualizzare i modelli di dispositivo, le istanze di dispositivo, i gruppi di dispositivi, i dashboard, l'esportazione dei dati, la personalizzazione, i collegamenti guida, i ruoli personalizzati, le regole |
| Controllo completo | Visualizza, Esporta <br/> Altre dipendenze: visualizzare modelli di dispositivi, gruppi di dispositivi, dashboard dell'applicazione, esportazione dei dati, personalizzazione, collegamenti alla guida, ruoli personalizzati, regole |

**Autorizzazioni di fatturazione**

| name | Dipendenze |
| ---- | -------- |
| Gestione | Nessuno     |
| Controllo completo | Gestione |

#### <a name="managing-users-and-roles"></a>Gestione di utenti e ruoli

**Autorizzazioni per ruoli personalizzati**

| name | Dipendenze |
| ---- | -------- |
| Visualizza | Nessuno |
| Aggiornare | Visualizza |
| Create | Visualizza, aggiorna |
| Eliminazione | Visualizza |
| Controllo completo | Visualizzare, aggiornare, creare, eliminare |

**Autorizzazioni Gestione utenti**

| name | Dipendenze |
| ---- | -------- |
| Visualizza | Nessuno <br/> Altre dipendenze: visualizzare i ruoli personalizzati |
| Aggiungi | Visualizza <br/> Altre dipendenze: visualizzare i ruoli personalizzati |
| Eliminazione | Visualizza <br/> Altre dipendenze: visualizzare i ruoli personalizzati |
| Controllo completo | Visualizza, Aggiungi, Elimina <br/> Altre dipendenze: visualizzare i ruoli personalizzati |

> [!NOTE]
> Un utente che si trova in un ruolo personalizzato che concede loro l'autorizzazione ad aggiungere altri utenti può solo aggiungere utenti a un ruolo con le stesse autorizzazioni o un minor numero di autorizzazioni rispetto al proprio ruolo.

#### <a name="customizing-the-app"></a>Personalizzazione dell'app

**Autorizzazioni del dashboard dell'applicazione**

| name | Dipendenze |
| ---- | -------- |
| Visualizza | Nessuno     |
| Aggiornare | Visualizza   |
| Create | Visualizza, aggiorna |
| Eliminazione | Visualizza   |
| Controllo completo | Visualizzare, aggiornare, creare, eliminare |

**Autorizzazioni per Dashboard personali**

| name | Dipendenze |
| ---- | -------- |
| Visualizza | Nessuno     |
| Aggiornare | Visualizza   |
| Create | Visualizza, aggiorna   |
| Eliminazione | Visualizza   |
| Controllo completo | Visualizzare, aggiornare, creare, eliminare |

**Autorizzazioni per personalizzazione, favicon e colori**

| name | Dipendenze |
| ---- | -------- |
| Visualizza | Nessuno     |
| Aggiornare | Visualizza   |
| Controllo completo | Visualizza, aggiorna |

**Autorizzazioni collegamenti Guida**

| name | Dipendenze |
| ---- | -------- |
| Visualizza | Nessuno     |
| Aggiornare | Visualizza   |
| Controllo completo | Visualizza, aggiorna |

#### <a name="extending-the-app"></a>Estensione dell'app

**Autorizzazioni esportazione dati**

| name | Dipendenze |
| ---- | -------- |
| Visualizza | Nessuno     |
| Aggiornare | Visualizza   |
| Create | Visualizza, aggiorna  |
| Eliminazione | Visualizza   |
| Controllo completo | Visualizzare, aggiornare, creare, eliminare |

**Autorizzazioni token API**

| name | Dipendenze |
| ---- | -------- |
| Visualizza | Nessuno     |
| Create | Visualizza   |
| Eliminazione | Visualizza   |
| Controllo completo | Visualizza, crea, Elimina |

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come gestire utenti e ruoli nell'applicazione IoT Central di Azure, il passaggio successivo suggerito consiste nell'apprendere come [gestire la fattura](/howto-view-bill-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).
