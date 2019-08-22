---
title: Gestire utenti e ruoli nell'applicazione IoT Central di Azure | Microsoft Docs
description: Come amministratore, come gestire utenti e ruoli nell'applicazione IoT Central di Azure
author: v-krghan
ms.author: v-krghan
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 0c1a6b7370a4d1f8e01b22bfd52caa6cb6973947
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69880644"
---
# <a name="manage-users-and-roles-in-your-iot-central-application"></a>Gestire utenti e ruoli nell'applicazione IoT Central

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Questo articolo descrive come un amministratore può aggiungere, modificare ed eliminare gli utenti nell'applicazione IoT Central di Azure e anche come gestire i ruoli nell'applicazione di Azure IoT Central.

Per accedere alla sezione **Amministrazione** e usarla, è necessario disporre del ruolo **Amministratore** per l'applicazione Azure IoT Central. All'utente che crea un'applicazione Azure IoT Central viene automaticamente assegnato il ruolo **Amministratore** per l'applicazione.


## <a name="add-users"></a>Aggiungere utenti

Ogni utente deve avere un account utente prima di poter accedere a un'applicazione Azure IoT Central. Gli account Microsoft (account del servizio gestito) e gli account di Azure Active Directory (Azure AD) sono supportati in Azure IoT Central. I gruppi di Azure Active Directory non sono attualmente supportati in Azure IoT Central.

Per altre informazioni, vedere [Guida account Microsoft](https://support.microsoft.com/products/microsoft-account?category=manage-account) e [Guida introduttiva: aggiungere nuovi utenti ad Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

1. Per aggiungere un utente a un'applicazione IoT Central, passare alla pagina **Utenti** nella sezione **Amministrazione**.

    ![Elenco di utenti](media/howto-manage-users-roles-pnp/image1.png)

1. Per aggiungere un utente, nella pagina **Utenti** scegliere **+ Aggiungi utente**.

1. Scegliere un ruolo per l'utente dal menu a discesa **Ruolo**. Per altre informazioni sui ruoli, vedere la sezione [Gestire i ruoli](#manage-roles) in questo articolo.

    ![Selezione ruoli](media/howto-manage-users-roles-pnp/image3.png)

    > [!NOTE]
    >  Per aggiungere utenti in blocco, immettere gli ID utente di tutti gli utenti che si vuole aggiungere, separati da punti e virgola. Scegliere un ruolo dal menu a discesa **Ruolo**. Selezionare quindi **Salva**.

### <a name="edit-the-roles-that-are-assigned-to-users"></a>Modificare i ruoli assegnati agli utenti

I ruoli non possono essere modificati dopo che sono stati assegnati. Per modificare il ruolo assegnato a un utente, eliminare l'utente e aggiungerlo nuovamente con un ruolo diverso.

> [!NOTE]
> I ruoli assegnati sono specifici dell'applicazione IoT Central e non possono essere gestiti dal portale di Azure.

## <a name="delete-users"></a>Eliminare gli utenti

Per eliminare gli utenti, selezionare una o più caselle di controllo nella pagina **Utenti**. Selezionare **Elimina**.

## <a name="manage-roles"></a>Gestire i ruoli

I ruoli permettono di controllare quali utenti all'interno dell'organizzazione possono eseguire varie attività in IoT Central. Agli utenti dell'applicazione è possibile assegnare tre ruoli diversi.

### <a name="administrator"></a>Amministratore

Gli utenti con ruolo **Administrator** hanno accesso a tutte le funzionalità in un'applicazione.

All'utente che ha creato un'applicazione viene assegnato automaticamente il ruolo **Amministratore**. Ci deve sempre essere almeno un utente con il ruolo **Administrator** (Amministratore).

### <a name="application-builder"></a>Generazione applicazioni

Gli utenti con ruolo **Application Builder** (Compilatore applicazioni) possono eseguire tutte le operazioni in un'applicazione ad eccezione dell'amministrazione dell'applicazione. I generatori possono creare, modificare ed eliminare i dispositivi e i modelli di dispositivo, gestire i set di dispositivi ed eseguire analisi e processi. Questi utenti non hanno accesso alla sezione **Amministrazione** dell'applicazione.

### <a name="application-operator"></a>Operatore applicazione

Gli utenti con ruolo **Application Operator** (Operatore applicazioni) non possono apportare modifiche ai modelli di dispositivo né amministrare l'applicazione. Gli operatori possono aggiungere ed eliminare dispositivi, gestire set di dispositivi ed eseguire analisi e processi. Questi utenti non hanno accesso alle pagine **Application Builder** (Compilatore applicazioni) e **Amministrazione** dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come gestire gli utenti e i ruoli nel IoT Central di Azure, il passaggio successivo suggerito consiste nell'apprendere come [visualizzare la fattura](howto-view-bill-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) in Azure IOT Central.
